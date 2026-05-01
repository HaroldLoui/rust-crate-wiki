
# Rust Crate Wiki 维护指南

## 概述

负责把 Rust crate 的最新 API 文档从 docs.rs 抓取下来，整理成结构化 markdown 文件，
存入 `~/wiki-rust/` 并推送到 GitHub。

## 注意

- **docs.rs 从国内访问可能很慢**，每个页面可能几秒到十几秒。大 crate（如 ratatui）有几十个 API 页面，串行抓取可能超时。建议先 `curl` 主页获取模块列表，**只抓核心模块**，不需要每个页面都抓。
- 如果超时了，检查 `~/wiki-rust/crates/` 下是否已经有写好的文件，有的话手动 commit 即可。
- git 配置已在 `~/wiki-rust/.git/config` 中设好，直接 `git add -A && git commit -m "..."` 即可。

## 前置条件

- `~/wiki-rust/` 目录已存在（Git 仓库，关联 GitHub）
- 目录下有 `.git/hooks/post-commit` 自动推送脚本
- 环境变量 `TENCENT_CODING_TOKEN` 可选（仅周报用，wiki 不需要）

## 目录结构

```
~/wiki-rust/
├── SCHEMA.md        # 格式规范（必须遵守）
├── index.md         # 所有 crate 索引（更新时必须修改）
├── log.md           # 操作日志（更新时必须追加）
├── README.md        # 使用说明
└── crates/
    ├── rand.md      # 示例文件，可参考格式
    └── ...
```

## 抓取文档步骤

### 1. 确定版本

```bash
curl -sL "https://crates.io/api/v1/crates/CRATE_NAME" | python3 -c "import json,sys; d=json.load(sys.stdin); print(d['crate']['max_stable_version'])"
```

### 2. 抓取 API 列表

用 docs.rs 的 HTML 页面提取核心 API：

```bash
curl -sL "https://docs.rs/CRATE_NAME/latest/CRATE_NAME/index.html"
```

解析方式：用 Python 正则提取 `<section>` 里的 `<a>` 标签，按类型分组（struct/trait/fn/mod/macro）。

### 3. 抓取关键 API 签名

对核心 trait 和 struct，抓取详情页：

```bash
curl -sL "https://docs.rs/CRATE_NAME/latest/CRATE_NAME/trait.TRAIT_NAME.html"
```

### 4. 写入 wiki 文件

在 `~/wiki-rust/crates/CRATE_NAME.md` 创建文件。参考 `rand.md` 的格式。

### 5. 更新索引和日志

- 在 `~/wiki-rust/index.md` 的 `## Crates` 小节追加一行
- 在 `~/wiki-rust/log.md` 追加一条记录

格式参考：

```markdown
## [YYYY-MM-DD] ingest | xxx crate 文档
- 从 docs.rs 获取 xxx x.x.x 最新 API
- 写入 crates/xxx.md
- 更新 index.md
```

### 6. 提交并推送

```bash
cd ~/wiki-rust
git add -A
git commit -m "📝 添加 xxx crate API 文档 (vx.x.x)"
# post-commit hook 会自动 push
```

等待 2 秒确认推送成功：

```bash
sleep 2 && git log --oneline -3
```

## 文件格式规范

每个 crate 文件必须包含：

```markdown
---
crate: CRATE_NAME
version: x.x.x
updated: YYYY-MM-DD
source: https://docs.rs/CRATE_NAME/latest/CRATE_NAME/
---

# CRATE_NAME — 简短描述

## 快速开始

```rust
// 最常用的 3-5 行示例
```

## 核心 Trait/Struct

| 名称 | 说明 |
|------|------|

## 核心函数

## 模块

## 关键示例（3-5 个）

## 注意事项（版本变更、坑等）
```

## 已有的 crate

查看当前已有：

```bash
ls ~/wiki-rust/crates/
cat ~/wiki-rust/index.md
```

## 验证

推送后打开 GitHub 确认：

```bash
curl -s "https://api.github.com/repos/HaroldLoui/rust-crate-wiki/contents/crates" | python3 -c "import json,sys; [print(x['name']) for x in json.load(sys.stdin)]"
```
