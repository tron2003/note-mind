# NoteMind

A local-first desktop app for markdown notes. Edit notes in an Obsidian-style
editor, and NoteMind keeps a search index beside them so you can:

- **search by meaning**: hybrid keyword and semantic search finds notes
  phrased differently from your query;
- **ask questions** about your notes and get answers that cite the exact note
  and line they came from;
- **see related notes and links you haven't made yet**, and insert a link
  in one click.

Your vault stays a plain folder of `.md` files. An existing Obsidian vault
works as-is. The index is derived data kept separately and can always be
rebuilt. Nothing leaves your computer unless you configure a remote model,
and Settings tells you when that is the case.

Windows · macOS · Linux  |  [User guide](docs/USER_GUIDE.md) · [Changelog](CHANGELOG.md) · [Security](SECURITY.md)

---

## Install

Download the installer for your platform from the
[Releases](../../releases) page:

| Platform | File |
|---|---|
| Windows 10/11 (x64) | `NoteMind-<version>-win-x64.exe` |
| macOS (Apple silicon) | `NoteMind-<version>-mac-arm64.dmg` |
| macOS (Intel) | `NoteMind-<version>-mac-x64.dmg` |
| Linux (x64) | `NoteMind-<version>-linux-x86_64.AppImage` or `.deb` |

Everything is bundled, including Python. NoteMind updates itself from GitHub
Releases and asks before restarting.

Then open a vault folder, and see [Connect a model](docs/USER_GUIDE.md#connect-a-model)
to enable questions. Search and related notes work with no setup at all.

---

## Develop

Prerequisites: **Python 3.11+** and **Node 20+**.

```bash
git clone <your fork> notemind && cd notemind

python -m venv .venv
# Windows: .venv\Scripts\activate     macOS/Linux: source .venv/bin/activate
pip install --require-hashes --no-deps -r backend/requirements.lock
pip install -r requirements-dev.txt

npm ci
npm start          # builds the editor bundle, launches Electron + the backend
```

Electron starts the Python backend itself, preferring `.venv` in the project.
Install `pre-commit install` to lint on every commit.

| Command | What it does |
|---|---|
| `npm start` | Run the app from source |
| `npm run check` | Everything CI runs: lint, types, unit, integration, e2e |
| `npm run lint` / `npm run typecheck` | ruff + ESLint / mypy |
| `npm test` | Python tests with coverage gate, then renderer and supervisor tests |
| `npm run test:e2e` | Drive the real app with Playwright against a fake model server |
| `npm run audit` | npm audit + pip-audit of the hashed lock |
| `npm run dist` | Freeze the backend and build installers for this OS into `release/` |
| `npm run lock:py` | Regenerate `backend/requirements.lock` after changing requirements |

Tests need no API key, model or network: model endpoints are faked and
everything else (vault, SQLite, chunker, retriever, HTTP, Electron) is real.

### Layout

```
backend/            Python sidecar (FastAPI): vault, indexer, search, RAG, providers
electron/           main process: window, menu, sidecar supervisor, auto-update
renderer/           UI: editor (CodeMirror 6), tree, preview, graph, dialogs
tests/              pytest (backend), tests/js (node:test), tests/e2e (Playwright)
packaging/          PyInstaller spec for the frozen backend
scripts/            backend build + self-test, version consistency check
build-resources/    icons, macOS entitlements
docs/               PRD, architecture, ADRs, threat model, test plan, guides
.github/workflows/  CI, release, CodeQL
```

### Documentation

| For | Read |
|---|---|
| Using the app | [docs/USER_GUIDE.md](docs/USER_GUIDE.md) |
| What it must do | [docs/PRD.md](docs/PRD.md), traced to code and tests in [docs/TRACEABILITY.md](docs/TRACEABILITY.md) |
| How it works | [docs/ARCHITECTURE.md](docs/ARCHITECTURE.md) and the decisions in [docs/adr/](docs/adr/) |
| How it is tested | [docs/TEST_PLAN.md](docs/TEST_PLAN.md) |
| Security | [SECURITY.md](SECURITY.md), [docs/THREAT_MODEL.md](docs/THREAT_MODEL.md) |
| Contributing | [CONTRIBUTING.md](CONTRIBUTING.md) |
| Shipping a release | [RELEASING.md](RELEASING.md) |

## License

[MIT](LICENSE)
