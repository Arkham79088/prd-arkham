# HTML 原型生成指南

本文件指导 Claude 为每个需求自动生成高质量的 HTML 交互原型。

---

## 界面语言

按以下优先级决定：
1. 读取 `_配置/项目配置.md` 中的「原型界面语言」设置
2. 未配置则根据需求内容自动判断（如需求文字、产品面向的用户群体）
3. 仍不确定则询问用户

`<html>` 标签的 `lang` 属性与界面语言保持一致（如 `zh-CN`、`en`、`es`）。

---

## 技术栈（固定）

```html
<!DOCTYPE html>
<html lang="{界面语言代码}">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>{页面标题}</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css" rel="stylesheet">
</head>
```

所有代码写在**单个 HTML 文件**中（CSS 用 `<style>`，JS 用 `<script>`）。

---

## 布局模式

### Web 后台系统

```
┌──────────────────────────────────────────────────┐
│ 左侧边栏（w-64, bg-slate-800）  │ 顶栏（h-16）   │
│  ├── Logo / 系统名               │ 面包屑 + 用户  │
│  ├── 菜单组                      ├────────────────┤
│  │   ├── 菜单项（当前高亮）       │                │
│  │   └── 菜单项                  │ 内容区         │
│  └── ...                        │ bg-gray-100    │
└──────────────────────────────────────────────────┘
```

- 侧边栏固定，内容区可滚动
- 当前菜单项：`bg-slate-700 border-l-4 border-blue-500`
- 外层容器：`flex h-screen overflow-hidden`

### 移动端 APP

```html
<div class="bg-gray-200 min-h-screen flex justify-center">
  <div class="max-w-[390px] w-full min-h-screen bg-white relative">
    <!-- 顶部导航栏 -->
    <div class="h-12 flex items-center px-4 bg-white border-b sticky top-0 z-10">
      <i class="fa-solid fa-chevron-left text-gray-600"></i>
      <span class="flex-1 text-center font-medium">{页面标题}</span>
    </div>
    <!-- 内容区 -->
    <div class="p-4 pb-24">...</div>
    <!-- 底部吸底按钮（如有） -->
    <div class="fixed bottom-0 left-1/2 -translate-x-1/2 w-full max-w-[390px] p-4 bg-white border-t">
      <button class="w-full py-3 bg-black text-white rounded-xl font-medium">{提交按钮文字}</button>
    </div>
  </div>
</div>
```

- 外层灰色背景模拟手机外壳
- 多屏状态可横向排列展示，用黄色标签标注状态名称

---

## 交互实现模式

### Tab 切换
```javascript
function switchTab(tabId) {
    document.querySelectorAll('[data-tab-content]').forEach(el => el.classList.add('hidden'));
    document.querySelector(`[data-tab-content="${tabId}"]`).classList.remove('hidden');
    document.querySelectorAll('[data-tab-btn]').forEach(btn => {
        btn.classList.toggle('active-tab', btn.dataset.tabBtn === tabId);
    });
}
```

### 弹窗（Modal）
```javascript
function openModal(id) { document.getElementById(id).classList.remove('hidden'); }
function closeModal(id) { document.getElementById(id).classList.add('hidden'); }
```
遮罩层：`fixed inset-0 bg-black/50 flex items-center justify-center z-50`

### Toggle 开关
```css
.toggle-checkbox:checked { right: 0; border-color: #3b82f6; }
.toggle-checkbox:checked + .toggle-label { background-color: #3b82f6; }
.toggle-label { width: 3rem; height: 1.5rem; }
```

### Toast 提示
```javascript
function showToast(message, type = 'success') {
    const toast = document.getElementById('toast');
    toast.textContent = message;
    toast.className = `fixed top-4 right-4 px-4 py-2 rounded-lg text-white z-50 transition-opacity ${
        type === 'success' ? 'bg-green-500' : 'bg-red-500'
    }`;
    toast.classList.remove('hidden');
    setTimeout(() => toast.classList.add('hidden'), 3000);
}
```

### 表单校验（失焦触发）
```javascript
input.addEventListener('blur', () => {
    const value = input.value.trim();
    const error = input.nextElementSibling;
    if (!value) {
        input.classList.add('border-red-500', 'bg-red-50');
        error.textContent = '{错误提示}';
    } else {
        input.classList.remove('border-red-500', 'bg-red-50');
        error.textContent = '';
    }
});
```

### Loading 状态
```javascript
function setLoading(btn, loading) {
    btn.disabled = loading;
    btn.innerHTML = loading
        ? '<i class="fa-solid fa-spinner fa-spin mr-2"></i>处理中...'
        : '{原始文字}';
}
```

---

## 示例数据填充原则

1. **符合目标市场习惯**：语言、货币格式、日期格式、电话格式与项目实际用户一致
2. **真实感**：用真实姓名风格、合理金额区间、近期日期，避免 "test123" 或 "AAA"
3. **覆盖度**：列表页至少展示 5-8 行；包含不同状态的数据（正常/异常/待处理）
4. **表单页**：展示初始态 + 至少一种交互态（已填写/错误/成功）

---

## 状态覆盖原则

### 原则一：全状态渲染

**每个包含数据的组件，原型里必须至少渲染出 2 个可见状态。**

| 组件类型 | 必须渲染的状态 | 实现方式 |
|---------|-------------|---------|
| 列表 / 表格 | 有数据态 + 空状态（图标 + 说明文字） | Tab 或顶部切换按钮在两态间切换 |
| 异步加载列表 | 有数据态 + 骨架屏加载态 | 加个「模拟加载」按钮触发骨架屏 |
| 表单提交按钮 | 正常可点击态 + 提交中（spinner）+ 成功后变化 | `setLoading()` + Toast 组合 |
| 弹窗内列表 | 有数据态 + 空状态（不能只渲染有数据版本） | 弹窗内 Tab 或 toggle 切换 |

空状态样式参考：
```html
<!-- 空状态占位 -->
<div class="flex flex-col items-center justify-center py-16 text-gray-400">
  <i class="fa-regular fa-folder-open text-4xl mb-3"></i>
  <p class="text-sm">{暂无数据说明文字}</p>
</div>
```

骨架屏参考：
```html
<!-- 骨架屏行（重复 3-5 次） -->
<div class="flex items-center space-x-3 p-3 animate-pulse">
  <div class="h-4 bg-gray-200 rounded w-1/4"></div>
  <div class="h-4 bg-gray-200 rounded w-1/3"></div>
  <div class="h-4 bg-gray-200 rounded w-1/6"></div>
</div>
```

### 原则二：关键分支路径可触发

**每个有明确错误场景的交互，原型里必须有对应的失败状态可触发，不只演示成功路径。**

| 交互类型 | 必须提供的失败态 | 实现方式 |
|---------|--------------|---------|
| 表单提交 | 提交失败（红色 Toast + 按钮恢复可点击） | `showToast(msg, 'error')` |
| 表单校验 | 字段错误提示（红色边框 + 错误文字） | 失焦校验逻辑 |
| 权限不足 | 提示文案 / 按钮置灰 + 说明 | 初始态直接渲染置灰版本，旁边加说明 |
| 网络异常 | 重试入口（「加载失败，点击重试」） | 在列表区域渲染一个失败态 + 重试按钮 |

> 实现优先级：成功路径 → 表单校验失败 → 提交失败 → 其余错误态。如果时间有限，前两条是底线。

---

## 配色规范

| 用途 | Tailwind 类 |
|------|------------|
| 主操作色 | `blue-500` / `blue-600` |
| 成功/通过 | `green-500` |
| 警告/待处理 | `yellow-500` / `amber-500` |
| 错误/拒绝 | `red-500` |
| 侧边栏底色 | `slate-800` |
| 页面背景 | `gray-100` |
| 卡片/内容块 | `white` |

如项目有品牌色，优先使用品牌色替代蓝色系。

---

## 页面类型速查

| 页面类型 | 必备元素 |
|---------|---------|
| 列表页 | 筛选栏 + 操作按钮 + 数据表格 + 分页 |
| 表单页 | 字段分组 + 实时/失焦校验 + 提交/取消 |
| 详情页 | 信息卡片 + Tab 内容区 + 操作按钮组 |
| 配置页 | 多 Tab + 开关/矩阵 + 即时保存提示 |
| 弹窗 | 标题 + 内容体 + 底部按钮组 |
| 移动端表单 | 渐进式披露组件 + 吸底提交按钮 |
| 移动端流程 | 步骤进度条 + 多状态横向展示 |
| 仪表盘 | 数据卡片 + 图表 + 快捷操作 |

---

## 生成流程

1. 从 PRD 提取所有页面、功能点和交互规则
2. 判断布局模式（Web 后台 / 移动端 / 其他）
3. 搭建页面结构（侧边栏 + 顶栏 + 内容区，或移动端框架）
4. 填充符合目标市场习惯的示例数据
5. 实现核心交互（Tab / 弹窗 / 表单校验 / Toast 等）
6. 保存为 `原型.html`
7. 如有原始截图，截图与 HTML 原型并存

---

## 生成后自检

- [ ] 所有 Tab 可以切换
- [ ] 弹窗可以打开和关闭
- [ ] 表单有基础校验反馈（错误态 + 错误提示文字）
- [ ] 示例数据符合目标市场语言和格式习惯
- [ ] 界面文字语言与项目配置一致
- [ ] 布局在浏览器中无溢出、无错位
- [ ] 列表类组件有空状态或骨架屏（不只有有数据的版本）
- [ ] 表单提交有失败路径可触发（红色 Toast 或错误提示）
