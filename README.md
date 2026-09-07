## History of Interactive Media & Games

Course site for COMM 2003, History of Interactive Media & Games, Fitchburg
State University. An Obsidian vault published as a static site with Quartz 5.

Live at https://mroberts1.github.io/fsu-interactive-media-fa26/

Reading PDFs are gitignored, so the links to them 404 on the published site.

Converted from the Quarto site at
`/Volumes/Projects/Quarto/fsu-interactive-media-fa25`.

```
./           Obsidian vault root, which is what Obsidian opens
  content/   what the site is built from. Notes and their assets go here
  .quartz/   the Quartz 5 install, hidden from Obsidian
  public/    build output, gitignored
```

Notes at the vault root are drafts and reference material. Only what is under
`content/` is published.

## Pages

| Page                    | Source in the Quarto site        |
| ----------------------- | -------------------------------- |
| `index.md`              | `index.qmd`, front matter through assignments |
| `schedule.md`           | `index.qmd`, the Schedule section |
| `policies.md`           | `index.qmd`, the Policies section |
| `panels.md`             | `groups.qmd`                     |
| `agendas/w1-intro.md`   | `w1-intro.qmd`                   |

PDFs live in `content/pdf/` and images in `content/img/`, both linked relative
to the content root.

## Working on it

```
./dev.sh
```

Then open http://localhost:8080. `./build.sh` writes the static site to
`public/` without serving it. Both scripts work from any directory. If a port is
already taken, override it: `PORT=8081 WS_PORT=3004 ./dev.sh`.

Pushing to `main` deploys; there is nothing to build locally first.

How to publish a reading, push to Blackboard, and make things visible to
students is in [PUBLISHING.md](PUBLISHING.md).

Agent-facing notes, including the gotchas worth reading before debugging
anything, are in [AGENTS.md](AGENTS.md).

## The theme

Carried over unchanged from the `lang-media-arts` vault this one was cloned
from.

| Role      | Light     | Dark      |
| --------- | --------- | --------- |
| light     | `#e2e2e2` | `#191919` |
| lightgray | `#4e4e4e` | `#393639` |
| gray      | `#4e4e4e` | `#e2e2e2` |
| darkgray  | `#4e4e4e` | `#e2e2e2` |
| dark      | `#4e4e4e` | `#ebebec` |
| secondary | `#4e4e4e` | `#7c7c7c` |
| tertiary  | `#c0ffe1` | `#c0ffe1` |

Type is Departure Mono for headers, Roboto Mono for body, Ubuntu Mono for code.
Departure Mono is not on Google Fonts, so it is self-hosted from
`.quartz/quartz/static/fonts/`. Quartz still asks Google for it and logs
`Failed to fetch font Departure Mono with weight 700` on every build. That
warning is expected and harmless; the `@font-face` in
`.quartz/quartz/styles/custom.scss` is what actually loads the font.

Everything the YAML config can't express lives in that `custom.scss`: the
self-hosted font, a tighter heading scale, wrapped code blocks, a card grid for
folder listings, and an extra `> [!custom]` callout type using the mint accent.

## Updating Quartz

`.quartz/` is vendored, not a clone, so `npx quartz update` is not available.
See the deployment section of [AGENTS.md](AGENTS.md) for the upgrade procedure
and the pinned upstream commit.
