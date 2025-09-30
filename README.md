[English](README.md) | [中文文档 / Chinese](README.zh-CN.md)

# Rules Mirror & Sync

This repository mirrors and keeps in sync a curated set of rule files and geo databases from upstream projects, using a scheduled GitHub Actions workflow.

> ✅ **Safe for README** — The workflow keeps both `README.md` and `README.zh-CN.md` intact.  
> ⚠️ **Heads-up** — Synced folders are mirrored with `--delete`. Don’t put custom files inside them.

---

## Upstreams

- **Blackmatrix7 / ios_rule_script**  
  Folders: `blank/`, `external/`, `rewrite/`, `rule/`, `script/`  
  <https://github.com/blackmatrix7/ios_rule_script>

- **MetaCubeX / meta-rules-dat** (branch: `sing`)  
  Folder: `geo/` (mirrored into repo `geo/`)  
  <https://github.com/MetaCubeX/meta-rules-dat>

- **VirgilClyne / GetSomeFries**  
  Folder: `ruleset/`  
  <https://github.com/VirgilClyne/GetSomeFries>

---

## Repository Layout

```
.
├── .github/workflows/sync-rules.yml   # The sync workflow
├── README.md                          # English docs (this file)
├── README.zh-CN.md                    # Chinese docs
├── blank/                             # from ios_rule_script
├── external/                          # from ios_rule_script
├── rewrite/                           # from ios_rule_script
├── rule/                              # from ios_rule_script
├── script/                            # from ios_rule_script
├── geo/                               # from meta-rules-dat (sing/geo)
└── ruleset/                           # from GetSomeFries
```

All synced folders are **mirrored** using `rsync -a --delete`, so their contents always match upstream.

---

## How it works

- The workflow file is at `.github/workflows/sync-rules.yml`.
- Triggers:
  - **Schedule**: daily at `00:00` UTC (`0 0 * * *`)
  - **Manual**: via “Run workflow”
  - **Push**: when the workflow file changes
- Strategy:
  1. Create a temporary branch.
  2. Clean the repo root **but keep** `.git`, `.github`, `README.md`, `README.zh-CN.md`.
  3. Sparse checkout upstreams and mirror selected folders into this repo.
  4. Commit, push temp branch, and attempt a fast-forward merge back to the target branch.
     - If branch protection prevents it, the temp branch stays for manual review.

> Note: The job uses `rsync -a --delete` for each mapped directory to ensure a clean mirror.

---

## CDN usage (cdn.jsdmirror.com)

Use the **jsDelivr mirror domain** for fast static delivery:

```
https://cdn.jsdmirror.com/gh/<OWNER>/<REPO>@<REF>/<PATH-TO-FILE>
```

- `<REF>` can be a **branch** (`main`), a **tag** (`v1.2.3`), or a **commit SHA**.  
- For reliability and cache stability, prefer **tags/commits** in production.

**Examples** (replace placeholders):

- A ruleset file from `ruleset/`:
  ```
  https://cdn.jsdmirror.com/gh/<OWNER>/<REPO>@main/ruleset/Example.list
  ```
- A rule from `rule/`:
  ```
  https://cdn.jsdmirror.com/gh/<OWNER>/<REPO>@main/rule/AdBlock/AdBlock.list
  ```
- Geo data from `geo/`:
  ```
  https://cdn.jsdmirror.com/gh/<OWNER>/<REPO>@main/geo/geosite.dat
  https://cdn.jsdmirror.com/gh/<OWNER>/<REPO>@main/geo/geoip.dat
  ```

> **Caching note:** CDNs cache aggressively. If you need instant propagation, reference immutable tags/commits. Avoid relying on branch tips for time‑critical updates.

---

## Customization

- **Keep more root files**  
  If you want to preserve additional files (e.g., `LICENSE`, `CONTRIBUTING.md`), extend the exclusion list in the cleanup step:
  ```bash
  ! -name 'README.md' ! -name 'README.zh-CN.md' ! -name 'LICENSE' ...
  ```

- **Change schedule**  
  Edit the cron under `on.schedule`.

- **Add or remove upstream folders**  
  Adjust the sparse-checkout lists and the `rsync` blocks accordingly.

- **Pin to specific branches/commits**  
  In each `actions/checkout` step, set `ref:` to a tag/commit instead of a branch.

---

## FAQ

**Q: Will my README files be deleted?**  
A: No. Both `README.md` and `README.zh-CN.md` are explicitly preserved by the workflow.

**Q: My custom file disappeared. Why?**  
A: It was likely inside a mirrored directory (`blank/`, `external/`, `rewrite/`, `rule/`, `script/`, `geo/`, `ruleset/`). These are synced with `--delete`. Put custom assets outside these folders.

**Q: How do I trigger a sync now?**  
A: Go to **Actions → Sync Rules → Run workflow**.

---

## Acknowledgements & Licenses

- All rule content and geo databases are the property of their respective upstreams.  
- This repository’s workflow and documentation are provided under the **MIT License** (unless noted otherwise).  
- Please check each upstream project for their specific licenses and usage terms.

---

## Contributing

Issues and PRs are welcome for workflow improvements or documentation fixes. For rule content changes, please open issues/PRs upstream.

---
