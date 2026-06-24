---
type: knowledge
status: reference
area: 知识
review: monthly
created: 2026-06-18
updated: 2026-06-23
---

# HTML closest 与 data 标识

> 来源：Write Your Future（近期理解/HTML.md）
> 整合日期：2026-06-18

---

## Element.closest()

匹配特定选择器且离当前元素最近的祖先元素，匹配不到返回 `null`。

## 使用 data-type 做业务标识

```HTML
<el-button data-type="A">A</el-button>
<el-button data-type="B">B</el-button>
<el-button data-type="C">C</el-button>
```

```TypeScript
const btnAllFunc = (event: MouseEvent) => {
  const target = event.target as HTMLElement;
  const buttonEl = target.closest(".el-button");
  if (!buttonEl) return;
  const type = buttonEl.getAttribute("data-type");
  console.log(type);
};
```

## 关键认知

> **data-xxx 更适合做业务标识。**

- `vue` 更适合直接绑到 `el-button`，多个 button 除外。vue 本质是**状态驱动视图**，而不是 DOM。
- 当多个同类元素需要统一事件处理时，用 `closest` + `data-xxx` 做委托比逐个绑定更合适。
