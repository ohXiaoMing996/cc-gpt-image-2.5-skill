# cc-gpt-image-2.5-skill

> GPT Image 2.5 文生图 / 图生图 skill。默认 `gpt-image-2.5-flare`，4K/高清/高质量自动切 `gpt-image-2.5-sunburst`。  
> **一个仓库，三端通用**：Codex · WorkBuddy · Claude Code。

## 给 agent 的安装指令

把下面任意一段贴给 **Codex / WorkBuddy / Claude Code**，agent 会自动 clone + 自配置依赖：

### Codex

```
git clone https://github.com/<your-account>/cc-gpt-image-2.5-skill ~/.codex/skills/cc-gpt-image-2.5-skill
```

### WorkBuddy

```
git clone https://github.com/<your-account>/cc-gpt-image-2.5-skill ~/.workbuddy/skills/cc-gpt-image-2.5-skill
```

### Claude Code

```
git clone https://github.com/<your-account>/cc-gpt-image-2.5-skill ~/.claude/skills/cc-gpt-image-2.5-skill
```

> 也可以让 agent 自己挑路径（比如项目级 `.claude/skills/` 或 `.workbuddy/skills/`），只要 SKILL.md 能被发现就行。

## 首次配置

装好 skill 后，第一次让 agent 画图会提示配置。把 API key 和 base_url 发给它即可：

```
我的 api_key 是 sk-xxx，base_url 是 https://xxx/v1
```

agent 会调用 `bin/cc_imagegen config` 把配置写进 `~/.cc-gpt-image/config.json`（JSONC，含注释，权限 0600，**永久生效**）。也可手动编辑该文件，或参照 `references/config.example.json` 模板。

## 用法

自然语言说就行，比如：

- 「画一张猫咪在窗边的图」→ 走 `gpt-image-2.5-flare`
- 「给我来一张 4K 高清产品主视觉」→ 自动切 `gpt-image-2.5-sunburst` + `quality=high`
- 「把这张图背景换成纯白摄影棚」+ 附图 → 走 `/images/edits`（图生图）
- 「2K 的就行」→ flare + `quality=medium`
- 「生成 4 张备选」→ `--batch 4`（先报单价）

## 质量词速查

| 关键词 | 模型 | quality |
|---|---|---|
| 默认 / 普通 | `gpt-image-2.5-flare` | — |
| 1k / low | flare | `low` |
| 2k / medium / standard | flare | `medium` |
| 4k / 高清 / 高质量 / 超清 / hd / hq / high | `gpt-image-2.5-sunburst` | `high` |

## 仓库结构

```
cc-gpt-image-2.5-skill/
├── SKILL.md                      # 三端通用的 skill 清单
├── README.md
├── CHANGELOG.md
├── LICENSE                       # MIT
├── .gitignore
├── agents/
│   └── openai.yaml               # Codex 界面元数据
├── bin/
│   └── cc_imagegen               # 入口薄壳：自检 python + 建 venv + exec 脚本
├── references/
│   └── config.example.json       # 配置模板（JSONC，含注释）
└── scripts/
    └── cc_imagegen.py            # 业务脚本（OpenAI 兼容 + AI Hive 双协议）
```

## 兼容性

| 平台 | SKILL.md 路径约定 | 状态 |
|---|---|---|
| Codex | `~/.codex/skills/<name>/SKILL.md` | ✅ 验证过 |
| WorkBuddy | `~/.workbuddy/skills/<name>/SKILL.md` | ✅ 验证过 |
| Claude Code | `~/.claude/skills/<name>/SKILL.md` | ✅ 路径规范相同 |

## License

MIT — 详见 [LICENSE](LICENSE)
