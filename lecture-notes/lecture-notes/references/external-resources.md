# External resources

`references/external-resources.md` records the libraries and typefaces a lecture document can load, each checked against the requirements in "How to use `references/external-resources.md`", and is the place defaults live: SKILL.md mentions a library by name only in passing, as an example, never as a choice, so editing `references/external-resources.md`, which is the user's to do, is what changes which resources get used. The path `references/external-resources.md`, in SKILL.md and in `references/external-resources.md` alike, resolves relative to the lecture-notes skill's own directory, not to the working directory.

`references/external-resources.md` applies to HTML documents only. A lecture document delivered as a Word file, a PDF or slides is produced by that format's own tool or skill, and SKILL.md's "A lecture document in another format" sets the rules in `references/external-resources.md` aside for it.

Every version, file path and API detail in `references/external-resources.md` was checked against the registry, the CDN or the library's own source on 2026-09-21, which is when each fact held, not a promise that it still does.

## How to use `references/external-resources.md`

These resources have already been checked, so starting from this file saves the research. **This is a reference, not a whitelist.** A resource outside this file may fit a given document better, and choosing one is fine; what is fixed is the rules in this section, not the list of entries.

Read this file while planning, before deciding which figures the document will have, because knowing what "Libraries" covers, and what it does not, is what shows early which figures have a library behind them and which will need one found beyond this file or asked about. Read it again before adding a figure to a document that already exists. Every HTML document loads typefaces, including one with no figures at all, so "Typefaces" applies even when no library is needed.

### Rules for any resource

- **Start from the relevant entry in this file.** If one fits, use it and skip the search.
- **Verify every URL by requesting it** before it goes into the document — scripts, stylesheets and font endpoints alike. Request font stylesheets with a browser User-Agent: Google Fonts answers a plain request with a different stylesheet (TrueType sources) from the one a browser gets (WOFF2 sources), so a check made without one is not a check of what the reader will receive.
- **Report every external resource the document loads**, libraries and typefaces both, with its version where it has one and what it is for. Mark any that did not come from this file and give the reason it was chosen. This file is read-only guidance that no kind of turn edits, as "The skill's own files are read-only guidance" in SKILL.md states, so put a proposed entry in the reply, written in the shape "Entry format" sets out, for the user to add.
- **If nothing suitable exists, ask the user** rather than building the missing piece or silently producing a weaker result. Ask about that one figure, not about the document: everything else is delivered, with a marked gap where the figure belongs. A single unresolved figure never holds up the rest.
- **Load nothing the document does not use.** A page with no formulas loads no math renderer. Typefaces are the exception in one direction: every HTML document loads the faces it sets text in, rather than naming local families and inheriting whatever the reader has installed.

### Additional rules for libraries

- **A library must be a large, actively maintained project with a CDN build.** Be wary of low-level toolkits: reaching for one usually means writing the layout, geometry and interaction by hand, which is the work this skill exists to avoid. That is a reason to prefer a higher-level library, not a ban.
- **Resolve the version yourself.** Query the registry — `https://data.jsdelivr.com/v1/packages/npm/<package>` reports the current `latest` — then request the exact file before writing its URL into the document, and pin that exact version. The versions recorded in this file are a starting point for that check, not a substitute for it.
- **Check the API against the version loaded**, in that version's documentation or source. Do not write library calls from memory. Item 15 in SKILL.md asks for the same check, and doing it carefully is what keeps a wrong call out, because at generation time such a call surfaces only where the code path actually runs: during the one page load, or during the pass over the native controls. A wrong call inside a drag handler or a canvas-drawn control's callback runs at neither, and goes unnoticed until the runtime round.

### Additional rules for typefaces

- **A typeface must come from a stable font CDN**, not a personal or single-purpose host.
- **Request only the families and weights the document sets text in.** A stylesheet endpoint that carries no version is verified by request rather than pinned; a versioned font package is pinned like a library.
- **Confirm the family names match.** A typeface has no API to check, so what takes the place of checking one is confirming that the family names in the document's CSS are the names the endpoint actually serves.

### Loading under the synchronous rule

SKILL.md allows two arrangements: libraries loaded with plain synchronous script tags, or every inline script that calls a library — the skeleton's shared script where it makes such a call, as the `mermaid.initialize` call in the Mermaid entry does, each section's, and the final pass's — waiting for `DOMContentLoaded`. The entries in this file assume the first, which is the simpler one, and the rules in this section are written for it. Under the second, `defer` is also safe, since a deferred script runs before `DOMContentLoaded` fires, but only when every one of those scripts waits, the final pass included: the final pass sits at the end of the body and looks late enough, and is not, since an inline script runs when the parser reaches it and a deferred library runs only after parsing ends — `renderMathInElement(document.body)` called plainly from the final pass under a deferred KaTeX fails with the function undefined. `async` is safe under neither arrangement, since it can run after `DOMContentLoaded`, and an ES-module build fits neither, since what a module exports is not a global a section's script can call. Four rules apply under the first arrangement, the first two because library documentation often shows what it does not allow:

- **Drop `defer` and `async`.** The MathJax package's own README, for one, loads it with `defer`. Under synchronous loading the attribute goes.
- **Do not use an ES-module build.** A `<script type="module">` is deferred by default even with no attribute on it, so it breaks the rule silently. Use the classic build each entry lists, which exposes a global.
- **Load every library in the skeleton, before the shared script and any section's script**, and load an extension after the library it extends — KaTeX's `auto-render` and `mhchem` after `katex.min.js`.
- **Some libraries finish their work asynchronously even when their script loads synchronously.** MathJax typesets after it starts up, Mermaid renders its diagrams on page load, and OpenSheetMusicDisplay loads a score before it can draw it. Every entry's **Loading** bullet states whether that library finishes asynchronously and what signal it gives, and that is where the answer for any particular library is. A section's inline script must not read what such a library produces — the size of a typeset formula, a rendered diagram, an engraved score — while the page is still parsing. Wait for the signal its entry names. SKILL.md's one-load check waits for the same signals before judging whether content rendered.

### Entry format

A proposed entry in a reply, and every entry in "Libraries" and "Typefaces", takes one of two shapes.

A **library** entry is a level-four heading, `#### <Name> — <what it is for>`, followed by these bullets in this order, omitting none:

- **Package and version** — the npm package, the version, and the date it was checked.
- **Files** — every file the page loads for it, as full CDN URLs, in load order.
- **Global** — the name a section's script calls.
- **Loading** — how it behaves under "Loading under the synchronous rule", including any signal to wait for.
- **Use it for** — what the figure has to do for this to be the right choice, and what sends the choice elsewhere.
- **Pitfalls** — failures found in checking that are not obvious from its documentation.

A **typeface** entry is a row in the role table under "Typefaces", together with the record of where it was verified: for Google Fonts, a URL in the list of verified stylesheets under the same heading; for Fontsource, the package with its pinned version.

## Libraries

### Selection map

| What the figure has to do | Use |
| --- | --- |
| Show an object with parameters, where points are dragged or constrained; or drive several panels from one shared state | JSXGraph |
| Plot a dataset with interaction — hover, zoom, selection | Plotly.js |
| Plot a simple dataset where hover and legend toggling are enough | Chart.js |
| Show nodes and edges, draggable and automatically laid out, trees included | Cytoscape.js |
| Show a fixed structural diagram that does not need to be manipulated | Mermaid |
| Typeset mathematical notation | KaTeX, or MathJax in the cases its entry names |
| Typeset chemical formulae and equations | KaTeX with its mhchem extension |
| Show source code with syntax colouring | highlight.js |
| Show short musical examples written as notation text | abcjs |
| Show a full score that exists as MusicXML | OpenSheetMusicDisplay |
| Show phonetic transcription | No library — this is a typeface question; see "Phonetic transcription" |
| Nothing in the selection map fits | Look beyond `references/external-resources.md` under the rules in "How to use `references/external-resources.md`"; ask the user if nothing suitable exists |

### Entries

#### JSXGraph — the default figure engine

- **Package and version** — `jsxgraph`, 1.13.3, checked 2026-09-21.
- **Files** — `https://cdn.jsdelivr.net/npm/jsxgraph@1.13.3/distrib/jsxgraphcore.js` and `https://cdn.jsdelivr.net/npm/jsxgraph@1.13.3/distrib/jsxgraph.css`. The stylesheet is not optional; board rendering depends on it.
- **Global** — `JXG`; boards are created with `JXG.JSXGraph.initBoard`.
- **Loading** — synchronous, no signal to wait for.
- **Use it for** — anything with parameters where points are dragged or constrained. It is declarative and constraint-based: declare the objects and the relations between them, and dragging, recomputation and redrawing follow. That is why it is the default — the alternative is recomputing geometry by hand on every pointer event. Several boards can be driven from one set of underlying objects, which is how panels that share state are built.
- **Pitfalls** — a board takes its size from its container, so the container needs a size in CSS. Resizing with the container is on by default in this version (the board option `resize` defaults to enabled), so give the container a width that follows the page and an aspect ratio or height, and the board follows the window without further code. Labels can be typeset through KaTeX; confirm the option name in the loaded version's documentation.

#### Plotly.js — interactive data charts

- **Package and version** — `plotly.js-dist-min` for the full bundle, or `plotly.js-basic-dist-min` for basic chart types only; both 4.1.1, checked 2026-09-21.
- **Files** — `https://cdn.jsdelivr.net/npm/plotly.js-dist-min@4.1.1/plotly.min.js` (about 4.6 MB), or `https://cdn.jsdelivr.net/npm/plotly.js-basic-dist-min@4.1.1/plotly-basic.min.js` (about 1.1 MB). Prefer the basic bundle when it covers the charts used.
- **Global** — `Plotly`; plots are created with `Plotly.newPlot`.
- **Loading** — synchronous, no signal to wait for.
- **Use it for** — plotting a dataset the reader explores by hovering, zooming or selecting.
- **Pitfalls** — the `responsive` config option defaults to off in this version, so a plot keeps the size it was drawn at unless `{responsive: true}` is passed to `Plotly.newPlot`.

#### Chart.js — simple charts

- **Package and version** — `chart.js`, 4.5.1, checked 2026-09-21.
- **Files** — `https://cdn.jsdelivr.net/npm/chart.js@4.5.1/dist/chart.umd.js`.
- **Global** — `Chart`.
- **Loading** — synchronous, no signal to wait for.
- **Use it for** — a chart whose only interaction is hover and legend toggling. A figure needing dragging, constraints or linked panels belongs to JSXGraph instead.
- **Pitfalls** — `responsive` defaults to on in Chart.js, the opposite of Plotly, and the chart sizes itself from its container, so the container needs a size in CSS.

#### Cytoscape.js — graphs and networks

- **Package and version** — `cytoscape`, 3.34.3, checked 2026-09-21.
- **Files** — `https://cdn.jsdelivr.net/npm/cytoscape@3.34.3/dist/cytoscape.min.js`.
- **Global** — `cytoscape`.
- **Loading** — synchronous, no signal to wait for.
- **Use it for** — any structure expressible as nodes and edges, draggable and automatically laid out, including trees such as syntax trees. Layout algorithms beyond the built-in ones ship as separate extension packages.
- **Pitfalls** — it draws into its container at the container's computed size, so give the container a size in CSS, and call `cy.resize()` if the container's size is changed by script.

#### Mermaid — static structural diagrams

- **Package and version** — `mermaid`, 12.0.0, checked 2026-09-21.
- **Files** — `https://cdn.jsdelivr.net/npm/mermaid@12.0.0/dist/mermaid.min.js` (about 5.3 MB).
- **Global** — `mermaid`.
- **Loading** — synchronous. `startOnLoad` defaults to on in this build, which renders every element with the class `mermaid` on page load but gives no signal when it has finished — the asynchronous work "Loading under the synchronous rule" warns about, with nothing to wait on. So turn it off with `mermaid.initialize({startOnLoad: false})` in the skeleton and call `mermaid.run()` in the final pass instead: `run` is asynchronous and returns a promise, and that promise is the signal to wait on. Call it again for diagrams added afterwards.
- **Use it for** — a diagram that does not need to be manipulated. Diagrams are written as text, which costs far less output than hand-drawn SVG and cannot go wrong geometrically.
- **Pitfalls** — it is not interactive; if the reader should move or vary something, this is the wrong entry. It is also the heaviest file in this reference, so do not load it for a single small diagram that a static figure would serve.

#### KaTeX and MathJax — notation

- **Package and version** — `katex`, 0.18.7, and `mathjax`, 4.1.3; both checked 2026-09-21.
- **Files** — KaTeX, the default: `https://cdn.jsdelivr.net/npm/katex@0.18.7/dist/katex.min.js`, `https://cdn.jsdelivr.net/npm/katex@0.18.7/dist/katex.min.css` and `https://cdn.jsdelivr.net/npm/katex@0.18.7/dist/contrib/auto-render.min.js`; add `https://cdn.jsdelivr.net/npm/katex@0.18.7/dist/contrib/mhchem.min.js` for chemistry. MathJax: `https://cdn.jsdelivr.net/npm/mathjax@4.1.3/tex-mml-chtml.js`.
- **Global** — `katex` and `renderMathInElement` for KaTeX; `MathJax` for MathJax.
- **Loading** — KaTeX typesets synchronously and without reflow, which matters because figure labels are re-typeset on every drag frame. Call `renderMathInElement(document.body)` once, in the final pass, after every section is in the page. MathJax is configured by assigning a global `MathJax = {…}` object in a script placed before its tag; drop the `defer` its README uses. Its typesetting is asynchronous whatever the loading: wait on `MathJax.startup.promise` before reading typeset output, and call `MathJax.typesetPromise()` for content added after load.
- **Use it for** — KaTeX by default. Switch to MathJax when the document needs TeX coverage KaTeX lacks, such as less common packages or automatic line breaking of long displayed equations, or when its expression explorer is wanted. For chemistry, KaTeX's mhchem extension adds `\ce{…}` for formulae and equations and `\pu{…}` for physical units.
- **Pitfalls** — **auto-render does not recognize single dollar signs by default.** Its default inline delimiter is `\(…\)`; the `$…$` pair is present in its source but commented out, because a lone `$` is also money. Write inline math as `\(…\)`, or pass a `delimiters` option that adds `$`. Math written `$x$` without that option is left on the page as raw source, which item 13 in SKILL.md exists to catch. Keep MathJax out of drag handlers, since its typesetting is asynchronous.

#### highlight.js — code with syntax colouring

- **Package and version** — `@highlightjs/cdn-assets`, 11.12.0, checked 2026-09-21; last published 2026-08-12.
- **Files** — `https://cdn.jsdelivr.net/npm/@highlightjs/cdn-assets@11.12.0/highlight.min.js` and one theme stylesheet, such as `https://cdn.jsdelivr.net/npm/@highlightjs/cdn-assets@11.12.0/styles/github.min.css`.
- **Global** — `hljs`.
- **Loading** — synchronous. Call `hljs.highlightAll()` once, in the final pass, after every `<pre><code>` block is in the page.
- **Use it for** — any code the document shows. Colouring is tokenizing, which is parsing, and SKILL.md rules out writing a parser. Chosen over Prism, whose stable line was last published in 2025-03 with its next major version still in alpha.
- **Pitfalls** — name the language with a `language-<name>` class on each `<code>` element rather than relying on detection, which misreads short snippets. Pair it with the monospace face under "Typefaces".

#### abcjs — short musical examples

- **Package and version** — `abcjs`, 6.7.0, checked 2026-09-21; last published 2026-08-07.
- **Files** — `https://cdn.jsdelivr.net/npm/abcjs@6.7.0/dist/abcjs-basic-min.js`.
- **Global** — `ABCJS`; `ABCJS.renderAbc` takes a target element and ABC notation text.
- **Loading** — synchronous, no signal to wait for.
- **Use it for** — short notated examples written as text in ABC notation: text in, engraved music out, which does for musical examples what Mermaid does for diagrams. A full score that already exists as a file belongs to OpenSheetMusicDisplay.
- **Pitfalls** — ABC is its own notation; write it against the version's documentation rather than from memory.

#### OpenSheetMusicDisplay — full scores from MusicXML

- **Package and version** — `opensheetmusicdisplay`, 2.1.3, checked 2026-09-21; last published 2026-09-19.
- **Files** — `https://cdn.jsdelivr.net/npm/opensheetmusicdisplay@2.1.3/build/opensheetmusicdisplay.min.js`.
- **Global** — `opensheetmusicdisplay`, whose `OpenSheetMusicDisplay` class draws a score into a container.
- **Loading** — synchronous script; loading a score into it is asynchronous, so render only after the load resolves, as the version's documentation sets out.
- **Use it for** — a score that exists as MusicXML. Engraving a score note by note is not listed: VexFlow, which OpenSheetMusicDisplay draws with, places notes by hand and falls under the caution about low-level toolkits.
- **Pitfalls** — none found in checking beyond the asynchronous load.

## Typefaces

Every HTML document loads its typefaces rather than naming local families, so the typography does not depend on what the reader happens to have installed. Two delivery routes, both fine:

- **Google Fonts** — `https://fonts.googleapis.com/css2?family=<family>:<axes>&display=swap`. The endpoint carries no version, so it is verified by request rather than pinned, with a browser User-Agent as "Rules for any resource" requires. It serves CJK families as many small subset files rather than one large download. Preconnect with `<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>`; without `crossorigin` the preconnect is not used for font files.
- **Fontsource on jsDelivr** — `https://cdn.jsdelivr.net/npm/@fontsource/<family>@5.3.0/<weight>.css`, one stylesheet per weight; for example `https://cdn.jsdelivr.net/npm/@fontsource/noto-serif-jp@5.3.0/400.css`. The same families as versioned npm packages, pinned like any library, checked 2026-09-21. Use it when a pinned version matters more than Google's subsetting.

Pick one family per role and no more. A document needs a prose face, a monospace face only if it shows code, and a face for phonetic transcription only if it contains IPA its prose face cannot set — that is a role of its own, and "Phonetic transcription" says which face fills it.

| Role | Checked options |
| --- | --- |
| Japanese prose | Noto Serif JP, Noto Sans JP |
| Chinese prose (simplified) | Noto Serif SC, Noto Sans SC |
| Latin prose | Noto Serif, Source Serif 4 |
| Monospace | JetBrains Mono, Noto Sans Mono |

Verified Google Fonts stylesheets, at the weights each URL names, all returning a WOFF2 stylesheet to a browser User-Agent on 2026-09-21:

- `https://fonts.googleapis.com/css2?family=Noto+Serif+JP:wght@400;700&display=swap`
- `https://fonts.googleapis.com/css2?family=Noto+Sans+JP:wght@400;700&display=swap`
- `https://fonts.googleapis.com/css2?family=Noto+Serif+SC:wght@400;700&display=swap`
- `https://fonts.googleapis.com/css2?family=Noto+Sans+SC:wght@400;700&display=swap`
- `https://fonts.googleapis.com/css2?family=Noto+Serif:ital,wght@0,400;0,700;1,400&display=swap`
- `https://fonts.googleapis.com/css2?family=Source+Serif+4:ital,opsz,wght@0,8..60,400;0,8..60,700;1,8..60,400&display=swap`
- `https://fonts.googleapis.com/css2?family=JetBrains+Mono:wght@400;700&display=swap`
- `https://fonts.googleapis.com/css2?family=Noto+Sans+Mono:wght@400;700&display=swap`

These are also what SKILL.md's offline fallback uses: when the network is unavailable, these URLs and the library files in "Entries" are the record to fall back on, reported as unverified.

### Phonetic transcription

IPA is a typeface question, not a library one, and the faces differ more than their stylesheets suggest. Every face in the role table under "Typefaces" declares a unicode-range covering the IPA characters, but a declared range says only which subset file is responsible for those characters, not that the file contains them. Checked on 2026-09-21 by reading the glyph tables of the subset files themselves, against ə ɔ ʃ ʊ ŋ ɾ ʔ ː ˈ and a combining tilde:

| Face, as served by Google Fonts | IPA glyphs present |
| --- | --- |
| Noto Serif | all ten |
| Source Serif 4 | four; lacks ɔ ʃ ʊ ɾ ʔ ː |
| Noto Serif JP, Noto Sans JP, Noto Serif SC, Noto Sans SC | none |
| Charis SIL, Gentium Plus | eight; lacks the length mark ː and the stress mark ˈ in every subset file |
| Noto Sans Mono | all ten |
| JetBrains Mono | three |

So the IPA characters need a face whose files actually contain their glyphs, and of the faces checked only Noto Serif and Noto Sans Mono have all ten. Where the face setting the text can be one of those, use it as that face. Where it cannot, because the text itself needs a face that lacks them, as Japanese or Chinese prose needs its CJK face, keep that face and list one that has them after it in the stack — for example `font-family: "Noto Serif JP", "Noto Serif", serif` — so the browser falls back to it for each IPA character instead of to whatever the reader's system happens to have. Charis SIL and Gentium Plus do not qualify as Google Fonts serves them, though both are designed for IPA: the hosted versions lack the length and stress marks that transcription depends on.

### Notes that matter in use

- A CJK family covers ordinary Latin text, so a Japanese or Chinese document needs no separate Latin face for its prose. It does not cover IPA; "Phonetic transcription" says what to add.
- KaTeX and MathJax ship their own math faces with their stylesheets. Do not substitute a prose face into math.
- Request only the weights the document uses. Requesting a full weight range multiplies the download for nothing.
