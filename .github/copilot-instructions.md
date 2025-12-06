<!-- Copilot / AI agent instructions for `my-feedback-app` -->

# Repo snapshot

- Minimal React app (Create React App tooling) that implements an in-memory feedback UI.
- Entry: `src/index.js` → `src/App.js`.
- Main patterns: React Context for state, small presentational components under `src/components`, simple routing with `react-router-dom`, and animations via `framer-motion`.

# Quick commands

- Start dev server: `npm start` (uses `react-scripts start`).
- Build production bundle: `npm run build`.
- Run tests: `npm test` (CRA test runner).

# Architecture & important files

- State & business logic: `src/context/FeedbackContext.js`

  - Exposes: `feedback`, `deleteFeedback(id)`, `addFeedback(newFeedback)`, `editFeedback(item)`, `feedbackEdit`, `updateFeedback(id, updItem)`.
  - IDs are generated with `uuidv4()` inside `addFeedback`.
  - The app is single-page and stores data in-memory (no backend API integration).

- Routing and composition: `src/App.js`

  - Wraps app with `<FeedbackProvider>` then `react-router` `<Router>` and `<Routes>`.
  - Root route (`/`) renders `FeedbackForm`, `FeedbackStats`, `FeedbackList`.
  - `/about` route renders `src/pages/AboutPage.jsx` and `src/components/AboutIconLink.jsx` provides navigation.

- UI components:

  - `src/components/FeedbackForm.jsx` — controlled form, validation rule: text must be > 10 chars, uses `RatingSelect` and `Button`/`Card` shared components.
  - `src/components/FeedbackList.jsx` — reads `feedback` from context and uses `framer-motion` (`AnimatePresence`, `motion`) to animate items.
  - `src/components/FeedbackItem.jsx` — uses context `deleteFeedback` and `editFeedback` and shows `FaTimes`/`FaEdit` icons.
  - Shared primitives: `src/components/shared/Button.jsx`, `src/components/shared/Card.jsx` (Card supports `reverse` prop via inline style).

- Sample data file: `src/data/FeedbackData.js` — local sample dataset; not authoritative for runtime (context defines its own initial state).

# Project-specific conventions & patterns

- File extensions: `.jsx` for React components, `.js` for context/data and app entry files.
- Component props: most components use `propTypes` and `defaultProps` — maintain these when adding props.
- UI composition: small reusable `Card` and `Button` components are preferred over ad-hoc markup.
- ID generation and edits: new items are given `id` via `uuidv4()` in the context; edits set `feedbackEdit` (shape: `{ item, edit: boolean }`) so components read `feedbackEdit.item`.
- Validation: `FeedbackForm` enforces text length (10 chars) and toggles a submit button (`isDisabled` prop on `Button`). Follow the same pattern for new forms.

# Integration points & dependencies (explicit)

- `react-router-dom` for routing (`src/App.js`).
- `framer-motion` for list animations (`src/components/FeedbackList.jsx`).
- `react-icons` for iconography (`src/components/FeedbackItem.jsx`, `AboutIconLink.jsx`).
- `uuid` for id generation in context (`FeedbackContext.js`).
- `prop-types` is used across components for prop validation.

# What agents should and should not change

- Should preserve public API of the Context: do not rename `addFeedback`, `deleteFeedback`, `editFeedback`, `updateFeedback`, or `feedbackEdit` without updating all callers (`FeedbackForm`, `FeedbackItem`, etc.).
- Prefer adding features by extending the Context value rather than moving business logic into components.
- Do not introduce a backend or API wiring unless the user explicitly requests it — the current app is intentionally in-memory.

# Code examples (patterns found in repo)

- Add feedback (context):

  - Call: `addFeedback({ text, rating })` — context assigns `id`.

- Edit flow (component → context):

  - `editFeedback(item)` sets `feedbackEdit` → `FeedbackForm` reads `feedbackEdit` in `useEffect` to populate fields.

- Rating select pattern:
  - `RatingSelect` creates radios with `Array.from({ length: 10 })` and uses `name='rating'` to group them.

# Notes for tests & debugging

- Run `npm start` to debug locally. Open developer console to inspect `feedback` state (React DevTools recommended).
- Unit tests are not present beyond CRA defaults; if adding tests, mock Context by wrapping components with a test `FeedbackProvider` or by mocking the exported context value.

# Files to reference when editing or adding features

- `src/context/FeedbackContext.js` — single source of truth for feedback state.
- `src/components/FeedbackForm.jsx` — form validation and submit logic.
- `src/components/FeedbackList.jsx` & `src/components/FeedbackItem.jsx` — list rendering and delete/edit handlers.
- `src/components/shared/*.jsx` — shared UI primitives (Button, Card).

# Closing

If anything above is unclear or you want the agent to follow stricter rules (for example, add TypeScript, integrate a backend, or write tests), tell me which direction to take and I'll update these instructions.
