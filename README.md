# Skills Collection

A curated collection of agent skills for AI coding assistants.

## Install

```bash
npx skills add miguelnavarro/skills
```

This installs all 20 skills to your project or global agent directories.

## Skills

- **accessibility**: Accessibility specialist for WCAG compliance, semantic HTML, ARIA usage, keyboard navigation, color contrast, and screen reader support. Use when advising on accessible patterns, planning UI features, reviewing components for accessibility, or implementing accessible code and fixes.
- **agent-browser**: Browser automation using Vercel's agent-browser CLI. Use when you need to interact with web pages, fill forms, take screenshots, or scrape data. Alternative to Playwright MCP - uses Bash commands with ref-based element selection. Triggers on "browse website", "fill form", "click button", "take screenshot", "scrape page", "web automation".
- **art-to-web-analysis**: Analyze visual art, mockups, and designs (images/videos) to produce pixel-perfect web implementation specs. Use when converting design artwork to CSS/HTML specifications, reverse-engineering visual interfaces, or creating implementation specs from screenshots or design files.
- **astro-framework**: Build Astro websites using components, pages, layouts, content collections, routing, SSR, View Transitions, and integrations. Use when creating Astro projects, .astro files, content collections, server islands, actions, or when asking about Astro patterns, Islands Architecture, or static site generation.
- **bdd**: Apply BDD/TDD methodology for writing tests and specifications. Use when writing tests, Gherkin scenarios, feature files, acceptance criteria, discussing test strategy, mocking patterns, or when the user mentions BDD, TDD, Given-When-Then, pytest, unittest, test coverage, or test-first development.
- **bug-triage**: Classify, prioritize, and structure bug reports into actionable triage summaries. Use when a bug report is ambiguous, incomplete, or needs severity assessment. Triggers on: error logs, stack traces, 'something is broken', bug reports missing repro steps, severity disputes, or any unstructured failure signal. Produces a structured triage block with severity, blast radius, reproducibility, and next steps. Complements triage-issue (Jira operations) by handling the assessment layer.
- **bunjs**: Use Bun as runtime, package manager, bundler, and test runner. Use when running JS/TS with bun, installing packages with bun install, bundling with bun build, testing with bun test, or when user mentions Bun, bunfig.toml, or bun.lock.
- **code-review**: Perform structured code reviews on local changes or remote PRs. Analyzes correctness, security, performance, maintainability, and more. Supports goal-oriented reviews when a plan/spec is provided. Use when reviewing code, PRs, diffs, or when the user mentions code review, review this, or review PR.
- **cursor**: Comprehensive guide for Cursor IDE setup, configuration, features, and troubleshooting. Use when assisting with Cursor installation, agent modes, context features, rules, skills, MCP servers, integrations, or diagnosing issues.
- **gcp-logs**: Query and analyze GCP logs using gcloud CLI across resource types (GKE, Cloud Run, GCE, Cloud Functions, App Engine). Use when investigating errors, debugging services, analyzing incidents, or searching operational logs in Google Cloud.
- **identity**: Identity and authentication specialist for advising, planning, reviewing, and implementing sign-in/sign-up flows, passkeys, OAuth, and secure authentication. Use when designing identity systems, building auth features, integrating passkey or OAuth flows, or reviewing auth-related code.
- **logging-best-practices**: Logging best practices focused on wide events (canonical log lines) for powerful debugging and analytics.
- **mermaid-charts**: Write accurate Mermaid charts by understanding critical syntax rules and selecting the appropriate chart type for your visualization needs. Use when creating or reviewing flowcharts, sequence diagrams, class diagrams, state machines, ER diagrams, Gantt charts, or any of 24+ diagram types. Includes syntax pitfalls, chart selection guide, and links to official documentation.
- **nextjs**: Build Next.js 16 applications with App Router, Server/Client Components, data fetching, caching, and routing. Use when creating Next.js projects, pages, layouts, route handlers, server actions, or when asking about App Router patterns, RSC, streaming, or caching strategies.
- **opencode**: Setup, configure, and use OpenCode CLI tool - installation, providers, agents, tools, permissions, models, and troubleshooting. Use when working with OpenCode, opencode.json config, AGENTS.md, or when user mentions opencode, oc, TUI, or asks about AI terminal coding tools.
- **performance**: Web performance specialist for planning, advising, reviewing, and implementing performance optimizations. Use when building UI components, optimizing pages, debugging slow performance, improving LCP/CLS/INP metrics, or applying hands-on performance fixes.
- **refactoring**: Code refactoring specialist for analyzing code smells, applying refactoring techniques, and suggesting design patterns. Use when improving code quality, restructuring code, reviewing for maintainability, or hands-on refactoring implementations.
- **security**: Web security specialist for planning, advising, reviewing, and implementing security measures. Use when implementing features that handle sensitive data, building APIs, adding third-party dependencies, reviewing code for vulnerabilities (XSS, CSRF, injection, etc.), or hands-on hardening of applications.
- **tailwindcss**: Install and configure Tailwind CSS v4 for Vite, PostCSS, or Next.js projects. Customize themes with @theme directive, set up dark mode, extend design tokens. Use when setting up Tailwind, configuring themes, adding custom colors/fonts/spacing, or when user mentions Tailwind installation, @theme, CSS variables, or design tokens.
- **webos-tv**: Develop and deploy hosted web apps for LG webOS TV. Use when building TV apps, configuring appinfo.json, handling Magic Remote input, managing app lifecycle events, or deploying Next.js/web apps to webOS TV.
