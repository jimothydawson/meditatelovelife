# Meditate Love Life

Static website for **Kari Hamele** — Vedic meditation teacher, Killarney Heights, Sydney.

Live: [meditatelovelife.com](https://meditatelovelife.com)

---

## Stack

- [Astro](https://astro.build) — static site generator, `output: 'static'`
- Zero framework dependencies — vanilla Astro components, plain CSS
- Minimal JavaScript — one small inline script for the mobile nav toggle
- Deploy target: **Netlify** (primary) or Vercel

---

## Content management

This site is managed via the **companion CMS app** (`middleware-cursor`), an AI-powered chat interface that allows Kari to update content by describing changes in plain English. The CMS reads and edits files in `src/content/` using a `listFiles → readFile → proposeEdit` workflow, creating a GitHub pull request and a Netlify deploy preview for every change. Kari reviews the preview and approves or discards before anything goes live.

### CMS allowed paths

The CMS is configured with the following `allowed_paths` in `user_repositories`:

```
src/content      ← all user-facing text (JSON files, one per section)
src/pages        ← page composition (rarely needs editing)
src/styles       ← design tokens in global.css (CSS custom properties)
```

Components (`src/components/`) and layouts (`src/layouts/`) are intentionally outside `allowed_paths`. Structural changes require a developer.

---

## Content node map

Every piece of user-facing text lives in `src/content/`. Each JSON file is one CMS-addressable content node:

| File | Controls | Key fields |
|------|----------|------------|
| `src/content/site.json` | Site-wide globals | `name`, `tagline`, `email`, `abn`, `location`, `metaDescription`, `footerNote` |
| `src/content/hero.json` | Hero section | `heading`, `subheading`, `tagline`, `ctaPrimary`, `ctaSecondary`, `mandalaAlt` |
| `src/content/about.json` | About Kari | `heading`, `paragraphs` (array), `imageSrc`, `imageAlt` |
| `src/content/vedic.json` | Vedic meditation explainer | `heading`, `intro`, `paragraphs` (array), `pullQuote` |
| `src/content/services.json` | Sessions & courses | `heading`, `intro`, `services` (array of objects), `note` |
| `src/content/booking.json` | Booking section | `heading`, `intro`, `embedType`, `embedCode`, `fallbackCta`, `fallbackEmail` |
| `src/content/contact.json` | Contact & footer | `heading`, `intro`, `email`, `location`, `locationNote`, `abn` |

### Placeholder fields

Fields that need real content before launch are prefixed with `PLACEHOLDER —` in their value. The CMS AI will show these values when reading the file, making them easy to spot and update.

---

## Booking widget

The booking section shows a direct email link until a booking system is chosen. To activate:

1. Choose a platform (Square Appointments, Acuity, or Calendly)
2. Update `src/content/booking.json`:
   - Set `embedType` to `"square"`, `"acuity"`, or `"calendly"`
   - Paste the embed code into `embedCode`
3. A developer should verify the embed renders correctly before publishing

---

## Local development

```bash
# Install dependencies
npm install

# Start dev server (http://localhost:4321)
npm run dev

# Build for production
npm run build

# Preview production build locally
npm run preview
```

---

## Deployment

### Netlify (primary)

1. Connect this repository to a new Netlify site
2. Netlify will detect `netlify.toml` automatically:
   - Build command: `npm run build`
   - Publish directory: `dist`
3. Set the `netlify_site_id` and `live_url` in the CMS's `user_repositories` record to enable deploy previews

### Vercel (alternative)

Zero-config — Vercel auto-detects Astro. Connect the repo and deploy.

---

## Project structure

```
meditate-love-life/
├── public/
│   └── images/
│       ├── mandala.svg          ← PLACEHOLDER: replace with final mandala artwork
│       └── kari-placeholder.jpg ← PLACEHOLDER: replace with Kari's portrait photo
├── src/
│   ├── content/                 ← CMS-editable (all allowed_paths)
│   │   ├── site.json
│   │   ├── hero.json
│   │   ├── about.json
│   │   ├── vedic.json
│   │   ├── services.json
│   │   ├── booking.json
│   │   └── contact.json
│   ├── layouts/
│   │   └── BaseLayout.astro     ← head, fonts, meta tags
│   ├── components/
│   │   ├── Nav.astro
│   │   ├── Hero.astro
│   │   ├── About.astro
│   │   ├── VedicMeditation.astro
│   │   ├── Services.astro
│   │   ├── Booking.astro
│   │   └── Contact.astro
│   ├── pages/
│   │   └── index.astro          ← single-page composition
│   └── styles/
│       └── global.css           ← design tokens (CMS-editable CSS vars)
├── astro.config.mjs
├── netlify.toml
├── package.json
├── tsconfig.json
└── README.md
```

---

## Design

- Palette: warm cream (`#FAF8F4`), terracotta accent (`#8B6E5A`), sage green (`#6B8F71`)
- Typography: Nunito Sans (Google Fonts, weights 300/400/600)
- Mobile-first, responsive — no CSS frameworks
- Design tokens live in `src/styles/global.css` as CSS custom properties

To adjust colours or typography sitewide, update the `:root` variables at the top of `global.css`.

---

## Developer notes

- This is a standalone repo — it does not import from or depend on `middleware-cursor`
- All components are zero-JS Astro components except `Nav.astro`, which has a tiny inline script for the mobile menu toggle
- The `About` section portrait image gracefully degrades if the placeholder image path is broken (`onerror` fallback)
- The `Booking` section automatically shows a friendly email prompt while `embedType` starts with `"PLACEHOLDER"`, and switches to the embed widget once a real type is set
