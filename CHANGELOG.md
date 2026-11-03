# CHANGELOG

## 1.0.0 — 2026-11-03

- 基于 `gpt-image-2` 裸接口脚本改造，独立为 `cc-gpt-image-2.5-skill`。
- 默认 `provider=openai`、`model=gpt-image-2.5-flare`；用户输入 4K/高清/高质量/超清/hd/hq 时自动切 `gpt-image-2.5-sunburst` 并附 `quality=high`。
- 新增 `--quality` 质量词映射（`1k`/`2k`/`4k`/`hd`/`hq`/`高清`/`超清`/`low`/`medium`/`high`/`auto`）。
- 新增 `--model` 别名（`flare`/`sunburst`/`fast`/`hq`/`default`/`hd`）。
- 配置文件改为 `~/.cc-gpt-image/config.json`，支持 JSONC 注释；`config` 子命令非交互写盘并保留注释头。
- 缺 `api_key`/`base_url` 时输出可直接回显的引导（不交互、不开浏览器）。
- 同步发行 Codex 与 WorkBuddy 两份 SKILL.md。
