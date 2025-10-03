# Personal Portfolio & Blog 

This document explains the structure, configuration, and extensibility of the project. This project is based on:
https://github.com/markhorn-dev/astro-nano

---

## Project Structure

```
astro-nano/
├── .astro/                 # Astro-generated content schemas and metadata
│   ├── content-assets.mjs
│   ├── content-modules.mjs
│   ├── content.d.ts
│   ├── data-store.json
│   ├── settings.json
│   ├── types.d.ts
│   └── collections/
│       ├── blog.schema.json
│       ├── projects.schema.json
│       └── work.schema.json
├── src/                    # Source code (components, content, pages, styles, etc.)
│   ├── consts.ts
│   ├── env.d.ts
│   ├── types.ts
│   ├── components/
│   │   ├── ArrowCard.astro
│   │   ├── BackToPrev.astro
│   │   ├── BackToTop.astro
│   │   ├── Container.astro
│   │   ├── Footer.astro
│   │   ├── FormattedDate.astro
│   │   ├── Head.astro
│   │   ├── Header.astro
│   │   └── Link.astro
│   ├── content/
│   │   ├── config.ts
│   │   ├── blog/
│   │   │   ├── 01-getting-started/index.md
│   │   │   ├── 02-blog-collection/index.md
│   │   │   ├── 03-projects-collection/index.md
│   │   │   ├── 04-work-collection/index.md
│   │   │   ├── 05-markdown-syntax/
│   │   │   │   ├── index.md
│   │   │   │   └── spongebob.webp
│   │   │   ├── 06-mdx-syntax/
│   │   │   │   ├── component.astro
│   │   │   │   └── index.mdx
│   │   │   ├── 07-year-sorting-example/index.md
│   │   │   └── 08-draft-example/index.md
│   │   ├── projects/
│   │   │   ├── project-1/index.md
│   │   │   └── project-2/index.md
│   │   └── work/
│   │       ├── apple.md
│   │       ├── facebook.md
│   │       ├── google.md
│   │       └── mcdonalds.md
│   ├── layouts/
│   │   └── PageLayout.astro
│   ├── lib/
│   │   └── utils.ts
│   ├── pages/
│   │   ├── index.astro
│   │   ├── robots.txt.ts
│   │   ├── rss.xml.ts
│   │   ├── blog/
│   │   │   ├── [...slug].astro
│   │   │   └── index.astro
│   │   ├── projects/
│   │   │   ├── [...slug].astro
│   │   │   └── index.astro
│   │   └── work/index.astro
│   └── styles/global.css
├── public/                 # Static assets (images, fonts, etc.)
│   ├── astro-nano.png
│   ├── astro-sphere.jpg
│   ├── deploy_netlify.svg
│   ├── deploy_vercel.svg
│   ├── favicon-dark.svg
│   ├── favicon-light.svg
│   ├── favicon-dark.png
│   ├── favicon-light.png
│   ├── lighthouse.png
│   ├── patrick.webp
│   └── fonts/
│       ├── atkinson-bold.woff
│       ├── atkinson-regular.woff
│       ├── MonaSans-Light.woff2
│       ├── MonaSans-Regular.woff2
│       └── MonaSans-SemiBold.woff2
├── dist/                   # Production build output (empty until build)
└── ...                     # Config files (astro.config.mjs, tailwind.config.mjs, etc.)
```

---

## 1. `.astro` — Astro Content System
**Purpose:** Stores generated schemas, types, and metadata for content collections.

**Key Files:**
- `content-assets.mjs`, `content-modules.mjs`: Internal Astro content management.
- `content.d.ts`, `types.d.ts`: TypeScript types for content collections.
- `data-store.json`, `settings.json`: Content metadata and settings.
- `collections/`: JSON schemas for each collection: `blog.schema.json`, `projects.schema.json`, `work.schema.json`

> **Note:** You rarely need to edit this folder directly.

Schemas are generated from your `config.ts`.

---

## 2. `src` — Source Code

### 2.1 Configuration & Types
- **`consts.ts`**: Site-wide constants (site name, email, social links, homepage limits).
- **`types.ts`**: TypeScript types for props and data.
- **`env.d.ts`**: TypeScript environment definitions.

### 2.2 Components
Reusable UI blocks in `components/`:
- `ArrowCard.astro`, `BackToPrev.astro`, `BackToTop.astro`, `Container.astro`, `Footer.astro`, `FormattedDate.astro`, `Head.astro`, `Header.astro`, `Link.astro`

### 2.3 Content Collections
Organized in `content/`:
- **`config.ts`**: Defines schemas for blog, projects, work.
- **`blog/`**: Markdown/MDX posts.
- **`projects/`**: Markdown project entries.
- **`work/`**: Markdown work history.

> **Note:** To add content: Create a new folder/file in the relevant collection.

### 2.4 Layouts
- **`layouts/PageLayout.astro`**: Main layout wrapper for pages.

### 2.5 Lib
- **`lib/utils.ts`**: Utility functions.

### 2.6 Pages
Routes in `pages/`:
- `index.astro`: Homepage.
- `robots.txt.ts`, `rss.xml.ts`: Dynamic endpoints.
- `blog/`, `projects/`, `work/`: Listing and detail pages.

> **Note:** To add a page: Create a new `.astro` file in `pages/`.

### 2.7 Styles
- **`styles/global.css`**: Global CSS (uses Tailwind).

---

## 3. `public` — Static Assets
- **Images:** `astro-nano.png`, `astro-sphere.jpg`, `lighthouse.png`, etc.
- **SVGs:** Favicons, deployment badges.
- **Fonts:** In `fonts/` (WOFF/WOFF2 files).
- **Other assets:** `patrick.webp`, etc.

> **Note:** To add assets: Place files here and reference with `/filename.ext` in your code or markdown.

---

## 4. `dist` — Production Build Output
- Empty until you run `npm run build`.
- Contains the static site ready for deployment.

---

## Configuration & Customization

### Site Metadata
Edit `consts.ts`:

```ts
export const SITE = {
  NAME: "Mehdi Maleki",
  EMAIL: "mosiocode@gmail.com",
  NUM_POSTS_ON_HOMEPAGE: 3,
  NUM_WORKS_ON_HOMEPAGE: 2,
  NUM_PROJECTS_ON_HOMEPAGE: 3,
};
```

### Social Links
Edit the `SOCIALS` array in `consts.ts`:

```ts
export const SOCIALS = [
  { NAME: "twitter-x", HREF: "https://twitter.com/mosiocode" },
  { NAME: "github", HREF: "https://github.com/mosioc" },
  { NAME: "linkedin", HREF: "https://www.linkedin.com/in/mosioc" }
];
```

### Content Collections
- Edit `config.ts` to change schemas or add new collections.

### Styling
- Edit `global.css` for custom CSS.
- Edit `tailwind.config.mjs` for Tailwind settings.

### Components
- Add new `.astro` files in `components/`.
- Import and use in pages/layouts.

### Pages
- Add new `.astro` files in `pages/`.
- Astro auto-generates routes.

### Assets
- Add images, fonts, etc. to `public/`.

---

## Adding/Removing Features
- **Add a collection:** Update `config.ts`, create a folder in `content`, update pages/components.
- **Remove a feature:** Delete or comment out the relevant files and references.
- **Add a page:** Create a new `.astro` file in `pages/`.
- **Add a component:** Create a new `.astro` file in `components/`.

---

## Common Commands

| Command                   | Action                                           |
| :------------------------ | :----------------------------------------------- |
| `npm install`             | Installs dependencies                            |
| `npm run dev`             | Starts local dev server at `localhost:4321`      |
| `npm run dev:network`     | Starts local dev server on local network         |
| `npm run sync`            | Generates TypeScript types for all Astro modules.|
| `npm run build`           | Build your production site to `./dist/`          |
| `npm run preview`         | Preview your build locally, before deploying     |
| `npm run preview:network` | Preview build on local network                   |
| `npm run astro ...`       | Run CLI commands like `astro add`, `astro check` |
| `npm run astro -- --help` | Get help using the Astro CLI                     |
| `npm run lint`            | Run ESLint                                       |
| `npm run lint:fix`        | Auto-fix ESLint issues                           |

> **Note:** All commands are run from the root of the project, from a terminal: 
> Replace npm with your package manager of choice. `npm`, `pnpm`, `yarn`, `bun`, etc

---

## References
- `consts.ts` — Site config
- `config.ts` — Content schemas
- `pages/` — Routing/pages
- `PageLayout.astro` — Main layout
- `components/` — UI components
- `global.css` — Styles
- `public/` — Assets

---

## How Everything Works Together
- Astro builds static pages from `pages/`, using content from `content/` and UI from `components/`.
- Content collections are defined in `config.ts` and validated by Astro using schemas in `collections/`.
- Layouts and components provide consistent UI.
- Assets in `public/` are available site-wide.
- Configuration is centralized in `consts.ts`.

> **Note:** To extend, configure, or remove features, update the relevant files as described above. For advanced customization, refer to the [Astro documentation](https://docs.astro.build/en/getting-started/).

