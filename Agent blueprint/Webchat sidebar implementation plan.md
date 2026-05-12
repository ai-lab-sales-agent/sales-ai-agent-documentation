# Webchat Sidebar Implementation Plan

> Created: May 5, 2026

---

## Phase 1: Design Prep

- Get landing page design tokens from the designer (colors, fonts, spacing, border radius, shadows)
- Decide sidebar dimensions (width, height, position)
- Decide open/close trigger (button, icon, page scroll, etc.)
- Get the Client ID from Botpress Dashboard → Webchat → Advanced Settings

## Phase 2: Setup

- Install the package: `npm install @botpress/webchat`
- Requires React 18+
- Create a `ChatSidebar` component in the website codebase

## Phase 3: Build the Sidebar Component

Use individual Botpress components for full control:

- `Container` — wraps everything, handles connection status + file uploads
- `Header` — chat header (customize or replace with your own)
- `MessageList` — renders messages
- `Composer` — input field + send button

Use `useWebchat` hook to manage connection state + open/close. Wrap in `StylesheetProvider` for base styles, then override with your own CSS.

Position as fixed sidebar:

```css
position: fixed;
right: 0;
top: 0;
height: 100vh;
width: 400px;
```

Add open/close toggle button styled to match the landing page.

## Phase 4: Styling

- Override Botpress CSS classes to match landing page design
- Style message bubbles, input field, header, fonts, colors
- Test responsive behavior (mobile: full-width overlay, desktop: sidebar)

## Phase 5: Testing

- Test full conversation flows (Discovery → Handoff)
- Test edge cases (long messages, file uploads if enabled, connection drops)
- Test open/close behavior
- Test on mobile + desktop

---

## Task Description for Claude Code (Website Repository)

Paste this when starting work in the website repo:

---

**Task: Implement Botpress Webchat Sidebar using React Library**

This project's sales AI chatbot runs on Botpress. The chat UI needs to be embedded as a sidebar on the landing page using the `@botpress/webchat` React library instead of the default Botpress floating bubble.

**What to build:**

- A `ChatSidebar` React component using individual Botpress webchat components (`Container`, `Header`, `MessageList`, `Composer`) from `@botpress/webchat`
- Fixed sidebar panel (right side, full height) with a toggle button to open/close
- Styled to match the landing page design system (colors, fonts, spacing)
- Use `useWebchat` hook for connection management
- Wrap in `StylesheetProvider`, override CSS to match site design

**Botpress connection:**

- Client ID: [will be provided from Botpress Dashboard → Webchat → Advanced Settings]
- Package: `@botpress/webchat` (requires React 18+)

**Docs:**

- React library: https://botpress.com/docs/webchat/react-library/get-started.md
- Components: https://botpress.com/docs/webchat/react-library/components/container.md
- Webchat methods: https://botpress.com/docs/webchat/interact/reference.md

**Local development:**

- Set up local dev server so I can test the chat sidebar without pushing to production
- The Botpress bot is already published — the webchat connects to it via Client ID, so it works in local dev
- Provide instructions to run locally after setup

**Do not:**

- Use the default Botpress embed scripts or floating bubble
- Push to main branch without explicit approval
- Modify any existing landing page components without asking first
