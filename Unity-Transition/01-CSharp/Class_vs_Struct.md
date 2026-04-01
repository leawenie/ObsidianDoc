# class vs struct

## 核心区别

- class：引用类型
- struct：值类型（会拷贝）

---

## 关键理解

当从 Dictionary 取出 struct：

```csharp
var item = dict[id];
item.Count += 1;