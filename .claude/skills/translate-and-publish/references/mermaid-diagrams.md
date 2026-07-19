# Mermaid diagram convention (this blog)

The Hugo site renders Mermaid **centrally**. You do NOT theme, color, or size diagrams
inside a post — two layout files own all of that:

- `layouts/_markup/render-codeblock-mermaid.html` — turns ` ```mermaid ` fences into
  `<pre class="mermaid">`, and **strips any `%%{init}%%` directive and `classDef` color
  lines** out of the source.
- `layouts/_partials/extend_head.html` — loads mermaid.js only on pages that contain a
  diagram, defines the light/dark theme colors, the 7 semantic node classes (light + dark),
  the card container, larger font, and **re-renders on theme toggle** (watches
  `html[data-theme]`).

So the diagrams adapt to dark/light automatically. When you author a ` ```mermaid ` block,
follow this formula.

## Do

- **Give meaningful nodes a semantic class** so the site colors them (theme-aware). The
  seven classes and their intent:
  | class | 用途 (intent) | hue |
  |---|---|---|
  | `canon` | 정본 · 핵심 자산 · 원본 | indigo |
  | `derived` | 파생물 · 인덱스 · 캐시 | teal |
  | `proc` | 처리 · 과정 · 게이트 · 합성 | amber |
  | `ext` | 외부 · 경계 · 입출력 | sky |
  | `good` | 성공 · 완료 · 유지 · 통과 | green |
  | `bad` | 제거 · 실패 · 거부 | rose |
  | `neutral` | 중립 · 기타 | slate |

  Tag nodes with `class node1,node2 canon` (or `node:::canon`). **Keep the `class`
  assignments** — the render hook keeps them and the site colors them for both modes.

- **Prefer `flowchart TB` (top-down / vertical).** Wide `LR` diagrams shrink to fit the
  column on mobile → tiny, unreadable text. Long chains (`a --> b --> c --> …`) should flow
  top-down. Use `LR` only for genuinely short 2–3 node flows.

- **Use `<br/>` inside labels** to control node width and balance. Re-balance per language —
  English and Japanese run longer than Korean, so the line breaks differ.

- **Sequence diagrams**: just `sequenceDiagram` with participants/messages/notes. No init
  directive; the site themes actors, signals, notes, and loops.

## Don't

- **Don't bake `%%{init: {'theme':'base','themeVariables':{…}}}%%`.** It is stripped, and a
  baked light theme is exactly what broke dark mode. Theme is the site's job.
- **Don't write `classDef <name> fill:#… stroke:#… color:#…`.** It is stripped. Node colors
  live once in `extend_head.html`.
- **Don't hardcode hex colors** on nodes. Use the semantic classes above.

## To retune the palette, font, or card (once, for every diagram)

Edit `layouts/_partials/extend_head.html`:
- **Node colors** — the `--mmd-<class>-fill/stroke/text` CSS variables (light in `:root`,
  dark in `html[data-theme="dark"]`).
- **Base font size** — `const SIZE` (currently `16px`).
- **Chrome** (lines, clusters, edge labels, sequence actors/notes) — the `LIGHT` / `DARK`
  `themeVariables`.
- **Card** background/border — `--mmd-bg`, `--mmd-border`.

## i18n

Posts live under `content/ko/`, `content/en/`, `content/ja/` with the **same filename**
(directory-based i18n — NOT `slug.en.md` / `slug.ja.md` suffixes). When translating a
diagram, translate the **label text** but keep node ids, arrows, structure, `class`
assignments, and orientation **identical** across the three languages.
