# portfolio

Single source of truth for Aleix Mariné-Tena's CV / portfolio.

The whole résumé lives in [`portfolio.yml`](portfolio.yml) and is rendered to
PDF, HTML, DOCX and Markdown with [YAMLResume](https://yamlresume.dev/), run
through Docker so nothing has to be installed on the host.

## Layout

| Path             | Purpose                                                              |
| ---------------- | ------------------------------------------------------------------- |
| `portfolio.yml`  | The résumé content and the output layouts (HTML, LaTeX, DOCX, Markdown). |
| `compose.yml`    | Docker Compose service that runs the `yamlresume/yamlresume` image. |
| `.env`           | `UID` / `GID` so files written into the bind mount are owned by you. |
| `content/`       | Source material the `portfolio.yml` was built from (LaTeX CV, LinkedIn export, GitHub profile). Ignored by git. |

Generated artefacts (`portfolio.pdf`, `portfolio.html`, `portfolio.tex`,
`portfolio.docx`, `portfolio.md`, and the LaTeX aux files) are produced next to
`portfolio.yml` and are git-ignored.

## Requirements

- Docker
- Docker Compose v2 (`docker compose`, not `docker-compose`)

No local Node.js, LaTeX or fonts needed — everything runs inside the
`yamlresume/yamlresume` image.

## Usage

### Generate the PDF (and every other format)

```bash
docker compose run --rm --remove-orphans portfolio dev portfolio.yml
```

This mounts the working directory into the container, renders `portfolio.yml`
and writes `portfolio.pdf` alongside it (plus `portfolio.html`,
`portfolio.tex`, `portfolio.docx` and `portfolio.md`, per the `layouts:` block
at the bottom of `portfolio.yml`). `--rm` removes the container on exit and
`--remove-orphans` cleans up any stale containers from an earlier run. See
[Development mode](#development-mode) for the watch behaviour of `dev`.

### Development mode

```bash
docker compose run --rm --remove-orphans portfolio dev portfolio.yml
```

Enters development mode: renders `portfolio.yml` and then keeps watching it,
re-rendering on every save. Press `Ctrl+C` to stop.

### Using the default Compose command

`compose.yml` already sets the command to `dev portfolio.yml`, so the watcher
can also be started with:

```bash
docker compose up
```

## Editing

Edit `portfolio.yml`. The schema is documented at
<https://yamlresume.dev/docs/compiler/schema>; the
`# yaml-language-server` line at the top of the file wires up autocomplete and
validation in editors that support the YAML Language Server.
