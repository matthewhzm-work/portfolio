# Matthew Ho — Portfolio Site

## Project Overview
Personal portfolio site for Matthew Ho, Senior Creative (Copywriter · Strategist · Campaign Lead), based in Singapore. Built in vanilla HTML/CSS/JS — no frameworks, no build tools. Deployed on GitHub Pages at matthewho.work.

## File Structure
- `index.html` — Homepage: 3-column independent flex grid, About section, Recognition section
- `work.html` — All project pages (single file, template-driven by `projectTemplate` field)
- `admin.html` — Password-protected CMS. Password: Righttingz2026!
- `data.js` — Single source of truth for all content. Exposes `PORTFOLIO_DATA` global.
- `images/` — All processed images as WebP (80% quality, 2400px max) or optimised GIF

## Design System
- Fonts: Lexend (display/titles), Inter (body), IBM Plex Mono (labels/UI/mono)
- Colours: --paper:#F5F4EF  --ink:#0A0A0A  --red:#E8200C  --mid:#888  --light:#E2E1DC
- Nav: fixed, transparent, 2-row on mobile. Name left · tagline+awardstrip centre · links right
- Cards: 3 independent flex columns (true Pinterest masonry), 8px gap, translateY(-8px) lift on hover, red overlay, NO shadow, NO image zoom
- Video embeds: max-width 1075px, centred
- Images: always full height (height:auto), never cropped

## data.js Structure
Each project has:
  id, brand, title, subtitle, award, tags, placeholderColor, gridSpan,
  thumbnailUrl, thumbnailType, active, projectTemplate,
  description, fullDescription,
  videos: [{url, type, caption}],       ← array, NOT single videoUrl
  collaterals: [{type, url, caption, orientation}],
  thumbnails: [{url, caption, active}], ← gallery thumbs (HSBC Mind Athletes)
  prints: [{url, caption}],             ← portrait prints (MHA)
  quote,                                ← opening quote (HSBC Mind Athletes)
  openingQuestion                       ← opening question (CWYL)

## Project Templates (projectTemplate field → branch in work.html)
- `lux`                          — video → 3-col writeup → 2-col image grid + mobile slider
- `hsbc-mind-athletes`           — quote → video → writeup → 2-col images → thumb gallery row
- `writeup-then-video`           — writeup → centred video
- `writeup-left-video-right`     — 50/50: writeup left, video right
- `video-left-writeup-right`     — 50/50: video left, writeup right
- `writeup-left-video-right-row3`— split top (writeup L, main vid R) + 3 supporting vids row
- `video-left-writeup-right-row3`— split top (main vid L, writeup R) + 3 supporting vids row
- `two-videos-side-by-side`      — 2 vids side by side → writeup below
- `prints-side-by-side`          — 2 prints full width → centred writeup below
- `mha`                          — video → writeup → 3 portrait prints in 1 row
- `cwyl`                         — opening question → image viewer (← →) → writeup L / vid R
- `default`                      — videos stacked → writeup → collateral grid

## Lightbox Architecture
Pool-based. LB_POOLS object stores named arrays: 'cols', 'thumbs', 'mhaPrints', 'cwylImgs', 'prints'.
Inline onclick uses string keys: onclick="lbOpen('cols', 0)"
Never use variable references in inline onclick strings — they're out of scope after root.innerHTML is set.

## Active/Hidden Projects
- active: true  → shown on homepage grid and project rail
- active: false → deployed but hidden everywhere
- Currently hidden: mercedes-me-adapter, trifecta-snow-surf-skate

## Project Rail
Bottom of every project page. Dark ink background. All active projects as thumbnails.
Auto-scrolls to centre current project on load. Current project dimmed (opacity 0.35).
Desktop: ‹ › arrow buttons. Mobile: swipe only (buttons hidden).

## Image Processing
- Convert to WebP at 80% quality, max 2400px wide
- GIFs preserved as-is (animated), optimised with gifsicle --lossy=80 --colors 128
- Thumbnails for cards: 1400×900px landscape WebP or GIF
- All images go in images/[project-id]/ folder

## Phone Number
REMOVED everywhere — do not add it back to any file.

## GitHub Deployment
Repo: matthewho.github.io (or similar). Deploy from main branch via GitHub Pages.
Images uploaded via GitHub Desktop (not web UI — too slow for bulk).
Custom domain: matthewho.work

## CMS Admin
URL: admin.html. Password: Righttingz2026!
5 tabs per project: Basic Info, Thumbnail, Video (multi-video list), Collaterals, Gallery Thumbs.
Toggle Live/Hidden per project in list view.
Export + Download data.js after making changes.
All save operations flush DOM → state before writing (no oninput closures).

## Remaining Projects Not Yet Built
The following projects are on default template and need bespoke layouts when Matthew provides assets:
- nea-good-for-me-good-for-the-environment
- bybit-valentines-day
- ofnoah-discreet-digital-clinic
- vivo-v15
- volkswagen-prints

## Key Rules
1. Never use oninput closures for save logic — always read from DOM by ID on save
2. Never put variable references in inline onclick strings — use LB_POOLS string keys
3. Always filter D.projects by active !== false before rendering grid or rail
4. Images must use height:auto — never object-fit:cover on displayed images
5. Video max-width is 1075px (25% bigger than the original 860px)
6. No box-shadow on card hover — translateY(-8px) lift only
7. No credentials/meta sidebar on any project page template
8. data.js uses videos[] array not videoUrl/videoType/videoCaption single fields