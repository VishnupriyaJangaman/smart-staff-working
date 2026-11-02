# Smart Staff Working

A college academic office app. It runs entirely in the browser.

**There is no backend.** Everything here is frontend code: HTML, CSS and plain JavaScript (no framework, no build step).
Data is kept in the browser of the person using it. See "Where data lives" below.

## Files

| File | What it holds |
|---|---|
| `index.html` | Page shell: sidebar, main area, toast, script and style links, CDN libraries |
| `style.css` | All styling: colour tokens, layout, dashboard, boards, animations, print rules |
| `assets.css` | Image variables (board shape, the two peeking figures) stored as base64 |
| `app.js` | All the logic for every module (about 120 KB, dense) |

Open `index.html` in a browser to run it. Edit the colours at the top of `style.css` (`:root{--bg,--sf,--tx,--ac,--ac2 ...}`).

## Technology used

- HTML5, CSS3 (custom properties, grid, flexbox, keyframe animations, `@media print`)
- Vanilla JavaScript (ES2020)
- Storage: `localStorage` and `IndexedDB`
- Libraries from cdnjs: **jsPDF 2.5.1**, **jspdf-autotable 3.5.31** (PDF), **SheetJS xlsx 0.18.5** (Excel)
- Browser APIs: Canvas (image resize), FileReader, Web Share (files), Clipboard, SpeechRecognition, MediaRecorder, `MutationObserver`
- Inline SVG (charts, attendance bars, board shape)
- Claude runtime features (only exist inside the Claude page): `claude.use("sample")` for AI, `claude.use("downloads")` for saving files

## Where data lives

| Store | Key | Holds |
|---|---|---|
| `localStorage` | `sca2` | Main database `DB`: timetable text, calendar, events, attendance log, lesson plans, projects, notes, WhatsApp links |
| `localStorage` | `srp` | Student records from the portal |
| `IndexedDB` (db `srp`, store `f`) | `p:<id>`, `d:<id>`, `m:<id>` | Student photos and document images |

Data is per browser and per device. Clearing browser data deletes it.

## Where each module is in `app.js`

| Module | Main functions |
|---|---|
| Dashboard (Today) | `R.home`, `pins`, `PV` (proverbs), `briefGo` (AI briefing) |
| Timetable | `R.tt`, `ttAdd`, `ttl`, `genTT` |
| Academic calendar | `R.cal`, `calr`, `calAdd`, `calSend` (PDF) |
| Event scheduler | `R.ev`, `genEV`, `evl`, `calSend('ev')` |
| Registration link | `R.rl`, `rlSend`, `P_impS` (reads Google Form CSV) |
| Batch folders | `R.bf`, `V.stu`, `srch`, `fold`, `bfRun`, `bfXl`, `bfPdf` |
| Batch export | `V.exp`, `xl`, `pdfB`, `pdfOne` |
| Attendance | `R.at`, `attAsk` (AI), `mkSave`, `atr`, `attPct` |
| Projects | `R.pj` (generic `crud`) |
| Lesson plans | `R.lp`, `lpAdd`, `lpr` |
| Question papers | `R.qp`, `qpGo` (AI with images), `qpShow`, `qpPrint` |
| Circulars, Event reports | `R.ci`/`circ`, `R.er`/`rep` |
| Notes from syllabus | `R.nt`, `ntGen` (AI), `ntPdf` |
| AI agent | `R.ag`, `ask`, `askKw` (keyword fallback) |
| Look and feel | hand cursor (`#hand`), dot caret (`#cd`), board fields (`data-d` set by a `MutationObserver`) |

The student portal code sits inside one function (an IIFE) near the middle of `app.js`. It exposes its handlers as `P_...` globals.

## What does not work outside Claude

- AI features (`sample`) and file saving (`downloads`) use the Claude page runtime. On your own website they are unavailable, and the app falls back to simple keyword matching or shows a message.
- To use AI on your own site you need a server that calls the Claude API with your key. Never put the key in this front-end code.
- To save files on your own site, replace `claude.use("downloads")` with a normal `<a download>` link (see `save`, `bfSave`, `xl` in `app.js`).

## If you need a real backend

Shared data between staff, and student form answers arriving automatically, need a server and a database (for example Node.js with a database). That part is not written yet.
