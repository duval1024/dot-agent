---
name: code-walkthrough
description: Guide users through code walkthroughs step by step. Use when the user wants to understand a code flow, trace a message processing pipeline, or do a "code reading" session. Trigger when user asks "梳理流程", "走读代码", "trace the code", "how does X work", or "带我看代码". The skill provides an overview first, then steps through code one point at a time with file anchors, waiting for user to say "下一步" before proceeding.
---

# Code Walkthrough

帮助用户逐步理解代码流程，从宏观到微观，一步一步带用户走读代码。

## 工作流程

### Phase 1: 理解目标

首先明确用户想要理解什么：
- 是某个特定功能的消息处理流程？
- 是某个模块的架构？
- 是某个特定场景的代码路径？

如果用户没有明确说明，询问：
- 你想理解哪个流程？
- 从哪个入口开始？
- 想看到多深的细节？

### Phase 2: 预览整体流程

在开始逐步走读之前，先给出一个宏观的流程概览：

1. 用 ASCII 图或文字描述展示整体流程
2. 标注关键节点（入口 → 处理 → 输出）
3. 列出涉及的核心文件

**格式示例**：

```
消息处理流程概览：

入口 → 解析 → 识别 → 处理 → 输出
  │      │      │       │       │
  ▼      ▼      ▼       ▼       ▼
qqbot  message identity agent  reply
```

### Phase 3: 逐步走读

这是核心环节。按照以下规则：

#### 跳转锚点格式

**必须使用以下格式**（换行是必须的，否则 IDE 无法跳转）：

```markdown
跳转锚点：
`path/to/file.go:start-end`
```

例如：

```markdown
跳转锚点：
`backend/internal/qqbot/client.go:222-260`
```

#### 每一步的内容

每个步骤包含：
1. **跳转锚点**（文件路径 + 行号范围）
2. **关键代码片段**（可选，帮助用户定位）
3. **关键点说明**（1-3 个要点，简短）
4. **下一步预告**（告诉用户接下来会看什么）

#### 控制节奏

- **每一步只展示一个点**，不要一次展示多个
- **等待用户说"下一步"、"继续"才推进**
- 不要跳过中间环节

#### 步骤标题

使用清晰的标题格式：

```markdown
## 第一步：WebSocket 接收消息
## 第二步：消息回调设置
## 第三步：用户识别与会话获取
```

### Phase 4: 深入细节

如果用户对某个环节有疑问，可以：
- 展开该环节的内部实现
- 进入调用的子函数
- 展示数据流转

但仍然保持一步一个锚点的节奏。

### Phase 5: 总结

走读结束后，提供：
- 涉及的文件清单
- 关键函数/方法对照表
- 可选：用户可以深入的方向

## 输出格式模板

### 概览阶段

```markdown
## 消息处理完整流程

[ASCII 流程图]

---

涉及的核心文件：
- `qqbot/client.go` - WebSocket 入口
- `service/agent_service.go` - 核心处理
- `agent/loop.go` - ReAct 循环
- `memory/recall.go` - 智能召回

---

从 WebSocket 入口开始。请说 **下一步**。
```

### 走读阶段

```markdown
## 第N步：[环节名称]

跳转锚点：
`path/to/file.go:start-end`

关键代码：
[可选的代码片段，帮助定位]

关键点：
- XX 行：[做什么]
- YY 行：[做什么]

---

下一步会看 [下一个环节]。请说 **下一步**。
```

## 最佳实践

1. **锚点精确**：行号范围要覆盖核心代码，不要太宽泛
2. **简短说明**：每个步骤的说明控制在 3 行以内
3. **保持节奏**：用户说下一步才推进，不要跳步
4. **预告清晰**：告诉用户下一步要去哪里
5. **遇到分支**：如果流程有多个路径，询问用户想走哪条

## 示例对话

用户：梳理一下消息处理的完整流程

Claude：
[给出流程概览]
从 WebSocket 入口开始。请说 **下一步**。

用户：下一步

Claude：
## 第一步：WebSocket 接收消息
跳转锚点：
`backend/internal/qqbot/client.go:222-260`
关键点：240 行判断消息类型，255 行触发回调
下一步看回调设置。请说 **下一步**。

用户：下一步

Claude：
[继续第二步...]

## 注意事项

- 不要使用"代码位置："这样的文字，IDE 无法识别跳转
- 跳转锚点必须是独立的行，以 `path:line` 格式
- 如果用户中途有问题，可以暂停解释，然后再继续