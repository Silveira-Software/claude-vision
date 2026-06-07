---
name: video-analyzer
description: 使用 Google Gemini 分析视频文件，并返回结构化的 markdown 报告，涵盖整体摘要、逐场景拆解、音频转录（或诚实标注"静音"）、视觉细节以及带时间戳的关键时刻。具备强力的防幻觉保护措施——不会编造旁白者、画外音或说话人姓名。当你需要了解视频中实际发生了什么时使用此技能。
argument-hint: <视频路径/video.mp4> [--prompt "..."] [--fps N] [--model ...]
disable-model-invocation: true
allowed-tools: Bash, Read
---

# 分析视频

使用 Gemini 分析视频文件，并返回结构化的 markdown 报告。

## 前提条件

- Python 3.10+
- 全局安装 `google-genai`（任何通过 `python3` 能被 shell 找到的 Python 均可——已在 1.64.0 版本验证可用）
- 在用户的 shell 环境中设置 `GEMINI_API_KEY`（例如，在 `~/.zshrc` 中导出）

## 步骤

1. 从 `$ARGUMENTS` 解析参数：
   - **视频路径**（必填）——视频文件的路径
   - **--prompt**（可选）——自定义分析提示词；默认使用带防幻觉规则的结构化报告提示词
   - **--fps**（可选）——自定义帧采样率（对于捕捉快节奏画面中不到一秒的剪辑很有用）
   - **--model**（可选）——Gemini 模型 ID；默认为 `gemini-3-flash-preview`

2. 验证给定路径下的视频文件是否存在。如果不存在，报告错误并停止。

3. 使用安装位置的绝对路径运行分析脚本：

```bash
python3 ~/.claude/skills/video-analyzer/scripts/analyze_video.py $ARGUMENTS
```

4. 该脚本将：
   - 上传视频——对于 ≤18MB 的文件采用内联方式，对于更大的文件采用 Files API（轮询最长 300 秒以等待 ACTIVE 状态）
   - 将提示词连同附加的视频发送给 Gemini
   - 将完整的 markdown 报告打印到 stdout（信息/进度行输出到 stderr）

5. 捕获 stdout 并将报告呈现给用户。

6. 如果脚本以错误退出，帮助用户排查问题：
   - **缺少 API 密钥**：确认在用户的 shell 中 `echo $GEMINI_API_KEY` 非空。如果它仅存在于 `~/.zshrc` 中，可能需要启动新的终端或运行 `source ~/.zshrc`。
   - **不支持的格式**：必须是以下之一：mp4、mov、avi、webm、mpeg、mpg、wmv、3gpp、3gp、flv
   - **上传超时**：文件过大或连接缓慢——重试，或使用更短的片段
   - **模型错误 / 404**：尝试使用其他模型 `--model gemini-2.5-flash`

## 输出

打印到 stdout 的 markdown 报告，包含以下部分：

- **整体摘要**——用 2-3 句话概述视频中实际发生的内容
- **逐场景拆解**——为每个剪辑/场景标注 `MM:SS` 时间戳，包含屏幕内容、动作和逐字文本
- **音频**——带时间戳的逐字转录，或诚实标注"无音频 / 静音 / 仅环境音"（提示词明确禁止编造旁白者）
- **视觉细节**——屏幕文字、界面元素、产品、品牌、人物
- **关键时刻**——观众会记住的 3-7 个带时间戳的亮点
