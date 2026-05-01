# Wiki Schema

## Domain
Rust crate API 文档 — 常用 crate 的最新 API 签名、用法示例、注意事项。

## Conventions
- 文件名: 小写，用 crate 名（如 `tokio.md`, `rand.md`）
- 每个文件包含: 版本号、核心 API 签名、示例代码、注意事项
- 版本更新时覆盖旧内容，在 log.md 记录更新

## Frontmatter
```yaml
---
crate: rand
version: 0.9.x
updated: 2026-05-01
source: https://docs.rs/rand/latest/rand/
---
```
