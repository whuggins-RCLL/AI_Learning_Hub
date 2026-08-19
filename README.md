# The AI Learning Hub

The Robert Crown Law Library's AI Learning Hub for Stanford Law School, plus the
student-facing AI Skills it distributes.

The hub is **open to the whole SLS community** — students, staff, and faculty — and
requires no sign-in. That is the difference between this site and
[`sls-faculty-ai-skills`](https://github.com/whuggins-RCLL/sls-faculty-ai-skills),
which is written for faculty and sits behind Stanford authentication. The two sites
share one design system so they read as one family.

Like the faculty site, this one is embedded in a Google Sites page.

## The site

Plain static HTML — no build step, no framework, no server. Open `index.html` or
serve the directory and it works:

```
python3 -m http.server 8000
```

| File | What it is |
| --- | --- |
| `index.html` | The hub landing page |
| `pause-rule.html` | The PAUSE Rule — the AI use workflow |
| `tutorials.html` | The tutorial library: eight topics, ~34 guides and DIY modules |
| `ai-resources.html` | AI tools available to the SLS community, and the policy that governs them |
| `ai-in-the-library.html` | The library's AI display: 7 parts, 24 panels, 32 books |
| `reading-list.html` | The 32-book shelf on its own page (same data as the display) |
| `assets/books.js` | Shared reading-list data and card renderer |
| `events.html` | Curiosity Corners, trainings, the Tech Club charter, and the calendar |
| `past-events.html` | Archive of past sessions and Tech Club meetings |
| `skills.html` | The twenty-one downloadable AI skills, and the three one-click sets above them |
| `skills/bundles.json` | Which skills each set holds, and in what order |
| `scripts/build-skill-bundles.py` | Builds `assets/bundles/*.zip` from that manifest |
| `assets/skill-bundles.js` | The download-them-separately button on the set cards |
| `assets/writing-samples/` | Five fictional student drafts to practise the writing skills on |
| `install.html` | What a skill file is, and how to install one in ChatGPT or Claude |
| `case-study-anthropic-legal-skills.html` | Case study: reverse-engineering Anthropic's AI governance legal skills |
| `assets/copy-code.js` | The copy button on the case study's skill template |
| `assets/install-a-skill-guide.pdf` | Printable skill-installation guide with clickable links to both videos |
| `faculty.html` | Full-page embed of the faculty AI site |
| `faculty-publications.html` | SLS faculty publications on AI, embedded within the hub navigation |
| `ai-upload.html` | Full-page embed of The AI Upload, the weekly AI news digest |
| `assets/styles.css` | The design system |
| `your-ai-stack.html` | Your AI Stack — the searchable directory of 113 AI tools |
| `assets/ai-stack-data.js` | The tool catalogue and the retired-product list (generated) |
| `assets/ai-stack.js` | Browsing, filtering, comparing, and saving for Your AI Stack |
| `assets/hub.js` | The theme toggle and the collapsing navigation |

All of it is ported from the previous AI Learning Hub, which was a set of
standalone Tailwind and React pages, into the one design system below.

### Navigation

The header's primary row holds six destinations and no more. A utility row above
it places a Home button and a site-scoped search at the top right. The six primary
destinations are The AI Upload, Tutorials, Resources, Events, Skills, and Faulty
Support.

The footer is reserved for outbound Stanford links and does not repeat the site
navigation. Secondary pages are linked from relevant landing-page content.

The PAUSE Rule is not in the bar. It is a short link on the home hero rather than
a primary call to action or a destination card that restates the nav.

The bar and footer are the same markup on every page that has them, which no one
should be retyping eleven times. They are written by `scripts/nav.py`: it replaces
the `.siteHeader` and `.footer` block in each file in place and is idempotent. The
committed pages stay plain HTML with no build step, so an ordinary edit is still an
ordinary edit; re-run the script after changing a nav entry.

```
python3 scripts/nav.py
```

### Content that is data, not markup

Two pages keep their content in one array at the bottom of the file and render the
cards from it, because both are lists that grow and an entry beats a block of copied
HTML: `tutorials.html` (~34 tutorials) and `ai-in-the-library.html` (7 categories, 24
panels). The previous hub's tutorials page worked the same way. Everything else is
written out as HTML.

The thirty-two books are different: they live once in `assets/books.js` and are
rendered by both `reading-list.html` and the Selected Reading section of
`ai-in-the-library.html`. Edit the array in that file to update both pages.

### Your AI Stack

`your-ai-stack.html` was its own repository — `whuggins-RCLL/Your-AI-Stack`, a Vite +
React + Tailwind app. The hub link used to point at a Google Sites page wrapping it.
The directory now runs natively here: 113 tools, 29 retired products, the same
categories, and the guide sections, in the hub's design system with no build step.

The catalogue is generated rather than retyped. `scripts/port-ai-stack.mjs` reads
`src/data.ts` and `src/data/discontinuedAi.ts` out of the Your-AI-Stack checkout and
writes `assets/ai-stack-data.js`. Edit the entries there and re-run it; do not
hand-edit the generated file, or the two copies will drift.

```
node scripts/port-ai-stack.mjs
```

Everything else lives in `assets/ai-stack.js`, which is plain ES5-flavoured
JavaScript like `hub.js`. Two things differ from the app it replaces, and both are
navigation fixes rather than ports:

- **Every view is in the URL.** A search, a category, the saved list, an open tool,
  and a comparison are all encoded in `location.hash` — `#tool=notebooklm`,
  `#cat=Legal+Research+%26+Analysis`, `#cmp=claude,gemini&open=compare`. Any of them
  can be linked to or bookmarked, and Back closes an overlay rather than leaving the
  site. In the app all of this was component state.
- **Saves persist.** The saved list is in `localStorage`, so following a link out to a
  vendor and coming back does not empty it. The app kept saves in memory only.

Three of the app's features did **not** come across:

- **The blocking disclaimer modal.** The same text is now a note at the top of the
  page. A modal that has to be dismissed on every visit before anything can be read
  is a toll, not a disclosure, and it was the first thing every reader saw.
- **The html2pdf export**, which pulled in a 985 kB dependency. The saved list prints
  through a print stylesheet — every browser's print dialog saves to PDF — and there
  is a dependency-free Markdown download beside it.
- **Tool logos**, which were `picsum.photos` placeholder images keyed by tool name,
  so they were decorative noise fetched from a third party on every card.

### AI in the Library

`ai-in-the-library.html` was its own repository —
`whuggins-RCLL/AI-at-the-Robert-Crown-Law-Library`, a Vite + React app deployed at
`ai-at-rcll.vercel.app`. All of its content is here now: 7 categories, 24 exhibit
panels, 32 books, the About notes, and the acknowledgments. Nothing in the hub links
to the old deployment any more, so that repository can be retired.

Two of the app's features did **not** come across, and both were deliberate:

- **The Gemini "AI Curator" chat.** It needed a Google GenAI key. Vite inlines
  `VITE_API_KEY` into the client bundle, so on a public static site that key is a
  published key. The hub has no build step and no secret handling, and adding both to
  carry one chat widget was not the trade we wanted.
- **The reading-list PDF export**, which needed jsPDF. Each book's SearchWorks link
  is the durable version of the same thing.

The app also fetched book covers at runtime from the Open Library and Google Books
APIs. Covers here come from Open Library by ISBN as a plain image URL
(`covers.openlibrary.org/b/isbn/<isbn>-M.jpg?default=false`) rather than an API call.
`default=false` makes a missing cover a 404 instead of a blank placeholder, and an
`onerror` handler then removes the element so the card reflows to text. Books whose
covers the display had scanned itself keep those.

### Styling

`assets/styles.css` is the faculty site's `website/app/globals.css`, copied
verbatim, followed by one clearly marked section of hub-only additions (the PAUSE
Rule's gates and verdicts, the skill cards, the embed pages). Keeping the shared
part an unmodified copy is deliberate: a change to the design system is a re-copy
plus a look at the additions, not a merge.

The static site search submits to Google and is scoped at runtime to the hostname
serving the hub, so the repository remains portable without a search server or a
hard-coded deployment domain. The hub has no account link because there is nothing
to sign in to.

### The embedded sites

`faculty.html` and `ai-upload.html` are full-page frames for the faculty AI site
and The AI Upload. Each is the frame and nothing else: no header, no footer, no
explanatory strip, and no theme toggle. These framed sites carry their own
navigation and branding, so a bar of ours above one would put two sets of
destinations on the same screen. `scripts/nav.py` skips these pages and fails
loudly if either ever grows a `.siteHeader` block.

`faculty-publications.html` embeds the Stanford Law School faculty AI publications
list within a standard hub page, preserving the hub header, footer, and navigation
around the external content.

For the two full-page frames, there is no chrome to measure around, so each frame is simply the viewport —
`position: fixed; inset: 0`. Note that the design system copied from the faculty
site contains a `.digestEmbed` rule that assumes 68px of chrome above the frame;
nothing here uses it, and 68px is not what that bar actually measures.

The faculty site is behind Stanford sign-in. A reader who is not signed in sees
that site's own sign-in page inside the frame, which is its answer to give rather
than this site's. Worth knowing: a sign-in inside a frame — nested again inside a
Google Site — is the case browsers restrict, so signing in may have to happen in
the faculty site's own tab.

## The skills

Each skill is a validated `skill.zip` under `skills/`, containing its `SKILL.md`,
ChatGPT interface metadata, and supporting Stanford Law School references. Download
one and upload it to ChatGPT or Claude; you never unzip it.

### Core pathway

1. SLS AI Orientation
2. SLS AI Task-Fit Coach
3. SLS Case Learning Coach
4. SLS Legal Research Learning Coach
5. SLS AI Verification Lab

### Optional tool studios

- SLS Harvey Learning Studio
- SLS Legora Learning Studio
- SLS LexText Learning Studio
- SLS CICERO Oral Argument Studio
- SLS Gemini Notebook Learning Studio (formerly NotebookLM)
- SLS AI Tool Explorer

### Writing partner

Ten review skills for a draft that is already written. Each acts as a reviewer, not a
ghostwriter: it flags, explains, and locates, and hands the revision back to the
student.

1. SLS AI Use Gate — run first; is this AI use authorised here at all
2. SLS Writing Review — the full workflow, and a Word review copy
3. SLS Argument and Structure
4. SLS Flow and Organisation
5. SLS Clarity and Precision
6. SLS Audience and Reception
7. SLS Counterargument Stress Test
8. SLS Claims and Source Traceability
9. SLS Bluebook Audit
10. SLS Genre Fit

### Practice drafts

`assets/writing-samples/` holds five fictional student drafts — a case brief, three
memos of increasing length and citation density, and a timed exam answer — offered
on `skills.html` under **Practice drafts**. They exist so a student can watch a
review skill work on someone else's writing before handing it their own, so each
one has real problems in it. Everything in them is invented, including the student
authors, and the page says so in a caution above the cards: no authority cited in
them should be relied on, and none of them is a model answer.

The section is a sibling of the writing-partner grid rather than part of it. The
set card's *download the N skills separately* button collects the download links
inside the section it names, and these are Word documents, not skills; the button's
selector is also scoped to `.skillGrid` so a future non-skill download cannot be
swept into a set either.

### Skill sets

The top of `skills.html` offers three sets as one-click downloads: the Writing Partner
Set (10), the Core Pathway Set (5), and the Tool Studios Set (6). A set is a single ZIP
holding the member skill ZIPs **byte for byte**, plus a README naming what is inside and
how to install it — so a set and the individual buttons below it hand out the same
files, and there is no second copy of a skill to keep in step.

`skills/bundles.json` says what is in each set; the ZIPs are generated, not committed by
hand:

```
python3 scripts/build-skill-bundles.py          # rebuild assets/bundles/
python3 scripts/build-skill-bundles.py --check  # non-zero if a rebuild would change a ZIP
```

Output is deterministic — fixed entry timestamps, stored (not re-compressed) members — so
rebuilding without an input change produces no git diff. To add a set: add it to the
manifest, run the script, and add a card to the Skill sets section of `skills.html`.

The second button on each card, *Download the N skills separately*, is the progressive
enhancement in `assets/skill-bundles.js`. It saves each skill ZIP individually, so they
are upload-ready with nothing to unzip. It is deliberately not the primary action: a
browser prompts before saving several files at once and some refuse outright, so the
reliable one-click path has to be the single file. The button reads its file list from
the download links already in the section it names (`data-bundle-source="#writing"`),
which means a skill added to the page is in that set download as soon as its card is.

### The case study

`case-study-anthropic-legal-skills.html` is a long-form reading of Anthropic's
open-source [Claude for Legal](https://github.com/anthropics/claude-for-legal)
project (Apache-2.0) and its `ai-governance-legal` plugin: what a `SKILL.md` file
is, why the plugin is ten small skills rather than one large one, what belongs in
`references/`, `scripts/`, and `assets/`, and why an open skill is not the same
thing as a legal AI platform. It is an independent educational case study, not
affiliated with or endorsed by Anthropic, and it says so at the top and the
bottom.

It uses the document look already in the design system — `.docPage`, `.docMeta`,
`.docToc`, `.docPart`, `.module` — which the hub had inherited from the faculty
site's globals but never used. The source document drew its diagrams as ASCII
art; those are rebuilt as ordinary elements (`.layerStack`, `.flowChain`,
`.spectrumFig`, `.formulaFig`, `.codeBlock`), because ASCII art in a `<pre>`
either scrolls sideways or shrinks past legibility on a phone. The page is
entered from a card at the top of `skills.html`, above the sets: it answers
"what am I actually installing, and how was it built?" before a reader takes ten
files they have not opened.

### Shared principles

The skills use a problem-first approach and incorporate the PAUSE Rule, Stanford Law
School student AI guidance, Responsible AI at Stanford, productive struggle,
legal-source verification, structured and manageable data, transparent AI-use logs,
and accurate non-anthropomorphic explanations of AI systems.

---

For AI tools, access, legal research, or technical assistance, contact the Robert
Crown Law Library at **library@law.stanford.edu**.
