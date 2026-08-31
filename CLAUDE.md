# CLAUDE.md

## Goal

The goal of this repo is to write (redact) a `portfolio.yml` that is rendered with
[YAMLResume](https://yamlresume.dev/). **`portfolio.yml` is the single source of truth for all
CV- and portfolio-like content.**

This is a **portfolio**, not just a CV. Treat the portfolio as a superset of a CV: on top of the
usual CV sections it presents *real, working projects* that have been cloned, run and documented.
The rules below describe how a project earns its place in it.

## One-way synchronization

At all times, keep content flowing in one direction, from `portfolio.yml` into the two targets
under `content/`:

```
                 ┌─►  content/AleixMT/README.md      (GitHub profile README for AleixMT/AleixMT)
portfolio.yml ───┤
                 └─►  content/AleixMT.github.io/      (Jekyll portfolio website)
```

Rules:

- `portfolio.yml` is authored by hand. The two `content/` targets are **derived** from it.
- Never propagate changes the other way — nothing in `content/` feeds back into `portfolio.yml`.
- Whenever `portfolio.yml` changes, update **both** targets so they reflect it.
- Do not delete pre-existing material in a target that simply has no counterpart in
  `portfolio.yml` (e.g. `content/AleixMT.github.io/_posts/Projects/Academic_Projects/*Study-of-Artemia-sp*`)
  unless explicitly asked.
- On disk the paths are `content/AleixMT` and `content/AleixMT.github.io`; the user may also spell
  them lowercase (`content/aleixmt`, `content/aleixmt.github.io`).

## Repositories

- Root `portfolio` repo — tracks `portfolio.yml`, `compose.yml`, `README.md`. `content/` is
  gitignored here.
- `content/AleixMT/` — its own git repo, remote `AleixMT/AleixMT`; its `README.md` renders on the
  `AleixMT/AleixMT` GitHub profile.
- `content/AleixMT.github.io/` — its own git repo, remote `AleixMT/AleixMT.github.io`, branch
  `master`.

Commit inside whichever subdir you changed. Commit and push only when asked.

## Rendering portfolio.yml

```bash
docker compose run --rm --remove-orphans portfolio dev portfolio.yml
```

See `README.md` for details. Outputs (`portfolio.pdf`, `.html`, `.tex`, `.docx`, `.md`) land next
to `portfolio.yml` and are gitignored.

## Adding a new project to the portfolio

Follow these steps **in order**. Do not skip straight to editing `portfolio.yml`.

1. **Check that the project works.** Clone the repository into `projects/<name>/` (the `projects/`
   directory at the repo root is gitignored and exists only for this) and actually build/run it,
   following the project's own instructions (Docker, Make, language toolchain, etc.). Record what
   the entry points are, how it is run, what it depends on, and whether it currently works.
2. **Update that repo's `README.md`.** Rewrite it to follow the
   [Best-README-Template](https://github.com/othneildrew/Best-README-Template) structure — logo /
   title, badges, *About The Project*, *Built With*, *Getting Started* (*Prerequisites*,
   *Installation*), *Usage*, *Roadmap*, *Contributing*, *License*, *Contact*, *Acknowledgments*.
   Base the content on what step 1 revealed. This is a commit to the project's **own** repository
   (the clone under `projects/`); commit and push it only when asked.
3. **Update the portfolio.** Using the knowledge from running the project (step 1), the rewritten
   README (step 2) and a read of the repo contents, add/update the project in all three, one-way
   from `portfolio.yml` outward — see [How to sync the project section](#how-to-sync-the-project-section):
   - `portfolio.yml`
   - `content/AleixMT/README.md`
   - `content/AleixMT.github.io`
4. **Set the repo metadata on GitHub.** For every repository parsed this way, make sure its
   GitHub repository has:
   - the topic **`portfolio`** — via the REST API (`PUT /repos/<owner>/<repo>/topics` with the
     *full* topics list) or the repo's *About → Topics* panel. `AleixMT/AleixMT` already has it.
   - a **description** in the *About* panel — a one-line summary of the project, kept consistent
     with the `description` field of the project in `portfolio.yml`. Set it via the REST API
     (`PATCH /repos/<owner>/<repo>` with `{"description": "..."}`) or the *About* panel.

   `gh` is not installed here: use the REST API with the user's auth, or ask the user to make
   these changes via the repo's *About* panel.

## How to sync the project section

`portfolio.yml` projects live under `content.projects:` (before `content.skills:`), each with
`name`, `url`, `description`, `startDate`, `endDate`, `summary: |` (bullets) and `keywords`.

### content/AleixMT/README.md

Add/update a `<details>` block inside the "My projects" `<details>`, just before its closing
`<br></details>`. Match the existing block style: `<summary>` with the name, centered `Repository`
(and optional `Web page`) links, `<i>Month Year - Present</i>`, a `<ul>` of bullets, and a
`Used technologies:` `<h5>` with `.github/img/*` icon links. Comment out the screenshot `<p>` when
there is no image (see the `github-backup` block).

### content/AleixMT.github.io

One Jekyll post per project at
`_posts/Projects/{Job_Projects,Personal_Projects,Academic_Projects}/2023-07-04-<Name>.md`.

Front matter:

```yaml
layout: default
title: <Name>
permalink: /Projects/<Job|Personal|Academic>-Projects/<slug>
parent: <Job|Personal|Academic> Projects
grand_parent: Projects
nav_order: <next free integer under that parent>
categories: projects
tags: projects
```

Body: one short prose paragraph (from the `summary`) followed by a trailing `[Repository](url)`
line.

Categorisation: ICIQ / paid work → **Job Projects**; hobby / open-source → **Personal Projects**;
built for a university course → **Academic Projects**.

## Other sections

`portfolio.yml` also carries `basics`, `work`, `education`, `skills`, `languages`, `awards`,
`certificates`, `publications`, `volunteer` and `interests`. `content/AleixMT.github.io` has
matching stub pages under `_posts/` that are **not yet in sync**; extend them the same one-way way
when asked.
