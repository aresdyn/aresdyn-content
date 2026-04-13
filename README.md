# Ares Dynamics — Content Repository

This repo is the **single source of truth for all blog content** on aresdynamics.com.
The live site reads `posts.json` directly from this repo on every page load.

**Publishing a new article = pushing to this repo. No Ionos login. No FTP.**

---

## Repo Structure

```
aresdyn-content/
├── posts.json                          ← Master index. Controls what appears on the blog.
└── posts/
    ├── _template/
    │   └── index.html                  ← Copy this to start every new article
    ├── ai-social-engineering/
    │   └── index.html
    ├── executive-home-network/
    │   └── index.html
    └── travel-security-high-profile/
        └── index.html
```

---

## How the Live Site Reads This Repo

`blog/index.html` fetches:
```
https://raw.githubusercontent.com/mattcooper13/aresdyn-content/main/posts.json
```

It reads the post index, renders cards for published posts, and links out to the
article pages. **Article HTML files must be deployed to Ionos** — GitHub serves
`posts.json` only; the rendered article pages live at:
```
aresdynamics.com/pages/blog/posts/[slug]/index.html
```

---

## Publishing a New Article — Step by Step

### Step 1 — Add the post entry to `posts.json`

Open `posts.json` and add a new object to the array. Copy an existing entry and update:

```json
{
  "slug": "your-article-slug",
  "title": "Your Article Title",
  "excerpt": "One or two sentences. This appears on the blog index card.",
  "category": "threat",
  "categoryLabel": "Threat Brief",
  "date": "Apr 2025",
  "readTime": "6 min read",
  "featured": false,
  "pattern": "radar",
  "status": "published"
}
```

**category options:** `threat` · `intel` · `guide` · `ops`

**categoryLabel options:** `Threat Brief` · `Intelligence` · `Guide` · `Operations`

**pattern options:**
- threat category: `radar` · `triangulate` · `breach` · `signal`
- intel category: `venn` · `node-map` · `document` · `fingerprint`
- guide category: `chart-line` · `compass` · `checklist` · `layers`
- ops category: `bar-chart` · `terminal` · `shield-grid` · `flow`
- universal: `grid` · `topography` · `hex-field` · `circuit`

**status options:** `published` · `coming-soon`

Set `"featured": true` on only one post — it renders as the large featured card.

---

### Step 2 — Create the article HTML file

```
posts/your-article-slug/index.html
```

Copy `posts/_template/index.html` and fill in:
- The `<title>` tag
- The `<meta name="description">` tag
- The category tag class (`post-tag--threat`, `post-tag--intel`, etc.)
- The `<h1 class="article-title">` — your article title
- The `<p class="article-subtitle">` — one sentence summary
- The date and read time in `.article-meta`
- The `<div class="article-content">` body — full article HTML
- The `<nav class="article-toc">` — anchor links to `<h2 id="">` headings
- The sidebar CTA copy
- The related article links at the bottom of the sidebar

---

### Step 3 — Push to GitHub

```bash
git add posts.json posts/your-article-slug/
git commit -m "publish: your-article-slug"
git push origin main
```

The blog index updates immediately on next page load. No Ionos action needed.

---

### Step 4 — Upload the article HTML to Ionos (one-time per article)

The article page itself must live on Ionos so it renders with nav, footer, and CSS.
Upload via FTP:

```
Local:  aresdyn-content/posts/your-article-slug/index.html
Remote: /pages/blog/posts/your-article-slug/index.html
```

This is the only Ionos touch required per article.

---

## Automation — Claude or n8n Publishing

For automated publishing, the workflow is:

### 1. Generate the article content
Produce the article body as HTML paragraphs. Structure:
- `<p>` for body text
- `<h2 id="section-slug">Section Title</h2>` for sections
- `<strong>Subsection label</strong>` for subsections
- `<div class="article-callout">Key quote or insight</div>` for callouts
- `<hr class="article-divider" />` before the closing CTA paragraph

### 2. Update `posts.json`
Add the new post object to the array. Commit and push.

### 3. Populate `_template/index.html` with the article content
Fill all placeholder fields. Save as `posts/[slug]/index.html`. Commit and push.

### 4. Deploy to Ionos via FTP
Use Ionos FTP credentials to upload the new `posts/[slug]/index.html`.
n8n has an FTP node — configure it with Ionos host, username, and password.

### GitHub API for file commits (no git CLI required)
Claude and n8n can push files to GitHub via the REST API without a local git install:

```
PUT https://api.github.com/repos/mattcooper13/aresdyn-content/contents/posts.json
```

Required headers: `Authorization: Bearer YOUR_GITHUB_PAT`
Body: `{ "message": "publish: slug", "content": "<base64-encoded file content>", "sha": "<current file sha>" }`

Get the current SHA first:
```
GET https://api.github.com/repos/mattcooper13/aresdyn-content/contents/posts.json
```

---

## Keeping `posts.json` on Ionos in Sync

Ionos holds a local `posts.json` as a fallback in case GitHub is unreachable.
After updating the GitHub version, also upload the updated `posts.json` to:
```
/pages/blog/posts.json
```
This ensures the fallback stays current.

---

## Category Color Reference

| Category | Color   | Hex       |
|----------|---------|-----------|
| threat   | Red     | `#C0272D` |
| intel    | Purple  | `#9B8FD4` |
| guide    | Teal    | `#7EB8C9` |
| ops      | Green   | `#7DC97D` |
