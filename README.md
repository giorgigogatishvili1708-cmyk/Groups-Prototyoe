# Groups prototype (BoG)

This is a clickable, mobile-first prototype of the Groups module: create a group, then split or request money. It covers the 27-frame linear flow from Figma (file `o9couvV1p4q4ypRfCmchZg`, section "Groups · სწორხაზოვანი ფლოუ").

It mirrors the SwiftUI app structure:

- a `*View` per screen
- a NavigationStack-style `path`
- sheets and alerts as controlled overlays

All data is mocked in memory. Nothing is persisted, and there is no localStorage.

## Two versions

- **`Groups-Prototype.html`** / `http://localhost:5173/`: the full prototype with the dev panel (everything below).
- **`Groups-Flows.html`** / `http://localhost:5173/flows.html`: the usability-test version. It runs the five tasks as one continuous session (task 1 opens on load; each finished task leads into the next), has no phone frame or iOS status bar, and works in any phone browser. See SCENARIOS.md.

`npm run build` writes both files.

## Run it on your computer

**Quickest: double-click `Groups-Prototype.html`.** It opens in your browser straight from the folder, with no server and nothing to install. It is one self-contained file (all code, styles and images inlined), so you can also AirDrop or email it and open it on a phone. Rebuild it after code changes with `npm run build`.

For the live dev server (LAN URL for your phone), you need Node 18+, or Python 3 as a fallback:

- **macOS:** double-click `start.command`. The first time, right-click it and choose Open, or run `chmod +x start.command`. It opens the browser for you. If macOS blocks it, run `xattr -d com.apple.quarantine start.command` in Terminal, or use `bash start.command`.
- **Any OS:** in this folder, run `npm run dev` (same as `node scripts/serve.mjs`).

Then open **http://localhost:5173/**.

- **Desktop:** a 375×812 phone frame with a dev panel. The panel can jump to any frame 01–27, restart the flow, slow animations ×3, and show tap targets.
- **Phone:** connect to the same Wi-Fi and open the LAN URL the server prints (`http://<your-mac-ip>:5173/`). It runs full screen. Use "Add to Home Screen" for a test without browser chrome.
- **Hide the dev panel:** add `?dev=0`.
- **Component gallery:** `/?gallery&dev=0`.
- **Another port:** `PORT=8080 npm run dev`.

## The flow (see FLOW.md)

The tap path is:

01 დაწყება → 02 ჯგუფის შექმნა (03 empty-name alert, 04 filled) → დამატება → 05/06 pick members → 07 → 08 created → 09 group → თანხის მოთხოვნა → 10 sheet → თანხის გაყოფა → 11/12 pick transactions → 13 split (by amount / share / receipt) → 14 summary → 14s sent → 15 request sent → 16 details → დასრულება (16e alert → 16d done) → 17 incoming request → 18 → უარყოფა (19 alert) or შემდეგი → 20 transfer → 21 done → 22 settled → ⋯ → 23 menu → 24 edit (25 leave alert) → save → 26 toast (2.5 s) → 22 → ⋯ → წაშლა → 27 → 01.

Working behaviour:

- **Group name:** real input, with validation.
- **Members:** toggle with checkmark and selected count.
- **Group symbol:** 7 symbols. The strip scrolls horizontally by touch, mouse drag or wheel.
- **Split** (from the Bill Split / Money Request module): by amount (edit one, the rest re-split equally), by share (steppers) or by receipt (take a photo or upload a real image, max 2). Any remainder goes to the payer.
- **Settle up (28–30):** from the Details tab, pay every open request to one member at once (checkboxes, block sheet).
- **Details (16):** statuses, resend with toast, force-end alert, all-paid state and result page.
- **Requests:** statuses shown as DS Inline Messages and Group Request Bubbles.
- **Edit:** changes the group name, symbol and members.
- **Delete:** resets everything to 01.

## Motion (milestone F)

All timings come from tokens (`tokens/figma-variables.json` → `motion`):

- **Push/pop:** 450 ms `cubic-bezier(0.32,0.72,0,1)`. The previous screen shifts 30% and dims.
- **iOS edge swipe back:** drag from the left edge.
- **State changes:** 250 ms dissolve.
- **Sheets:** 400 ms. **Scrim:** 300 ms.
- **Menu:** scale 0.96→1 plus fade.
- **Toast:** holds 2.5 s.
- **Press:** scale 0.97.
- **Other:** checkmark pop, count-up on the 13/14 amounts, and a success entrance on 08/21.

`prefers-reduced-motion` switches everything to a 150 ms fade. Interrupted transitions settle immediately, so there is only ever one screen in the DOM.

## Design system and tokens

- **Components:** every UI element is a component from "Mobile Components - SwiftUI & Compose". They live in `src/ds/` and are listed in `COMPONENTS.md`, with Figma names and variant/prop names.
- **Tokens:** `tokens/figma-variables.json` is a snapshot of the Figma variables, with names kept. Run `npm run tokens` to regenerate `src/tokens/tokens.css|js`.
- **Lint:** `npm run lint` fails on raw hex, rgb, px or ms outside the token files.
- **Copy:** all copy is in `src/i18n/ka.js`, verbatim from Figma text nodes.
- **Assets:** icons, logos and photos were exported from Figma into `src/assets/`.

## QA

This needs Python Playwright.

- `python3 qa/walk.py`: the full 01→27 walk. It asserts `data-screen` on every frame, the split math, Esc/alerts, toast auto-dismiss and delete reset. It checks 44×44 hit areas and saves `qa/screens/{C,D,E}-NN.png`.
- `python3 qa/motion.py`: push/pop, interrupted navigation, edge swipe (cancel and commit), sheet out, count-up and reduced motion.
- `python3 qa/iphone.py`: iPhone 14 emulation of every frame (full-screen device mode).
- `qa/figma/NN.png` holds the Figma references. `QA_REPORT.md` has the per-frame comparison.

## Known gaps

Everything the Figma file doesn't define is logged in `OPEN_QUESTIONS.md`, and nothing was invented silently. The main points:

- **BOG font:** embedded as a web font (`src/assets/fonts/`, 400/500/600/700), so it works on every device.
- **Sample data:** the Figma sample data is inconsistent across frames, so the prototype keeps one consistent story.
- **Links Figma leaves undefined:** confirm-decline (19) and "leave" (25) have no Figma link; the behaviour chosen is noted in OPEN_QUESTIONS.
- **Menu material:** the Liquid Glass menu is approximated with a translucent blur.
- **Inert elements:** toolbar tabs, account selectors and "see all" have no destination in Figma. They give press feedback only.

## Stack

Plain ES modules and CSS, with no build step. The npm registry was blocked when this was built, so React, Vite and similar tools weren't available.

SwiftUI naming is kept: `*View`, `flowStore` as the NavigationStack path, and `VStack/HStack/ZStack` helpers.

| Folder | Contents |
|---|---|
| `src/tokens` | Generated tokens |
| `src/ds` | Design-system components |
| `src/screens` | Views, overlays, dev jumps |
| `src/store` | Flow, group and request stores |
| `src/mock` | Members, transactions |
| `src/motion` | Transitions, edge swipe |
| `src/dev` | Dev panel |
