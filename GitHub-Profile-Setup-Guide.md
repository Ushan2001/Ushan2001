# **GitHub Profile — Setup Guide** 

Everything you need to deploy the animated terminal profile, in order. 

**Before you start.** You need: a GitHub account, a free Vercel account (created in Phase 2), and about 45 minutes. No coding required — everything here is copy, paste, and click. If you're rebuilding the banner itself, that's the other document (the Master Prompt). 

## **Phase 0 — The profile repo** 

GitHub shows a special repo on your profile page: one named **exactly the same as your username** . 

1. Go to <mark>`github.com/new`</mark> 

2. Repository name: your username, exactly (e.g. <mark>`arifhaxn/arifhaxn`</mark> ) 

   - Set to **Public** , tick **Add a README file** 

3. 

4. Create. GitHub will confirm you found the "secret" repo. 

Upload your <mark>`dark.svg`</mark> and <mark>`light.svg`</mark> to the root of this repo (Add file → Upload files). 

## **Phase 1 — Banner in the README** 

The <mark>`<picture>`</mark> element makes GitHub swap images by theme automatically. Replace <mark>`USERNAME`</mark> throughout. 

##### `<picture>` 

```
  <source media="(prefers-color-scheme: dark)"
```

```
    srcset="https://raw.githubusercontent.com/USERNAME/USERNAME/main/dark.svg">
  <source media="(prefers-color-scheme: light)"
```

```
    srcset="https://raw.githubusercontent.com/USERNAME/USERNAME/main/light.svg">
  <img alt="YOUR NAME" src="https://raw.githubusercontent.com/USERNAME/USERNAME/main/
light.svg">
```

```
</picture>
```

**Test it:** switch your GitHub theme (avatar → Settings → Appearance) and reload. Both versions should appear. 

## **Phase 2 — Stats cards (self-hosted)** 

**Don't skip the self-hosting.** The public github-readme-stats instance is shared by thousands of users and constantly returns _"API rate limit exceeded."_ Self-hosting gives you a private rate limit that never runs out. It's a one-time 20-minute setup. 

### **1 Create a GitHub token** 

1. Go to <mark>`github.com/settings/tokens`</mark> → **Tokens (classic)** 

1 

#### 2. **Generate new token (classic)** 

3. Note: <mark>`readme-stats`</mark> ·  Expiration: **No expiration** 

   - Scopes: tick **repo** (the whole group) 

4. 

5. Generate, then **copy the token immediately** — GitHub shows it once 

**Treat this like a password.** Never paste it into a chat, a public repo, or a website. It only ever goes into Vercel's environment-variable field. 

### **2 Fork and deploy** 

1. Fork <mark>`github.com/anuraghazra/github-readme-stats`</mark> 

2. Go to <mark>`vercel.com`</mark> → Sign up with GitHub → Hobby (free) plan 

3. **Add New… → Project** → Import your fork 

4. Leave every build setting alone 

5. Under **Environment Variables** add: name <mark>`PAT_1` ,</mark> value = your token 

6. **Deploy** , wait ~2 min for the confetti 

7. Copy your URL: <mark>`your-instance.vercel.app`</mark> 

Verify: open <mark>`https://your-instance.vercel.app/api?username=USERNAME&show_icons=true`</mark> — a card should render. 

**3** 

### **Add the cards** 

```
<div align="center">
```

```
<img width="100%" src="https://streak-stats.demolab.com/?user=USERNAME&hide_border=true
&background=0A101F&stroke=22D3EE&ring=A78BFA&fire=10B981&currStreakLabel=22D3EE
```

```
&sideLabels=94A3B8&currStreakNum=F8FAFC&sideNums=F8FAFC&dates=64748B
```

```
&titleColor=22D3EE&card_width=1180" alt="streak" />
```

```
<br/>
```

```
<img width="49%" src="https://YOUR-INSTANCE.vercel.app/api?username=USERNAME
&show_icons=true&count_private=true&include_all_commits=true&hide_rank=true
&hide_border=true&title_color=22D3EE&icon_color=A78BFA&text_color=94A3B8
```

```
&bg_color=0A101F&card_width=500" alt="stats" />
```

```
<img width="49%" src="https://YOUR-INSTANCE.vercel.app/api/top-langs/?username=USERNAME
&layout=compact&langs_count=8&hide_border=true&title_color=22D3EE
```

```
&text_color=94A3B8&bg_color=0A101F&card_width=500" alt="top langs" />
</div>
```

_Join each URL into one line — they're wrapped here for print._ 

**Why** **<mark>`hide_rank=true` ?</mark>** The letter grade is heavily weighted toward stars and followers, so new accounts sit at "C" regardless of how much they code. It measures repo popularity, not skill. Hiding it is the honest choice. 

2 

**Phase 3 — Contribution snake** 

### **1 Enable Actions permissions** 

Your repo → **Settings** (repo tab, not account) → sidebar **Actions** → **General** → scroll to the bottom → **Workflow permissions** → select **Read and write permissions** → Save. 

This is inside the _repo's_ Settings, not your account settings. URL should look like <mark>`github.com/USER/ USER/settings/actions` .</mark> 

**2 Create the workflow** 

Add file → Create new file → name it exactly <mark>`.github/workflows/snake.yml`</mark> (the slashes create folders): 

```
name: Generate Snake Animation
on:
  schedule:
    - cron: "0 */12 * * *"
  workflow_dispatch:
  push:
    branches: [main]
jobs:
  generate:
    permissions:
      contents: write
    runs-on: ubuntu-latest
    timeout-minutes: 10
    steps:
      - name: Generate snake SVGs
        uses: Platane/snk/svg-only@v3
        with:
          github_user_name: ${{ github.repository_owner }}
          outputs: |
            dist/github-snake.svg?palette=github-
light&color_snake=0891B2&color_dots=#ebedf0,#a5b4fc,#818cf8,#6366f1,#0891B2
            dist/github-snake-dark.svg?palette=github-
dark&color_snake=10B981&color_dots=#2d3343,#4b5563,#7C3AED,#A78BFA,#22D3EE
      - name: Push to output branch
        uses: crazy-max/ghaction-github-pages@v3.1.0
        with:
          target_branch: output
          build_dir: dist
          commit_message: "Update snake animation [skip ci]"
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

Commit to <mark>`main` .</mark> Check the **Actions** tab — the run should go green in ~1 minute and create an <mark>`output`</mark> branch. 

3 

**The colour rule that matters:** the _first_ colour in <mark>`color_dots`</mark> is the empty cell. For the dark snake it must be a visible slate like <mark>`#2d3343`</mark> — against GitHub's dark background <mark>(</mark> <mark>`#0d1117` )</mark> a near-black empty cell disappears entirely and the grid looks broken. 

### **3 Display it** 

```
<div align="center">
<picture>
```

```
  <source media="(prefers-color-scheme: dark)"
```

```
    srcset="https://raw.githubusercontent.com/USER/USER/output/github-snake-dark.svg" />
  <source media="(prefers-color-scheme: light)"
```

```
    srcset="https://raw.githubusercontent.com/USER/USER/output/github-snake.svg" />
  <img alt="Snake eating my contributions"
```

```
    src="https://raw.githubusercontent.com/USER/USER/output/github-snake.svg" />
</picture>
```

```
</div>
```

Only add this **after** the Action runs green — the <mark>`output`</mark> branch won't exist before then, and the image will show as broken. 

It regenerates every 12 hours automatically. To force it: Actions → Generate Snake Animation → **Run workflow** . 

## **Phase 4 — Social badges** 

```
<div align="center">
```

```
<a href="https://www.linkedin.com/in/YOUR-ID/">
```

```
  <img src="https://img.shields.io/badge/LinkedIn-0A66C2?style=for-the-badge
&logo=linkedin&logoColor=white" alt="LinkedIn" />
```

```
</a>
```

```
&nbsp;&nbsp;
```

```
<a href="https://www.instagram.com/YOUR-HANDLE/">
```

```
  <img src="https://img.shields.io/badge/Instagram-0A101F?style=for-the-badge
&logo=instagram&logoColor=A78BFA&labelColor=0A101F" alt="Instagram" />
```

```
</a>
```

```
&nbsp;&nbsp;
<a href="mailto:YOU@EMAIL.COM">
  <img src="https://img.shields.io/badge/Email-0A101F?style=for-the-badge
&logo=gmail&logoColor=10B981&labelColor=0A101F" alt="Email" />
```

```
</a>
```

```
</div>
```

**The LinkedIn trap.** Shields.io has a documented bug: the LinkedIn logo _only_ renders on its brand blue <mark>`#0A66C2` .</mark> Give it any custom colour and the glyph silently vanishes, leaving just the word. Either accept brand blue (above), or embed the logo as a base64 data-URI to keep it themed. Other logos (Instagram, Gmail, Facebook) recolour fine. 

Skip a GitHub badge — it's circular on your own profile. Better: a portfolio or resume link. 

4 

## **Troubleshooting** 

### **"I changed it but nothing happened"** 

This will happen, and it's almost always caching — not a real bug. Diagnose in this order: 

1. **Check the file itself.** Open <mark>`https://raw.githubusercontent.com/.../file.svg?v=999`</mark> (the <mark>`?v=`</mark> bypasses the cache), press **Ctrl+U** , then **Ctrl+F** and search the hex colour you set. If it's there, generation worked and only the display is stale. 

2. **Check your theme.** Dark-mode assets only render in dark mode. If you're in light mode, you're looking at a different file entirely. 

3. **Check the Action ran.** Actions tab — is the newest run green _and_ timestamped after your edit? If not, hit Run workflow. 

4. **Then wait.** GitHub's CDN expires on its own, typically minutes to a few hours. Ctrl+Shift+R clears your browser but not their servers. 

|Symptom|Cause|
|---|---|
|Card shows "API rate limit exceeded"|Using the public instance — self-host (Phase 2)|
|Snake image is broken|`output`branch doesn't exist yet — run the Action|
|Snake grid invisible in dark mode|Empty-cell colour too close to<br>`#0d1117`|
|LinkedIn badge has no logo|Shields.io bug — needs<br>`#0A66C2` or base64|
|Action fails, mentions<br>`maxDuration`|Vercel free-tier limit — set it to 10 in<br>`vercel.json`|
|Colour change invisible|CDN cache, or you're in the other theme|



### **Good to know** 

- **Scheduled Actions pause after ~60 days of repo inactivity.** If you go quiet, the snake freezes until you push or click "Enable workflow". 

- **Stats totals won't match GitHub's exactly.** Different date windows and cache lag — a gap of a few contributions is normal, not a bug. 

- **Top-languages reflects code volume, not skill.** A template's CSS can dominate. Exclude repos if it misrepresents you. 

- **Banner SVGs can't contain working links** — GitHub strips them. Clickable links must be badges in the README. 

**Final order of operations:** repo → upload SVGs → banner block → self-host stats → snake workflow → badges. Test after each phase rather than pasting everything at once — when something breaks you'll know exactly what caused it. 

5 

