# appinfo.json Complete Reference

Full property reference for webOS TV app metadata.

## Required Properties

### id
- **Type**: String
- **Format**: Reverse DNS (`com.company.app.name`)
- **Rules**:
  - Lowercase letters, digits, periods, hyphens only
  - Must start with alphanumeric, min 2 chars
  - No `-` or `.+number` if using JS services
  - Cannot start with: `com.palm`, `com.webos`, `com.lge`, `com.palmdts`
- **Immutable** once published

### title
- **Type**: String
- **Usage**: Shown on Launcher and app window

### main
- **Type**: String
- **Default**: `index.html`
- **Path**: Relative to appinfo.json

### icon
- **Type**: String
- **Size**: 80x80 pixels PNG
- **Path**: Relative to appinfo.json

### type
- **Type**: String
- **Value**: Always `"web"`

### version
- **Type**: String
- **Format**: `X.Y.Z` (major.minor.revision)
- **Rules**:
  - No leading zeros (`2.1.0` not `002.1.0`)
  - All three numbers required
  - Max: `999999999.999999999.999999999`

## Optional Properties

### Display

| Property | Type | Description |
|----------|------|-------------|
| `largeIcon` | String | 130x130 PNG |
| `resolution` | String | `"1920x1080"` (default) or `"1280x720"` |
| `bgColor` | String | Background color (webOS 1.x/2.x only) |
| `bgImage` | String | 1920x1080 PNG background (webOS 1.x/2.x only) |
| `iconColor` | String | Icon background color, default `"white"` |
| `transparent` | Boolean | `true` = show system background clearly |

### Splash Screen

| Property | Type | Description |
|----------|------|-------------|
| `splashBackground` | String | 1920x1080 PNG while loading |
| `splashColor` | String | Splash background RGB (StandbyME only) |
| `splashFitModeOnPortrait` | String | `"none"` or `"width"` (StandbyME only) |

### Behavior

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| `disableBackHistoryAPI` | Boolean | `false` | `true` = app handles back button |
| `handlesRelaunch` | Boolean | `false` | `true` = app handles relaunch in background |
| `closeOnRotation` | Boolean | `false` | Close on portrait rotation (StandbyME) |
| `requiredMemory` | Number | - | Min MB required to run |

### Screensaver

```json
"screenSaverProperties": {
  "preferredType": 2
}
```

- Type 2: 30min timeout, gradual brightness decrease
- Type 3: 30min timeout (requires `"useGalleryMode": true`)

### Touch Support (StandbyME)

| Property | Type | Values |
|----------|------|--------|
| `supportTouchMode` | String | `"none"`, `"full"`, `"virtual"` |
| `supportPortraitMode` | String | `"false"`, `"true"` |

### Virtual Touch Config

```json
"virtualTouch": {
  "verticalThreshold": 40,
  "horizontalThreshold": 40,
  "positionEventOnPress": false,
  "shortTouchThreshold": 10
}
```

### Accessibility

```json
"accessibility": {
  "supportsAudioGuidance": true
}
```

Requires ARIA tags. Supported webOS TV 3.0+ (partial on 3.x).

### Other

| Property | Type | Description |
|----------|------|-------------|
| `vendor` | String | App owner name |
| `appDescription` | String | Tagline (max 60 chars) |
| `cloudgame_active` | Boolean | Direct gamepad input handling |
| `enablePigScreenSaver` | Boolean | PIG screensaver during non-fullscreen video |
| `useGalleryMode` | Boolean | Gallery picture mode |

## Full Example

```json
{
  "id": "com.company.app.myapp",
  "title": "My App",
  "main": "index.html",
  "icon": "icon_80x80.png",
  "largeIcon": "icon_130x130.png",
  "type": "web",
  "version": "1.0.0",
  "vendor": "My Company",
  "appDescription": "A great TV app",
  "resolution": "1920x1080",
  "splashBackground": "splash_1920x1080.png",
  "disableBackHistoryAPI": true,
  "handlesRelaunch": false,
  "requiredMemory": 50,
  "accessibility": {
    "supportsAudioGuidance": true
  }
}
```
