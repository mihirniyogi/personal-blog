# Mihir's Blog

A personal blog — life updates, things I'm trying for the first time, books I'm
reading, and the occasional musing. It started while I was on exchange in
Melbourne and grew from there. Live at **[blog.mihirniyogi.com](https://blog.mihirniyogi.com/)**.

If you're just here out of curiosity about the blog itself: most posts are short
write-ups of an experience or an idea, each tagged with the coffee I was drinking
and the song I had on at the time. Posts are organised two ways — a **category**
for what kind of post it is (Trying Things, Reading Recap, Living Life, Mindful
Musings), and a **series** for multi-part threads that belong together (e.g. a
book read chapter by chapter). There's also a private section I keep for personal
journalling that isn't part of the public feed.

## Tech setup

The site is built with [Hugo](https://gohugo.io/) (extended edition) on top of
the [Stack theme](https://github.com/CaiJimmy/hugo-theme-stack), which is pulled
in as a git submodule. The theme is used as-is; everything custom lives in a thin
override layer so theme updates stay painless.

### Layout

| Path | What it holds |
|---|---|
| `content/post/` | Public posts (one folder per post, with its images). |
| `content/private/` | Private journalling — separate section, not in the main feed. |
| `config/_default/` | Site config, split by concern (`hugo.toml`, `params.toml`, `menu.toml`, …). |
| `assets/scss/custom.scss` | All custom styling. Shared values (accent colours, card surfaces) are CSS variables defined once at the top. |
| `layouts/` | Hugo template overrides, kept minimal: |
| `layouts/single.html` | Post page — adds the back link and the series chip. |
| `layouts/_partials/article/series.html` | Renders the "part of the series" link to the series archive. |
| `layouts/shortcodes/youtube.html` | Replaces Hugo's built-in YouTube embed with a centered, responsive 16:9 one. |
| `layouts/_markup/render-image.html` | Image render hook — supports an optional `width` / caption via the Markdown title. |

### Content model

- **Categories** = the kind of post. Exactly one per post; shown on the homepage cards.
- **Series** = a specific multi-part thread (e.g. a book). Optional. Adds a link to the `/series/<name>/` archive.
- **Tags** = loose keywords.

Front matter also carries `drink` and `song` fields, surfaced as the post's "vibe" metadata.

**Cover image:** set the "Featured Image" field in the CMS (or `image:` in
front matter). The file lives in the post's own folder and becomes the card
cover on the homepage. Posts without one still publish — they just render as a
text card.

### Theming

The site-wide accent is a fixed blue, set as `--accent-color` /
`--accent-color-darker` in the `:root` block of `assets/scss/custom.scss`
(and mirrored in the dark-scheme block). Change it there if you ever want a
different default.

Each category has its own colour, which overrides that blue on its post
pages and is also used for its pill label. **All category colours live in one
place: `data/category_colors.yaml`**, keyed by the url-ized category name
(e.g. `trying-things`):

```yaml
trying-things:
  color:  "#d47254"   # post-page accent + pill background
  darker: "#bc5738"   # hover / link-darker shade
  text:   "#ffffff"   # pill label text
```

Two small consumers read this file: the pill via
`layouts/_partials/article/components/details.html`, and the post-page accent
via an inline style in `layouts/single.html`. To add a category colour, just
add an entry here — nothing else to touch. A category not listed still works:
it falls back to the global blue accent and the theme's hashed pill colour.

### Running locally

```sh
git clone --recurse-submodules https://github.com/mihirniyogi/personal-blog
cd personal-blog
hugo server --disableFastRender
```

If you cloned without submodules, run `git submodule update --init` first.
Adding or changing template/shortcode files needs a server restart; SCSS and
content hot-reload.

### Deployment

Pushing to `main` triggers a GitHub Actions workflow
(`.github/workflows/deploy.yml`) that builds the site and `rsync`s the output to
a self-hosted Raspberry Pi, reached over a Cloudflare Tunnel. A Telegram message
reports success or failure. No build artefacts are committed — `public/` and
`resources/` are gitignored.
