# Anthropic Skills 代码库分析报告

**分析日期**: 2026年2月21日  
**代码库地址**: https://github.com/anthropics/skills  
**报告版本**: v1.0

---

## 📋 目录

1. [项目概述](#项目概述)
2. [Skills 架构设计](#skills-架构设计)
3. [最受欢迎的 Skills 分析](#最受欢迎的-skills-分析)
4. [最有用的 Skills 详细介绍](#最有用的-skills-详细介绍)
5. [使用场景与推荐](#使用场景与推荐)
6. [社区生态](#社区生态)
7. [总结与建议](#总结与建议)

---

## 项目概述

### 什么是 Skills？

**Skills** 是 Anthropic 推出的一种模块化、可复用的指令包系统，用于扩展 Claude AI 的能力。每个 Skill 是一个包含指令、脚本和资源的文件夹，Claude 可以动态加载这些内容来执行特定任务。

### 核心价值

| 特性 | 描述 |
|------|------|
| **可复用性** | 一次创建，随处使用（Claude.ai、Claude Code、API） |
| **高效性** | 渐进式披露架构，仅加载必要内容 |
| **可组合性** | 多个 Skills 可同时激活，协同工作 |
| **可移植性** | 跨平台兼容，统一格式 |

### 代码库结构

```
anthropics/skills/
├── skills/                    # Skills 示例
│   ├── document-skills/       # 文档处理技能 (pdf, docx, xlsx, pptx)
│   ├── creative-design/       # 创意设计技能
│   ├── development/           # 开发工具技能
│   └── enterprise/            # 企业级技能
├── spec/                      # Agent Skills 规范
└── template/                  # Skill 模板
```

---

## Skills 架构设计

### 渐进式披露架构 (Progressive Disclosure)

Skills 采用三层渐进式加载机制：

```
┌─────────────────────────────────────────────────────────────┐
│  Level 1: YAML Frontmatter (~100 tokens)                   │
│  - 始终加载，用于判断 Skill 是否相关                          │
├─────────────────────────────────────────────────────────────┤
│  Level 2: SKILL.md 正文 (<5k tokens)                       │
│  - 当 Skill 相关时加载完整指令                                │
├─────────────────────────────────────────────────────────────┤
│  Level 3: 链接文件 (scripts/, references/, assets/)         │
│  - 按需加载，仅在需要时访问                                   │
└─────────────────────────────────────────────────────────────┘
```

### 标准 Skill 文件结构

```
your-skill-name/
├── SKILL.md              # 必需 - 主技能文件
├── scripts/              # 可选 - 可执行代码
│   └── helper.py
├── references/           # 可选 - 文档参考
│   └── api-guide.md
└── assets/               # 可选 - 模板、资源
    └── template.json
```

### YAML Frontmatter 格式

```yaml
---
name: skill-name-in-kebab-case
description: 技能描述 + 触发条件（必需，<1024字符）
license: MIT                    # 可选
compatibility: Claude.ai, API   # 可选
metadata:                       # 可选
  author: Your Name
  version: 1.0.0
  category: productivity
---
```

---

## 最受欢迎的 Skills 分析

根据代码库活跃度、社区反馈和实用性，以下是**最受欢迎的 Skills 排行榜**：

### 🏆 Top 10 受欢迎 Skills

| 排名 | Skill 名称 | 类别 | 受欢迎指数 |
|------|------------|------|------------|
| 1 | **pdf** | 文档处理 | ⭐⭐⭐⭐⭐ |
| 2 | **xlsx** | 文档处理 | ⭐⭐⭐⭐⭐ |
| 3 | **docx** | 文档处理 | ⭐⭐⭐⭐⭐ |
| 4 | **pptx** | 文档处理 | ⭐⭐⭐⭐⭐ |
| 5 | **mcp-builder** | 开发工具 | ⭐⭐⭐⭐ |
| 6 | **artifacts-builder** | 创意设计 | ⭐⭐⭐⭐ |
| 7 | **skill-creator** | 元技能 | ⭐⭐⭐⭐ |
| 8 | **webapp-testing** | 开发工具 | ⭐⭐⭐ |
| 9 | **frontend-design** | 创意设计 | ⭐⭐⭐ |
| 10 | **brand-guidelines** | 企业级 | ⭐⭐⭐ |

### 受欢迎原因分析

#### 文档类 Skills (pdf, xlsx, docx, pptx) 霸榜原因：

1. **高频使用场景**: 办公文档处理是最常见的AI应用场景
2. **生产环境验证**: 这些是 Claude 官方内置的文档能力底层实现
3. **功能全面**: 覆盖创建、编辑、分析、提取等完整工作流
4. **企业需求强**: 符合企业办公自动化的核心需求

#### 开发工具类 Skills 受欢迎原因：

1. **技术用户群体**: 开发者是 Claude 的核心用户群
2. **效率提升明显**: 可显著减少重复性开发工作
3. **与 MCP 生态结合**: 提供更强大的集成能力

---

## 最有用的 Skills 详细介绍

### 1. 📄 PDF Skill - 综合性最强的文档技能

**完整描述**: Comprehensive PDF manipulation toolkit for extracting text and tables, creating new PDFs, merging/splitting documents, and handling forms.

**核心功能**:
- ✅ 文本和表格提取
- ✅ 创建新 PDF 文档
- ✅ 合并/拆分文档
- ✅ 表单处理
- ✅ 元数据操作

**使用场景**:
```
用户说: "帮我提取这个PDF中的所有表格数据"
用户说: "把这些PDF文件合并成一个"
用户说: "读取这个PDF表单并填写"
```

**技术亮点**:
- 使用 `pypdfium2` 进行高质量渲染
- 支持复杂表格识别
- 表单字段自动检测

---

### 2. 📊 XLSX Skill - 数据分析利器

**完整描述**: Create, edit, and analyze Excel spreadsheets with support for formulas, formatting, data analysis, and visualization.

**核心功能**:
- ✅ 创建/编辑 Excel 文件
- ✅ 公式支持与计算
- ✅ 数据格式化
- ✅ 数据分析与可视化
- ✅ 图表生成

**使用场景**:
```
用户说: "根据这份数据创建一个销售分析报表"
用户说: "给这个表格添加条件格式"
用户说: "生成数据透视表"
```

**技术亮点**:
- 完整公式引擎支持
- 多图表类型生成
- 数据验证规则

---

### 3. 📝 DOCX Skill - Word 文档专家

**完整描述**: Create, edit, and analyze Word documents with support for tracked changes, comments, formatting preservation, and text extraction.

**核心功能**:
- ✅ 创建/编辑 Word 文档
- ✅ 修订追踪支持
- ✅ 批注功能
- ✅ 格式保留
- ✅ 文本提取

**使用场景**:
```
用户说: "帮我写一份合同文档"
用户说: "对比这两个Word文档的差异"
用户说: "给这段文字添加批注"
```

---

### 4. 📽️ PPTX Skill - 演示文稿生成器

**完整描述**: Create, edit, and analyze PowerPoint presentations with support for layouts, templates, charts, and automated slide generation.

**核心功能**:
- ✅ 幻灯片创建/编辑
- ✅ 布局和模板支持
- ✅ 图表嵌入
- ✅ 自动化幻灯片生成
- ✅ 主题定制

**使用场景**:
```
用户说: "根据这份报告生成一个演示文稿"
用户说: "创建一个产品介绍PPT"
用户说: "把这个数据可视化到幻灯片上"
```

---

### 5. 🔧 MCP-Builder Skill - 集成开发专家

**完整描述**: Guide for creating high-quality MCP (Model Context Protocol) servers that enable LLMs to interact with external services through well-designed tools.

**核心功能**:
- ✅ MCP 服务器创建指导
- ✅ 最佳实践指南
- ✅ API 集成模式
- ✅ 工具设计规范
- ✅ 错误处理模式

**使用场景**:
```
用户说: "帮我创建一个连接 Notion API 的 MCP 服务器"
用户说: "设计一个数据库访问的 MCP 工具"
```

**为什么有用**:
- MCP 是 Claude 扩展的核心协议
- 提供企业级集成能力
- 与 Skills 形成互补（Skills=知识层，MCP=连接层）

---

### 6. 🎨 Artifacts-Builder Skill - 前端构建神器

**完整描述**: Build complex claude.ai HTML artifacts using React, Tailwind CSS, and shadcn/ui components.

**核心功能**:
- ✅ React 组件构建
- ✅ Tailwind CSS 样式
- ✅ shadcn/ui 组件库
- ✅ 响应式设计
- ✅ 现代 UI 模式

**使用场景**:
```
用户说: "创建一个登录页面"
用户说: "设计一个数据仪表盘"
用户说: "构建一个交互式表单"
```

---

### 7. 🛠️ Skill-Creator Skill - 元技能

**完整描述**: Interactive skill creation tool that guides you through building new skills with Q&A.

**核心功能**:
- ✅ 交互式创建向导
- ✅ 自动生成 SKILL.md
- ✅ 最佳实践检查
- ✅ 触发词建议
- ✅ 结构验证

**使用场景**:
```
用户说: "Use the skill-creator to help me build a skill for [your task]"
```

**为什么是"元技能之最"**:
- 可以创建其他所有 Skills
- 降低 Skills 开发门槛
- 内置质量检查

---

### 8. 🧪 Webapp-Testing Skill - 自动化测试

**完整描述**: Test local web applications using Playwright for UI verification and debugging.

**核心功能**:
- ✅ Playwright 自动化
- ✅ UI 验证
- ✅ 截图对比
- ✅ 表单填写测试
- ✅ 数据提取

**使用场景**:
```
用户说: "测试这个网页的登录功能"
用户说: "验证这个表单的提交流程"
用户说: "截取这个页面的截图"
```

---

## 使用场景与推荐

### 按用户类型推荐

| 用户类型 | 推荐 Skills | 理由 |
|----------|-------------|------|
| **办公人员** | pdf, xlsx, docx, pptx | 日常文档处理必备 |
| **开发者** | mcp-builder, webapp-testing, artifacts-builder | 提升开发效率 |
| **设计师** | algorithmic-art, canvas-design, frontend-design | 创意工作支持 |
| **团队管理者** | internal-comms, brand-guidelines | 企业沟通规范 |
| **AI 探索者** | skill-creator | 创建自定义能力 |

### 按任务类型推荐

#### 📊 数据处理任务
```
首选: xlsx, pdf
辅助: docx (报告生成)
```

#### 🎨 内容创作任务
```
首选: canvas-design, artifacts-builder
辅助: brand-guidelines (品牌一致性)
```

#### 🔌 系统集成任务
```
首选: mcp-builder
辅助: skill-creator (创建集成工作流)
```

#### 📋 文档管理任务
```
首选: docx, pdf
辅助: pptx (演示文稿)
```

---

## 社区生态

### 官方 Skills 分布

| 类别 | Skills 数量 | 代表 Skill |
|------|-------------|------------|
| 文档处理 | 4 | pdf, xlsx, docx, pptx |
| 创意设计 | 3 | algorithmic-art, canvas-design, slack-gif-creator |
| 开发工具 | 3 | mcp-builder, artifacts-builder, webapp-testing |
| 企业级 | 2 | brand-guidelines, internal-comms |
| 元技能 | 2 | skill-creator, template-skill |

### 社区贡献的优秀 Skills

| Skill | 来源 | 描述 |
|-------|------|------|
| **obra/superpowers** | 社区 | 20+ 个实战技能库，含 TDD、调试、协作模式 |
| **playwright-skill** | 社区 | 通用浏览器自动化 |
| **ios-simulator-skill** | 社区 | iOS 应用构建与测试 |
| **claude-d3js-skill** | 社区 | D3.js 数据可视化 |
| **web-asset-generator** | 社区 | 生成网站图标、社交媒体图片 |
| **Trail of Bits Security Skills** | 安全研究 | CodeQL/Semgrep 静态分析 |

### Skills vs 其他工具对比

| 特性 | Skills | MCP | System Prompts |
|------|--------|-----|----------------|
| **目的** | 任务专业知识 | 外部数据/API集成 | 一次性指令 |
| **可移植性** | ✅ 跨平台一致 | ⚠️ 需服务器配置 | ❌ 会话特定 |
| **Token 效率** | ✅ 30-50 tokens (元数据) | ⚠️ 视实现而定 | ❌ 始终在上下文 |
| **可维护性** | ✅ 集中更新 | ✅ 版本控制 | ❌ 手动更新 |
| **可组合性** | ✅ 自动叠加 | ✅ 可组合 | ⚠️ 手动组合 |

**最佳实践**: Skills + MCP 结合使用
- MCP 提供"厨房"（工具和原料）
- Skills 提供"食谱"（工作流程）

---

## 总结与建议

### 核心发现

1. **文档处理类 Skills 最具价值**
   - pdf, xlsx, docx, pptx 是生产环境验证的核心能力
   - 覆盖最广泛的办公自动化需求

2. **开发工具类 Skills 效率提升显著**
   - mcp-builder 和 artifacts-builder 大幅减少重复开发
   - 与 MCP 生态结合提供强大扩展性

3. **skill-creator 是最重要的元技能**
   - 降低 Skills 开发门槛
   - 内置最佳实践和质量检查

4. **渐进式披露架构是核心竞争力**
   - 高效的 Token 使用
   - 按需加载机制

### 使用建议

#### 对于个人用户
```
1. 安装文档处理 Skills (pdf, xlsx, docx, pptx)
2. 根据需要安装开发工具 Skills
3. 使用 skill-creator 创建个性化 Skills
```

#### 对于团队
```
1. 统一部署核心 Skills
2. 创建团队专属 Skills (品牌规范、工作流程)
3. 建立内部 Skills 版本管理
```

#### 对于企业
```
1. 评估安全风险后部署
2. 建立技能审核流程
3. 监控 Skill 使用效果
```

### 安装方式

#### Claude Code
```bash
# 注册官方 Skills 市场
/plugin marketplace add anthropics/skills

# 安装文档处理技能包
/plugin install document-skills@anthropic-agent-skills

# 安装示例技能包
/plugin install example-skills@anthropic-agent-skills
```

#### Claude.ai
1. 设置 > 功能 > Skills
2. 浏览可用技能或上传自定义技能
3. 启用所需技能

### 安全提醒

⚠️ **重要安全注意事项**:
- 只安装来自可信来源的 Skills
- 启用前审查 SKILL.md 和所有脚本
- 警惕请求敏感数据访问的 Skills
- 在生产环境部署前仔细审计

---

## 附录

### A. 完整 Skills 列表

#### 文档处理类
| Skill | 功能描述 |
|-------|----------|
| pdf | PDF 创建、编辑、提取、合并、表单处理 |
| xlsx | Excel 创建、编辑、公式、数据分析、可视化 |
| docx | Word 文档创建、编辑、修订追踪、批注 |
| pptx | PowerPoint 创建、编辑、模板、图表 |

#### 创意设计类
| Skill | 功能描述 |
|-------|----------|
| algorithmic-art | 使用 p5.js 创建生成艺术 |
| canvas-design | 使用设计理念创建视觉艺术 |
| slack-gif-creator | 创建 Slack 优化的 GIF 动画 |
| frontend-design | 避免AI风格，创建独特前端设计 |

#### 开发工具类
| Skill | 功能描述 |
|-------|----------|
| mcp-builder | MCP 服务器创建指南 |
| artifacts-builder | React + Tailwind + shadcn/ui 组件构建 |
| webapp-testing | Playwright 自动化测试 |

#### 企业级类
| Skill | 功能描述 |
|-------|----------|
| brand-guidelines | 应用 Anthropic 品牌规范 |
| internal-comms | 内部沟通文档撰写 |
| theme-factory | 10种预设主题或自定义主题 |

#### 元技能类
| Skill | 功能描述 |
|-------|----------|
| skill-creator | 交互式 Skill 创建向导 |
| template-skill | Skill 模板起点 |

### B. 参考资源

- [官方代码库](https://github.com/anthropics/skills)
- [Agent Skills 规范](https://agentskills.io)
- [Skills API 文档](https://docs.claude.com/en/api/skills-guide)
- [MCP 文档](https://modelcontextprotocol.io)
- [社区 Skills 列表](https://github.com/travisvn/awesome-claude-skills)

---

**报告结束**

*本报告基于 2026年2月 的代码库状态生成，Skills 生态持续更新中，建议定期查看官方获取最新信息。*
