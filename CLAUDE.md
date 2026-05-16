# Matthew Ho — Portfolio Site

## Project Overview
Personal portfolio site for Matthew Ho, Senior Creative (Copywriter · Strategist · Campaign Lead), based in Singapore. Built in vanilla HTML/CSS/JS — no frameworks, no build tools. Deployed on GitHub Pages at matthewho.work.

## File Structure
- `index.html` — Homepage: 3-column independent flex grid, About section, Recognition section
- `work.html` — All project pages (single file, block-based renderer + legacy template fallback)
- `admin.html` — Password-protected CMS
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
  quote,                                ← opening quote (HSBC Mind Athletes, vivo)
  openingQuestion,                      ← opening question (CWYL)
  blocks: [{...}]                       ← page builder blocks (see below — overrides template if present)

## Block-Based Page Builder (PRIMARY system)
Every active project now has a `blocks` array in data.js. When `P.blocks && P.blocks.length > 0`, work.html renders from blocks and ignores the projectTemplate entirely.

### Block Types
- `video`     — {id, type, url, videoType, caption, width}
                width: 'full' | 'wide' (1075px) | 'medium' (720px) | 'narrow' (560px)
- `callout`   — {id, type, text, size, align}
                size: 'xl' (clamp 28→52px) | 'lg' | 'md' | 'sm'
                align: 'center' | 'left'
- `text`      — {id, type, html, columns, width}
                html: Quill-generated HTML string
                columns: 1 | 2 | 3
- `image`     — {id, type, url, caption, width, lightbox}
- `image-row` — {id, type, images:[{url,caption}], columns, gap, lightbox}
                gap: 'tight' (4px) | 'normal' (12px) | 'wide' (32px)
- `split`     — {id, type, leftType, leftUrl, leftVideoType, leftHtml, leftLabel,
                              rightType, rightUrl, rightVideoType, rightHtml, rightLabel}
                leftType/rightType: 'text' | 'video' | 'image'
                Renders as 50/50 grid, collapses to 1-col on mobile

### Block IDs
Each block has a short unique id (e.g. 'b101', 'ba03'). These must be unique across the entire data.js — use a project-prefix convention.

### Lightbox Architecture
Pool-based. LB_POOLS object stores named arrays. Block renderer auto-registers:
  LB_POOLS['blk_'+b.id] for image and image-row blocks with lightbox:true
Never use variable references in inline onclick strings — use LB_POOLS string keys.

## Legacy Templates (fallback only — do not use for new projects)
If a project has no blocks array (or empty blocks), work.html falls back to projectTemplate:
- `lux`, `hsbc-mind-athletes`, `writeup-then-video`, `writeup-left-video-right`,
  `video-left-writeup-right`, `writeup-left-video-right-row3`, `video-left-writeup-right-row3`,
  `two-videos-side-by-side`, `prints-side-by-side`, `mha`, `cwyl`, `ofnoah`, `vivo`, `vw-prints`, `default`
Hidden projects (active:false) still use legacy templates and do not need blocks.

## Active/Hidden Projects
- active: true  → shown on homepage grid and project rail
- active: false → deployed but hidden everywhere
- Currently hidden: mercedes-me-adapter, trifecta-snow-surf-skate, nea-good-for-me-good-for-the-environment

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
URL: admin.html. Password stored in sessionStorage only — not documented here.
3-tab project editor: Card | Page Content | Gallery
- Card tab: brand, title, subtitle, award, tags, description (RTE), thumbnail
- Page Content tab: full block builder — add/remove/reorder blocks of any type
- Gallery tab: toggle individual thumbnail images on/off
Toggle Live/Hidden per project in list view. Visual 3-column grid mirrors homepage order.
← → arrows to reorder projects. Export + Download data.js after making changes.
All save operations read from DOM by ID on save (no oninput closures).

## Remaining Projects Not Yet Built
The following projects are hidden (active:false) and need bespoke blocks when Matthew provides assets:
- nea-good-for-me-good-for-the-environment
- bybit-valentines-day (active, has blocks — may need richer layout)
- vivo-v15 (active, has blocks — social films are 4:5 ratio, currently rendered as 16:9)
- volkswagen-prints (active, has blocks)

## Key Rules
1. Never use oninput closures for save logic — always read from DOM by ID on save
2. Never put variable references in inline onclick strings — use LB_POOLS string keys
3. Always filter D.projects by active !== false before rendering grid or rail
4. Images must use height:auto — never object-fit:cover on displayed images
5. Video max-width is 1075px (25% bigger than the original 860px)
6. No box-shadow on card hover — translateY(-8px) lift only
7. No credentials/meta sidebar on any project page template
8. data.js uses videos[] array not videoUrl/videoType/videoCaption single fields
9. Block IDs must be unique across all projects in data.js
10. **Any direct code edit to a project's page content (copy, layout, assets) must also be reflected in that project's blocks[] array in data.js so the CMS stays in sync.** Do not patch work.html templates or hardcode content that bypasses the block system.
11. Never document passwords or credentials in this file or any tracked file in the repo.
