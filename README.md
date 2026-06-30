# jsPDF Builder

A visual PDF layout editor that generates copy-paste-ready jsPDF JavaScript code. Design your layout by dragging elements onto a scaled A4 canvas, tweak properties visually, and get the exact code to reproduce it in any JavaScript project.

**[→ Try it live](https://jspdf-builder.vercel.app/)**

---

## The Problem

Building PDFs with jsPDF means guessing coordinates in millimetres, running the code, checking the output, adjusting, and repeating. Text baseline offsets, portrait vs landscape confusion, and centering math make it worse. Every layout change is a tedious cycle of code → generate → compare → adjust.

## The Solution

jsPDF Builder gives you a scaled A4 canvas where you place elements visually. Every coordinate maps directly to jsPDF's millimetre system — what you see on the canvas is what jsPDF renders. When your layout looks right, hit "Generate Code" and paste the output into your project.

---

## Features

**Visual Canvas**
- Drag-and-drop placement of text, lines, rectangles, and image placeholders
- All coordinates stored in millimetres — matches jsPDF's coordinate system exactly
- Portrait and landscape orientation toggle
- Click-to-select with a property panel for precision editing

**Property Panel**
- Fine-tune position (X/Y in mm), dimensions, color, font family, font size, and text alignment
- Alignment tools — center horizontally, vertically, or both
- Duplicate elements and reorder layers

**Code Generation**
- Live jsPDF code output that updates as you edit
- Baseline Y-offset correction built into the generator (`pdfY = canvasY + fontSize × 0.352`) so PDF output matches visual placement
- Custom font name registry so generated code references your fonts correctly
- One-click copy to clipboard

**Layout Management**
- Save and load layouts as JSON for reuse and sharing
- Built-in "How To" guide covering the full custom font pipeline (TTF → base64 → jsPDF registration)

---

## Architecture

```
Toolbar ──────────────────────────────────────────────┐
  (add element, toggle orientation, save/load JSON)    │
                                                       ▼
Canvas ─────────────── reads/writes ──────► useBuilderStore.ts
  (drag to reposition, click to select)                │
                                                       │
PropertyPanel ─────── reads/writes ───────────────────┘
  (edit x, y, width, font, color, etc.)                │
                                                       ▼
                                              codeGenerator.ts
                                                       │
                                                       ▼
                                              CodeOutput.tsx
                                         (displays generated code)
```

A single Zustand store owns all builder state — elements, selection, orientation, custom fonts. Components read from and dispatch to this store directly, avoiding prop drilling. The code generator is a pure function with no side effects: it takes the element array and orientation, and outputs a jsPDF code string. This separation means the generation logic is independently testable and easy to extend with new element types.

---

## Tech Stack

| Technology | Role |
|------------|------|
| React 19 | UI framework |
| TypeScript | Type safety across the codebase |
| Vite | Build tool and dev server |
| Zustand | Single-store state management |
| jsPDF | PDF generation reference |
| Clerk | Authentication gate |
| uuid | Unique element ID generation |

---

## Technical Decisions

**Millimetres everywhere.** All stored coordinates are in mm, matching jsPDF's native system. The canvas converts between CSS pixels and mm using a fixed scale factor. This eliminates the class of bugs where visual position and generated code diverge — which is exactly what the predecessor tool got wrong.

**Pure code generation.** `codeGenerator.ts` has no React imports, no store dependency, no side effects. It's a data-in, string-out transformation. The app does not generate actual PDF files at runtime — it generates the code that will, so you paste it into your own project with confidence that coordinates are correct.

**Baseline correction.** jsPDF's `doc.text()` Y coordinate references the text baseline, not the top of the element. The generator applies `fontSize × 0.352` mm offset automatically so what you position on the canvas is where the text appears in the PDF.

**What it replaced.** An earlier builder had a coordinate mismatch bug — portrait A4 dimensions (210 × 297mm) were being used for landscape layouts (297 × 210mm), and text baseline offsets weren't accounted for. This tool was built from scratch to fix both issues with explicit orientation handling that affects canvas rendering and code output.

---

## Demo

**[→ jspdf-builder.vercel.app](https://jspdf-builder.vercel.app/)**

---

## Source Code Access

The source code for this project is maintained in a private repository. If you'd like to review the codebase — for hiring evaluation, collaboration, or technical discussion — please reach out:

- **GitHub:** [@M33p5t3r](https://github.com/M33p5t3r)

Access is granted on request.

---

## License

MIT
