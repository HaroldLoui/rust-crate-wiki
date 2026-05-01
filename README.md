# Rust Crate Wiki 📚

由 AI 自动维护的 Rust 第三方 crate 最新 API 知识库。

## 用途

每次查完一个 crate 的最新 API 文档后，整理成结构化文档存到这里。
下次再问同一个 crate 就不需要联网查了，直接读本地文件，零 token 开销、零幻觉。

## 给 Claude Code 使用

在项目的 `CLAUDE.md` 里加上：

```markdown
## 知识库
Rust crate API 参考位于项目根目录的 `wiki-rust/crates/`，
查 crate 用法时优先看这里的本地文件。
```

然后 clone 到项目目录下：

```bash
git clone https://github.com/HaroldLoui/rust-crate-wiki.git wiki-rust
```

## 目录结构

```
├── SCHEMA.md        # 格式规范
├── index.md          # 所有 crate 索引
├── log.md            # 更新日志
└── crates/
    ├── rand.md       # 随机数生成器 (v0.10.x)
    └── ...           # 持续添加中
```

## 更新

AI 自动维护，commit 后会 `git push` 同步到 GitHub。
你本地 `git pull` 即可获取最新内容。
