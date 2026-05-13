# Webchat Sidebar Implementation Plan

> Created: May 5, 2026 | Updated: May 13, 2026

---

## Phase 1: Design Prep

- Get landing page design tokens from the designer (colors, fonts, spacing, border radius, shadows)
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

### Entry Points (how the chat opens)

1. **Floating text field** — fixed position, bottom-right corner. Placeholder text: "Ask me anything..." The visitor types a message and submits it. On submit, the chat sidebar/fullscreen opens and the message is sent as the first message in the conversation.
2. **Collapse/expand button** — on the sidebar edge. Clicking it opens the empty chat sidebar (no message sent yet).

### Responsive Behavior

**Desktop (>1024px)**

- Chat opens as a fixed sidebar: right side, 400-450px wide, full height
- Sidebar overlays on top of page content (no content shift, no backdrop)
- Collapse/expand button on the sidebar edge to open/close
- Slide-in animation from right (200-300ms ease)

**Tablet (768-1024px)**

- Same as desktop: overlay sidebar, 380px wide, full height
- Same collapse/expand button behavior

**Mobile (<768px)**

- Chat slides in from the right as full-screen (100vw x 100vh), same slide animation as desktop
- Close button in the chat header to return to the page
- No collapse/expand button
- Chat should not open automatically (blocks content)
- Composer stays above virtual keyboard when input is focused

### Floating Text Field

- Sticky (fixed position), stays visible at the bottom-right regardless of scroll
- Visible when chat is closed (all breakpoints). Hides when chat is open.
- Placeholder text: "Ask me anything..." (or similar, e.g. "Type your question here...")
- On submit: opens the chat and sends the typed message as the first message
- On mobile: full width with padding at bottom of screen. Tap opens full-screen chat, visitor types there

### Composer Input Behavior (all devices)

- **Send button disabled while bot is processing.** When the bot is generating a response (loader visible), the send button is unavailable. The visitor cannot send two messages in a row.
- **Speech-to-text icon** appears in the input field when it is empty (no text typed). As soon as at least one character is typed, the speech-to-text icon disappears and the send button appears instead. When the visitor stops dictating, the transcribed text appears in the field and the send button replaces the speech-to-text icon. Available on all devices.

## Phase 4: Styling

- Override Botpress CSS classes to match landing page design
- Style message bubbles, input field, header, fonts, colors
- Style the floating text field to match the landing page
- Style the collapse/expand button to match the landing page

## Phase 5: Testing

- Test full conversation flows (Discovery → Handoff)
- Test edge cases (long messages, file uploads if enabled, connection drops)
- Test open/close behavior via both entry points (floating field + collapse/expand)
- Test responsive behavior at all three breakpoints (desktop, tablet, mobile)
- Test that first message from floating field is sent correctly on chat open
- Test that floating field hides when chat is open and reappears when closed
- Test that send button is disabled while bot is processing/generating
- Test speech-to-text: icon visible when field is empty, disappears on typing, transcribed text appears after dictation
- Test virtual keyboard behavior on mobile

---

## Task Description for Claude Code (Website Repository)

Paste this when starting work in the website repo:

---

**Task: Implement Botpress Webchat Sidebar using React Library**

This project's sales AI chatbot runs on Botpress. The chat UI needs to be embedded as a sidebar on the landing page using the `@botpress/webchat` React library instead of the default Botpress floating bubble.

**What to build:**

- A `ChatSidebar` React component using individual Botpress webchat components (`Container`, `Header`, `MessageList`, `Composer`) from `@botpress/webchat`
- Use `useWebchat` hook for connection management
- Wrap in `StylesheetProvider`, override CSS to match site design
- Styled to match the landing page design system (colors, fonts, spacing)

**Entry points (how chat opens):**

1. Floating text field (fixed, bottom-right) with placeholder "Ask me anything..." — visitor types and submits, chat opens with that message sent. Hides when chat is open.
2. Collapse/expand button on the sidebar edge — opens empty chat

**Composer input behavior (all devices):**

The Botpress `Composer` component has built-in speech-to-text and a `disableComposer` prop. Verify the following behaviors work out of the box. Implement custom logic only if they don't:

- Send button disabled while bot is processing (loader visible) — visitor cannot send two messages in a row. Use `disableComposer` prop wired to bot processing state if not automatic.
- Speech-to-text icon in input field when empty. Disappears when any character is typed, replaced by send button. After dictation stops, transcribed text appears in field with send button.

**Responsive layout:**

- Desktop (>1024px): fixed sidebar, right side, 400-450px wide, full height, overlay on page content, slide-in animation (200-300ms ease)
- Tablet (768-1024px): same as desktop, 380px wide
- Mobile (<768px): full-screen, slides in from right (same animation as desktop), close button in header, no collapse/expand button, floating field is full-width at bottom, tap opens chat, composer stays above virtual keyboard

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
