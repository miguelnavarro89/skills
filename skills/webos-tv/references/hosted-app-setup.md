# Hosted Web App Setup for webOS TV

Deploy a Next.js or any web app hosted on Vercel/Netlify/etc to webOS TV.

## Directory Structure

```
webos-app/
├── appinfo.json
├── index.html
├── icon_80x80.png
├── icon_130x130.png
└── splash_1920x1080.png (optional)
```

## Step 1: Create appinfo.json

```json
{
  "id": "com.yourcompany.app.appname",
  "title": "Your App Name",
  "main": "index.html",
  "icon": "icon_80x80.png",
  "largeIcon": "icon_130x130.png",
  "type": "web",
  "version": "1.0.0",
  "vendor": "Your Company",
  "resolution": "1920x1080",
  "disableBackHistoryAPI": true,
  "splashBackground": "splash_1920x1080.png"
}
```

## Step 2: Create Redirect index.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Your App Name</title>
  <style>
    body {
      margin: 0;
      background: #000;
      display: flex;
      justify-content: center;
      align-items: center;
      height: 100vh;
      color: #fff;
      font-family: system-ui, sans-serif;
    }
    .loading {
      text-align: center;
    }
  </style>
</head>
<body>
  <div class="loading">
    <p>Loading...</p>
  </div>
  <script>
    // Redirect to hosted app
    window.location.href = 'https://your-app.vercel.app';
  </script>
</body>
</html>
```

## Step 3: Prepare Icons

| File | Size | Format |
|------|------|--------|
| icon_80x80.png | 80x80 | PNG |
| icon_130x130.png | 130x130 | PNG |
| splash_1920x1080.png | 1920x1080 | PNG (optional) |

For LG Content Store submission, you'll also need a 400x400 icon uploaded separately.

## Step 4: Package and Deploy

```bash
# Package
ares-package ./webos-app

# Install
ares-install -d myTV com.yourcompany.app.appname_1.0.0_all.ipk

# Launch
ares-launch -d myTV com.yourcompany.app.appname

# Debug
ares-inspect -d myTV com.yourcompany.app.appname --open
```

## TV-Specific Considerations for Your Web App

### 1. Navigation Support

Ensure your hosted app supports both pointer and 5-way (arrow key) navigation:

```javascript
// In your web app
document.addEventListener('keydown', (e) => {
  const keyMap = {
    37: 'left',
    38: 'up',
    39: 'right',
    40: 'down',
    13: 'enter',
    461: 'back'
  };
  
  if (keyMap[e.keyCode]) {
    handleNavigation(keyMap[e.keyCode]);
  }
});
```

### 2. Focus Management

```css
/* Clear focus indicators for TV */
:focus {
  outline: 3px solid #fff;
  outline-offset: 2px;
}

/* Large touch targets */
button, a, [role="button"] {
  min-width: 44px;
  min-height: 44px;
}
```

### 3. Typography for 10-foot UI

```css
/* Minimum readable sizes for TV viewing */
body {
  font-size: 24px;
  line-height: 1.5;
}

h1 { font-size: 48px; }
h2 { font-size: 36px; }
p, li { font-size: 24px; }
```

### 4. Back Button Handling

```javascript
// Detect webOS environment
const isWebOS = typeof window.webOS !== 'undefined' || 
                typeof window.PalmSystem !== 'undefined';

if (isWebOS) {
  document.addEventListener('keydown', (e) => {
    if (e.keyCode === 461) {
      e.preventDefault();
      // Your back navigation logic
      if (history.length > 1) {
        history.back();
      }
    }
  });
}
```

### 5. Detect TV vs Browser

```javascript
function detectPlatform() {
  const userAgent = navigator.userAgent.toLowerCase();
  
  if (userAgent.includes('webos') || userAgent.includes('web0s')) {
    return 'webos';
  }
  if (userAgent.includes('tizen')) {
    return 'tizen';
  }
  return 'browser';
}
```

## Next.js Specific

### Configure for TV

```javascript
// next.config.js
module.exports = {
  output: 'export', // If you want static export
  
  // Or for hosted:
  async headers() {
    return [
      {
        source: '/(.*)',
        headers: [
          // Allow embedding in webOS app
          { key: 'X-Frame-Options', value: 'SAMEORIGIN' },
        ],
      },
    ];
  },
};
```

### Environment Detection

```typescript
// lib/platform.ts
export const isTV = () => {
  if (typeof window === 'undefined') return false;
  const ua = navigator.userAgent.toLowerCase();
  return ua.includes('webos') || ua.includes('tizen') || ua.includes('smarttv');
};

export const isWebOS = () => {
  if (typeof window === 'undefined') return false;
  return typeof (window as any).webOS !== 'undefined' || 
         typeof (window as any).PalmSystem !== 'undefined';
};
```

## Version Updates

When updating your hosted app version:

1. Update `version` in appinfo.json
2. Repackage: `ares-package ./webos-app`
3. Reinstall: `ares-install -d myTV <new-ipk-file>`

The redirect URL in index.html can point to the same hosted URL - only the shell needs updating for webOS version tracking.
