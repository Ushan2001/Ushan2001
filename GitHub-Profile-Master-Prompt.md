# **Master Prompt — Complete GitHub Profile** 

Banner · Stats cards · Contribution snake · Social badges. One prompt, four phases. 

**Read this first.** This prompt builds a banner in _this style_ using _your_ photo — it will not reproduce anyone else's. The portrait is the output of a Python pipeline (dithering, background segmentation, trajectory matching) run against one specific image. Two people running this get two different banners, which is the point. Expect real back-and-forth on contrast and crop; the first attempt rarely lands. 

**You need:** a Claude session with **code execution enabled** (Python + Pillow/NumPy/SciPy), one clear photo, reference images for any real logos, a GitHub account, and a free Vercel account. Budget 1–2 hours including iteration. 

## **Choosing your photo — read before prompting** 

This determines the result more than anything in the prompt itself. 

- **Flat, uniform background** — a plain wall or studio backdrop. This is what allows clean background removal for dark mode. A busy background is the single biggest cause of a poor result. 

- **Clear separation** from the backdrop — don't wear a wall-coloured shirt. 

- **Even lighting on the face.** Harsh shadows survive dithering and read as blotches. 

- **Head-and-shoulders framing** , sharp, 1000px+ on the short edge. 

#### **▼ THE PROMPT — copy everything from here to "END OF PROMPT" ▼** 

Build my complete animated GitHub profile — banner, stats cards, contribution snake, and social badges. I've attached my photo and logo references. Work through the four phases below **in order** , and check in with me after each one. Don't generate five variations at once; show me one and let me react. 

### **My details** 

- Name: <mark>`[NAME]`</mark> · GitHub username: <mark>`[username]`</mark> (profile repo is <mark>`username/username` ,</mark> branch <mark>`main` )</mark> 

- Role: <mark>`[e.g. Full-Stack Developer]`</mark> 

- 

- Location: <mark>`[City, Country]`</mark> · Education: <mark>`[degree]`</mark> 

- 

- Status: <mark>`[e.g. Building + Learning + Shipping]`</mark> 

- ToolChain: <mark>`[e.g. VS Code, Git, Android Studio, Figma]`</mark> 

- 

- Languages: <mark>`[...]`</mark> · Frontend: <mark>`[...]`</mark> · Backend: <mark>`[...]`</mark> · Database: <mark>`[...]`</mark> · Infra: <mark>`[...]`</mark> 

- LinkedIn <mark>`[url]`</mark> · Instagram <mark>`[url]`</mark> · Facebook <mark>`[url]`</mark> · Email <mark>`[addr]`</mark> · Portfolio <mark>`[url or "coming soon"]`</mark> 

- Three logos to morph between: <mark>`[e.g. Flutter, a </> glyph, Vercel]`</mark> — I'm attaching reference images; **trace them, don't hand-draw them** 

1 

- Palette: portrait <mark>`[#A78BFA dark / #7C3AED light]`</mark> · UI chrome <mark>`[#22D3EE / #0891B2]`</mark> · accent <mark>`[#10B981]`</mark> · background <mark>`[#0A101F]`</mark> 

**Palette rule:** the portrait must be a different hue from the UI chrome, or the face blends into its own frame. 

### PHASE 1 — Banner (dark.svg / light.svg) 

One terminal window, **1180×610** , titled <mark>`profile.sh --live` .</mark> Left ~38% is a portrait frame labelled <mark>`VISUAL.MAP`</mark> . Right is a <mark>`SYSTEM.INFO`</mark> readout with dotted leaders, a pulsing red LIVE badge, and a coloured pill with my handle. 

#### **Portrait — build this in Python** 

- Crop **head + shoulders** , not a tight face crop (over-zoomed reads aggressive) 

- **300×340** grid, then **1-bit Floyd–Steinberg dither, serpentine order** 

- Contrast **1.3× only** , with <mark>`autocontrast(cutoff=1)`</mark> + <mark>`UnsharpMask(radius=3, percent=140)`</mark> 

- Draw dots as <mark>`<path>`</mark> runs with <mark>`shape-rendering="crispEdges"`</mark> — never font glyphs, they mush below ~2px 

- **Dark mode:** segment the background out (threshold on colour distance, binary closing, fill holes, keep largest component) so dots draw the lit subject on the panel. Hard-clear errordiffusion bleed at the mask edge. Without this, dark mode looks like a photo negative 

- **Light mode:** keep the background; dots draw the dark parts of the photo 

- Single hue — all tone from dot density 

- **No** grid lines, scanlines, glitch bars, or CRT flicker 

- 

#### **Animation** 

**Intro (~3.2s, once):** ~60 **interleaved random** groups fade in over ~2s. Each group must be scattered across the _whole_ portrait so dots appear everywhere at once and thicken together. Do **not** use a wipe. Do **not** group by spatial region — that reveals patch-by-patch instead of shimmering in. Verify with an evenness metric (~0.05 good, ~0.7 patchy). Needs a duplicate portrait layer (~180KB); merging to one layer breaks it. 

— **Loop (~14.2s):** portrait 3.0s, each logo 2.0s, 1.3s transitions. Use **explicit uneven** **<mark>`keyTimes`</mark>** evenly-spaced keyframes force every phase to hold the same length. 

#### **Two independent layers:** 

1. **Portrait** — full density (~17k dots), grouped into ~94 drift bands. On the loop each band translates ~42% toward the first logo's centroid while fading, then returns 

2. **Travellers** — ~900 dots that morph between logos, matched by optimal transport so each takes the shortest path. Opacity keyframes <mark>`0;0;0;1;1;...;0`</mark> so they're **hidden during the portrait phase** — otherwise their thicker dots crowd the fine dither 

**The trap that will bite you:** drift is a _linear_ function of position, so quantizing it into groups mathematically recreates a square grid — and the dissolve looks blocky. Add per-dot noise (sigma ~4) before grouping. Verify with a straight-boundary metric: ~0.01 organic, ~0.17 means you built a grid. 

2 

#### **Info panel** 

- Rows at **font-size 14** , header 13, LIVE 12, pill 14, spacing 23px 

- 

- Lock every row with <mark>`textLength`</mark> + <mark>`lengthAdjust="spacingAndGlyphs"`</mark> so values stay rightaligned in any browser font 

- Dotted leaders computed from label/value length — never hand-edit the SVG 

Rows: Subject, Role, Origin, Education, Status, ToolChain · Core.Lang, Core.Frontend, Core.Backend, Core.Database, Core.Infra · Grid.Mail, Grid.Portfolio, Grid.LinkedIn, Grid.GitHub, Grid.Facebook 

### PHASE 2 — Stats cards (self-hosted) 

Walk me through **self-hosting** github-readme-stats — don't just hand me public-instance URLs. The public instance is shared by thousands and constantly returns "API rate limit exceeded". Give me these steps explicitly: 

1. Create a GitHub **classic token** : Settings, Developer settings, Tokens (classic), Generate new (classic), <mark>`repo`</mark> scope, **No expiration** . Warn me to copy it immediately and never paste it anywhere public 

2. Fork <mark>`anuraghazra/github-readme-stats`</mark> 

3. Vercel, sign up with GitHub, Hobby (free), Add New Project, import the fork 

   - Add environment variable <mark>`PAT_1`</mark> = my token, then Deploy 

4. 

   - Ask me for my instance URL, then generate the themed block 

5. 

Then produce: a streak card (streak-stats.demolab.com) at <mark>`width="100%"` ,</mark> plus stats and toplangs side by side at <mark>`width="49%"` .</mark> Theme everything to my palette. Include <mark>`hide_rank=true`</mark> — the rank is stars-weighted and misleading for newer accounts. Explain why rather than just doing it. 

### PHASE 3 — Contribution snake 

Write me <mark>`.github/workflows/snake.yml`</mark> using <mark>`Platane/snk/svg-only@v3`</mark> , on a 12-hour cron plus <mark>`workflow_dispatch`</mark> plus push to main, pushing to an <mark>`output`</mark> branch via <mark>`crazy-max/ghactiongithub-pages@v3.1.0` .</mark> Include <mark>`permissions: contents: write` .</mark> 

Tell me to set repo **Settings, Actions, General, Workflow permissions, Read and write** , and be explicit that this is the _repo's_ settings, not my account settings. 

Two output SVGs — light and dark — themed to my palette. **The first colour in** **<mark>`color_dots`</mark> is the empty cell.** For the dark snake it must be a visible slate like <mark>`#2d3343` :</mark> against GitHub's <mark>`#0d1117`</mark> background a near-black empty cell disappears and the grid looks broken. Display via a theme-aware <mark>`<picture>`</mark> , and tell me to only add it _after_ the Action runs green — the <mark>`output`</mark> branch doesn't exist before then. 

### PHASE 4 — Social badges 

shields.io badges, <mark>`for-the-badge`</mark> style, my background colour, <mark>`&nbsp;&nbsp;`</mark> between each, all clickable. 

3 

**Warn me about the LinkedIn bug:** its logo only renders on brand blue <mark>`#0A66C2` .</mark> On any custom colour the glyph silently vanishes, leaving just text. Either use brand blue or embed the glyph as a base64 data-URI to keep it themed. Other logos (Instagram, Gmail, Facebook) recolour fine. 

Skip a GitHub badge — it's circular on my own profile. 

### FINALLY — assemble 

Give me the complete README in one block: banner <mark>`<picture>` ,</mark> then stats, then snake, then badges, with every <mark>`USERNAME`</mark> filled in. Then a short checklist of what I do by hand (upload SVGs, create the token, deploy Vercel, enable Actions permissions). 

### **How to work with me** 

- **Verify by measurement, not by eye.** cairosvg renders only the first SMIL frame and mishandles additive transforms and <mark>`textLength` .</mark> Use correlation vs the approved render, band distributions, ink coverage — then tell me to check in a browser 

- **When I say something "didn't change,"** check the file first: 

<mark>`raw.githubusercontent.com/.../file.svg?v=999` ,</mark> view-source, search the hex. It's almost 

always CDN cache, not a bug. Also check I'm in the right theme — dark assets only render in dark mode 

- **Flag file size honestly.** The banner lands ~900KB–1MB. Warn me before expensive changes 

- 

- **Tell me when I'm wrong.** If an idea won't work or costs more than it's worth, say so instead of building it 

- **If I reject something twice, stop and ask** rather than trying a third variation 

- 

- Keep the generator script and <mark>`.npy`</mark> data — they're the source of truth, not the SVG 

#### **▲ END OF PROMPT ▲** 

4 

**What to expect** 

### **Phase by phase** 

|Phase|Efort|Iteration?|
|---|---|---|
|1 — Banner|1–2 hrs|**Yes**— contrast, crop, timing|
|2 — Stats|20 min|No — confg only|
|3 — Snake|10 min|No — confg only|
|4 — Badges|5 min|No — confg only|



Phase 1 is the whole project. Phases 2–4 are copy-paste and could be done without AI at all — they're in the prompt so you get one continuous session instead of switching documents. 

### **Known issue: 1080p moiré** 

At GitHub's ~900px README width the dot lattice sits near 1 dot per screen pixel and can produce faint vertical banding. It vanishes when you zoom. Already tried and rejected: 

|Attempt|Result|
|---|---|
|Remove<br>`crispEdges`|−40% banding, still visible; softens portrait|
|Cap run lengths|No measurable efect|
|Per-dot jitter|Helps slightly; fle balloons to 2.8MB|
|Shorter dots (0.75–0.85)|−12–25%; portrait correlation 1.0 to 0.90|



The only real fix is fewer, larger dots — a coarser portrait. Most visitors never notice. Don't burn hours here. 

### **Approaches that already failed** 

- **Bayer / ordered dithering** — too chunky, loses facial detail 

- 

- **2.4× contrast** — harsh and skull-like. A moody reference photo's look comes from its _lighting_ , not its dithering 

- **Tight face crop** — reads aggressive; head-and-shoulders is friendlier 

- 

- **Full-swarm morph (1,500 dots)** — portrait becomes a loose impression 

- **Signature-grouped morph** — shapes rendered at 23–52% accuracy, 831KB 

- **Convergence clustering** — produced blocky tiles (the grid trap) 

- **ASCII / character portrait** — glyphs mush at small sizes 

5 

**The core tradeoff** 

Portrait quality comes from ~17,000 dots. Living per-dot motion requires ~1,000. **These are incompatible in one SVG.** The two-layer design is the resolution: a dense portrait that dissolves, plus a sparse swarm that travels. If you ask for both at once, that's the wall you'll hit. 

**If you only want stats, snake, and badges** — skip this document entirely. Those need no AI and no Python. The companion **Setup Guide** is 45 minutes of copy-paste and covers them with full troubleshooting. 

6 

