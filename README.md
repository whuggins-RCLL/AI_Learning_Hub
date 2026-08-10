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
| `events.html` | Curiosity Corners, trainings, the Tech Club charter, and the calendar |
| `past-events.html` | Archive of past sessions and Tech Club meetings |
| `skills.html` | The eleven downloadable AI skills |
| `install.html` | What a skill file is, and how to install one in ChatGPT or Claude |
| `faculty.html` | Full-page embed of the faculty AI site |
| `ai-upload.html` | Full-page embed of The AI Upload, the weekly AI news digest |
| `assets/styles.css` | The design system |
| `assets/hub.js` | The theme toggle and the collapsing navigation |

All of it is ported from the previous AI Learning Hub, which was a set of
standalone Tailwind and React pages, into the one design system below.

### Navigation

The header bar holds six destinations and no more. That is the width at which a
seventh wraps the bar onto a second row, measured on the faculty site with the same
logo and type. Everything else — install, past events, the two embeds, the outbound
links — is reachable from the footer.

The bar and footer are the same markup on all ten pages, which no one should be
retyping ten times. They are written by `scripts/nav.py`: it replaces the
`.siteHeader` and `.footer` block in each file in place and is idempotent. The
committed pages stay plain HTML with no build step, so an ordinary edit is still an
ordinary edit; re-run the script after changing a nav entry.

```
python3 scripts/nav.py
```

### Content that is data, not markup

`tutorials.html` keeps its ~34 tutorials in one array at the bottom of the file and
renders the cards from it, which is how the previous hub's tutorials page worked
too. Adding a tutorial is one entry rather than a block of copied HTML. Everything
else is written out as HTML.

### Styling

`assets/styles.css` is the faculty site's `website/app/globals.css`, copied
verbatim, followed by one clearly marked section of hub-only additions (the PAUSE
Rule's gates and verdicts, the skill cards, the embed pages). Keeping the shared
part an unmodified copy is deliberate: a change to the design system is a re-copy
plus a look at the additions, not a merge.

Two things the faculty site has that the hub deliberately does not: a search form,
which needs a server to post to, and an account link, since there is nothing to
sign in to. The header is destinations only.

### The embedded sites

`faculty.html` and `ai-upload.html` frame
`sls-faculty-ai-skills.vercel.app` and `ai-upload-stanford-law.vercel.app`. Both
carry a permanent "open in a new tab" link above the frame. For the faculty site
that link is the reliable route rather than a fallback: it is behind Stanford
sign-in, and a sign-in inside a frame — nested again inside a Google Site — is the
case browsers restrict.

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

### Shared principles

The skills use a problem-first approach and incorporate the PAUSE Rule, Stanford Law
School student AI guidance, Responsible AI at Stanford, productive struggle,
legal-source verification, structured and manageable data, transparent AI-use logs,
and accurate non-anthropomorphic explanations of AI systems.

---

For AI tools, access, legal research, or technical assistance, contact the Robert
Crown Law Library at **library@law.stanford.edu**.
