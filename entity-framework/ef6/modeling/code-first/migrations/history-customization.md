---
title: 自訂的移轉歷程記錄資料表-EF6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: ed5518f0-a9a6-454e-9e98-a4fa7748c8d0
caps.latest.revision: 3
ms.openlocfilehash: 3805d99be6d37d037096f5c5fb69fd30197c1e91
ms.sourcegitcommit: 390f3a37bc55105ed7cc5b0e0925b7f9c9e80ba6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2018
ms.locfileid: "39120454"
---
# <a name="customizing-the-migrations-history-table"></a><span data-ttu-id="68494-102">自訂的移轉歷程記錄資料表</span><span class="sxs-lookup"><span data-stu-id="68494-102">Customizing the migrations history table</span></span>
> [!NOTE]
> <span data-ttu-id="68494-103">**僅限 EF6 及更新版本** - Entity Framework 6 已引進此頁面中所討論的功能及 API 等等。</span><span class="sxs-lookup"><span data-stu-id="68494-103">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="68494-104">如果您使用的是較早版本，則不適用部分或全部的資訊。</span><span class="sxs-lookup"><span data-stu-id="68494-104">If you are using an earlier version, some or all of the information does not apply.</span></span>

> [!NOTE]
> <span data-ttu-id="68494-105">本文假設您知道如何在基本案例中使用 Code First 移轉。</span><span class="sxs-lookup"><span data-stu-id="68494-105">This article assumes you know how to use Code First Migrations in basic scenarios.</span></span> <span data-ttu-id="68494-106">如果不這麼做，則您將需要閱讀[Code First 移轉](~/ef6/modeling/code-first/migrations/index.md)再繼續進行。</span><span class="sxs-lookup"><span data-stu-id="68494-106">If you don’t, then you’ll need to read [Code First Migrations](~/ef6/modeling/code-first/migrations/index.md) before continuing.</span></span>

## <a name="what-is-migrations-history-table"></a><span data-ttu-id="68494-107">什麼是移轉歷程記錄資料表？</span><span class="sxs-lookup"><span data-stu-id="68494-107">What is Migrations History Table?</span></span>

<span data-ttu-id="68494-108">移轉歷程記錄資料表是使用 Code First 移轉來儲存移轉套用至資料庫的詳細資料的資料表。</span><span class="sxs-lookup"><span data-stu-id="68494-108">Migrations history table is a table used by Code First Migrations to store details about migrations applied to the database.</span></span> <span data-ttu-id="68494-109">在資料庫中資料表的名稱是預設\_ \_MigrationHistory 它也會建立和套用第一個移轉執行資料庫時。</span><span class="sxs-lookup"><span data-stu-id="68494-109">By default the name of the table in the database is \_\_MigrationHistory and it is created when applying the first migration do the database.</span></span> <span data-ttu-id="68494-110">Entity Framework 5 在這個資料表會是系統資料表，如果應用程式使用 Microsoft Sql Server 資料庫。</span><span class="sxs-lookup"><span data-stu-id="68494-110">In Entity Framework 5 this table was a system table if the application used Microsoft Sql Server database.</span></span> <span data-ttu-id="68494-111">這已但是變更在 Entity Framework 6 中，移轉歷程記錄資料表不再標示為系統資料表。</span><span class="sxs-lookup"><span data-stu-id="68494-111">This has changed in Entity Framework 6 however and the migrations history table is no longer marked a system table.</span></span>

## <a name="why-customize-migrations-history-table"></a><span data-ttu-id="68494-112">為何要進行自訂的移轉歷程記錄資料表嗎？</span><span class="sxs-lookup"><span data-stu-id="68494-112">Why customize Migrations History Table?</span></span>

<span data-ttu-id="68494-113">移轉歷程記錄資料表應該僅由 Code First 移轉，並將它手動變更可能會中斷移轉。</span><span class="sxs-lookup"><span data-stu-id="68494-113">Migrations history table is supposed to be used solely by Code First Migrations and changing it manually can break migrations.</span></span> <span data-ttu-id="68494-114">不過有時候預設設定不適合，資料表需要進行自訂，例如：</span><span class="sxs-lookup"><span data-stu-id="68494-114">However sometimes the default configuration is not suitable and the table needs to be customized, for instance:</span></span>

-   <span data-ttu-id="68494-115">您需要變更名稱和 （或） 要啟用 3 的資料行的 facet<sup>rd</sup>合作對象移轉提供者</span><span class="sxs-lookup"><span data-stu-id="68494-115">You need to change names and/or facets of the columns to enable a 3<sup>rd</sup> party Migrations provider</span></span>
-   <span data-ttu-id="68494-116">您想要變更資料表的名稱</span><span class="sxs-lookup"><span data-stu-id="68494-116">You want to change the name of the table</span></span>
-   <span data-ttu-id="68494-117">您需要使用非預設結構描述\_ \_MigrationHistory 資料表</span><span class="sxs-lookup"><span data-stu-id="68494-117">You need to use a non-default schema for the \_\_MigrationHistory table</span></span>
-   <span data-ttu-id="68494-118">您需要儲存內容的特定版本的其他資料，因此您需要將額外的資料行加入至資料表</span><span class="sxs-lookup"><span data-stu-id="68494-118">You need to store additional data for a given version of the context and therefore you need to add an additional column to the table</span></span>

## <a name="words-of-precaution"></a><span data-ttu-id="68494-119">字組的預防措施</span><span class="sxs-lookup"><span data-stu-id="68494-119">Words of precaution</span></span>

<span data-ttu-id="68494-120">變更移轉歷程記錄資料表是功能強大，但您需要小心不濫用。</span><span class="sxs-lookup"><span data-stu-id="68494-120">Changing the migration history table is powerful but you need to be careful to not overdo it.</span></span> <span data-ttu-id="68494-121">EF 執行階段目前不會檢查自訂的移轉歷程記錄資料表是否與執行階段相容。</span><span class="sxs-lookup"><span data-stu-id="68494-121">EF runtime currently does not check whether the customized migrations history table is compatible with the runtime.</span></span> <span data-ttu-id="68494-122">如果不是您的應用程式可能會在執行階段中斷或非預期的方式運作。</span><span class="sxs-lookup"><span data-stu-id="68494-122">If it is not your application may break at runtime or behave in unpredictable ways.</span></span> <span data-ttu-id="68494-123">這是更重要的是如果您使用每個資料庫的多個內容在這種情況下多個內容可以使用相同的移轉歷程記錄資料表來儲存移轉的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="68494-123">This is even more important if you use multiple contexts per database in which case multiple contexts can use the same migration history table to store information about migrations.</span></span>

## <a name="how-to-customize-migrations-history-table"></a><span data-ttu-id="68494-124">如何自訂移轉歷程記錄資料表？</span><span class="sxs-lookup"><span data-stu-id="68494-124">How to customize Migrations History Table?</span></span>

<span data-ttu-id="68494-125">開始之前要知道，只會在套用第一次移轉之前，您可以自訂的移轉歷程記錄資料表。</span><span class="sxs-lookup"><span data-stu-id="68494-125">Before you start you need to know that you can customize the migrations history table only before you apply the first migration.</span></span> <span data-ttu-id="68494-126">現在，程式碼所示。</span><span class="sxs-lookup"><span data-stu-id="68494-126">Now, to the code.</span></span>

<span data-ttu-id="68494-127">首先，您必須建立衍生自 System.Data.Entity.Migrations.History.HistoryContext 類別的類別。</span><span class="sxs-lookup"><span data-stu-id="68494-127">First, you will need to create a class derived from System.Data.Entity.Migrations.History.HistoryContext class.</span></span> <span data-ttu-id="68494-128">HistoryContext 類別被衍生自 DbContext 類別中，所以設定移轉歷程記錄資料表非常類似於使用 fluent API 設定 EF 模型。</span><span class="sxs-lookup"><span data-stu-id="68494-128">The HistoryContext class is derived from the DbContext class so configuring the migrations history table is very similar to configuring EF models with fluent API.</span></span> <span data-ttu-id="68494-129">您只需要覆寫在 OnModelCreating 方法，並使用 fluent API 來設定資料表。</span><span class="sxs-lookup"><span data-stu-id="68494-129">You just need to override the OnModelCreating method and use fluent API to configure the table.</span></span>

>[!NOTE]
> <span data-ttu-id="68494-130">通常當您設定 EF 模型不需要呼叫基底。從覆寫的 OnModelCreating 方法因為 DbContext.OnModelCreating() 具有空白主體的 onmodelcreating （)。</span><span class="sxs-lookup"><span data-stu-id="68494-130">Typically when you configure EF models you don’t need to call base.OnModelCreating() from the overridden OnModelCreating method since the DbContext.OnModelCreating() has empty body.</span></span> <span data-ttu-id="68494-131">這不是大小寫設定的移轉歷程記錄資料表時。</span><span class="sxs-lookup"><span data-stu-id="68494-131">This is not the case when configuring the migrations history table.</span></span> <span data-ttu-id="68494-132">在此情況下第一個 onmodelcreating （） 覆寫中的做法是呼叫基底。在 onmodelcreating （)。</span><span class="sxs-lookup"><span data-stu-id="68494-132">In this case the first thing to do in your OnModelCreating() override is to actually call base.OnModelCreating().</span></span> <span data-ttu-id="68494-133">這會設定移轉歷程記錄資料表，然後調整覆寫方法中的預設方式。</span><span class="sxs-lookup"><span data-stu-id="68494-133">This will configure the migrations history table in the default way which you then tweak in the overriding method.</span></span>

<span data-ttu-id="68494-134">例如，假設您想要重新命名移轉歷程記錄資料表，並將它放至稱為"admin"的自訂結構描述。</span><span class="sxs-lookup"><span data-stu-id="68494-134">Let’s say you want to rename the migrations history table and put it to a custom schema called “admin”.</span></span> <span data-ttu-id="68494-135">此外，想要您重新命名 MigrationId 資料行移轉至您的 DBA\_識別碼。</span><span class="sxs-lookup"><span data-stu-id="68494-135">In addition your DBA would like you to rename the MigrationId column to Migration\_ID.</span></span>  <span data-ttu-id="68494-136">您可以建立下列類別衍生自 HistoryContext 來達到此目的設定：</span><span class="sxs-lookup"><span data-stu-id="68494-136">You could achieve this by creating the following class derived from HistoryContext:</span></span>

``` csharp
    using System.Data.Common;
    using System.Data.Entity;
    using System.Data.Entity.Migrations.History;

    namespace CustomizableMigrationsHistoryTableSample
    {
        public class MyHistoryContext : HistoryContext
        {
            public MyHistoryContext(DbConnection dbConnection, string defaultSchema)
                : base(dbConnection, defaultSchema)
            {
            }

            protected override void OnModelCreating(DbModelBuilder modelBuilder)
            {
                base.OnModelCreating(modelBuilder);
                modelBuilder.Entity<HistoryRow>().ToTable(tableName: "MigrationHistory", schemaName: "admin");
                modelBuilder.Entity<HistoryRow>().Property(p => p.MigrationId).HasColumnName("Migration_ID");
            }
        }
    }
```

<span data-ttu-id="68494-137">您自訂的 HistoryContext 準備就緒後，您需要讓 EF 知道它來註冊它透過[程式碼為基礎的組態](http://msdn.com/data/jj680699):</span><span class="sxs-lookup"><span data-stu-id="68494-137">Once your custom HistoryContext is ready you need to make EF aware of it by registering it via [code-based configuration](http://msdn.com/data/jj680699):</span></span>

``` csharp
    using System.Data.Entity;

    namespace CustomizableMigrationsHistoryTableSample
    {
        public class ModelConfiguration : DbConfiguration
        {
            public ModelConfiguration()
            {
                this.SetHistoryContext("System.Data.SqlClient",
                    (connection, defaultSchema) => new MyHistoryContext(connection, defaultSchema));
            }
        }
    }
```

<span data-ttu-id="68494-138">就差不多就是這麼簡單。</span><span class="sxs-lookup"><span data-stu-id="68494-138">That’s pretty much it.</span></span> <span data-ttu-id="68494-139">現在您可以移至 Package Manager Console Enable-migrations，新增移轉並最後更新資料庫。</span><span class="sxs-lookup"><span data-stu-id="68494-139">Now you can go to the Package Manager Console, Enable-Migrations, Add-Migration and finally Update-Database.</span></span> <span data-ttu-id="68494-140">這應該會導致將加入資料庫的移轉歷程記錄資料表設定根據您指定 HistoryContext 衍生類別中的詳細資料。</span><span class="sxs-lookup"><span data-stu-id="68494-140">This should result in adding to the database a migrations history table configured according to the details you specified in your HistoryContext derived class.</span></span>

![資料庫](~/ef6/media/database.png)