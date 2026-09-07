# AGENTS.md

Technical notes for agents working on this Obsidian vault, which is published as
a static site with Quartz 5. Human-facing orientation lives in `README.md`.

This vault was cloned from `~/Obsidian/lang-media-arts`, which was itself
built from the same Quartz template, so most of this file carries over from
there. Entries are structural to the template and expected to hold, but were
not all re-tested here.

## Layout

```
./              Obsidian vault root (this is what Obsidian actually opens)
  content/      what Quartz builds from. Notes and their assets go here
  .quartz/      the Quartz 5 install, hidden from Obsidian
  public/       build output, gitignored, safe to delete
```

The site was converted from the Quarto project at
`/Volumes/Projects/Quarto/fsu-interactive-media-fa25`. Page mapping:

| Quartz page           | Quarto source                                 |
| --------------------- | --------------------------------------------- |
| `index.md`            | `index.qmd`, front matter through assignments |
| `schedule.md`         | `index.qmd`, the Schedule section             |
| `policies.md`         | `index.qmd`, the Policies section             |
| `panels.md`           | `groups.qmd`                                  |
| `agendas/w1-intro.md` | `w1-intro.qmd`                                |

The Quarto site put the schedule and the policies inside `index.qmd`. They are
split out here because Quartz navigates by page in the explorer, where one
350-line index would be a single opaque entry.

Quarto constructs that had no Quartz equivalent and were converted rather than
carried over:

- `::: {.column-margin}` asides. Quartz has no margin column. Video asides
  became inline embeds; the reading asides in `groups.qmd` became
  `> [!note] Materials` callouts.
- `{{< video https://youtu.be/ID >}}` shortcodes became
  `![](https://www.youtube.com/watch?v=ID)`, which Quartz turns into an iframe.
- `:::: {.content-visible when-format="html"}` / `when-format="pdf"` pairs.
  Only the HTML branch survived; there is no PDF output here.
- The Quarto `_extensions/` (fontawesome, lightbox, PrettyPDF) and the
  `ember.scss` / `custom_callouts.scss` theme were not ported. Image lightbox
  is handled by the `quartz-image-zoom` plugin instead, and the theme is the
  one inherited from `lang-media-arts`.

Known gaps carried over from the Quarto source, not introduced by the
conversion:

- `index.qmd` linked `pdf/dibbell-gold-farmers.pdf`, which does not exist in
  the Quarto project. The Week 7 entry on `schedule.md` names the article
  without a link.
- The Counseling Services paragraph in `policies.md` reads "outreach
  ALTERNATIVE ECOSYSTEMSs", a mangled find-and-replace in the original. Left
  verbatim.
- `index.qmd` had an empty `## Objectives` heading. It is still empty.
- `panels.md` has had the Fall 2025 rosters cleared: every entry reads
  `Panel: *TBA*`, awaiting the Fall 2026 roster. Dates are already rolled
  forward.

## The wi26 agenda pages

`agendas-wi26/` at the vault root holds six lecture pages imported from a
*different* course: the Quarto project at
`/Volumes/Projects/Quarto/fsu-interactive-media-wi26` (Winter 2026), not the
Fall 2025 project the rest of this site came from. They are staged, not
published: nothing under the vault root is built. Publishing them is
`git mv agendas-wi26 content/agendas-wi26`, after which the explorer picks them
up automatically; no sidebar wiring is needed.

Five were under `Agendas` in the Quarto sidebar; `w1-commentary` was under
`Commentary Papers` and is included because it is the sixth non-index page.

None of these are committed to the Fall 2026 syllabus yet. `w3-dark-forest` in
particular is provisional: the instructor may drop the Dark Forest unit and is
keeping the page staged in the meantime. Do not wire any of them into
`content/schedule.md` or link them from a published page until asked. Dropping
one is `git rm -r` on the page plus the assets only it uses.

Their assets travelled with them in `agendas-wi26/img/` and `agendas-wi26/pdf/`,
referenced as page-relative `img/...` and `pdf/...`, which is what Quartz
resolves correctly from a subfolder page. Verified by building with the folder
temporarily under `content/`: 41 of 42 asset references returned 200.

Conversion notes beyond the ones listed above for the Fall 2025 pages:

- `[text]{.aside}` spans became GFM footnotes, since Quartz has no margin
  column and the spans sit mid-sentence. A span that was its own paragraph
  became a `> [!note] Aside` callout instead.
- The 16-thumbnail strip on `w3-avatars` became a `<div class="thumb-gallery">`,
  styled in `.quartz/quartz/styles/custom.scss`.
- Quarto `width=` attributes became raw `<img width>` (or `<a><img></a>` when
  the image was also a link), because a wikilink embed cannot be wrapped in a
  markdown link.
- `<br>` runs that existed only to push content down the Quarto margin column
  were dropped.

Two gotchas found while verifying, both worth remembering:

- Quartz slugifies asset filenames on emit. `Lurking_ Intro.pdf` became
  `lurking_-intro.pdf`, so its own link 404'd, and the space also broke
  markdown link parsing outright. The staged PDFs were renamed to slug-safe
  names and the links rewritten. Keep asset filenames lowercase and
  hyphenated.
- Relative asset paths resolve to the wrong directory until the dev server has
  indexed the asset. Copying in a folder of markdown *and* images, then reading
  the page before a restart, shows `../img/x.png` instead of
  `../<folder>/img/x.png`. Restart before concluding a path is wrong.

`w3-dark-forest` links `pdf/yancey-strickler-medium-pt1.pdf`, which is absent
from the Quarto source: that project renamed the file to
`yancey-strickler-dark-forest-pt1.pdf` without updating the link. The copy in
`~/Obsidian/teaching/library/` still carries the original name and is
byte-identical to the renamed one (md5 `f8ef9b5e…`), so it was copied in under
the linked name and the link left alone. All 42 asset references now resolve.

`~/Obsidian/teaching/library/` is where the shared PDF library lives; prefer it
over a per-course `pdf/` directory when hunting for a missing reading.

## Screening clips

Video embeds live on `content/yt-library.md`, not inline in the schedule: eight
600px iframes made `schedule.md` hard to scan. The schedule links into that
page by heading anchor (`yt-library#week-5-habitat`), so a renamed heading there
silently breaks the link. Quartz slugifies headings with github-slugger, and
`## Week 5: Habitat` becomes `week-5-habitat` — note the pipe form used for the
schedule's own headings (`Week 5 | F 10/02`) would slug to `week-5--f-1002`,
which is why the library uses a colon instead.

After editing either file, check the anchors still match:

```
curl -s localhost:8080/yt-library | grep -oE '<h2 id="[^"]*"'
curl -s localhost:8080/schedule   | grep -oE 'href="[^"]*yt-library[^"]*"'
```

## Semester dates

Dates were rolled from Fall 2025 to Fall 2026 against the university's
undergraduate day school academic calendar, not by shifting a year:

- Classes begin Thu 3 Sept 2026, so week 1 is Fri 09/04. Labor Day is Mon
  09/07 and does not affect a Friday class.
- Thanksgiving recess runs T 11/24 4:45 p.m. to Sun 11/29, so week 13
  (Fri 11/27) is still the no-class week, matching Fall 2025.
- Last day of classes is Fri 11 Dec 2026, so week 15 is an actual meeting.
  In Fall 2025 the equivalent Friday fell inside the exam period and there was
  no week 15 session, so that slot has no content carried over and needs
  filling. Reading day is Mon 12/14; exams run 12/15-21 with 12/22 as a snow
  day.
- No other fall holiday lands on a Friday: Indigenous Peoples' Day is Mon
  10/12 and Veterans Day is Wed 11/11.

Re-check these against the calendar before reusing this site for another term;
the Labor Day shift moves the whole grid.

Source: https://www.fitchburgstate.edu/academics/academic-affairs-division/undergraduate-day-school-academic-calendar

Drafts and reference material go at the vault root, outside `content/`, so they
are not part of the site. Moving a file into `content/` is what publishes it.

## Running it

Use the scripts, never `npx quartz`. The repo's own bin is not linked into
`node_modules/.bin`, so npx falls through to an unrelated `quartz` package on
the registry (v0.0.1, a transmission-daemon client).

| Task            | Command                           |
| --------------- | --------------------------------- |
| Serve with HMR  | `./dev.sh` (8080, ws 3003)        |
| Build to public | `./build.sh`                      |
| Override ports  | `PORT=8081 WS_PORT=3004 ./dev.sh` |

Both scripts work from any directory.

`.quartz/.node-version` pins `v22.16.0`, which nodenv does not have installed
(verified here). `./dev.sh` then dies immediately with
`nodenv: version 'v22.16.0' is not installed` and never reaches Quartz. Work
around it per-run with `NODENV_VERSION=22.22.2 ./dev.sh`. The permanent fixes
are `nodenv install 22.16.0` or editing the pin, but that file belongs to the
vendored Quartz tree, so prefer the env var unless the user wants it changed.
Quartz only requires node >= 22.

## Gotchas that cost real time

Check these before diagnosing anything else. Each records the symptom as well
as the cause, because most of them presented as a different problem.

Everything the site serves must live under `content/`. Quartz only globs the
directory passed via `-d`. A file above it is never copied to `public/`, and a
`../` path does not escape: Quartz normalises it away, so `../img/x.jpg` emits
as `src="././img/x.jpg"` and 404s. Subdirectories at any depth are fine, and
the folder name does not matter. `content/img/` is the convention here.

Adding a new asset file needs a server restart. The incremental rebuild picks
up markdown edits but does not copy newly added non-markdown files. The symptom
is an `<img>` that renders while the file itself 404s. Restart `./dev.sh` and
it is copied. Editing markdown afterwards hot reloads normally.

A YAML frontmatter error kills the whole dev server, not just the page. It
prints `Failed to process markdown` and the process exits, so the port goes
dead. Body-text errors do not do this, only frontmatter. Before investigating
hot reload, check the server is still alive:
`lsof -nP -iTCP:8080 -sTCP:LISTEN`. The recurring trigger is an unquoted colon
in a title, which YAML reads as a nested mapping. Quote it:
`title: "History of Interactive Media & Games"`.

The hot-reload client never reconnects. Quartz emits
`new WebSocket("ws://localhost:3003").addEventListener("message", () => location.reload(true))`
with no `onclose` handler. Any tab open across a server restart or crash holds a
dead socket forever, rebuilds correctly server-side, and never refreshes. Always
hard-reload the page after restarting the server.

Deleting a file while the server runs can poison the rebuild. The pending delete
is retried on every subsequent rebuild and fails with
`ENOENT: no such file or directory, unlink '../public/...'`, which blocks all
further rebuilds. Restart to clear it. Relevant when cleaning up scratch files.

Never run `build.sh` while `dev.sh` is running. Both write to `public/` and
`build.sh` cleans it first, which deletes the files the dev server is serving
and poisons its rebuild loop. The symptom is a server that still answers 200
with stale content but silently stops picking up edits. Restart to clear it.

Open Obsidian on the project root, not on `content/`, matching the sibling
vaults. Consequence: in the sidebar `content` is just one folder among others,
and anything created at the top level lands beside it rather than inside it,
where Quartz cannot see it.

`.obsidian/` was deliberately not copied from `lang-media-arts`: its plugin
`data.json` files hold live credentials and one plugin ships a 59MB binary. So
Obsidian will create a fresh `.obsidian/` on first open. Set
`attachmentFolderPath` to `content/img` then, or pasted images default to the
vault root and 404.

## Authoring

Images. Standard markdown works but has no width control. Use the wikilink form
to size, where the number is pixels and height stays auto:

```markdown
![[img/photo.jpg|500]]
![[img/photo.jpg|500x300]]
```

Callouts. All 13 Obsidian types render, plus a 14th `custom` defined by this
theme in `custom.scss`. A blank line ends a callout, there is no closing marker;
use a bare `>` for a blank line inside one. Append `-` to start collapsed, `+`
for expanded but foldable.

```markdown
> [!custom] An explicit title
> Body.
>
> Second paragraph, same box.
```

Always give `[!custom]` a title. With none, Quartz falls back to the capitalised
type name and the header reads "Custom". An empty title and `&nbsp;` both fall
back too; only a literal zero-width space renders blank, which is not worth the
invisible character. To drop the header entirely, hide
`.callout[data-callout="custom"] > .callout-title` in `custom.scss`, accepting
that it removes the icon and titles from every custom callout.

YouTube. An image embed pointed at a watch URL becomes an iframe:

```markdown
![](https://www.youtube.com/watch?v=VIDEO_ID)
```

The iframe is a fixed `width="600px"` with no aspect-ratio rule, so it does not
scale on narrow screens. No responsive CSS exists for it yet in any vault here.

## Configuration

Prefer `.quartz/quartz.config.yaml` over patching vendored Quartz source, since
config survives an upgrade. Sidebar component labels take a `title` option:

```yaml
  - source: "@quartz-community/explorer"
    enabled: true
    options:
      title: Pages
```

Note that the explorer's mobile toggle `aria-label` is hardcoded in the compiled
plugin under `node_modules` and is not reachable from config or from the i18n
strings in `.quartz/quartz/i18n/locales/en-US.ts`.

Everything the YAML cannot express lives in `.quartz/quartz/styles/custom.scss`:
the self-hosted font, a tighter heading scale, wrapped code blocks, a card grid
for folder listings, and the `[!custom]` callout.

Departure Mono is not on Google Fonts. Every build logs a failed fetch for it
(here: `Failed to fetch font Departure Mono with weight 700, got Bad Request`).
This is expected and harmless; the `@font-face` in `custom.scss` is what
actually loads it. Pinning `weights: [400]` in the config stops the request
asking for a weight that does not exist anywhere, but does not silence the
warning.

`baseUrl` is `mroberts1.github.io/fsu-interactive-media-fa26`, including the subpath,
because Pages serves this repo under a path rather than at a domain root.
Dropping the subpath breaks every generated link.

The `@quartz-community/cname` plugin is disabled. It writes `public/CNAME` from
`baseUrl`, and any CNAME file makes Pages try to serve a custom domain, which
fails on a `github.io` subpath. Re-enable it only alongside a real domain.

Local deviations from the stock template, all in `quartz.config.yaml`:
`page-title` and `graph` are disabled, the explorer is titled `Pages`,
`table-of-contents` is capped at `maxDepth: 2` (h1 and h2 only), and
`content-meta` has `showReadingTime: false`. The site title lives in
`configuration.pageTitle`, not in the disabled `page-title` component, and it
still reaches the page through the `og:site_name` meta tag.

## Deployment

The site publishes to GitHub Pages from `main` via `.github/workflows/deploy.yml`:
`npm ci` in `.quartz/`, then the same build command as `build.sh`, then
`upload-pages-artifact` on `public/`. Pushing to `main` is the whole deploy
step; there is nothing to run locally first.

Because a push is a deploy, do not commit or push while iterating. Work
locally against `./dev.sh` and let the user decide when to publish.

Live at https://mroberts1.github.io/fsu-interactive-media-fa26/
Repo: https://github.com/mroberts1/fsu-interactive-media-fa26 (public)

## What is deliberately not published

The repo's history starts at one root commit. The conversion work happened over
seven commits that contained the Fall 2025 student roster, an office-hours Zoom
link with its password in the query string, and 79MB of reading PDFs. Rather
than push that and rewrite it afterwards, `main` was rebuilt as a single clean
commit before the first push. The original seven commits survive locally on the
`backup-pre-purge` branch, which must never be pushed.

Three standing consequences:

- `*.pdf` is gitignored. The PDFs stay on disk locally but are absent from the
  repo, so eight links on `/schedule` and `/panels` 404 on the live site. That
  is expected, not a regression. Restoring them means removing the ignore rule
  and accepting a 79MB repo, or hosting the readings elsewhere and repointing
  the links.
- `content/panels.md` carries no names. Every panel reads `*TBA*`. Keep it that
  way on the public site; a roster belongs in a private note, not here.
- The Zoom link on `content/index.md` is the bare meeting URL. Do not paste
  back a link with `?pwd=` in it.

Before any future push, check nothing sensitive is being added:

```
git grep -nE 'pwd=|^- [A-Z][a-z]+ [A-Z][a-z]+$' main -- content
git ls-tree -r --name-only main | grep '\.pdf$'
```

CI resolves node from `.quartz/.node-version` via `setup-node`, which installs
`v22.16.0` on demand. Only local nodenv lacks that version, so the workaround
above is a local concern and must not be "fixed" by editing the pin, which
would change what CI builds with.

`.quartz/` is vendored, not a git clone. Its own `.git` was removed so the
outer repo could track the files, so `git pull` from upstream Quartz is not
available. It sits at `jackyzha0/quartz` branch `v5`, commit
`754058ad54665fe80de8bcaaaa1626e1d0fcf118`. To upgrade, clone that repo fresh
and reapply the local changes, which are `quartz.config.yaml`, `.node-version`,
`quartz/styles/custom.scss`, and `quartz/static/fonts/`.

`.obsidian/` is gitignored. Plugin `data.json` files hold live credentials, and
one plugin ships a 59MB binary. The site build never reads it. `.smart-env/`
(Smart Connections embeddings) is gitignored for the same reason.

## Publishing to Blackboard

`script/blackboard` renders the pages listed in `blackboard.json` and pushes
each one as a document into the course's Syllabus folder, plus an external link
back to the Pages site. Blackboard is an output, like the Pages site and the
PDF; the vault is the source. Editing a generated document in the Blackboard UI
works, but the next push overwrites it.

    script/blackboard pages    the content pages listed in blackboard.json
    script/blackboard link     the "Web syllabus" external link
    script/blackboard all      both
    --print                    write the HTML locally, push nothing
    --publish                  make the documents visible to students

Without `--publish` documents are created unavailable, so a push can be checked
in Blackboard before students can see it. Generated `blackboard-*.html` files
are build artifacts and gitignored.

Course id, site url, folder id and page list live in `blackboard.json`. The
Fall 2026 shell is `_109741_1` and its Syllabus folder is `_3433065_1`. Both
FA25 and FA26 are `ultraStatus: CLASSIC`, not Ultra, even though `/ultra/` URLs
render them.

Fitchburg does not hand instructors an API key, so auth is the logged-in
browser session driven through `agent-browser`, the same as `script/canvas` in
`marlboro-digital-culture`. Log in once with
`agent-browser open --headed https://blackboard.fitchburgstate.edu`, which goes
through Microsoft SSO. The session carries `timeout:28800`, so it lasts 8 hours:
once per working day, not once per push.

Things that cost time to find, each established by testing rather than docs:

Writes need Blackboard's XSRF token as an `X-Blackboard-XSRF` header. Without it
every write is `403 {"message":"This request is not associated with a valid
session."}` while reads keep working, which reads like a permissions problem
rather than a missing header. The token is the `xsrf:` field inside the
`BbRouter` cookie, which is HttpOnly, so page JavaScript cannot reach it.
`xsrf()` shells out to `agent-browser cookies get` and extracts only that field.
That command prints the entire cookie jar, so never run it by hand and paste the
output anywhere; keeping it inside the script is what lets the permission be
`Bash(script/blackboard:*)` rather than a standing right to read every session.

The Ultra SPA monkey-patches both `window.fetch` and `XMLHttpRequest`. A
relative URL passed to fetch on an `/ultra/*` page is rewritten and comes back
as an S3 `NoSuchKey` XML error, which looks exactly like the endpoint not
existing. `goto_api()` parks the browser on a raw API URL first, where no SPA
bundle is loaded and both wrappers are absent.

Unlike Canvas, the REST write path does essentially no sanitising. `img`,
`iframe`, `table`, `pre`/`code`, `section`, `div`, and `id`/`class`/`data-*`
attributes all round-trip intact, so Quartz output needs far less rewriting
here than Canvas needs. Pasting into Blackboard's WYSIWYG is the opposite: it
rewrites everything, which is why the hand-built FA25 syllabus carries 135
`<span style="font-family: helvetica...">`.

Content written over REST arrives unstyled and renders at Blackboard's small
default. `transform()` wraps each document in a single
`<div style="font-size: 14pt;">`. Measured on a live page, `p` and `li` inside
that div compute to 18.67px, exactly the 14pt the hand-pasted FA25 syllabus
used. Size is set in `blackboard.json` as `font_size`.

font-family is deliberately not set. Blackboard's own CSS puts a family on
p/li/h2 with higher specificity, so an inherited one is ignored and text stays
Open Sans, its interface font. A `font_family` key still works but only affects
elements Blackboard does not style; matching FA25's helvetica would need inline
styles on every element, which is what makes that document 135 spans long.

Look items up by title, never by remembered id. `children()` matches on title so
a document moved or renamed in the UI is found rather than duplicated.

Font Awesome icons become text labels. The glyphs come from an `@font-face` in
`custom.scss` that only the Pages site loads, so an `<i>` reaches Blackboard
with no glyph and leaves an empty, unclickable link. Icons are a Pages-only
affordance.

Course availability and content availability are separate gates, and the outer
one is easy to miss. `--publish` only sets `availability.available` on the
documents; if the course shell itself is unavailable, students still see
nothing. There is no button for it on any content page. It lives at Control
Panel > Customization > Properties, a radio group named `available` with Yes /
No / Use Term Availability:

    /webapps/blackboard/execute/cp/courseProperties?dispatch=editProperties&family=cp_edit_properties&course_id=<id>

The course header reads "(Course is unavailable to students)" while it is off.
Check `availability.available` on the course via the API before concluding a
push failed to reach anyone.

Edit Mode is per user and per course, and hidden items do not appear in a
folder listing while it is off. A folder that looks empty after a successful
push usually means Edit Mode is off, not that the push failed.

## Reading PDFs and where they live

`script/blackboard files` uploads every gitignored PDF that a vault page links
to, attaches it as a file item in the Course Documents folder, and rewrites the
markdown link to the Blackboard URL. Run it after adding a reading, before
pushing pages.

The split is the copyright one. Own work such as the syllabus is committed and
served publicly from Pages, and a relative `pdf/...` link to it is correct.
Third-party scans stay out of git, so a relative link 404s for everyone; those
belong in Blackboard behind institutional login. `is_tracked()` decides which
is which, so the gitignore rule is the single control.

Rewriting the markdown rather than the pushed HTML is deliberate. One URL then
serves both outputs: Blackboard students get the reading, and a visitor to the
public Pages site gets Blackboard's login wall instead of a 404 or an exposed
scan. The vault stays the single source of truth.

The URL form is
`/webapps/blackboard/execute/displayIndividualContent?course_id=<c>&content_id=<i>`.
Unauthenticated it returns Blackboard's `error.jsp` with HTTP 200, not the
file, so a 200 from curl does not mean the scan is public; check the body.

Uploads go through curl, not the browser. Chrome refuses an HTTPS page's fetch
to `http://127.0.0.1` and hangs rather than erroring, even with the Private
Network Access opt-in headers, so a local file server does not work; and one
scan is 17MB, far past ARG_MAX for passing bytes through an eval argument.
`curl_config()` writes the session cookie and XSRF header to a 0600 temp file
and passes it with `--config`, keeping a live session off the command line
where ps would show it. Only Blackboard's own cookies are sent: the browser jar
also holds Watermark/EvaluationKIT cookies, and `agent-browser cookies get`
reports no domains to filter on.

Uploading is two steps. `POST /uploads` returns only an upload id, which is not
a link; attaching it to a content item with `resource/x-bb-file` is what puts
the bytes in Course Files and yields a durable URL. Course Files was empty in
both FA25 and FA26 before this: the previous arrangement served scans from the
public Pages site instead.

Never generate into the Assignments folder. Those items are
`resource/x-bb-assignment`, gradebook objects carrying student submissions, not
content. `blackboard.json` points only at the Syllabus folder.

## Keeping this file current

Update this file when a change invalidates something above, or when a new
non-obvious behaviour costs time to diagnose. Record the symptom alongside the
cause.

## Plugins installed from git

`quartz-image-zoom` (lightbox on click) is installed from
`github:vazome/quartz-image-zoom`, pinned in `.quartz/quartz.lock.json`.

Install with the bootstrap CLI, not npx:
`node ./quartz/bootstrap-cli.mjs plugin add github:<owner>/<repo>` from
`.quartz/`.

Git plugins install to `.quartz/.quartz/plugins/`, not `.quartz/plugins/`. The
loader joins `process.cwd()` with `.quartz/plugins` and the CLI already runs
from `.quartz/`, so the path doubles up. That directory is gitignored as a
cache, and `bootstrap-cli.mjs build` does not fetch missing plugins, so CI runs
`npm run install-plugins` before building. Dropping that step yields a green
build with the plugin silently missing.
