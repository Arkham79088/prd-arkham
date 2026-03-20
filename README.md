# PRD Arkham

面向产品经理的 AI 需求管理 Skill，将原始需求（口头描述、Word 文档、设计截图）标准化整理为结构化 PRD 和 HTML 交互原型。

支持 **Claude Cowork** 和 **Cursor** 两种使用方式。

---

## 功能

- **需求拆解确认**：写 PRD 前先输出结构化摘要并提问待确认事项，避免理解偏差
- **PRD 自动生成**：按区域/组件结构化格式输出，含需求边界、功能详述、交互规范、异常处理
- **HTML 原型生成**：Tailwind CSS 单文件原型，含空状态、骨架屏、错误路径等完整状态
- **微交互覆盖清单**：按需检查按钮、表单、列表、弹窗、异常降级等交互维度
- **增量版本管理**：v1.0 为完整基线，v2.0+ 只写差异，保留变更前后对照
- **项目层级管理**：市场 → 系统 → 模块 → 需求 → 版本，支持多市场多系统并行
- **APP 需求专项**：自动识别 APP 端需求，按需补充埋点整理、灰度发布节奏、分流逻辑、BI 数据提取
- **零推断原则**：原始需求未明确的场景一律提问，不自行填入 PRD

---

## 安装

### Claude Cowork / claude.ai

下载 [`prd-arkham.skill`](./专业PRD文档/prd-arkham.skill)，点击「Copy to your skills」安装，重启对话即可使用。

### Cursor

下载 [`prd-arkham.mdc`](./专业PRD文档/prd-arkham.mdc)，放入项目根目录的 `.cursor/rules/` 文件夹下即可生效。文件已完整内嵌所有模板和原型生成指南，无需额外文件。

---

## 文件说明

| 文件 | 用途 |
|------|------|
| `prd-arkham.skill` | Claude Cowork 安装包，一键安装 |
| `prd-arkham.mdc` | Cursor Rules 文件，自包含所有内容 |

---

## License

MIT
