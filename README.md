# SEED Emulators website

A Jekyll site with an English homepage, three emulator project pages, and an
optional Chinese language switch. No frontend framework or Node build is needed.

## Run locally

Install Ruby 3.3 and Bundler. From the repository root:

```sh
bundle install
bundle exec jekyll serve --host 127.0.0.1 --port 4173
```

Open http://127.0.0.1:4173/. Changes to pages, includes, data and CSS rebuild
automatically; refresh the browser to see them. Restart Jekyll after changing
`_config.yml`. On Windows, use RubyInstaller with Devkit.

Build without starting a server:

```sh
bundle exec jekyll build
```

`_site/` is generated output, ignored by Git. Edit source files, never `_site/`.

## Current site structure

```text
index.html                        Homepage: assembles sections only
internet/index.html               Internet project content and resource lists
satellite/index.html              Satellite project content and resource lists
blockchain/index.html             Blockchain project content and resource lists
_config.yml                       Site URL, title, defaults and build settings
_data/
  home.yml                        Homepage hero, project cards and contact copy
  portal_navigation.yml           Current site's top navigation
  portal_themes.yml               Default, Night, Teal and Dawn palettes
  portal_footer.yml               Footer links and team data, shared with sidebar
  zh.json                         English-to-Chinese text translations
_layouts/
  emulators.html                  HTML shell, head, header, main, footer, scripts
  emulator.html                   Project layout, nested inside emulators.html
_includes/
  emulators/
    header.html                   Logo and navigation
    theme-picker.html             Inline palette buttons in the navigation bar
    theme-init.html               Restores the saved palette before page paint
    hero.html                     Homepage banner
    network-art.html              Banner illustration
    projects.html                 Loops over home.yml project cards
    project.html                  One reusable project card
    sidebar.html                  Team and resource sidebar
    contact.html                  Homepage contact strip
    footer.html                   Shared footer and language switch
    footer-links.html             One footer link group
    resource.html                 One project resource row
  components/                     Shared icons, video player, language button, etc.
  head.html                       Metadata and stylesheets
  scripts.html                    Shared JavaScript entry points
  language.html                   Translation data and language script
css/emulators.css                 Current homepage and project styles
css/portal-themes.css             Generates palette overrides and styles buttons
css/site.css                      Shared base styles and older detail-page styles
js/language.js                    Chinese/English switching
js/portal-theme.js                Applies and remembers the portal palette
js/site.js                        Navigation and shared interactions
images/                           Logos, card images and illustrations
video/                            Local video files
```

## How a page is rendered

The homepage declares `layout: emulators` in its YAML front matter:

```text
_layouts/emulators.html
  head + emulators/header.html
  main: {{ content }}
    index.html
      emulators/hero.html
      emulators/projects.html -> emulators/project.html (one per data entry)
      emulators/sidebar.html
      emulators/contact.html
  emulators/footer.html + scripts
```

This is why `index.html` does not include the header and footer itself. Its
rendered content is inserted at `{{ content }}` in the layout, once.

Project pages use `layout: emulator`. Jekyll renders their content inside
`_layouts/emulator.html` (overview, resources, videos), then wraps that with
`_layouts/emulators.html` for the same header and footer.

## Where to make changes

| Change | File |
| --- | --- |
| Homepage headline, description, project images and summaries | `_data/home.yml` |
| Top navigation labels, order and links | `_data/portal_navigation.yml` |
| Author, email, footer links and sidebar resources | `_data/portal_footer.yml` |
| Homepage section order | `index.html` |
| Header or footer markup | `_includes/emulators/header.html`, `footer.html` |
| Card structure used by every project | `_includes/emulators/project.html` |
| Colors, spacing, cards and responsive layout | `css/emulators.css` |
| Palette names, swatches and color values | `_data/portal_themes.yml` |
| Project overview, downloads and videos | Each project's `index.html` |
| Chinese copy | `_data/zh.json` |
| Public domain and deployment subpath | `_config.yml` |

Keep English as the source language. If English copy changes, update the matching
key in `_data/zh.json`. The footer language choice lasts for the current browser
tab. Code samples, protocol names and image contents keep their original form.

Use includes for reusable markup, `_data` for structured shared content, and
layouts for the page shell. Page-specific prose belongs in the page itself.
Use `relative_url` for local links and media paths so `baseurl` works.

## Add a project or resource

1. Create `project-name/index.html` with `layout: emulator`, `portal: true`, a
   unique `project` key, `title`, `category` and `description` in front matter.
   Write its overview below the closing `---`.
2. Add its card to `_data/home.yml` under `projects`; copy an existing entry.
3. Add the navigation entry to `_data/portal_navigation.yml` using the same
   `project` key as its `key`. Add its footer link to `_data/portal_footer.yml`.
4. Add translations to `_data/zh.json` and place preview images in `images/`.

Project front matter accepts `repository`, `resources` and `videos`. Copy an
existing project's `index.html` for the complete format. A resource with
`url: null` displays "Coming soon". Videos use `title`, `description`, and either
`url` for an external video or `src`, optional `webm`, and optional `poster` for
a local video. No video entries displays the existing availability message.

The satellite illustration is conceptual artwork. Its project documentation,
repository and video links are still awaiting actual materials.

## Older detail pages

`why.html`, `how.html`, `capabilities.html`, `cases.html`, `bsday.html` and
`404.html` use the older `default` / `page` layouts. They remain available;
the Internet project links to some of them.

Their header/footer are `_includes/header.html` and `_includes/footer.html`.
Their content uses `_includes/sections/`, `_data/site_content.yml` and
`_data/navigation.yml`. Their five-color theme selector uses `_data/themes.yml`,
`css/themes.css`, `css/theme-picker.css` and `js/theme.js`.

The current homepage and three project pages use `_includes/emulators/` and
offer four palettes in the navigation bar. Their choice is stored in
`seed-portal-theme` in localStorage, separately from the older detail-page
themes. Add a palette by copying a complete entry in `_data/portal_themes.yml`;
the buttons and CSS are generated from that list. Keep IDs unique and add
translated names to `_data/zh.json`. The first entry is the default.
To edit those pages, start with the files in the table
above. Some base styles and scripts are shared between both layout families.

## GitHub Pages

In **Settings -> Pages**, choose **GitHub Actions**. The workflow at
`.github/workflows/static.yml` builds the Jekyll site and publishes `_site/`
on pushes to `master` or manual dispatch. Pull requests targeting `master`
build without deploying. A push to `test` does not trigger this workflow.

The configured public URL is https://gandlfst.github.io. Update `url` and
`baseurl` in `_config.yml` before publishing elsewhere. Dependencies are locked
in `Gemfile.lock`; after changing gems, run `bundle install` and commit the
updated lockfile together with `Gemfile`.
