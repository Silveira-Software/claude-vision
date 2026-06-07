# Claude Vision — 视频分析技能

加入 SCALE AI 内部 550 多个绩效营销团队：https://www.skool.com/scale-ai/about

赋予 Claude Code "观看"视频的能力。

这是一个 Claude Code 技能，它将任何视频文件通过 Google 的 Gemini API（具备原生视频理解能力）进行路由，并返回结构化的 markdown 报告——整体摘要、带时间戳的逐场景拆解、音频转录、视觉细节和关键时刻。适用于屏幕录制、UGC 广告、教程、演示、会议录像——任何 Gemini 能够处理的内容。

它具备强力的防幻觉保护措施：不会编造视频中实际不存在的旁白者、画外音或说话人姓名。

## 安装

### 1. 克隆此仓库

```bash
git clone https://github.com/mikefutia/claude-vision.git
```

### 2. 将其移动到你的 Claude Code 技能文件夹

```bash
mv claude-vision ~/.claude/skills/video-analyzer
```

文件夹名称**必须**为 `video-analyzer`——这是 Claude Code 查找该技能的方式。

### 3. 获取免费的 Gemini API 密钥

前往 [Google AI Studio](https://aistudio.google.com/apikey) 创建密钥。免费额度很慷慨，足够个人使用。

### 4. 设置 API 密钥

最简单的方法：在任意项目中打开 Claude Code，让它为你配置密钥。比如：

> "将我的 GEMINI_API_KEY 设置为 `your_key_here`，让它在每个新 shell 中都可用。"

Claude Code 会将 export 添加到你的 shell 配置文件中并确认其生效。你无需自己动 `.zshrc`。

### 5. 安装 Python 依赖

该技能使用 Google 官方的 Gemini SDK：

```bash
pip install google-genai
```

如果 pip 报告外部管理环境的错误，请使用：

```bash
pip install google-genai --break-system-packages
```

### 6. 使用该技能

在 Claude Code 中，只需将其指向某个视频：

> "在 /path/to/my-video.mp4 上使用 video-analyzer 技能"

或直接调用：

> "/video-analyzer ~/Downloads/demo.mp4"

Claude 将运行分析并呈现结构化报告。

## 你可以用它做什么

- **广告拆解**——放入竞争对手的 UGC 广告，获得逐节拍的拆解
- **教程 → 标准作业程序（SOP）**——将 Loom 录像转化为书面的分步指南
- **会议回顾**——从通话中提取决策和行动项
- **演示笔记**——总结屏幕录制中发生的内容
- **通用的"这个视频里有什么？"**——任何视频，任何问题

## 支持的格式

mp4、mov、webm、avi、mpeg、mpg、flv、wmv、3gpp、3gp

## 可选标志

```
/video-analyzer <路径> [--prompt "自定义提示词"] [--fps N] [--model gemini-2.5-flash]
```

- `--prompt`——用你想要的任何内容覆盖默认的结构化报告提示词
- `--fps`——更改帧采样率（默认 1 fps；对于快速剪辑的内容可调高）
- `--model`——选择其他 Gemini 模型（默认 `gemini-3-flash-preview`）

## 故障排查

- **"GEMINI_API_KEY environment variable is not set"**——你的密钥对 Claude Code 运行所在的 shell 不可见。打开新终端重试，或让 Claude Code 修复。
- **"google-genai is not installed"**——运行 `pip install google-genai`（见步骤 5）。
- **大文件上传超时**——Gemini 的 Files API 处理较长视频可能需要 30–60 秒。脚本最多轮询 5 分钟后才放弃。
- **模型 404**——如果默认的 preview 模型在你所在地区不可用，尝试 `--model gemini-2.5-flash`。

## 许可证

MIT——随你怎么用。
