---
title: First 資料註解-EF6 的程式碼
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: 80abefbd-23c9-4fce-9cd3-520e5df9856e
caps.latest.revision: 3
ms.openlocfilehash: 91d1d8c2608df8f7b38e70b565a4225cf10ae21f
ms.sourcegitcommit: bdd06c9a591ba5e6d6a3ec046c80de98f598f3f3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2018
ms.locfileid: "39120574"
---
# <a name="code-first-data-annotations"></a><span data-ttu-id="376b9-102">Code First 資料註解</span><span class="sxs-lookup"><span data-stu-id="376b9-102">Code First Data Annotations</span></span>
> [!NOTE]
> <span data-ttu-id="376b9-103">**EF4.1 及更新版本僅**-功能、 Api、 Entity Framework 4.1 中導入等本頁所述。</span><span class="sxs-lookup"><span data-stu-id="376b9-103">**EF4.1 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 4.1.</span></span> <span data-ttu-id="376b9-104">如果您使用的是較早版本，則不適用部分或全部的資訊。</span><span class="sxs-lookup"><span data-stu-id="376b9-104">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="376b9-105">此頁面上的內容是來自和文章作者： Julie Lerman 原始撰寫 (\<http://thedatafarm.com>)。</span><span class="sxs-lookup"><span data-stu-id="376b9-105">The content on this page is adapted from and article originally written by Julie Lerman (\<http://thedatafarm.com>).</span></span>

<span data-ttu-id="376b9-106">Entity Framework Code First 可讓您使用您自己的網域類別，代表模型 EF 依賴執行查詢時，變更追蹤和更新函式。</span><span class="sxs-lookup"><span data-stu-id="376b9-106">Entity Framework Code First allows you to use your own domain classes to represent the model which EF relies on to perform querying, change tracking and updating functions.</span></span> <span data-ttu-id="376b9-107">程式碼首先會利用稱為慣例優於組態的程式設計模式。</span><span class="sxs-lookup"><span data-stu-id="376b9-107">Code first leverages a programming pattern referred to as convention over configuration.</span></span> <span data-ttu-id="376b9-108">這表示該程式碼首先會假設您的類別都遵循的慣例，EF 會使用。</span><span class="sxs-lookup"><span data-stu-id="376b9-108">What this means is that code first will assume that your classes follow the conventions that EF uses.</span></span> <span data-ttu-id="376b9-109">在此情況下，EF 將能夠運作，它需要執行其作業的詳細資訊。</span><span class="sxs-lookup"><span data-stu-id="376b9-109">In that case, EF will be able to work out the details it needs to do its job.</span></span> <span data-ttu-id="376b9-110">不過，如果您的類別並不遵守這些慣例，您可以將組態新增至您的類別，為 EF 提供所需的資訊。</span><span class="sxs-lookup"><span data-stu-id="376b9-110">However, if your classes do not follow those conventions, you have the ability to add configurations to your classes to provide EF with the information it needs.</span></span>

<span data-ttu-id="376b9-111">程式碼首先提供兩種方式可將這些設定新增至您的類別。</span><span class="sxs-lookup"><span data-stu-id="376b9-111">Code first gives you two ways to add these configurations to your classes.</span></span> <span data-ttu-id="376b9-112">其中一個使用簡單的屬性，稱為 DataAnnotations，另一個是第一次使用程式碼是 Fluent API，可讓您在程式碼中，以命令方式描述設定。</span><span class="sxs-lookup"><span data-stu-id="376b9-112">One is using simple attributes called DataAnnotations and the other is using code first’s Fluent API, which provides you with a way to describe configurations imperatively, in code.</span></span>

<span data-ttu-id="376b9-113">這篇文章將著重在使用 DataAnnotations （在 System.ComponentModel.DataAnnotations 命名空間中），若要設定您的類別 – 反白顯示最常需要的組態。</span><span class="sxs-lookup"><span data-stu-id="376b9-113">This article will focus on using DataAnnotations (in the System.ComponentModel.DataAnnotations namespace) to configure your classes – highlighting the most commonly needed configurations.</span></span> <span data-ttu-id="376b9-114">DataAnnotations 也會辨識由數項.NET 應用程式，例如 ASP.NET MVC 可讓這些應用程式運用相同的註釋的用戶端驗證。</span><span class="sxs-lookup"><span data-stu-id="376b9-114">DataAnnotations are also understood by a number of .NET applications, such as ASP.NET MVC which allows these applications to leverage the same annotations for client-side validations.</span></span>


## <a name="the-model"></a><span data-ttu-id="376b9-115">模型</span><span class="sxs-lookup"><span data-stu-id="376b9-115">The model</span></span>

<span data-ttu-id="376b9-116">我將示範如何撰寫程式碼使用類別的簡單組的第一個 DataAnnotations： 部落格和文章。</span><span class="sxs-lookup"><span data-stu-id="376b9-116">I’ll demonstrate code first DataAnnotations with a simple pair of classes: Blog and Post.</span></span>

``` csharp
    public class Blog
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public string BloggerName { get; set;}
        public virtual ICollection<Post> Posts { get; set; }
    }

    public class Post
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public DateTime DateCreated { get; set; }
        public string Content { get; set; }
        public int BlogId { get; set; }
        public ICollection<Comment> Comments { get; set; }
    }
```

<span data-ttu-id="376b9-117">如有需要，部落格和後置類別方便遵循程式碼的第一個慣例，且需要協助使用它們的 EF 不調整。</span><span class="sxs-lookup"><span data-stu-id="376b9-117">As they are, the Blog and Post classes conveniently follow code first convention and required no tweaks to help EF work with them.</span></span> <span data-ttu-id="376b9-118">但是，您也可以使用 註解，提供詳細資訊以 EF 的類別和它們對應到資料庫。</span><span class="sxs-lookup"><span data-stu-id="376b9-118">But you can also use the annotations to provide more information to EF about the classes and the database that they map to.</span></span>

 

## <a name="key"></a><span data-ttu-id="376b9-119">Key</span><span class="sxs-lookup"><span data-stu-id="376b9-119">Key</span></span>

<span data-ttu-id="376b9-120">Entity Framework 依賴具有索引鍵的值，它會追蹤實體的每個實體。</span><span class="sxs-lookup"><span data-stu-id="376b9-120">Entity Framework relies on every entity having a key value that it uses for tracking entities.</span></span> <span data-ttu-id="376b9-121">其中一個程式碼首先取決於的慣例是如何表示哪一個屬性是在每個程式碼的第一個類別中的索引鍵。</span><span class="sxs-lookup"><span data-stu-id="376b9-121">One of the conventions that code first depends on is how it implies which property is the key in each of the code first classes.</span></span> <span data-ttu-id="376b9-122">該慣例是尋找一個名為 「 識別碼 」，或結合了 「 識別碼 」，例如 「 BlogId"與類別名稱的其中一個屬性。</span><span class="sxs-lookup"><span data-stu-id="376b9-122">That convention is to look for a property named “Id” or one that combines the class name and “Id”, such as “BlogId”.</span></span> <span data-ttu-id="376b9-123">屬性會對應至資料庫中的主索引鍵資料行。</span><span class="sxs-lookup"><span data-stu-id="376b9-123">The property will map to a primary key column in the database.</span></span>

<span data-ttu-id="376b9-124">部落格和後置類別都遵循這個慣例。</span><span class="sxs-lookup"><span data-stu-id="376b9-124">The Blog and Post classes both follow this convention.</span></span> <span data-ttu-id="376b9-125">但是，如果它們並未呢？</span><span class="sxs-lookup"><span data-stu-id="376b9-125">But what if they didn’t?</span></span> <span data-ttu-id="376b9-126">如果部落格已使用名稱*PrimaryTrackingKey*改為或甚至*foo*嗎？</span><span class="sxs-lookup"><span data-stu-id="376b9-126">What if Blog used the name *PrimaryTrackingKey* instead or even *foo*?</span></span> <span data-ttu-id="376b9-127">如果程式碼第一次找不到符合此慣例的屬性則會因為 Entity Framework 的需求，您必須有索引鍵屬性來擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="376b9-127">If code first does not find a property that matches this convention it will throw an exception because of Entity Framework’s requirement that you must have a key property.</span></span> <span data-ttu-id="376b9-128">您可以使用索引鍵的註解來指定要用作 EntityKey 的哪一個屬性。</span><span class="sxs-lookup"><span data-stu-id="376b9-128">You can use the key annotation to specify which property is to be used as the EntityKey.</span></span>

``` csharp
    public class Blog
    {
        [Key]
        public int PrimaryTrackingKey { get; set; }
        public string Title { get; set; }
        public string BloggerName { get; set;}
        public virtual ICollection<Post> Posts { get; set; }
    }
```

<span data-ttu-id="376b9-129">如果您是第一次使用程式碼是資料庫產生功能、 部落格資料表擁有名為 PrimaryTrackingKey 也定義預設做為身分識別的主要索引鍵資料行。</span><span class="sxs-lookup"><span data-stu-id="376b9-129">If you are using code first’s database generation feature, the Blog table will have a primary key column named PrimaryTrackingKey which is also defined as Identity by default.</span></span>

![jj591583_figure01](~/ef6/media/jj591583-figure01.png)

### <a name="composite-keys"></a><span data-ttu-id="376b9-131">複合索引鍵</span><span class="sxs-lookup"><span data-stu-id="376b9-131">Composite keys</span></span>

<span data-ttu-id="376b9-132">Entity Framework 支援複合索引鍵-多個屬性所組成的主索引鍵。</span><span class="sxs-lookup"><span data-stu-id="376b9-132">Entity Framework supports composite keys - primary keys that consist of more than one property.</span></span> <span data-ttu-id="376b9-133">例如，您可能會有的 Passport 類別，其主索引鍵為 PassportNumber 和 IssuingCountry 的組合。</span><span class="sxs-lookup"><span data-stu-id="376b9-133">For example, your could have a Passport class whose primary key is a combination of PassportNumber and IssuingCountry.</span></span>

``` csharp
    public class Passport
    {
        [Key]
        public int PassportNumber { get; set; }
        [Key]
        public string IssuingCountry { get; set; }
        public DateTime Issued { get; set; }
        public DateTime Expires { get; set; }
    }
```

<span data-ttu-id="376b9-134">如果您嘗試並使用上述的類別，您會得到 InvalidOperationExceptions 指出; EF 模型中</span><span class="sxs-lookup"><span data-stu-id="376b9-134">If you were to try and use the above class in your EF model you wuld get an InvalidOperationExceptions stating;</span></span>

<span data-ttu-id="376b9-135">*無法判斷複合主索引鍵排序類型 'Passport'。使用 ColumnAttribute 或 HasKey 方法來指定複合主索引鍵的順序。*</span><span class="sxs-lookup"><span data-stu-id="376b9-135">*Unable to determine composite primary key ordering for type 'Passport'. Use the ColumnAttribute or the HasKey method to specify an order for composite primary keys.*</span></span>

<span data-ttu-id="376b9-136">當您有複合索引鍵時，Entity Framework 會要求您定義的索引鍵屬性的順序。</span><span class="sxs-lookup"><span data-stu-id="376b9-136">When you have composite keys, Entity Framework requires you to define an order of the key properties.</span></span> <span data-ttu-id="376b9-137">您可以使用資料行註解，以指定的順序。</span><span class="sxs-lookup"><span data-stu-id="376b9-137">You can do this using the Column annotation to specify an order.</span></span>

>[!NOTE]
> <span data-ttu-id="376b9-138">將順序值是相對的 （而非以索引為基礎） 讓您可以使用任何值。</span><span class="sxs-lookup"><span data-stu-id="376b9-138">The order value is relative (rather than index based) so any values can be used.</span></span> <span data-ttu-id="376b9-139">例如，100 和 200 可接受來取代 1 和 2。</span><span class="sxs-lookup"><span data-stu-id="376b9-139">For example, 100 and 200 would be acceptable in place of 1 and 2.</span></span>

``` csharp
    public class Passport
    {
        [Key]
        [Column(Order=1)]
        public int PassportNumber { get; set; }
        [Key]
        [Column(Order = 2)]
        public string IssuingCountry { get; set; }
        public DateTime Issued { get; set; }
        public DateTime Expires { get; set; }
    }
```

<span data-ttu-id="376b9-140">如果您有複合外部索引鍵的實體，您必須指定相同的資料行排序，用於對應的主索引鍵屬性。</span><span class="sxs-lookup"><span data-stu-id="376b9-140">If you have entities with composite foreign keys then you must specify the same column ordering that you used for the corresponding primary key properties.</span></span>

<span data-ttu-id="376b9-141">只有的相對順序內外部索引鍵屬性必須是相同的實際的值指派給**順序**不需要比對。</span><span class="sxs-lookup"><span data-stu-id="376b9-141">Only the relative ordering within the foreign key properties needs to be the same, the exact values assigned to **Order** do not need to match.</span></span> <span data-ttu-id="376b9-142">例如，在下列類別中，3 和 4 可能可用來取代 1 和 2。</span><span class="sxs-lookup"><span data-stu-id="376b9-142">For example, in the following class, 3 and 4 could be used in place of 1 and 2.</span></span>

``` csharp
    public class PassportStamp
    {
        [Key]
        public int StampId { get; set; }
        public DateTime Stamped { get; set; }
        public string StampingCountry { get; set; }

        [ForeignKey("Passport")]
        [Column(Order = 1)]
        public int PassportNumber { get; set; }

        [ForeignKey("Passport")]
        [Column(Order = 2)]
        public string IssuingCountry { get; set; }

        public Passport Passport { get; set; }
    }
```

## <a name="required"></a><span data-ttu-id="376b9-143">必要</span><span class="sxs-lookup"><span data-stu-id="376b9-143">Required</span></span>

<span data-ttu-id="376b9-144">必要的註釋會告知 EF 所需之特定的屬性。</span><span class="sxs-lookup"><span data-stu-id="376b9-144">The Required annotation tells EF that a particular property is required.</span></span>

<span data-ttu-id="376b9-145">加入所需的 Title 屬性會強制 EF （和 MVC） 以確保屬性中沒有資料。</span><span class="sxs-lookup"><span data-stu-id="376b9-145">Adding Required to the Title property will force EF (and MVC) to ensure that the property has data in it.</span></span>

``` csharp
    [Required]
    public string Title { get; set; }
```

<span data-ttu-id="376b9-146">任何其他含應用程式中的程式碼或標記變更，在 MVC 應用程式將會執行用戶端驗證，甚至以動態方式建置訊息使用的屬性和註釋名稱。</span><span class="sxs-lookup"><span data-stu-id="376b9-146">With no additional no code or markup changes in the application, an MVC application will perform client side validation, even dynamically building a message using the property and annotation names.</span></span>

![jj591583_figure02](~/ef6/media/jj591583-figure02.png)

<span data-ttu-id="376b9-148">必要的屬性也會影響所產生的資料庫，藉由對應的內容不可為 null。</span><span class="sxs-lookup"><span data-stu-id="376b9-148">The Required attribute will also affect the generated database by making the mapped property non-nullable.</span></span> <span data-ttu-id="376b9-149">請注意，[標題] 欄位已變更為"not null"。</span><span class="sxs-lookup"><span data-stu-id="376b9-149">Notice that the Title field has changed to “not null”.</span></span>

>[!NOTE]
> <span data-ttu-id="376b9-150">在某些情況下它可能無法在資料庫中是不可為 null，即使需要此屬性，資料行。</span><span class="sxs-lookup"><span data-stu-id="376b9-150">In some cases it may not be possible for the column in the database to be non-nullable even though the property is required.</span></span> <span data-ttu-id="376b9-151">比方說，當使用 TPH 繼承策略資料的多個類型會儲存在單一資料表。</span><span class="sxs-lookup"><span data-stu-id="376b9-151">For example, when using a TPH inheritance strategy data for multiple types is stored in a single table.</span></span> <span data-ttu-id="376b9-152">如果衍生型別包含必要的屬性資料行無法進行不可為 null 因為並非所有的型別階層架構中會有這個屬性。</span><span class="sxs-lookup"><span data-stu-id="376b9-152">If a derived type includes a required property the column cannot be made non-nullable since not all types in the hierarchy will have this property.</span></span>

 

![jj591583_figure03](~/ef6/media/jj591583-figure03.png)

 

## <a name="maxlength-and-minlength"></a><span data-ttu-id="376b9-154">MaxLength 與 MinLength</span><span class="sxs-lookup"><span data-stu-id="376b9-154">MaxLength and MinLength</span></span>

<span data-ttu-id="376b9-155">MaxLength 與 MinLength 屬性可讓您指定額外的屬性驗證，就像您一樣需要。</span><span class="sxs-lookup"><span data-stu-id="376b9-155">The MaxLength and MinLength attributes allow you to specify additional property validations, just as you did with Required.</span></span>

<span data-ttu-id="376b9-156">以下是 BloggerName 長度需求。</span><span class="sxs-lookup"><span data-stu-id="376b9-156">Here is the BloggerName with length requirements.</span></span> <span data-ttu-id="376b9-157">此範例也示範如何結合屬性。</span><span class="sxs-lookup"><span data-stu-id="376b9-157">The example also demonstrates how to combine attributes.</span></span>

``` csharp
    [MaxLength(10),MinLength(5)]
    public string BloggerName { get; set; }
```

<span data-ttu-id="376b9-158">MaxLength 註解會影響資料庫，藉由設定屬性的長度為 10。</span><span class="sxs-lookup"><span data-stu-id="376b9-158">The MaxLength annotation will impact the database by setting the property’s length to 10.</span></span>

![jj591583_figure04](~/ef6/media/jj591583-figure04.png)

<span data-ttu-id="376b9-160">MVC 用戶端註釋和 EF 4.1 伺服器端註解將會同時採用這項驗證，再以動態方式建立一則錯誤訊息: 「 BloggerName 欄位必須是字串或陣列類型最大長度為 '10'。 」該訊息是有點長。</span><span class="sxs-lookup"><span data-stu-id="376b9-160">MVC client-side annotation and EF 4.1 server-side annotation will both honor this validation, again dynamically building an error message: “The field BloggerName must be a string or array type with a maximum length of '10'.” That message is a little long.</span></span> <span data-ttu-id="376b9-161">許多註釋可讓您與 ErrorMessage 屬性中指定的錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="376b9-161">Many annotations let you specify an error message with the ErrorMessage attribute.</span></span>

``` csharp
    [MaxLength(10, ErrorMessage="BloggerName must be 10 characters or less"),MinLength(5)]
    public string BloggerName { get; set; }
```

<span data-ttu-id="376b9-162">您也可以指定錯誤訊息所需的註解中。</span><span class="sxs-lookup"><span data-stu-id="376b9-162">You can also specify ErrorMessage in the Required annotation.</span></span>

![jj591583_figure05](~/ef6/media/jj591583-figure05.png)

 

## <a name="notmapped"></a><span data-ttu-id="376b9-164">NotMapped</span><span class="sxs-lookup"><span data-stu-id="376b9-164">NotMapped</span></span>

<span data-ttu-id="376b9-165">程式碼的第一個慣例會要求每個支援的資料類型的屬性會以資料庫中。</span><span class="sxs-lookup"><span data-stu-id="376b9-165">Code first convention dictates that every property that is of a supported data type is represented in the database.</span></span> <span data-ttu-id="376b9-166">但這不一定在您的應用程式。</span><span class="sxs-lookup"><span data-stu-id="376b9-166">But this isn’t always the case in your applications.</span></span> <span data-ttu-id="376b9-167">例如，您可能會建立標題和 BloggerName 欄位為基礎的程式碼的部落格類別中有屬性。</span><span class="sxs-lookup"><span data-stu-id="376b9-167">For example you might have a property in the Blog class that creates a code based on the Title and BloggerName fields.</span></span> <span data-ttu-id="376b9-168">該屬性會自動建立，而且不需要儲存。</span><span class="sxs-lookup"><span data-stu-id="376b9-168">That property can be created dynamically and does not need to be stored.</span></span> <span data-ttu-id="376b9-169">您可以將不會對應至 NotMapped 註釋，例如此 BlogCode 屬性具有任何屬性的標記。</span><span class="sxs-lookup"><span data-stu-id="376b9-169">You can mark any properties that do not map to the database with the NotMapped annotation such as this BlogCode property.</span></span>

``` csharp
    [NotMapped]
    public string BlogCode
    {
        get
        {
            return Title.Substring(0, 1) + ":" + BloggerName.Substring(0, 1);
        }
    }
```

 

## <a name="complextype"></a><span data-ttu-id="376b9-170">ComplexType</span><span class="sxs-lookup"><span data-stu-id="376b9-170">ComplexType</span></span>

<span data-ttu-id="376b9-171">不常見描述您的領域實體透過一組類別，然後層這些類別來描述完整的實體。</span><span class="sxs-lookup"><span data-stu-id="376b9-171">It’s not uncommon to describe your domain entities across a set of classes and then layer those classes to describe a complete entity.</span></span> <span data-ttu-id="376b9-172">比方說，您可能會新增至您的模型稱為 BlogDetails 的類別。</span><span class="sxs-lookup"><span data-stu-id="376b9-172">For example, you may add a class called BlogDetails to your model.</span></span>

``` csharp
    public class BlogDetails
    {
        public DateTime? DateCreated { get; set; }

        [MaxLength(250)]
        public string Description { get; set; }
    }
```

<span data-ttu-id="376b9-173">請注意 BlogDetails 沒有任何類型的索引鍵屬性。</span><span class="sxs-lookup"><span data-stu-id="376b9-173">Notice that BlogDetails does not have any type of key property.</span></span> <span data-ttu-id="376b9-174">在 網域導向設計，BlogDetails 被指值的物件。</span><span class="sxs-lookup"><span data-stu-id="376b9-174">In domain driven design, BlogDetails is referred to as a value object.</span></span> <span data-ttu-id="376b9-175">Entity Framework 會將稱為複雜類型的值物件。</span><span class="sxs-lookup"><span data-stu-id="376b9-175">Entity Framework refers to value objects as complex types.</span></span>  <span data-ttu-id="376b9-176">複雜型別無法自行追蹤。</span><span class="sxs-lookup"><span data-stu-id="376b9-176">Complex types cannot be tracked on their own.</span></span>

<span data-ttu-id="376b9-177">不過部落格類別，它將會追蹤部落格物件的一部分的 BlogDetails 中的屬性。</span><span class="sxs-lookup"><span data-stu-id="376b9-177">However as a property in the Blog class, BlogDetails it will be tracked as part of a Blog object.</span></span> <span data-ttu-id="376b9-178">為了讓第一次將此辨識的程式碼，您必須將 BlogDetails 類別標示為 ComplexType。</span><span class="sxs-lookup"><span data-stu-id="376b9-178">In order for code first to recognize this, you must mark the BlogDetails class as a ComplexType.</span></span>

``` csharp
    [ComplexType]
    public class BlogDetails
    {
        public DateTime? DateCreated { get; set; }

        [MaxLength(250)]
        public string Description { get; set; }
    }
```

<span data-ttu-id="376b9-179">現在您可以新增屬性來表示該部落格 BlogDetails 部落格類別中。</span><span class="sxs-lookup"><span data-stu-id="376b9-179">Now you can add a property in the Blog class to represent the BlogDetails for that blog.</span></span>

``` csharp
        public BlogDetails BlogDetail { get; set; }
```

<span data-ttu-id="376b9-180">在資料庫中，部落格資料表將包含的所有部落格，包括其 BlogDetail; 屬性所包含的屬性的屬性。</span><span class="sxs-lookup"><span data-stu-id="376b9-180">In the database, the Blog table will contain all of the properties of the blog including the properties contained in its BlogDetail property.</span></span> <span data-ttu-id="376b9-181">根據預設，每一個前面加上複雜類型，也就是 BlogDetail 的名稱。</span><span class="sxs-lookup"><span data-stu-id="376b9-181">By default, each one is preceded with the name of the complex type, BlogDetail.</span></span>

![jj591583_figure06](~/ef6/media/jj591583-figure06.png)

<span data-ttu-id="376b9-183">另一個有趣的註解是，雖然 DateCreated 屬性定義為不可為 null 的日期時間，在類別中，相關的資料庫欄位是可為 null。</span><span class="sxs-lookup"><span data-stu-id="376b9-183">Another interesting note is that although the DateCreated property was defined as a non-nullable DateTime in the class, the relevant database field is nullable.</span></span> <span data-ttu-id="376b9-184">如果您想要影響的資料庫結構描述，您必須使用必要的註解。</span><span class="sxs-lookup"><span data-stu-id="376b9-184">You must use the Required annotation if you wish to affect the database schema.</span></span>

 

## <a name="concurrencycheck"></a><span data-ttu-id="376b9-185">ConcurrencyCheck</span><span class="sxs-lookup"><span data-stu-id="376b9-185">ConcurrencyCheck</span></span>

<span data-ttu-id="376b9-186">ConcurrencyCheck 註釋可讓您用於並行存取檢查資料庫中，當使用者編輯或刪除實體的一或多個屬性的旗標。</span><span class="sxs-lookup"><span data-stu-id="376b9-186">The ConcurrencyCheck annotation allows you to flag one or more properties to be used for concurrency checking in the database when a user edits or deletes an entity.</span></span> <span data-ttu-id="376b9-187">如果您使用的 EF 設計工具，這會與屬性的 ConcurrencyMode 設定為 已修正。</span><span class="sxs-lookup"><span data-stu-id="376b9-187">If you've been working with the EF Designer, this aligns with setting a property's ConcurrencyMode to Fixed.</span></span>

<span data-ttu-id="376b9-188">我們來看看 ConcurrencyCheck 藉由將 BloggerName 屬性的運作方式。</span><span class="sxs-lookup"><span data-stu-id="376b9-188">Let’s see how ConcurrencyCheck works by adding it to the BloggerName property.</span></span>

``` csharp
    [ConcurrencyCheck, MaxLength(10, ErrorMessage="BloggerName must be 10 characters or less"),MinLength(5)]
    public string BloggerName { get; set; }
```

<span data-ttu-id="376b9-189">當呼叫 SaveChanges 時，由於 ConcurrencyCheck 上的註解 BloggerName 欄位中，該屬性的原始值將用於更新。</span><span class="sxs-lookup"><span data-stu-id="376b9-189">When SaveChanges is called, because of the ConcurrencyCheck annotation on the BloggerName field, the original value of that property will be used in the update.</span></span> <span data-ttu-id="376b9-190">此命令會嘗試找出正確的資料列篩選的索引鍵的值不僅在 BloggerName 的原始值。</span><span class="sxs-lookup"><span data-stu-id="376b9-190">The command will attempt to locate the correct row by filtering not only on the key value but also on the original value of BloggerName.</span></span>  <span data-ttu-id="376b9-191">以下是 傳送到資料庫的更新命令的重要部分，其中您可以看到此命令會更新資料列具有 PrimaryTrackingKey 是 1 到"Julie"，也就是原始的值，該部落格已從資料庫擷取時的 BloggerName。</span><span class="sxs-lookup"><span data-stu-id="376b9-191">Here are the critical parts of the UPDATE command sent to the database, where you can see the command will update the row that has a PrimaryTrackingKey is 1 and a BloggerName of “Julie” which was the original value when that blog was retrieved from the database.</span></span>

``` SQL
    where (([PrimaryTrackingKey] = @4) and ([BloggerName] = @5))
    @4=1,@5=N'Julie'
```

<span data-ttu-id="376b9-192">如果有人變更過該部落格部落客名稱在此同時，這項更新將會失敗，您會收到要處理 DbUpdateConcurrencyException。</span><span class="sxs-lookup"><span data-stu-id="376b9-192">If someone has changed the blogger name for that blog in the meantime, this update will fail and you’ll get a DbUpdateConcurrencyException that you'll need to handle.</span></span>

 

## <a name="timestamp"></a><span data-ttu-id="376b9-193">時間戳記</span><span class="sxs-lookup"><span data-stu-id="376b9-193">TimeStamp</span></span>

<span data-ttu-id="376b9-194">就較常使用的並行存取檢查的 rowversion 或時間戳記欄位。</span><span class="sxs-lookup"><span data-stu-id="376b9-194">It's more common to use rowversion or timestamp fields for concurrency checking.</span></span> <span data-ttu-id="376b9-195">但是，而不是使用 ConcurrencyCheck 註釋，您可以使用更特定的時間戳記註解，只要將屬性的型別是位元組陣列。</span><span class="sxs-lookup"><span data-stu-id="376b9-195">But rather than using the ConcurrencyCheck annotation, you can use the more specific TimeStamp annotation as long as the type of the property is byte array.</span></span> <span data-ttu-id="376b9-196">程式碼第一次將時間戳記屬性視為相同 ConcurrencyCheck 屬性，但它也會確保程式碼第一次會產生的資料庫欄位是不可為 null。</span><span class="sxs-lookup"><span data-stu-id="376b9-196">Code first will treat Timestamp properties the same as ConcurrencyCheck properties, but it will also ensure that the database field that code first generates is non-nullable.</span></span> <span data-ttu-id="376b9-197">您只能有一個時間戳記屬性中指定的類別。</span><span class="sxs-lookup"><span data-stu-id="376b9-197">You can only have one timestamp property in a given class.</span></span>

<span data-ttu-id="376b9-198">部落格類別中加入下列屬性：</span><span class="sxs-lookup"><span data-stu-id="376b9-198">Adding the following property to the Blog class:</span></span>

``` csharp
    [Timestamp]
    public Byte[] TimeStamp { get; set; }
```

<span data-ttu-id="376b9-199">在第一次建立資料庫資料表中的 不可為 null 的時間戳記資料行的程式碼中的結果。</span><span class="sxs-lookup"><span data-stu-id="376b9-199">results in code first creating a non-nullable timestamp column in the database table.</span></span>

![jj591583_figure07](~/ef6/media/jj591583-figure07.png)

 

## <a name="table-and-column"></a><span data-ttu-id="376b9-201">資料表和資料行</span><span class="sxs-lookup"><span data-stu-id="376b9-201">Table and Column</span></span>

<span data-ttu-id="376b9-202">如果您讓 Code First 建立的資料庫，您可能想要變更的資料表和其建立的資料行的名稱。</span><span class="sxs-lookup"><span data-stu-id="376b9-202">If you are letting Code First create the database, you may want to change the name of the tables and columns it is creating.</span></span> <span data-ttu-id="376b9-203">您也可以使用 Code First 與現有的資料庫。</span><span class="sxs-lookup"><span data-stu-id="376b9-203">You can also use Code First with an existing database.</span></span> <span data-ttu-id="376b9-204">但它不是一定的類別和您的網域中的屬性名稱比對的資料表和資料庫中的資料行的名稱。</span><span class="sxs-lookup"><span data-stu-id="376b9-204">But it's not always the case that the names of the classes and properties in your domain match the names of the tables and columns in your database.</span></span>

<span data-ttu-id="376b9-205">我的類別名為部落格，並依照慣例，程式碼先假設這會對應到名為部落格的資料表。</span><span class="sxs-lookup"><span data-stu-id="376b9-205">My class is named Blog and by convention, code first presumes this will map to a table named Blogs.</span></span> <span data-ttu-id="376b9-206">如果不是您可以指定資料表的名稱與資料表屬性。</span><span class="sxs-lookup"><span data-stu-id="376b9-206">If that's not the case you can specify the name of the table with the Table attribute.</span></span> <span data-ttu-id="376b9-207">此處，例如註解會指定資料表名稱 InternalBlogs。</span><span class="sxs-lookup"><span data-stu-id="376b9-207">Here for example, the annotation is specifying that the table name is InternalBlogs.</span></span>

``` csharp
    [Table("InternalBlogs")]
    public class Blog
```

<span data-ttu-id="376b9-208">資料行註解是更多的精英中指定的對應資料行的屬性。</span><span class="sxs-lookup"><span data-stu-id="376b9-208">The Column annotation is a more adept in specifying the attributes of a mapped column.</span></span> <span data-ttu-id="376b9-209">您可以規定名稱、 資料型別或甚至是將資料行出現在資料表中的順序。</span><span class="sxs-lookup"><span data-stu-id="376b9-209">You can stipulate a name, data type or even the order in which a column appears in the table.</span></span> <span data-ttu-id="376b9-210">以下是資料行屬性的範例。</span><span class="sxs-lookup"><span data-stu-id="376b9-210">Here is an example of the Column attribute.</span></span>

``` csharp
    [Column(“BlogDescription", TypeName="ntext")]
    public String Description {get;set;}
```

<span data-ttu-id="376b9-211">請勿混淆資料行的資料型別 DataAnnotation TypeName 屬性。</span><span class="sxs-lookup"><span data-stu-id="376b9-211">Don’t confuse Column’s TypeName attribute with the DataType DataAnnotation.</span></span> <span data-ttu-id="376b9-212">資料型別是用於 UI 的註釋，而且會忽略的 Code First。</span><span class="sxs-lookup"><span data-stu-id="376b9-212">DataType is an annotation used for the UI and is ignored by Code First.</span></span>

<span data-ttu-id="376b9-213">已重新產生之後，以下是資料表。</span><span class="sxs-lookup"><span data-stu-id="376b9-213">Here is the table after it’s been regenerated.</span></span> <span data-ttu-id="376b9-214">資料表名稱已變更為 InternalBlogs，描述複雜型別資料行現在已 BlogDescription。</span><span class="sxs-lookup"><span data-stu-id="376b9-214">The table name has changed to InternalBlogs and Description column from the complex type is now BlogDescription.</span></span> <span data-ttu-id="376b9-215">因為註解中指定的名稱，程式碼第一次不會使用資料行名稱開頭為複雜型別名稱的慣例。</span><span class="sxs-lookup"><span data-stu-id="376b9-215">Because the name was specified in the annotation, code first will not use the convention of starting the column name with the name of the complex type.</span></span>

![jj591583_figure08](~/ef6/media/jj591583-figure08.png)

 

## <a name="databasegenerated"></a><span data-ttu-id="376b9-217">DatabaseGenerated</span><span class="sxs-lookup"><span data-stu-id="376b9-217">DatabaseGenerated</span></span>

<span data-ttu-id="376b9-218">重要的資料庫功能是能夠有計算屬性。</span><span class="sxs-lookup"><span data-stu-id="376b9-218">An important database features is the ability to have computed properties.</span></span> <span data-ttu-id="376b9-219">如果您對應 Code First 類別，藉此資料表包含計算資料行，您不想要嘗試更新這些資料行的 Entity Framework。</span><span class="sxs-lookup"><span data-stu-id="376b9-219">If you're mapping your Code First classes to tables that contain computed columns, you don't want Entity Framework to try to update those columns.</span></span> <span data-ttu-id="376b9-220">但您想 EF 來插入或更新的資料之後，從資料庫傳回這些值。</span><span class="sxs-lookup"><span data-stu-id="376b9-220">But you do want EF to return those values from the database after you've inserted or updated data.</span></span> <span data-ttu-id="376b9-221">您可以使用 DatabaseGenerated 註解來標示那些屬性，在您的類別，以及計算列舉。</span><span class="sxs-lookup"><span data-stu-id="376b9-221">You can use the DatabaseGenerated annotation to flag those properties in your class along with the Computed enum.</span></span> <span data-ttu-id="376b9-222">其他列舉都是無和身分識別。</span><span class="sxs-lookup"><span data-stu-id="376b9-222">Other enums are None and Identity.</span></span>

``` csharp
    [DatabaseGenerated(DatabaseGenerationOption.Computed)]
    public DateTime DateCreated { get; set; }
```

<span data-ttu-id="376b9-223">您可以使用程式碼第一次產生資料庫時，產生位元組或時間戳記資料行上的資料庫，否則您應該只使用這個選項指向現有的資料庫，因為程式碼第一次將無法判斷計算資料行的公式。</span><span class="sxs-lookup"><span data-stu-id="376b9-223">You can use database generated on byte or timestamp columns when code first is generating the database, otherwise you should only use this when pointing to existing databases because code first won't be able to determine the formula for the computed column.</span></span>

<span data-ttu-id="376b9-224">您閱讀上述，根據預設，是一個整數的索引鍵屬性，將會成為在資料庫中的識別索引鍵。</span><span class="sxs-lookup"><span data-stu-id="376b9-224">You read above that by default, a key property that is an integer will become an identity key in the database.</span></span> <span data-ttu-id="376b9-225">這會是 DatabaseGenerated 設 DatabaseGenerationOption.Identity 相同。</span><span class="sxs-lookup"><span data-stu-id="376b9-225">That would be the same as setting DatabaseGenerated to DatabaseGenerationOption.Identity.</span></span> <span data-ttu-id="376b9-226">如果您不想要識別索引鍵，值可以設 DatabaseGenerationOption.None。</span><span class="sxs-lookup"><span data-stu-id="376b9-226">If you do not want it to be an identity key, you can set the value to DatabaseGenerationOption.None.</span></span>

 

## <a name="index"></a><span data-ttu-id="376b9-227">索引</span><span class="sxs-lookup"><span data-stu-id="376b9-227">Index</span></span>

> [!NOTE]
> <span data-ttu-id="376b9-228">**EF6.1 及更新版本僅**-Entity Framework 6.1 中導入的索引屬性。</span><span class="sxs-lookup"><span data-stu-id="376b9-228">**EF6.1 Onwards Only** - The Index attribute was introduced in Entity Framework 6.1.</span></span> <span data-ttu-id="376b9-229">如果您使用較早版本不適用這一節的資訊。</span><span class="sxs-lookup"><span data-stu-id="376b9-229">If you are using an earlier version the information in this section does not apply.</span></span>

<span data-ttu-id="376b9-230">您可以使用的一或多個資料行上建立索引**IndexAttribute**。</span><span class="sxs-lookup"><span data-stu-id="376b9-230">You can create an index on one or more columns using the **IndexAttribute**.</span></span> <span data-ttu-id="376b9-231">將屬性加入至一個或多個屬性將會導致 EF 在資料庫中建立對應的索引，建立資料庫時，或建立對應的結構**CreateIndex**呼叫如果您使用 Code First 移轉。</span><span class="sxs-lookup"><span data-stu-id="376b9-231">Adding the attribute to one or more properties will cause EF to create the corresponding index in the database when it creates the database, or scaffold the corresponding **CreateIndex** calls if you are using Code First Migrations.</span></span>

<span data-ttu-id="376b9-232">例如，下列程式碼會導致上所建立的索引**分級**資料行**文章**資料庫資料表中的。</span><span class="sxs-lookup"><span data-stu-id="376b9-232">For example, the following code will result in an index being created on the **Rating** column of the **Posts** table in the database.</span></span>

``` csharp
    public class Post
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }
        [Index]
        public int Rating { get; set; }
        public int BlogId { get; set; }
    }
```

<span data-ttu-id="376b9-233">根據預設，將會命名為索引**IX\_&lt;屬性名稱&gt;** (IX\_評等在上述範例中)。</span><span class="sxs-lookup"><span data-stu-id="376b9-233">By default, the index will be named **IX\_&lt;property name&gt;** (IX\_Rating in the above example).</span></span> <span data-ttu-id="376b9-234">您也可以透過指定索引的名稱。</span><span class="sxs-lookup"><span data-stu-id="376b9-234">You can also specify a name for the index though.</span></span> <span data-ttu-id="376b9-235">下列範例會指定應該命名為索引**PostRatingIndex**。</span><span class="sxs-lookup"><span data-stu-id="376b9-235">The following example specifies that the index should be named **PostRatingIndex**.</span></span>

``` csharp
    [Index("PostRatingIndex")]
    public int Rating { get; set; }
```

<span data-ttu-id="376b9-236">根據預設，索引為非唯一的但您可以使用**IsUnique**具名參數來指定應該是唯一的索引。</span><span class="sxs-lookup"><span data-stu-id="376b9-236">By default, indexes are non-unique, but you can use the **IsUnique** named parameter to specify that an index should be unique.</span></span> <span data-ttu-id="376b9-237">下列範例會介紹一個唯一的索引上**使用者**的登入名稱。</span><span class="sxs-lookup"><span data-stu-id="376b9-237">The following example introduces a unique index on a **User**'s login name.</span></span>

``` csharp
    public class User
    {
        public int UserId { get; set; }

        [Index(IsUnique = true)]
        [StringLength(200)]
        public string Username { get; set; }

        public string DisplayName { get; set; }
    }
```

### <a name="multiple-column-indexes"></a><span data-ttu-id="376b9-238">多個資料行索引</span><span class="sxs-lookup"><span data-stu-id="376b9-238">Multiple-Column Indexes</span></span>

<span data-ttu-id="376b9-239">跨越多個資料行的索引已指定在多個索引註解中使用相同的名稱，指定的資料表。</span><span class="sxs-lookup"><span data-stu-id="376b9-239">Indexes that span multiple columns are specified by using the same name in multiple Index annotations for a given table.</span></span> <span data-ttu-id="376b9-240">當您建立多重資料行索引時，您必須指定在索引中的資料行的順序。</span><span class="sxs-lookup"><span data-stu-id="376b9-240">When you create multi-column indexes, you need to specify an order for the columns in the index.</span></span> <span data-ttu-id="376b9-241">比方說，下列程式碼會建立多重資料行索引上**分級**並**BlogId**呼叫**IX\_BlogAndRating**。</span><span class="sxs-lookup"><span data-stu-id="376b9-241">For example, the following code creates a multi-column index on **Rating** and **BlogId** called **IX\_BlogAndRating**.</span></span> <span data-ttu-id="376b9-242">**BlogId**是在索引中的第一個資料行和**分級**是第二個。</span><span class="sxs-lookup"><span data-stu-id="376b9-242">**BlogId** is the first column in the index and **Rating** is the second.</span></span>

``` csharp
    public class Post
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }
        [Index("IX_BlogIdAndRating", 2)]
        public int Rating { get; set; }
        [Index("IX_BlogIdAndRating", 1)]
        public int BlogId { get; set; }
    }
```

 

## <a name="relationship-attributes-inverseproperty-and-foreignkey"></a><span data-ttu-id="376b9-243">屬性關聯性： InverseProperty 和 ForeignKey</span><span class="sxs-lookup"><span data-stu-id="376b9-243">Relationship Attributes: InverseProperty and ForeignKey</span></span>

> [!NOTE]
> <span data-ttu-id="376b9-244">此頁面提供有關設定 Code First 模型使用資料註解中的關聯性資訊。</span><span class="sxs-lookup"><span data-stu-id="376b9-244">This page provides information about setting up relationships in your Code First model using Data Annotations.</span></span> <span data-ttu-id="376b9-245">如需 EF 以及如何存取和操作資料使用關聯性的關聯性的一般資訊，請參閱[關聯性和導覽屬性](~/ef6/fundamentals/relationships.md)。 \*</span><span class="sxs-lookup"><span data-stu-id="376b9-245">For general information about relationships in EF and how to access and manipulate data using relationships, see [Relationships & Navigation Properties](~/ef6/fundamentals/relationships.md).\*</span></span>

<span data-ttu-id="376b9-246">程式碼的第一個慣例會負責在您的模型中，最常見的關聯性，但有某些情況下，它需要說明的位置。</span><span class="sxs-lookup"><span data-stu-id="376b9-246">Code first convention will take care of the most common relationships in your model, but there are some cases where it needs help.</span></span>

<span data-ttu-id="376b9-247">變更建立問題貼文及其關聯性的部落格類別中的索引鍵屬性名稱。</span><span class="sxs-lookup"><span data-stu-id="376b9-247">Changing the name of the key property in the Blog class created a problem with its relationship to Post.</span></span> 

<span data-ttu-id="376b9-248">產生時的資料庫，程式碼第一次看到 BlogId 類別中的屬性後，並會將它，它符合類別名稱加上 「 識別碼 」，為部落格類別的外部索引鍵的慣例所辨識。</span><span class="sxs-lookup"><span data-stu-id="376b9-248">When generating the database, code first sees the BlogId property in the Post class and recognizes it, by the convention that it matches a class name plus “Id”, as a foreign key to the Blog class.</span></span> <span data-ttu-id="376b9-249">但是，部落格類別中沒有 BlogId 屬性。</span><span class="sxs-lookup"><span data-stu-id="376b9-249">But there is no BlogId property in the blog class.</span></span> <span data-ttu-id="376b9-250">這個解決方法是建立貼文中的導覽屬性，並使用外部索引 DataAnnotation 協助先了解如何建置這兩個類別之間的關聯性的程式碼 — 使用 Post.BlogId 屬性，以及如何指定條件約束中的資料庫。</span><span class="sxs-lookup"><span data-stu-id="376b9-250">The solution for this is to create a navigation property in the Post and use the Foreign DataAnnotation to help code first understand how to build the relationship between the two classes —using the Post.BlogId property — as well as how to specify constraints in the database.</span></span>

``` csharp
    public class Post
    {
            public int Id { get; set; }
            public string Title { get; set; }
            public DateTime DateCreated { get; set; }
            public string Content { get; set; }
            public int BlogId { get; set; }
            [ForeignKey("BlogId")]
            public Blog Blog { get; set; }
            public ICollection<Comment> Comments { get; set; }
    }
```

<span data-ttu-id="376b9-251">在資料庫中的條件約束會顯示 InternalBlogs.PrimaryTrackingKey Posts.BlogId 之間的關聯性。</span><span class="sxs-lookup"><span data-stu-id="376b9-251">The constraint in the database shows a relationship between InternalBlogs.PrimaryTrackingKey and Posts.BlogId.</span></span> 

![jj591583_figure09](~/ef6/media/jj591583-figure09.png)

<span data-ttu-id="376b9-253">當您有多個類別之間的關聯性時，會使用 InverseProperty。</span><span class="sxs-lookup"><span data-stu-id="376b9-253">The InverseProperty is used when you have multiple relationships between classes.</span></span>

<span data-ttu-id="376b9-254">在 Post 類別中，您可能想要追蹤是誰撰寫的部落格文章的以及誰可以編輯它。</span><span class="sxs-lookup"><span data-stu-id="376b9-254">In the Post class, you may want to keep track of who wrote a blog post as well as who edited it.</span></span> <span data-ttu-id="376b9-255">以下是 Post 類別的兩個新導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="376b9-255">Here are two new navigation properties for the Post class.</span></span>

``` csharp
    public Person CreatedBy { get; set; }
    public Person UpdatedBy { get; set; }
```

<span data-ttu-id="376b9-256">您也必須新增這些屬性所參考的 Person 類別中。</span><span class="sxs-lookup"><span data-stu-id="376b9-256">You’ll also need to add in the Person class referenced by these properties.</span></span> <span data-ttu-id="376b9-257">Person 類別有回到文章中，一個用於所有人員，另一個用於所有由該位人員更新的文章所撰寫的文章的導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="376b9-257">The Person class has navigation properties back to the Post, one for all of the posts written by the person and one for all of the posts updated by that person.</span></span>

``` csharp
    public class Person
    {
            public int Id { get; set; }
            public string Name { get; set; }
            public List<Post> PostsWritten { get; set; }
            public List<Post> PostsUpdated { get; set; }
    }
```

<span data-ttu-id="376b9-258">程式碼第一次不能符合其本身上的兩個類別中的屬性。</span><span class="sxs-lookup"><span data-stu-id="376b9-258">Code first is not able to match up the properties in the two classes on its own.</span></span> <span data-ttu-id="376b9-259">貼文的資料庫資料表應該有一個外部索引鍵的 CreatedBy 人員和另一個用於 UpdatedBy 人員但程式碼第一次會建立四個外部索引鍵屬性將會： 人\_識別碼、 人員\_Id1、 CreatedBy\_識別碼和UpdatedBy\_識別碼。</span><span class="sxs-lookup"><span data-stu-id="376b9-259">The database table for Posts should have one foreign key for the CreatedBy person and one for the UpdatedBy person but code first will create four will foreign key properties: Person\_Id, Person\_Id1, CreatedBy\_Id and UpdatedBy\_Id.</span></span>

![jj591583_figure10](~/ef6/media/jj591583-figure10.png)

<span data-ttu-id="376b9-261">若要修正這些問題，您可以使用 InverseProperty 註解來指定屬性的對齊方式。</span><span class="sxs-lookup"><span data-stu-id="376b9-261">To fix these problems, you can use the InverseProperty annotation to specify the alignment of the properties.</span></span>

``` csharp
    [InverseProperty("CreatedBy")]
    public List<Post> PostsWritten { get; set; }

    [InverseProperty("UpdatedBy")]
    public List<Post> PostsUpdated { get; set; }
```

<span data-ttu-id="376b9-262">親自轉交 PostsWritten 屬性會知道這是指 Post 型別，因為它會建置 Post.CreatedBy 的關聯性。</span><span class="sxs-lookup"><span data-stu-id="376b9-262">Because the PostsWritten property in Person knows that this refers to the Post type, it will build the relationship to Post.CreatedBy.</span></span> <span data-ttu-id="376b9-263">同樣地，PostsUpdated 將會連接到 Post.UpdatedBy。</span><span class="sxs-lookup"><span data-stu-id="376b9-263">Similarly, PostsUpdated will be connected to Post.UpdatedBy.</span></span> <span data-ttu-id="376b9-264">程式碼第一次不會產生額外的外部索引鍵。</span><span class="sxs-lookup"><span data-stu-id="376b9-264">And code first will not create the extra foreign keys.</span></span>

![jj591583_figure11](~/ef6/media/jj591583-figure11.png)

 

## <a name="summary"></a><span data-ttu-id="376b9-266">總結</span><span class="sxs-lookup"><span data-stu-id="376b9-266">Summary</span></span>

<span data-ttu-id="376b9-267">DataAnnotations 不僅可讓您描述在您的程式碼的第一個類別，用戶端和伺服器端驗證，但它們也可讓您增強和甚至修正第一次程式碼可讓您根據其慣例的類別的相關假設。</span><span class="sxs-lookup"><span data-stu-id="376b9-267">DataAnnotations not only let you describe client and server side validation in your code first classes, but they also allow you to enhance and even correct the assumptions that code first will make about your classes based on its conventions.</span></span> <span data-ttu-id="376b9-268">使用 DataAnnotations 您可以不只有磁碟機產生資料庫結構描述，但您也可以將您的程式碼的第一個類別對應到預先存在的資料庫。</span><span class="sxs-lookup"><span data-stu-id="376b9-268">With DataAnnotations you can not only drive database schema generation, but you can also map your code first classes to a pre-existing database.</span></span>

<span data-ttu-id="376b9-269">雖然它們是非常大的彈性，請記住，DataAnnotations 提供中最常需要組態變更，您可以對您的程式碼的第一個類別。</span><span class="sxs-lookup"><span data-stu-id="376b9-269">While they are very flexible, keep in mind that DataAnnotations provide only the most commonly needed configuration changes you can make on your code first classes.</span></span> <span data-ttu-id="376b9-270">若要設定您的類別，針對某些邊緣案例，您應該查看的其他設定機制，Code First 的 Fluent API。</span><span class="sxs-lookup"><span data-stu-id="376b9-270">To configure your classes for some of the edge cases, you should look to the alternate configuration mechanism, Code First’s Fluent API .</span></span>