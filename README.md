# Code Playground

A lightweight, in-browser code development environment built with Vue 3 and CodeMirror 6. This project provides a real-time sandbox for testing HTML, CSS, and JavaScript with layout-responsive UI controls.

## Tech Stack
- **Framework:** Vue 3 (Composition API)
- **Editor Engine:** CodeMirror 6
- **Styling:** Tailwind CSS
- **Build Tool:** Vite
- **Language:** TypeScript

## Features
- **Live Preview:** Real-time rendering of code using iframe `srcdoc`.
- **Tabbed Interface:** Seamless switching between HTML, CSS, and JavaScript.
- **Layout Control:** Fixed sizing options (30/70, 50/50, 70/30) for optimizing the workspace.
- **Modular Architecture:** Decoupled editor and preview components for maintainability.

## Known Limitations & Security Constraints
Because the preview environment runs inside a sandboxed `iframe`, the following restrictions apply to protect the parent application:

- **Restricted Origins:** The preview runs in a unique origin. Access to `localStorage`, `sessionStorage`, and `document.cookie` is blocked by the browser.
- **Console Visibility:** `console.log` statements are executed within the iframe's scope. They will not appear in the main application's browser console by default.
- **Navigation Blocked:** The code cannot trigger top-level navigations, `window.open` (popups), or redirect the parent page.
- **Form Submission:** Form actions are disabled.
- **Modals:** `alert()`, `prompt()`, and `confirm()` are blocked by the browser's sandbox security policies.
- **Parent Access:** The code cannot access the parent `window` object or the main application's DOM.

## Installation

### Prerequisites
- Node.js (v18+)
- npm

### Setup
1. Clone the repository:
   ```bash
   git clone [your-repo-url]
   cd code-playground