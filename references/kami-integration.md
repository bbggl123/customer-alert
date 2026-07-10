# KAMI 排版集成说明

> 本文档说明如何在分贝通客户商机雷达 Skill 中使用 KAMI 排版系统生成印刷级专业 PDF。

---

## KAMI 是什么

**KAMI（紙，かみ）** 是由 tw93 开发并开源的一款 AI 驱动的文档设计系统，以 Claude Code Skill 的形式发布。它专为解决当前 AI 生成文档普遍存在的排版同质化、视觉质感弱、缺乏专业印刷感等问题而生。

### 核心特性

- **约束系统而非 UI 框架**：通过严格的排版规则确保每次输出都稳定达到专业标准
- **8 种文档模板**：One-Pager、Long Doc、Letter、Portfolio、Resume、Slides、Equity Report、Changelog
- **印刷级设计规范**：暖米色底色、油墨蓝强调色、衬线字体、固定字重、规范行距
- **多语言支持**：中文（仓耳今楷02）、英文（Charter）、日文（YuMincho）
- **WeasyPrint 渲染**：将 HTML/CSS 转换为高质量 PDF

### 为什么选择 KAMI

本 Skill 输出的客户商机早报包含大量结构化信息（客户动态、商机评估、跟进话术、质检报告），传统 Markdown 或纯文本输出缺乏视觉质感，不够专业。KAMI 提供的 One-Pager 模板非常适合单页核心信息展示，让销售可以直接打印或转发给客户。

---

## KAMI 安装方法

### 前置要求

- 已安装 Claude Code、Codex 或 Claude Desktop（支持 skill 调用）
- 系统安装 Python 3.10+（用于 WeasyPrint 渲染）
- 网络可访问 GitHub / jsDelivr（首次安装字体）

### 安装方式

#### 方式 1：命令行一键安装（推荐）

```bash
npx skills add tw93/kami -a claude-code -g -y
```

#### 方式 2：Claude Code 插件市场

```
/plugin marketplace add tw93/Kami
/plugin install kami@kami
```

#### 方式 3：Claude Desktop 手动导入

1. 从 [GitHub Releases](https://github.com/tw93/Kami/releases) 下载 `kami.zip`
2. 打开 Claude Desktop → `Customize` → `Skills` → `+ Create Skill` → 上传 ZIP

#### 方式 4：通用 AI 工具安装

```bash
npx skills add tw93/kami -a '*' -g -y
```

### 验证安装成功

在 Claude Code 终端输入 `/`，随后键入 `kami`。若出现：

```
kami - Generate professional documents with print-ready typography...
```

即表示安装成功。

---

## KAMI 设计规范（10 条排版铁律）

KAMI 的美学压缩成一句话：**温暖的 parchment 画布，油墨蓝作为唯一强调色，衬线字体承载层级，避免冷灰和硬阴影**。

| 规则 | 具体要求 | 本 Skill 应用 |
|------|---------|--------------|
| 画布底色 | `#f5f4ed` 暖米色，永不纯白 | PDF 页面底色为暖米色，给人温暖、专业、值得信赖的感觉 |
| 强调色 | `#1B365D` 油墨蓝，占比 ≤5% | 标题、关键数据、高优先级标记使用油墨蓝 |
| 灰色调 | 所有灰色暖调（黄棕底色），禁冷蓝灰 | 正文、次要信息使用暖灰色系 `#3d3d3a` ~ `#6b6a64` |
| 字体栈 | 中文：仓耳今楷02 / 英文：Charter | 早报标题和正文使用衬线字体，保持印刷质感 |
| 字重控制 | 标题 500，正文 400，不合成粗体 | 标题和正文固定字重，用字号区分层级 |
| 行距规范 | 标题紧凑 1.1-1.3 / 正文密排 1.4-1.45 | 确保阅读舒适度，标题紧凑，正文密排 |
| 字间距 | 中文正文 0.3pt / 英文正文 0 | 优化中文排版间距 |
| 标签背景 | 纯色 hex，禁 rgba() | 避免标签使用 rgba 背景（WeasyPrint 会产生双层矩形 bug） |
| 阴影策略 | 仅用 ring shadow 或 whisper shadow | 使用柔和光晕阴影，禁用硬投影 |
| 禁用斜体 | PDF 模板中不使用 `font-style: italic` | 保持印刷规范，不使用斜体 |

---

## 本 Skill 如何使用 KAMI

### 调用流程

```
质检通过（Evaluator） → Formatter 触发 → 调用 KAMI skill → One-Pager 模板 → PDF 输出
```

1. **质检完成**：Evaluator 检查 Markdown 格式早报，确保内容合格（无模板感、无AI味、信息准确）

2. **Formatter 触发**：Formatter 环节启动，准备调用 KAMI

3. **调用 KAMI**：
   - 使用自然语言触发：`"帮我把这份早报排版成专业的 PDF"`
   - 或使用斜杠命令：`/kami`
   - 指定模板：`"使用 One-Pager 模板"`

4. **内容映射**：将 Markdown 早报映射为 KAMI One-Pager 结构：
   ```
   Page Header      → 早报标题 + 日期（油墨蓝）
   Summary Section  → 监控概览（暖米色背景）
   Main Content     → 重点商机列表（独立区块）
   Action Section   → 行动建议（列表样式）
   Footer/Meta      → 质检报告（暖灰色小字）
   ```

5. **生成 PDF**：KAMI 通过 WeasyPrint 生成印刷级 PDF

6. **输出交付**：PDF 文件可下载、打印或转发

### 内容到模板映射规则

| 早报内容区块 | KAMI 区域 | 设计处理 |
|-------------|----------|---------|
| 标题 + 日期 | Page Header | 油墨蓝 `#1B365D` + 衬线字体，字重500，字号最大 |
| 监控概览 | Summary Section | 暖米色背景，数据（客户数、动态数）用油墨蓝强调 |
| 重点商机 | Main Content Blocks | 每个商机独立区块，星级标记用油墨蓝 |
| 商机评估 | Block Content | 暖灰色正文 `#3d3d3a`，关键数据用油墨蓝 |
| 跟进话术 | Quote/Highlight Box | 引用样式，左缩进，背景略亮 `#faf9f5` |
| 原文链接 | Inline Link | 油墨蓝链接色 `#2D5A8A`，可点击跳转 |
| 行业标签 | Tag/Label | 暖灰色标签，纯色背景 `#e8e6dc` |
| 行动建议 | Action Section | 列表样式，编号建议项 |
| 质检报告 | Footer/Meta Section | 暖灰色小字 `#6b6a64`，次要信息 |

### 视觉增强策略

- **高优先级商机**（⭐⭐⭐⭐⭐）：使用油墨蓝边框或背景标记，突出视觉层级
- **关键数据**（融资金额、差旅支出、客户数）：使用油墨蓝标注，占比控制在 5% 以内
- **跟进话术**：使用引用框样式（左缩进、背景略亮），区别于正文
- **原文链接**：油墨蓝链接色，可点击跳转，提升可用性

---

## KAMI 调用示例

### 示例 1：自然语言触发

```markdown
##  客户商机早报 | 2026-07-10

### 📊 监控概览
- 监控客户：3家 | 新动态：2条 | 行业匹配：1条

### 🔥 重点商机

**1. 拓竹科技 — 完成B轮融资2亿元** ⭐⭐⭐⭐⭐
- 📌 融资后团队预计扩张至300人+
- 📌 差旅支出至少翻3倍
- 💰 商机评估：融资窗口期，支出管控需求强烈
- 🔗 [查看原文](https://36kr.com/p/123456)

💬 **跟进话术**（独立版）：
> "B轮2亿，按行业经验，6个月内团队会扩到300人+，差旅支出至少翻3倍。我们帮{同行客户}做了商旅+费控一体化，3个月上线，报销量降了90%。融资后30天是部署窗口期，这周方便聊聊吗？"

---

**2. 快造科技 — 进入欧洲市场** ⭐⭐⭐
- 📌 出海扩张，海外差旅需求增长
- 💰 商机评估：海外商旅管理复杂度提升
- 🔗 [查看原文](https://example.com/news)

### 💡 行动建议
1. 融资后30天跟进拓竹科技（支出管控窗口期）
2. 出海前沟通快造科技（海外差旅管理需求）

### 📌 质检报告
- 话术通过率：2/2
- 新发现动态：2条 | 重复过滤：0条
- 原文链接：已附
```

**触发命令**：
```
帮我把这份早报排版成专业的 PDF，使用 One-Pager 模板，暖米色底色，油墨蓝强调色
```

### 示例 2：斜杠命令触发

```
/kami
```

然后提供早报内容和模板选择：
```
模板：One-Pager
内容：{粘贴上述 Markdown 早报}
```

---

## KAMI 排版质量验收

### 验收标准

| 验收项 | 标准 | 检查方式 |
|--------|------|---------|
| 底色正确 | 暖米色 `#f5f4ed`，非纯白 | 视觉检查，确认不是 `#ffffff` |
| 强调色占比 | 油墨蓝 `#1B365D` 占比 ≤5% | 估算油墨蓝使用面积占比 |
| 字体正确 | 中文仓耳今楷02 / 英文 Charter | 检查字体栈，确认未使用默认字体 |
| 行距合规 | 标题 1.1-1.3 / 正文 1.4-1.45 | 检查阅读舒适度，确保密排效果 |
| 标签无 rgba | 所有标签背景为纯色 hex | 检查标签渲染，无双层矩形 bug |
| 链接可用 | 原文链接可点击跳转 | 测试链接点击，确保跳转正常 |
| 内容完整 | 所有质检通过的内容都已排版 | 对照 Markdown 源，确认无遗漏 |
| 可打印 | PDF 符合印刷标准，可高质量打印 | 打印测试，检查印刷质量 |

### 常见问题与解决方案

#### 问题 1：KAMI skill 未安装

**症状**：触发 `/kami` 或自然语言调用时无响应

**解决方案**：
```bash
npx skills add tw93/kami -a claude-code -g -y
```

#### 问题 2：字体加载失败

**症状**：PDF 使用默认字体，非仓耳今楷02/Charter

**解决方案**：
- KAMI 会自动 fallback 到 jsDelivr CDN
- 确保网络可访问 jsDelivr（`https://cdn.jsdelivr.net/`）
- 如果仍失败，手动下载字体到本地

#### 问题 3：WeasyPrint 渲染失败

**症状**：PDF 生成失败或样式错乱

**解决方案**：
- 检查 HTML/CSS 是否符合 KAMI 规范
- 确认标签背景使用纯色 hex，非 rgba()
- 检查阴影样式是否为 ring shadow 或 whisper shadow

#### 问题 4：内容过长超出 One-Pager

**症状**：单页 PDF 无法容纳所有内容

**解决方案**：
- 提示精简内容，或切换到 KAMI 的 **Long Doc** 模板
- Long Doc 模板支持长篇内容，适合研究报告

#### 问题 5：强调色占比超标

**症状**：油墨蓝占比超过 5%，视觉显得堆砌

**解决方案**：
- 减少油墨蓝使用频率，仅用于最关键的信息
- 调整星级标记样式，使用油墨蓝边框而非填充
- 检查标签、数据、链接的强调色使用，控制总量

---

## KAMI 模板选择指南

### One-Pager（一页纸报告）

**适用场景**：
- 单页核心信息展示
- 客户商机早报（推荐）
- 项目简介、产品介绍

**特点**：
- 快速浏览，一眼抓住重点
- 信息密度适中
- 适合打印或转发

### Long Doc（长文档）

**适用场景**：
- 研究报告、技术文档
- 长篇分析内容
- 超出 One-Pager 容量的内容

**特点**：
- 支持多页内容
- 保持长文本阅读体验
- 适合深度阅读

### 其他模板

- **Letter**：正式信函、商务函件
- **Portfolio**：项目作品集、个人展示
- **Resume**：简历、个人履历
- **Slides**：演讲稿、演示幻灯片
- **Equity Report**：投资分析简报
- **Changelog**：产品更新日志

---

## 技术细节

### 字体栈配置

```css
/* 中文 */
font-family: "TsangerJinKai02",
 "Source Han Serif SC", "Noto Serif CJK SC",
 "Songti SC", "STSong",
 Georgia, serif;

/* 英文 */
font-family: Charter, Georgia, Palatino,
 "Times New Roman", serif;
```

### 色彩系统

```css
/* Brand */
--brand: #1B365D;         /* 油墨蓝 - 唯一强调色 */
--brand-light: #2D5A8A;   /* 油墨蓝浅色 - 链接 */

/* Surface */
--parchment: #f5f4ed;     /* 暖米色 - 页面底色 */
--ivory: #faf9f5;         /* 卡片底色 */
--warm-sand: #e8e6dc;     /* 按钮底色 */
--dark-surface: #30302e;  /* 暖黑 */

/* Text */
--near-black: #141413;    /* 主文字 */
--dark-warm: #3d3d3a;     /* 次级文字 */
--olive: #504e49;         /* 描述文字 */
--stone: #6b6a64;         /* 次要文字 */

/* Border */
--border: #e8e6dc;        /* 边框 */
--border-soft: #e5e3d8;   /* 软边框 */
```

### WeasyPrint 渲染流程

```
HTML + CSS → WeasyPrint → PDF
```

- **HTML**：KAMI 生成的结构化 HTML，包含所有内容和样式
- **CSS**：遵循 10 条排版铁律的 CSS 样式表
- **WeasyPrint**：Python HTML-to-PDF 引擎，支持 CSS 2.1 和部分 CSS 3
- **PDF**：印刷级 PDF，符合出版规范

---

## 参考资料

- **KAMI GitHub**: [https://github.com/tw93/Kami](https://github.com/tw93/Kami)
- **KAMI 设计系统**: `references/design.md`（KAMI 项目内）
- **KAMI 使用教程**: `references/usage.md`（KAMI 项目内）
- **WeasyPrint 官方文档**: [https://weasyprint.org/](https://weasyprint.org/)
- **仓耳今楷02 字体**: [https://tsanger.cn/](https://tsanger.cn/)（个人免费，商用需授权）

---

## 总结

通过集成 KAMI 排版系统，分贝通客户商机雷达 Skill 现在可以输出印刷级专业 PDF，包含：

- ✅ 暖米色底色 `#f5f4ed`，温暖专业
- ✅ 油墨蓝强调色 `#1B365D`，克制优雅
- ✅ 仓耳今楷02/Charter 衬线字体，印刷质感
- ✅ 高优先级商机突出标记，视觉层级清晰
- ✅ 可点击原文链接，提升可用性
- ✅ 完整质检报告，信息透明

最终交付的 PDF 可以直接打印、转发给客户，或存档备查，大幅提升了销售工作效率和客户沟通的专业度。