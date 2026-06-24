---
type: knowledge
status: reference
area: 知识
review: monthly
created: 2026-06-18
updated: 2026-06-23
---

# SOLID 原则

> 来源：Write Your Future（设计模式/Index + 单一职责原则 + 开放封闭原则）
> 整合日期：2026-06-18

---

`SOLID` 是面向对象设计和编程中的五个基本原则的缩写：

1. **单一职责原则**（Single Responsibility Principle, SRP）
2. **开放/封闭原则**（Open/Closed Principle, OCP）
3. **里氏替换原则**（Liskov Substitution Principle, LSP）
4. **接口隔离原则**（Interface Segregation Principle, ISP）
5. **依赖反转原则**（Dependency Inversion Principle, DIP）

> 促使创建具有**高内聚、低耦合、易扩展和易维护性**的软件系统。

---

## 单一职责原则（SRP）

要求一个类或者模块**只负责完成一个职责或功能**。

### 反例

```java
class Chef {
  cookDish(dish) {
    // 烹饪菜品的具体实现
  }
  washDishes() {
    // 洗碗的具体实现
  }
}
```

> 这违反了单一职责原则，因为它有两个职责。导致它们可能会互相影响。

### 正例

```java
class Chef {
  cookDish(dish) {
    // 烹饪菜品的具体实现
  }
}

class Dishwasher {
  washDishes() {
    // 洗碗的具体实现
  }
}
```

`Chef` 类专注于烹饪，`Dishwasher` 类专注于洗碗。每个类都有一个单一的职责，代码更清晰、易于理解，在未来变更中更具弹性。

---

## 开放/封闭原则（OCP）

要求软件实体（类、模块、函数等）应该**对扩展开放，对于修改关闭**。

简而言之，一个模块在扩展新功能时**不应该修改原有的代码**，而是通过添加新的代码来实现扩展。

---

## 待补充

- 里氏替换原则（LSP）
- 接口隔离原则（ISP）
- 依赖反转原则（DIP）
