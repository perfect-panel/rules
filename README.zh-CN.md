[English](README.md) | [中文文档 / Chinese](README.zh-CN.md)

# 规则镜像与同步

本仓库通过 GitHub Actions **定时同步**上游项目的规则与地理库文件，并在本地镜像到指定目录。

> ✅ **README 安全** — 工作流会保留 `README.md` 与 `README.zh-CN.md`。  
> ⚠️ **重要提示** — 同步目录使用 `--delete` 覆盖镜像，请勿在这些目录内存放自定义文件。

---

## 上游来源

- **Blackmatrix7 / ios_rule_script**  
  目录：`blank/`、`external/`、`rewrite/`、`rule/`、`script/`  
  <https://github.com/blackmatrix7/ios_rule_script>

- **MetaCubeX / meta-rules-dat**（分支：`sing`）  
  目录：`geo/`（镜像到本仓库 `geo/`）  
  <https://github.com/MetaCubeX/meta-rules-dat>

- **VirgilClyne / GetSomeFries**  
  目录：`ruleset/`  
  <https://github.com/VirgilClyne/GetSomeFries>

---

## 仓库结构

```
.
├── .github/workflows/sync-rules.yml   # 同步工作流
├── README.md                          # 英文文档
├── README.zh-CN.md                    # 中文文档（本文件）
├── blank/                             # 来自 ios_rule_script
├── external/                          # 来自 ios_rule_script
├── rewrite/                           # 来自 ios_rule_script
├── rule/                              # 来自 ios_rule_script
├── script/                            # 来自 ios_rule_script
├── geo/                               # 来自 meta-rules-dat (sing/geo)
└── ruleset/                           # 来自 GetSomeFries
```

上述目录均以 **镜像方式** 同步（`rsync -a --delete`），内容始终与上游一致。

---

## 使用 CDN（cdn.jsdmirror.com）

使用 **jsDelivr 镜像域名** 快速分发静态文件：

```
https://cdn.jsdmirror.com/gh/<OWNER>/<REPO>@<REF>/<PATH-TO-FILE>
```

- `<REF>` 可为 **分支**（如 `main`）、**标签**（如 `v1.2.3`）或 **提交 SHA**。  
- 生产环境建议固定到 **标签/提交**，避免分支缓存延迟。

**示例**（自行替换占位符）：

- `ruleset/` 目录文件：
  ```
  https://cdn.jsdmirror.com/gh/<OWNER>/<REPO>@main/ruleset/Example.list
  ```
- `rule/` 目录文件：
  ```
  https://cdn.jsdmirror.com/gh/<OWNER>/<REPO>@main/rule/AdBlock/AdBlock.list
  ```
- `geo/` 目录文件：
  ```
  https://cdn.jsdmirror.com/gh/<OWNER>/<REPO>@main/geo/geosite.dat
  https://cdn.jsdmirror.com/gh/<OWNER>/<REPO>@main/geo/geoip.dat
  ```

> **缓存提示：** CDN 会缓存资源。需要立即生效时，请使用不可变的标签/提交引用，避免依赖分支最新指针。

---

## 工作流说明

- 工作流位于 `.github/workflows/sync-rules.yml`。
- 触发方式：定时、手动、以及工作流文件变更时自动触发。
- 策略：创建临时分支 → 清理但保留 README → 稀疏检出上游 → `rsync` 镜像 → 尝试 FF 合并。

---

## 自定义

- **保留更多根目录文件**：在清理步骤追加排除条件（如 `LICENSE`）。  
- **调整定时**：修改 `on.schedule` 的 cron。  
- **增删上游目录**：调整 sparse-checkout 列表与 `rsync` 复制块。  
- **固定分支/提交**：在 `actions/checkout` 的 `ref:` 指定 tag 或 commit。

---

## 常见问题

- **README 会被删吗？**不会，`README.md` 与 `README.zh-CN.md` 会被保留。  
- **自定义文件不见了？**多半放进了镜像目录（`blank/`、`external/`、`rewrite/`、`rule/`、`script/`、`geo/`、`ruleset/`），这些目录使用 `--delete` 同步。  
- **如何立刻触发同步？**Actions → Sync Rules → Run workflow。

---

## 许可

- 规则与地理库内容归各上游项目所有。  
- 本仓库工作流与文档（除另有说明）以 **MIT License** 提供。

---
