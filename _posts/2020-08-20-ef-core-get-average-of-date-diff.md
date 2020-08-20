---
title: Entity Framework Core 查詢日期相差的平均值
categories:
- Entity Framework 
---

有個需求是要統計事件的起始時間與結束時間差的平均值，原本想說下面這個寫法可以動：

```csharp
var result = _db.Event
    .GroupBy(x => x.Name)
    .Select(x => new
    {
        Name = x.Key,
        Average = x.Average(y => (y.EndTime.Value - y.BeginTime.Value).Days)
    })
    .ToList();
```

結果出現以下錯誤，看來這個寫法是沒辦法順利轉成 SQL

```txt
The LINQ expression '((EntityShaperExpression: 
EntityType: Event
ValueBufferExpression: 
(ProjectionBindingExpression: EmptyProjectionMember)
IsNullable: False
).BeginTime.Value - (EntityShaperExpression: 
EntityType: Event
ValueBufferExpression: 
(ProjectionBindingExpression: EmptyProjectionMember)
IsNullable: False
).EndTime.Value).Days' could not be translated. 
Either rewrite the query in a form that can be translated, or switch to client evaluation explicitly by inserting a call to either AsEnumerable(), AsAsyncEnumerable(), ToList(), or ToListAsync().
See https://go.microsoft.com/fwlink/?linkid=2101038 for more information.
```

最後發現 [DbFunctions](https://docs.microsoft.com/en-us/dotnet/api/microsoft.entityframeworkcore.dbfunctions?view=efcore-3.1) 提供許多的 Datediff 方法

於是將程式碼改成以下寫法，問題解決

```csharp
var result = _db.Event
    .GroupBy(x => x.Name)
    .Select(x => new
    {
        Name = x.Key,
        Average = x.Average(y => EF.Functions.DateDiffDay(y.BeginTime.Value, y.EndTime.Value))
    })
    .ToList();
```

### 相關連結

- [EFCore 2.2 GroupBy Sum and DateDiff](https://stackoverflow.com/questions/54670684/efcore-2-2-groupby-sum-and-datediff)
