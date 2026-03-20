# PRD Arkham Skill（专业版）

适合有一定 AI 使用经验、需要完整需求管理体系的产品团队。

## 与通用版的区别

| 功能 | 通用版 | 专业版 |
|------|--------|--------|
| 需求拆解确认 | ✅ | ✅ |
| PRD + HTML 原型生成 | ✅ | ✅ |
| 项目层级管理（市场/系统/模块） | ❌ | ✅ |
| 需求索引与版本管理 | ❌ | ✅ |
| 增量 PRD（v2.0 只写差异） | ❌ | ✅ |
| APP 专项（埋点/灰度/分流/BI） | ❌ | ✅ |
| 交叉引用与精度地图 | ❌ | ✅ |
| 首次配置 + 启动检测 | ❌ | ✅ |

## 安装方式

### Claude Cowork / claude.ai

下载打包文件后点击「Copy to your skills」安装：

👉 [下载 prd-arkham.skill](./prd-arkham.skill)

### Cursor

将 `SKILL.md` 内容复制到项目的 `.cursorrules` 文件中。

## 文件说明

```
03-pro-skill/
├── SKILL.md                  # Skill 主文件
├── prd-arkham.skill          # 打包安装文件
└── references/
    ├── prd-template.md       # PRD 完整模板
    └── prototype-guide.md    # HTML 原型生成指南
```

## 适合谁

- 多系统、多市场并行的产品团队
- 需要长期维护需求版本历史的 PM
- 有 APP 端需求（埋点、灰度、BI）的团队
