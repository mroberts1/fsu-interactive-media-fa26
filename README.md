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

Colours come from the letterpress (light) and cyanotype (dark) palettes of
[jzhao.xyz](https://jzhao.xyz/): navy ink and vermilion on warm paper, and
light blue and vermilion on prussian blue.

| Role      | Light     | Dark      |
| --------- | --------- | --------- |
| light     | `#f5eedd` | `#06182f` |
| lightgray | `#e3d9c0` | `#122845` |
| gray      | `#9a8e76` | `#7191b8` |
| darkgray  | `#2d4673` | `#caddf4` |
| dark      | `#16294e` | `#eef4fc` |
| secondary | `#284d78` | `#8fb9de` |
| tertiary  | `#c8482b` | `#e0552f` |

Type is Helvetica Neue throughout, matching the other course vaults and the
personal site. It is a system face rather than a webfont, so `fontOrigin` is
`local` and nothing is fetched at build time; the fallback stack for machines
without it lives in `.quartz/quartz/styles/custom.scss`.

Everything the YAML config can't express lives in that `custom.scss`: the
self-hosted font, a tighter heading scale, wrapped code blocks, a card grid for
folder listings, and an extra `> [!custom]` callout type using the vermilion
accent.

## Updating Quartz

`.quartz/` is vendored, not a clone, so `npx quartz update` is not available.
See the deployment section of [AGENTS.md](AGENTS.md) for the upgrade procedure
and the pinned upstream commit.
