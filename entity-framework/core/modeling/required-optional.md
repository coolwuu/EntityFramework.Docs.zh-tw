---
title: 必要/選用的屬性-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: ddaa0a54-9f43-4c34-aae3-f95c96c69842
uid: core/modeling/required-optional
ms.openlocfilehash: b6716a5b03e1afc2933e317d606ef50f986c22c7
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42995493"
---
# <a name="required-and-optional-properties"></a>必要和選擇性的屬性

屬性會被視為選擇性是否有效，才能包含`null`。 如果`null`不是有效的值指派給屬性，則它會被視為是必要的屬性。

## <a name="conventions"></a>慣例

依照慣例，其 CLR 型別可以包含 null 的屬性會設定為選擇性 (`string`， `int?`， `byte[]`，依此類推。)。 將設定其 CLR 類型不能包含 null 的屬性視需要而定 (`int`， `decimal`， `bool`，依此類推。)。

> [!NOTE]  
> CLR 類型不能包含 null 的屬性無法設定為選擇性。 屬性會一律視為 Entity Framework 所需。

## <a name="data-annotations"></a>資料註釋

您可以使用資料註解表示是必要屬性。

<!-- [!code-csharp[Main](samples/core/Modeling/DataAnnotations/Samples/Required.cs?highlight=4)] -->
``` csharp
public class Blog
{
    public int BlogId { get; set; }
    [Required]
    public string Url { get; set; }
}
```

## <a name="fluent-api"></a>Fluent API

您可以使用 Fluent API，以表示所需的屬性。

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Samples/Required.cs?highlight=7,8,9)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Blog>()
            .Property(b => b.Url)
            .IsRequired();
    }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }
}
```
