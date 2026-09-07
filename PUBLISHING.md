# Publishing this course

How to get things from this vault onto the web and into Blackboard. Written for
you, not for an agent. The technical reasoning lives in `AGENTS.md`.

This file sits at the vault root, so it is never published. Only `content/` is.

## The three outputs

| Output     | What it is                        | How it updates                  |
| ---------- | --------------------------------- | ------------------------------- |
| Pages site | https://mroberts1.github.io/fsu-interactive-media-fa26/ | `git push`, then wait ~40s |
| Blackboard | the FA26 course shell             | `script/blackboard all --publish` |
| PDF        | a printable syllabus              | `script/pdf`                    |

The vault is the source. All three are outputs. Editing a page inside
Blackboard works until the next push overwrites it, so make changes here.

## Log in first

Blackboard needs a browser session, and it has to be agent-browser's own
browser, not your everyday Chrome. Logging into Chrome does nothing for it.

    agent-browser open --headed https://blackboard.fitchburgstate.edu

Sessions last 8 hours, so this is roughly once a working day, not once per
push. If a push says "no Blackboard session found", this is why.

## Adding a reading

1. Put the PDF in `content/pdf/`, lowercase and hyphenated, no spaces.
2. Link it from a page as `[Title](pdf/the-file.pdf)`.
3. Run `script/blackboard files`.
4. Run `script/blackboard all --publish`.
5. Commit and push.

Step 3 is the one that is easy to forget. It decides where the PDF lives:

- If the PDF is tracked by git, it publishes to the Pages site and the link
  stays as it is. Use this for your own work.
- If the PDF is gitignored, which is the default because `.gitignore` has a
  blanket `*.pdf`, it gets uploaded to Blackboard and the link in your markdown
  is rewritten to a Blackboard URL.

That second case is what you want for scanned book chapters and articles.
Students reach them through Blackboard; anyone else hits Fitchburg's login
page. Nothing copyrighted ends up on GitHub.

If you skip step 3, the link points at a file that was never published and
students get a 404, on the site and in Blackboard both.

To publish a PDF openly instead, add an exception to `.gitignore`, the way
`nick-montfort-adventure.pdf` and `nick-montfort-zork.pdf` are handled.

## Making things visible to students

Two separate switches, and both must be on. This is the thing that wasted an
afternoon.

Course availability. Whether the shell is open at all. Not on any content page:
Control Panel, then Customization, then Properties. Currently set to Use Term
Availability, so it follows the Fall 2026 dates, 3 September to 22 December.

Content availability. Whether each document is visible. That is what
`--publish` sets. Without it documents are created hidden, which is useful for
checking formatting first.

A page can opt out permanently with `"publish": false` in `blackboard.json`.
Panels is set that way, because panels are cut for Fall 2026.

## Things that look broken but are not

A folder that looks empty in Blackboard after a successful push usually means
Edit Mode is off. Hidden items do not appear in listings.

A push that says "created" for something that already exists means the title
did not match. Avoid ampersands and other punctuation in document titles; that
is why the syllabus is "History of Interactive Media and Games".

If a push stops with "duplicate titles in the target folder", two items share a
name and it refuses to guess which one students are reading. Delete the extra
in Blackboard, then push again. `script/blackboard delete --ids <id>` can do it
if you have the id from the error.

## Commands

    script/blackboard files              upload and relink gitignored PDFs
    script/blackboard all                push everything, leave it hidden
    script/blackboard all --publish      push and make visible
    script/blackboard pages --print      build the HTML locally, push nothing
    script/blackboard delete --ids X Y   remove content items by id
    script/pdf                           regenerate the syllabus PDF
    ./dev.sh                             preview the site at localhost:8080
