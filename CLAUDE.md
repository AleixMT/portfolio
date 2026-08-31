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
  `portfolio.yml` (e.g. `content/AleixMT.github.io/_posts/Organizations/Personal_Projects_AleixMT/*Study-of-Artemia-sp*`)
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

> **⚠️ Step 4 (GitHub repo metadata) is mandatory and is the one most often forgotten.**
> Adding a project is **not done** until the repo has the `portfolio` topic *and* a description on
> GitHub. If you cannot reach the GitHub API, stop and tell the user it is still pending — do not
> silently finish without it.

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
4. **Set the repo metadata on GitHub. — REQUIRED, DO NOT SKIP.** For every repository parsed this
   way (including this run: whatever project the user just asked to add), make sure its GitHub
   repository has:
   - the topic **`portfolio`** — via the REST API (`PUT /repos/<owner>/<repo>/topics` with the
     *full* topics list) or the repo's *About → Topics* panel. `AleixMT/AleixMT` already has it.
   - a **description** in the *About* panel — a one-line summary of the project, kept consistent
     with the `description` field of the project in `portfolio.yml`. Set it via the REST API
     (`PATCH /repos/<owner>/<repo>` with `{"description": "..."}`) or the *About* panel.

   `gh` is not installed here: use the REST API with the user's auth (a GitHub token is available
   through git's credential helper — `printf 'protocol=https\nhost=github.com\n\n' | git credential
   fill`), or ask the user to make these changes via the repo's *About* panel.

**Before reporting the project as added, confirm all four:** ☐ `portfolio.yml` ☐
`content/AleixMT/README.md` ☐ `content/AleixMT.github.io` ☐ GitHub topic `portfolio` + description.

## How to sync the project section

`portfolio.yml` projects live under `content.projects:` (before `content.skills:`), each with
`name`, `url`, `description`, `startDate`, `endDate`, `summary: |` (bullets) and `keywords`.

Both targets group projects by the **GitHub organization that owns the repo**:

- `ICIQ-DMP/*` → **ICIQ-DMP**
- `Gua-tk/*` → **Gua-tk software**
- `URV-BioGEI/*` → **URV-BioGEI**
- `vidwise/*` → **VidWise**
- a repo under the personal `AleixMT` account, and university / coursework projects with no
  GitHub org of their own → **Personal projects (AleixMT)**

(`ASBTEC` and `Equipaments Hosteleria Salou` also exist as organizations but currently have no
projects.)

### content/AleixMT/README.md

Projects live inside the "🏘 My organizations" `<details>`, nested under their organization's
`<details>`. Add/update a project `<details>` as the last child of the owning organization's
block, just before that organization's trailing `<br><br>`. Match the existing block style:
`<summary>` with the name, centered `Repository` (and optional `Web page`) links,
`<i>Month Year - Present</i>`, a `<ul>` of bullets, and a `Used technologies:` `<h5>` with
`.github/img/*` icon links. Comment out the screenshot `<p>` when there is no image (see the
`github-backup` block). If the owning organization has no block yet, add one first (centered
`github.com/<org>` link, short `<ul>` blurb) in the right nav_order slot.

### content/AleixMT.github.io

The site nav is **Organizations › organization › project** (just-the-docs). A top-level
`_posts/2023-07-05-Organizations.md` page (`nav_order: 3`, `has_children: true`) has one child
page per organization at `_posts/Organizations/2023-07-03-<Org>.md`; each project is a page under
its organization at `_posts/Organizations/<Org_dir>/2023-07-04-<Name>.md`.

Project page front matter:

```yaml
layout: default
title: <Name>
permalink: /Organizations/<Org-slug>/<slug>
parent: <Org name>
grand_parent: Organizations
nav_order: <next free integer under that organization>
categories: projects
tags: projects
```

Body: one short prose paragraph (from the `summary`) followed by a trailing `[Repository](url)`
line.

If the owning organization has no page yet, add `_posts/Organizations/2023-07-03-<Org>.md`
(`parent: Organizations`, no `grand_parent`, `has_children: true`, `categories`/`tags:
Organizations`, next free top-level `nav_order`) and create its `_posts/Organizations/<Org_dir>/`
folder. Current org folders: `ICIQ-DMP/`, `Gua-tk_software/`, `URV-BioGEI/`, `VidWise/`,
`Personal_Projects_AleixMT/`.

## Other sections

`portfolio.yml` also carries `basics`, `work`, `education`, `skills`, `languages`, `awards`,
`certificates`, `publications`, `volunteer` and `interests`. `content/AleixMT.github.io` has
matching stub pages under `_posts/` that are **not yet in sync**; extend them the same one-way way
when asked.
