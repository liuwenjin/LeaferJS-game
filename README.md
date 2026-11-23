# LeaferJS Game

**可独立运行的 2D 小游戏生成器**

> 一句自然语言需求，得到一个 **打开就能玩、手机也能玩** 的 2D 小游戏。
> 自带任意大模型即可运行；产物是零依赖的单文件 HTML，键盘与触屏双输入。

![BYOM](https://img.shields.io/badge/model-any%20LLM-6f5cff)
![LeaferJS](https://img.shields.io/badge/LeaferJS-2.3.4-6f5cff)
![Single File](https://img.shields.io/badge/output-single--file%20HTML-2ea44f)
![60 FPS](https://img.shields.io/badge/target-60%20fps-2ea44f)

---

## 简介

**LeaferJS Game 是一个可独立运行的 AI 工具**，不是某个平台的插件，也不需要登录任何账号。

它的全部能力来自一份系统提示词 [`appPrompt.md`](./appPrompt.md)：把这份提示词交给任意支持长输出的 LLM，模型就会把你的玩法描述变成一个可完整游玩的小游戏，并以严格 JSON 交付。取 JSON 里的 `code` 字段保存为 `.html`，双击就能开始。

```text
你的玩法描述 ──► 任意 LLM ──► JSON ──► game.html ──► 浏览器
                    ▲
              appPrompt.md
```

工具链上没有隐藏服务：没有后端、没有数据库、没有构建步骤。你需要的只是一个模型端点（云端 API 或本地模型）和一个浏览器。

## 为什么它可以独立运行

| 维度 | 说明 |
| --- | --- |
| **模型自由** | OpenAI、DeepSeek、Claude、Gemini、Ollama、vLLM 均可；只要支持 system prompt 与长输出 |
| **无平台锁定** | 不调用 transweb.cn 或任何私有接口；平台页面只是同款提示词的参考实现 |
| **本地执行** | 提示词、调用脚本、产物都在你的机器上；使用本地模型时可完全离线生成 |
| **产物可移植** | 输出是单个 `.html` 文件，可双击打开、邮件发送、静态托管或嵌入 iframe |
| **过程可审计** | 提示词、协议和生成结果全部可见，可 diff、可版本化、可二次修改 |

## 它生成什么

一个能完整玩一局的小游戏，而不是一张会动的场景：

- **单一核心循环**：一个核心动词（移动 / 射击 / 跳跃 / 躲避 / 收集 / 合成）对抗一种压力
- **8 秒法则**：打开页面 8 秒内看懂「这是什么、怎么玩、目标是什么」
- **完整状态机**：`ready → playing → paused → over`，失败后 1 秒内重开
- **固定步长**：1/60 秒固定更新，累积器追赶，逻辑与帧率解耦
- **双输入**：键盘（WASD / 方向键）+ 指针拖拽 / 触摸，两条路径都能完成一局
- **手感反馈**：加速度与摩擦、无敌帧、命中停顿、屏幕震动、粒子与飘字
- **公平难度**：分阶段推进，生成有预警、有安全距离，随机只制造变化不制造必死
- **自适应场地**：设计分辨率等比缩放居中，竖屏完整可见，不拉伸不裁切
- **性能预算**：节点 ≤ 300、粒子池 ≤ 120、敌人 ≤ 12、子弹 ≤ 60，目标 60fps
- **零外部素材**：无图片、无字体、无音频文件，全部程序化绘制

## 快速开始（独立运行）

### 前置条件

- Node.js 18+（内置 `fetch`，无需 `npm install`）
- 一个 OpenAI 兼容的模型端点；建议单次输出上限 **≥ 8K tokens**

### 1. 获取工具

```bash
git clone https://github.com/liuwenjin/LeaferJS-game.git
cd LeaferJS-game
```

### 2. 创建本地运行器

把下面的代码保存为 `run.mjs`（放在仓库根目录）：

> 不想用 Node.js？任何 HTTP 客户端都可以：把 `appPrompt.md` 作为 system message、需求作为 user message 发送，再解析返回的 JSON。

```js
// run.mjs — Node.js 18+，零依赖
import fs from 'node:fs';

const system = fs.readFileSync('appPrompt.md', 'utf8');
const idea = process.argv.slice(2).join(' ') || '控制一只猫在霓虹屋顶间跳跃，躲开无人机，收集鱼干。';
const base = (process.env.LLM_BASE_URL || 'https://api.deepseek.com').replace(/\/$/, '');

const res = await fetch(`${base}/chat/completions`, {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    Authorization: `Bearer ${process.env.LLM_API_KEY}`
  },
  body: JSON.stringify({
    model: process.env.LLM_MODEL || 'deepseek-chat',
    messages: [
      { role: 'system', content: system },
      { role: 'user', content: idea }
    ],
    max_tokens: Number(process.env.LLM_MAX_TOKENS || 8000),
    temperature: 0.7
  })
});

if (!res.ok) throw new Error(`HTTP ${res.status}: ${await res.text()}`);
const body = await res.json();
if (body.choices?.[0]?.finish_reason === 'length') {
  throw new Error('输出被截断：请提高 LLM_MAX_TOKENS，或换用输出上限更大的模型');
}

const raw = body.choices[0].message.content.trim()
  .replace(/^`{3}(?:json)?\s*/, '')
  .replace(/\s*`{3}$/, '');
const data = JSON.parse(raw);
fs.writeFileSync('game.html', data.code, 'utf8');
console.log(`✓ ${data.title} → game.html（${data.code.split('\n').length} 行）`);
```

### 3. 生成并开始游戏

```bash
LLM_API_KEY=sk-... node run.mjs "玩家控制一只在霓虹城市屋顶间跳跃的猫，躲开无人机，收集鱼干"
open game.html     # macOS；其他系统直接双击
```

点击「开始」后键盘事件才会绑定到页面；手机上用拖拽或触摸操作。游戏产物本身不需要 Node.js。

### 4. 换一个模型

| 服务 | `LLM_BASE_URL` | `LLM_MODEL` 示例 |
| --- | --- | --- |
| OpenAI | `https://api.openai.com/v1` | `gpt-4.1` |
| DeepSeek | `https://api.deepseek.com` | `deepseek-chat` |
| Ollama（本地） | `http://localhost:11434/v1` | `qwen2.5:14b` |
| vLLM（本地） | `http://localhost:8000/v1` | 你部署的模型名 |
| 其他网关 | 任意 OpenAI 兼容地址 | 以服务商文档为准 |

```bash
LLM_BASE_URL=https://api.openai.com/v1 LLM_MODEL=gpt-4.1 \
LLM_API_KEY=sk-... node run.mjs "在深海热泉口躲避喷发，收集发光矿石"
```

> Claude、Gemini 的原生 API 协议不同，可通过 OpenAI 兼容网关接入，或自行替换运行器中的请求部分。模型不是工具的一部分，可随时替换。

## 交付协议（硬约束）

模型必须**只输出一个合法 JSON 对象**，前后不允许任何解释文字、Markdown 代码围栏或注释。顶层固定 5 个字段，顺序固定：

| 字段 | 类型 | 约束 |
| --- | --- | --- |
| `type` | `string` | 固定为 `game` |
| `title` | `string` | 中文名称，≤ 12 字，不含句号、引号、书名号 |
| `description` | `string` | 一句话摘要，≤ 30 字，不换行 |
| `code` | `string` | 完整单文件 HTML，源码 400–850 行，以 `<!DOCTYPE html>` 开头、`</html>` 结尾 |
| `highlights` | `string[3]` | 恰好 3 条，每条 ≤ 20 字，不写序号 |

结构示意（`code` 已省略）：

```json
{
  "type": "game",
  "title": "暗礁穿梭",
  "description": "拖动飞船穿过不断收窄的暗礁通道。",
  "code": "<!DOCTYPE html> ... </html>",
  "highlights": [
    "一键拖拽 + 键盘双输入",
    "通道收窄的节奏型难度",
    "撞击停顿与屏幕震动反馈"
  ]
}
```

### `code` 字段的转义铁律

`code` 的值本身是一段 JSON 字符串。为了不让裸双引号提前结束字符串，提示词要求：

1. `code` 源码内**零双引号**：HTML 属性、JS 字符串、CSS 字体名全部用单引号，插值用反引号模板字符串。
2. `code` 源码内**零反斜杠**：不写 `\n`、`\t`、`\u`、正则转义或 `<\/script>`；换行直接写真实换行。
3. JSON 序列化时，真实换行写成 `\n`，这是 `code` 值里唯一允许的反斜杠形式。
4. 禁止尾随逗号、注释和代码围栏；缩进只用空格。

## 技术栈

| 层 | 选型 |
| --- | --- |
| 生成引擎 | `appPrompt.md`（系统提示词）+ 任意 LLM |
| 运行器 | 约 40 行 Node.js 脚本，仅用内置 `fetch` |
| 渲染引擎 | LeaferJS 2.3.4（唯一 CDN 依赖） |
| 主循环 | 单个 `requestAnimationFrame`，固定步长 `STEP = 1 / 60` |
| 场景层 | `world`（缩放居中）+ `shakeLayer`（震屏偏移） |
| 输入 | `keydown` / `keyup` + Pointer Events，两套输入都能完成一局 |
| HUD | DOM 覆盖层，`pointer-events: none`，按钮单独可点 |
| 产物 | 单文件 HTML，无构建、无框架、无 `import` / `require` |

约束：禁止 `alert` / `confirm` / `prompt`、`localStorage` / `sessionStorage` / `cookie`、外部字体 / 图片 / 音频；最高分只保存在内存。

LeaferJS 只允许引入一次：

```text
https://unpkg.com/leafer-ui@2.3.4/dist/web.min.js
```

## 目录结构

```text
LeaferJS-game/
├── appPrompt.md     # 工具引擎：完整系统提示词（必需）
├── README.md        # 本文件
├── appCode.vue      # 可选参考实现：平台端 UI（独立运行不需要）
├── appPage.html     # 可选参考实现：iframe 嵌入页
└── manifest.json    # 元数据
```

`run.mjs` 不在仓库内，按「快速开始」创建即可；`appCode.vue` 与 `appPage.html` 只是同款提示词在 transweb.cn 上的 Web 外壳，**不参与**独立运行链路，删掉它们不影响工具使用。

## 提示词设计要点

- **格式优先于内容**：JSON 不合法，游戏再好也是 0 分。
- **可运行优先于炫技**：能完整玩一局，胜过画面复杂但跑不起来的半成品。
- **一个核心机制**胜过十个半成品功能。
- 数值集中在顶部 `CFG` 对象，标注单位；难度曲线写成可读公式。
- 篇幅预算 400–850 行，预算紧张时先砍装饰，不砍核心玩法与重开闭环。

## 交付验收清单

- [ ] 冷启动显示加载提示，正常网络下 8 秒内可开始
- [ ] 30–90 秒内能走完一局
- [ ] 失败后 1 秒内可以重开
- [ ] 暂停真的冻结计时和生成
- [ ] 切到后台再回来，游戏自动暂停
- [ ] 手机竖屏下场地完整可见，不被裁切
- [ ] 键盘和触屏都能完成一局
- [ ] 无控制台异常、无 404 资源
- [ ] 页面无滚动条，滚轮不会缩放/拖动画面
- [ ] 所有按钮、所有键位都有效

## 常见问题

**为什么没有排行榜？**
为了让产物在任意环境（包括 `data:` URL 的 iframe）中都能运行，提示词禁用持久化存储；最高分只保留在内存里。需要排行榜时，可自行接入后端。

**必须联网吗？**
生成阶段需要访问模型；播放阶段需要加载 LeaferJS CDN。要完全离线，可下载 `web.min.js` 到本地并替换产物里的脚本地址，再配合本地模型使用。

**输出被截断怎么办？**
优先提高 `LLM_MAX_TOKENS`，或换用输出上限更大的模型。提示词已要求模型在预算内优先保证核心玩法与 `</html>` 完整。

**可以商用吗？**
本仓库暂未声明许可证。提示词与生成代码的版权归各自作者所有，商用前请自行确认授权范围。

## 参考实现（可选）

- 在线效果参考：<https://transweb.cn/agent.html?id=ac531ed5783afaa1bd0590a357539e5d>
- `appPage.html` 通过 iframe 嵌入同一在线版本；它只用于预览，不是运行本工具的必要条件。

## 相关链接

- LeaferJS 官网：<https://www.leaferjs.com/>
- LeaferJS 文档：<https://www.leaferjs.com/ui/guide/>
- 本仓库：<https://github.com/liuwenjin/LeaferJS-game>

## 许可

本仓库暂未指定开源许可证。提示词文本与生成的游戏代码版权归各自作者所有；使用前请自行确认授权范围。
