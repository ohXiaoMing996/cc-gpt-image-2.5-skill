---
name: cc-gpt-image-2.5-skill
description: "GPT Image 2.5 图片生成与编辑：用户说「画一张」「生成一张」「做一张图」「给我来张」「帮我画」「文生图」「图生图」「图片编辑」「改这张图」「换背景」「换风格」「电商主图」「商品详情页」「广告KV」「封面图」「4K 高清图」「超清图」「按参考图出一张」时调用。模型默认 gpt-image-2.5-flare；用户明确要 4K / 高清 / 高质量 / 超清时自动切 gpt-image-2.5-sunburst。也接受 GPT Image 2.5、gpt-image-2.5、flare、sunburst、4K、hd、hq 等关键词。"
description_zh: "GPT Image 2.5 图片生成与编辑：文生图、图生图、图片编辑、改图换背景换风格、电商主图、商品详情页、广告KV、封面图、4K 高清图。默认 flare，4K/高清/高质量自动切 sunburst。"
version: 1.0.0
---

# cc-gpt-image-2.5-skill

走 **OpenAI 兼容协议**（`/images/generations` / `/images/edits`）的 GPT Image 2.5 文生图 / 图生图工具，支持 **Codex、WorkBuddy、Claude Code**。

## 约定变量：`$SKILL_PATH`

`$SKILL_PATH` 指**本 SKILL.md 所在的目录**。三个平台安装位置不同，但命令模板完全一致，agent 只需替换 `$SKILL_PATH`：

| 平台 | `$SKILL_PATH` |
|---|---|
| Codex | `~/.codex/skills/cc-gpt-image-2.5-skill` |
| WorkBuddy | `~/.workbuddy/skills/cc-gpt-image-2.5-skill` |
| Claude Code | `~/.claude/skills/cc-gpt-image-2.5-skill` |

## 唯一入口：`bin/cc_imagegen`

```bash
$SKILL_PATH/bin/cc_imagegen generate --prompt "一只橘白小奶猫，窗边柔光"
```

首次调用会自动建 `$SKILL_PATH/.venv` 并装 `requests`，后续直接复用。**别绕开 bin 直接 `python3 scripts/cc_imagegen.py`**——系统 python 可能没 requests。

## 配置 · 三选一

先检查 `~/.cc-gpt-image/config.json` 是否已含 `api_key` + `base_url`；没有就用下表任选一种方式写入。**脚本未配置时会打印一段可直接转发的引导文本，原样回给用户即可。**

| 方式 | 场景 | 命令 |
|---|---|---|
| `config`（推荐，带注释） | 用户在对话里给了 key 和 base_url | `$SKILL_PATH/bin/cc_imagegen config --api-key sk-xxx --base-url https://xxx/v1 [--provider openai] [--model flare] [--note "..."]` |
| 用户手动编辑 | 让用户自己填 | 把 `references/config.example.json` 模板给用户（或脚本未配置时自动打印），填 `api_key` + `base_url` |
| 临时参数 | 一次性生成、不持久化 | `$SKILL_PATH/bin/cc_imagegen generate --api-key sk-xxx --base-url https://xxx/v1 --prompt ...` |

配置文件是 **JSONC**（支持 `//`/`/* */` 注释 + `_备注` 字段），权限自动 0600，**改完立即生效**。

## 模型与质量映射

| 用户说 | 实际模型 | 发出 quality |
|---|---|---|
| 未提 / 普通 / 标准 | `gpt-image-2.5-flare` | — |
| 1k / low | `gpt-image-2.5-flare` | `low` |
| 2k / medium / standard / normal | `gpt-image-2.5-flare` | `medium` |
| 4k / 高清 / 高质量 / 超清 / hd / hq / high | `gpt-image-2.5-sunburst` | `high` |

实现：`--quality 4k` 自动翻译成模型 + quality；也可 `--model sunburst` / `--model flare` / `--model hq` / `--model fast` / `--model hd`。

## 示例

```bash
# 默认（flare）
$SKILL_PATH/bin/cc_imagegen generate --prompt "一只橘白小奶猫，窗边柔光"

# 4K（自动切 sunburst + quality=high）
$SKILL_PATH/bin/cc_imagegen generate --prompt "产品主视觉" --quality 4k
# 等价：--model sunburst --quality high

# 图生图（multipart /images/edits）
$SKILL_PATH/bin/cc_imagegen generate --prompt "保持主体不变，背景换纯白摄影棚" --image input.png

# 批量 + 尺寸
$SKILL_PATH/bin/cc_imagegen generate --prompt "..." --batch 2 --size 1024x1024

# 只提交不落盘
$SKILL_PATH/bin/cc_imagegen generate --prompt "..." --no-download
```

## 执行约束（agent 必读）

1. **首配缺失时不要自己编 base_url，也不要反复 generate**——脚本会把引导文本打到 stderr，原文转发给用户即可。
2. **不要 `init`**（交互式、会拉浏览器）；用 `config` 子命令或让用户编辑 `~/.cc-gpt-image/config.json`。
3. 默认 `--routing COST_FIRST`、`--batch 1`；批量前先告诉用户单价数量等确认。
4. **拿到结果必须核实**：`ls -l` 看产物存在，再交付绝对路径。
5. **绝不替换 `gpt-image-2.5-flare`/`sunburst`** 为其它模型名。模型不存在就 `models` 查实时列表。
6. 图片编辑是 **multipart `/images/edits`**，字段名 `image[]`，可多张。**不支持远程 URL**——先 `curl` 下载到本地再 `--image`。
7. `--image` 是开关：有它走 edits，没它走 generations。
8. **AI Hive 协议保留但模型别名按 OpenAI 名下发**；用户 `provider=ai-hive` 时 publicModelId 跟 flare/sunburst 不对应——**保持 openai 协议**。

## 参数速查

| 参数 | 说明 | 默认 |
|---|---|---|
| `--prompt` | 描述或编辑要求（必填） | — |
| `--image a.png [b.png]` | 参考图；有就走 edits | — |
| `--model` | `flare`/`sunburst`/`fast`/`hq`/`hd`/`default` 或真实模型名 | `flare` |
| `--size` | `1024x1024`/`1024x1536`/`1536x1024` | 服务端默认 |
| `--quality` | `low`/`medium`/`high`/`auto`，或 `1k`/`2k`/`4k`/`hd`/`hq`/`高清`/`超清` | 服务端默认 |
| `--output-format` | `png`/`jpeg`/`webp` | `png` |
| `--batch` | 数量 | `1` |
| `--param key=value` | 透传 OpenAI 字段（background、moderation、resolution…） | — |
| `--routing` | `COST_FIRST`/`SPEED_FIRST`/`SUCCESS_FIRST`（AI Hive） | `COST_FIRST` |
| `--output-dir` | 输出目录 | `~/Downloads/CcGptImage` |
| `--no-download` | 只提交/打印 | 关 |
| `--api-key`/`--base-url`/`--verbose` | 覆盖 | — |

## 子命令

| 子命令 | 用途 |
|---|---|
| `generate` | 文生图 / 图生图主命令 |
| `models [--filter image]` | 列出当前 base_url 可见模型 |
| `task --task-id <id>` | 查询 AI Hive 异步任务（OpenAI 协议无用） |
| `upload --file x.png` | AI Hive 上传换 mediaId（OpenAI 协议无用） |
| `init` | 交互式引导（agent 场景别用） |
| **`config`** | **非交互式把 key/base_url 写进 `~/.cc-gpt-image/config.json`，带注释** |

## 故障

| 现象 | 处理 |
|---|---|
| 找不到 `python3` | 装 Python 3.9+；`bin/cc_imagegen` 会按 python3/python/homebrew 顺序找 |
| 缺少 `requests` | 正常情况 `bin/cc_imagegen` 已自动建 venv；若你直接跑 `python3 scripts/cc_imagegen.py`，请改走 `bin/cc_imagegen` |
| 401 | key 错或过期，`config` 重写 |
| `InvalidParameter` | 模型/尺寸/数量/格式枚举不对，先 `models` 查实时列表 |
| 输出没文件 | 看 `--output-dir` 权限；`--no-download` 本来就不落盘 |
| 用户给了远程图片 URL | `curl -o local.png URL`，再 `--image local.png` |
| 改 base_url / key | 直接编辑 `~/.cc-gpt-image/config.json`（JSONC 注释保留），或 `config` 重写 |
