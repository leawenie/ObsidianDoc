# 知识点概览
> 该系统主要用于练习`C Sharp`基本语法。所涉及的知识点如下。
1.  类基础
2.  `Action`事件基础
3.  `？`运算符
4.  数据结构`字典`基础
5.  结构体基础

# 类以及结构体基础
>类和结构体放在一起讨论。具体内容参见下方链接。
>[类与结构体]: ../01-CSharp/类与结构体
>[[类与结构体]] Obsidian关系图谱

# Action事件基础
>*本模块尚未完全了解，最后更新日期 2026/4/14* 
>其中关于`?`运算符的解释参见链接。
> [问号`?`运算符]: ../01-CSharp\问号运算符
> [[问号运算符]] Obsidian关系图谱

​	`Action`事件通过关键字`event`和选项`Action`创建。例：

```c#
public event Action<> actionName;
```

​	尖括号中可以添加数据类型关键字，用于表示该事件被触发时所携带的参数的类型，同时可以添加多个数据类型关键字，用于表示多个携带参数。例：

```c#
public event Action<int, int> actionName;
```

​	`Action`事件通过下方示例代码发布。其中关于`?`运算符的解释参见链接。

```c#
int arg1, arg2;
public event Action<int, int> actionName;
actionName?.Invoke(arg1, arg2);
```

​	`Action`事件通过下方示例代码订阅。

```c#
//Lambda表达式函数
public event Action<> actionName;
actionName += () => {
    Console.WriteLine($"actionName invoked");
};
//显示函数
public event Action<int, int> actionName;
public void actionNameChanged(int arg1, int arg2){
    Console.WriteLine($"actionName invoked {arg1}, {arg2}");
}
actionName += actionNameChanged;
```

# 数据结构字典基础
>具体内容参见下方链接。
>[字典]: ..\01-CSharp\字典.md
>[[字典]] Obsidian关系图谱

# Backpack_System 代码
```c#
using System;
using System.Collections.Generic;

class Affix
{
    public int Id;
    public float Val;

    public Affix(int id, float val)
    {
        Id = id;
        Val = val;
    }
}

class BackpackItem
{
    public int Count;
    public Affix Affix;

    public BackpackItem(int count, Affix affix)
    {
        Count = count;
        Affix = affix;
    }
}

class Backpack
{
    private Dictionary<int, BackpackItem> items = new Dictionary<int, BackpackItem>();

    public event Action<int, int> BackpackChanged;

    public BackpackItem GetItem(int id)
    {
        if (HasItem(id))
        {
            return items[id];
        }
        return null;
    }

    private void OnBackpackChanged(int itemId, int currentCount)
    {
        BackpackChanged?.Invoke(itemId, currentCount);
    }

    public void AddItem(int id, int count)
    {
        if (count <= 0)
        {
            Console.WriteLine("AddItem 的 count 必须大于 0");
            return;
        }

        if (HasItem(id))
        {
            items[id].Count += count;
        }
        else
        {
            Affix affix = new Affix(1, 0.2f);
            items.Add(id, new BackpackItem(count, affix));
        }

        OnBackpackChanged(id, items[id].Count);
    }

    public void RemoveItem(int id, int count)
    {
        if (count <= 0)
        {
            Console.WriteLine("RemoveItem 的 count 必须大于 0");
            return;
        }

        if (HasItem(id))
        {
            items[id].Count -= count;

            if (items[id].Count <= 0)
            {
                items.Remove(id);
                OnBackpackChanged(id, 0);
            }
            else
            {
                OnBackpackChanged(id, items[id].Count);
            }
        }
        else
        {
            Console.WriteLine($"物品 {id} 不存在");
        }
    }

    public int GetItemCount(int id)
    {
        if (HasItem(id))
        {
            return items[id].Count;
        }

        return 0;
    }

    public bool HasItem(int id)
    {
        return items.ContainsKey(id);
    }

    public void PrintItems()
    {
        Console.WriteLine("当前背包内容：");
        foreach (var pair in items)
        {
            Console.WriteLine(
                $"Id={pair.Key}, Count={pair.Value.Count}, AffixId={pair.Value.Affix.Id}, AffixVal={pair.Value.Affix.Val}"
            );
        }
    }
}

class BackPackView
{
    private void PrintItems(int id, int currentCount)
    {
        Console.WriteLine($"物品 {id} 当前数量为 {currentCount}");
    }

    public BackPackView(Backpack backpack)
    {
        backpack.BackpackChanged += PrintItems;
    }
}

class ItemView
{
    private int care_Id;
    private Backpack backpack;
    private void PrintItems(int id, int currentCount)
    {
        if (care_Id != id)
        {
            return;
        }
        if (backpack.GetItem(id))
        {
            Console.WriteLine($"物品 {id} 已不存在");
            return;
        }
        Console.WriteLine($"物品 {id} 当前数量为 {currentCount}");
    }

    public ItemView(Backpack backpack, int careId)
    {
        care_Id = careId;
        this.backpack = backpack;
        backpack.BackpackChanged += PrintItems;
    }
}

class Program
{
    public static void Main(string[] args)
    {
        Backpack backpack = new Backpack();

        ItemView view1001 = new ItemView(backpack, 1001);
        ItemView view1002 = new ItemView(backpack, 1002);

        backpack.AddItem(1001, 5);
        backpack.AddItem(1001, 3);
        backpack.AddItem(1002, 2);
        backpack.RemoveItem(1001, 4);

        Console.WriteLine($"1001 count = {backpack.GetItemCount(1001)}");
        Console.WriteLine($"1003 count = {backpack.GetItemCount(1003)}");

        backpack.PrintItems();
    }
}
```

