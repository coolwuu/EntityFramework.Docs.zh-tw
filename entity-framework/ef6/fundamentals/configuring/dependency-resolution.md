---
title: 相依性解析-EF6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: 32d19ac6-9186-4ae1-8655-64ee49da55d0
caps.latest.revision: 3
ms.openlocfilehash: 88fd859b4f9a8069eeb08f32bb1d35ddcd21aec5
ms.sourcegitcommit: bdd06c9a591ba5e6d6a3ec046c80de98f598f3f3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2018
ms.locfileid: "39120567"
---
# <a name="dependency-resolution"></a><span data-ttu-id="057c4-102">相依性解析</span><span class="sxs-lookup"><span data-stu-id="057c4-102">Dependency resolution</span></span>
> [!NOTE]
> <span data-ttu-id="057c4-103">**僅限 EF6 及更新版本** - Entity Framework 6 已引進此頁面中所討論的功能及 API 等等。</span><span class="sxs-lookup"><span data-stu-id="057c4-103">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="057c4-104">如果您使用的是較早版本，則不適用部分或全部的資訊。</span><span class="sxs-lookup"><span data-stu-id="057c4-104">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="057c4-105">從 EF6 開始，Entity Framework，包含一般用途的機制，來取得它所需要的服務的實作。</span><span class="sxs-lookup"><span data-stu-id="057c4-105">Starting with EF6, Entity Framework contains a general-purpose mechanism for obtaining implementations of services that it requires.</span></span> <span data-ttu-id="057c4-106">也就是說，EF 使用某些介面或基底類別的執行個體時它會要求提供的介面或基底類別的具體實作使用。</span><span class="sxs-lookup"><span data-stu-id="057c4-106">That is, when EF uses an instance of some interfaces or base classes it will ask for a concrete implementation of the interface or base class to use.</span></span> <span data-ttu-id="057c4-107">這是透過使用 IDbDependencyResolver 介面來達成：</span><span class="sxs-lookup"><span data-stu-id="057c4-107">This is achieved through use of the IDbDependencyResolver interface:</span></span>  

``` csharp
public interface IDbDependencyResolver
{
    object GetService(Type type, object key);
}
```  

<span data-ttu-id="057c4-108">GetService 方法通常會呼叫 EF，以及處理 IDbDependencyResolver 由 EF 或應用程式所提供的實作。</span><span class="sxs-lookup"><span data-stu-id="057c4-108">The GetService method is typically called by EF and is handled by an implementation of IDbDependencyResolver provided either by EF or by the application.</span></span> <span data-ttu-id="057c4-109">呼叫時，型別引數是所要求之服務的介面或基底類別型別和索引鍵的物件為 null 或這個物件提供內容資訊要求的服務。</span><span class="sxs-lookup"><span data-stu-id="057c4-109">When called, the type argument is the interface or base class type of the service being requested, and the key object is either null or an object providing contextual information about the requested service.</span></span>  

<span data-ttu-id="057c4-110">這篇文章不包含有關如何實作 IDbDependencyResolver，完整詳細資料，但會做為其 EF 呼叫 GetService 和語意索引鍵物件的每一種服務類型 （也就是，介面和基底類別類型） 的參考呼叫。</span><span class="sxs-lookup"><span data-stu-id="057c4-110">This article does not contain full details on how to implement IDbDependencyResolver, but instead acts as a reference for the service types (that is, the interface and base class types) for which EF calls GetService and the semantics of the key object for each of these calls.</span></span> <span data-ttu-id="057c4-111">這份文件會保持最新狀態新增其他服務。</span><span class="sxs-lookup"><span data-stu-id="057c4-111">This document will be kept up-to-date as additional services are added.</span></span>  

## <a name="services-resolved"></a><span data-ttu-id="057c4-112">判斷已解決的服務</span><span class="sxs-lookup"><span data-stu-id="057c4-112">Services resolved</span></span>  

<span data-ttu-id="057c4-113">除非另有指明傳回任何物件必須是安全執行緒，因為它可用來當做單一值。</span><span class="sxs-lookup"><span data-stu-id="057c4-113">Unless otherwise stated any object returned must be thread-safe since it can be used as a singleton.</span></span> <span data-ttu-id="057c4-114">在許多情況下，在此情況下是處理站時，傳回的物件 factory 本身必須是安全執行緒，但不需要從 factory 所傳回的物件，這是為安全執行緒，因為每次使用的處理站所要求的新執行個體。</span><span class="sxs-lookup"><span data-stu-id="057c4-114">In many cases the object returned is a factory in which case the factory itself must be thread-safe but the object returned from the factory does not need to be thread-safe since a new instance is requested from the factory for each use.</span></span>  

### <a name="systemdataentityidatabaseinitializertcontext"></a><span data-ttu-id="057c4-115">System.Data.Entity.IDatabaseInitializer < TContext\></span><span class="sxs-lookup"><span data-stu-id="057c4-115">System.Data.Entity.IDatabaseInitializer<TContext\></span></span>  

<span data-ttu-id="057c4-116">**版本導入**: EF6.0.0</span><span class="sxs-lookup"><span data-stu-id="057c4-116">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="057c4-117">**傳回物件**： 指定的內容類型的資料庫初始設定式</span><span class="sxs-lookup"><span data-stu-id="057c4-117">**Object returned**: A database initializer for the given context type</span></span>  

<span data-ttu-id="057c4-118">**索引鍵**： 無法使用，將會是 null</span><span class="sxs-lookup"><span data-stu-id="057c4-118">**Key**: Not used; will be null</span></span>  

### <a name="funcsystemdataentitymigrationssqlmigrationsqlgenerator"></a><span data-ttu-id="057c4-119">L o c k < System.Data.Entity.Migrations.Sql.MigrationSqlGenerator\></span><span class="sxs-lookup"><span data-stu-id="057c4-119">Func<System.Data.Entity.Migrations.Sql.MigrationSqlGenerator\></span></span>  

<span data-ttu-id="057c4-120">**版本導入**: EF6.0.0</span><span class="sxs-lookup"><span data-stu-id="057c4-120">**Version introduced**: EF6.0.0</span></span>

<span data-ttu-id="057c4-121">**傳回物件**： 若要建立可用來移轉和其他動作，會造成要建立的例如資料庫初始設定式與資料庫建立資料庫的 SQL 產生器的 factory。</span><span class="sxs-lookup"><span data-stu-id="057c4-121">**Object returned**: A factory to create a SQL generator that can be used for Migrations and other actions that cause a database to be created, such as database creation with database initializers.</span></span>  

<span data-ttu-id="057c4-122">**索引鍵**： 字串，包含指定的資料庫會為其產生 SQL 類型的 ADO.NET 的提供者非變異名稱。</span><span class="sxs-lookup"><span data-stu-id="057c4-122">**Key**: A string containing the ADO.NET provider invariant name specifying the type of database for which SQL will be generated.</span></span> <span data-ttu-id="057c4-123">例如，SQL Server SQL 產生器會傳回"System.Data.SqlClient"索引鍵。</span><span class="sxs-lookup"><span data-stu-id="057c4-123">For example, the SQL Server SQL generator is returned for the key "System.Data.SqlClient".</span></span>  

>[!NOTE]
> <span data-ttu-id="057c4-124">如需在 EF6 中的提供者相關的服務的詳細資訊，請參閱[EF6 提供者模型](~/ef6/fundamentals/providers/provider-model.md)一節。</span><span class="sxs-lookup"><span data-stu-id="057c4-124">For more details on provider-related services in EF6 see the [EF6 provider model](~/ef6/fundamentals/providers/provider-model.md) section.</span></span>  

### <a name="systemdataentitycorecommondbproviderservices"></a><span data-ttu-id="057c4-125">System.Data.Entity.Core.Common.DbProviderServices</span><span class="sxs-lookup"><span data-stu-id="057c4-125">System.Data.Entity.Core.Common.DbProviderServices</span></span>  

<span data-ttu-id="057c4-126">**版本導入**: EF6.0.0</span><span class="sxs-lookup"><span data-stu-id="057c4-126">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="057c4-127">**傳回物件**: EF 的提供者，以使用指定的提供者非變異名稱</span><span class="sxs-lookup"><span data-stu-id="057c4-127">**Object returned**: The EF provider to use for a given provider invariant name</span></span>  

<span data-ttu-id="057c4-128">**索引鍵**： 字串，包含 ADO.NET 提供者非變異名稱指定的資料庫提供者所需的類型。</span><span class="sxs-lookup"><span data-stu-id="057c4-128">**Key**: A string containing the ADO.NET provider invariant name specifying the type of database for which a provider is needed.</span></span> <span data-ttu-id="057c4-129">例如，SQL Server 提供者會傳回"System.Data.SqlClient"索引鍵。</span><span class="sxs-lookup"><span data-stu-id="057c4-129">For example, the SQL Server provider is returned for the key "System.Data.SqlClient".</span></span>  

>[!NOTE]
> <span data-ttu-id="057c4-130">如需在 EF6 中的提供者相關的服務的詳細資訊，請參閱[EF6 提供者模型](~/ef6/fundamentals/providers/provider-model.md)一節。</span><span class="sxs-lookup"><span data-stu-id="057c4-130">For more details on provider-related services in EF6 see the [EF6 provider model](~/ef6/fundamentals/providers/provider-model.md) section.</span></span>  

### <a name="systemdataentityinfrastructureidbconnectionfactory"></a><span data-ttu-id="057c4-131">System.Data.Entity.Infrastructure.IDbConnectionFactory</span><span class="sxs-lookup"><span data-stu-id="057c4-131">System.Data.Entity.Infrastructure.IDbConnectionFactory</span></span>  

<span data-ttu-id="057c4-132">**版本導入**: EF6.0.0</span><span class="sxs-lookup"><span data-stu-id="057c4-132">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="057c4-133">**傳回物件**： 將會依照慣例使用時，EF 會建立資料庫連接的連接 factory。</span><span class="sxs-lookup"><span data-stu-id="057c4-133">**Object returned**: The connection factory that will be used when EF creates a database connection by convention.</span></span> <span data-ttu-id="057c4-134">也就是當沒有連接或連接字串提供給 EF，和任何連接字串可在 app.config 或 web.config，然後此服務建立連線依照慣例會使用。</span><span class="sxs-lookup"><span data-stu-id="057c4-134">That is, when no connection or connection string is given to EF, and no connection string can be found in the app.config or web.config, then this service is used to create a connection by convention.</span></span> <span data-ttu-id="057c4-135">變更連線處理站可以預設為允許 EF 來使用不同類型的資料庫 (例如，SQL Server Compact Edition)。</span><span class="sxs-lookup"><span data-stu-id="057c4-135">Changing the connection factory can allow EF to use a different type of database (for example, SQL Server Compact Edition) by default.</span></span>  

<span data-ttu-id="057c4-136">**索引鍵**： 無法使用，將會是 null</span><span class="sxs-lookup"><span data-stu-id="057c4-136">**Key**: Not used; will be null</span></span>  

>[!NOTE]
> <span data-ttu-id="057c4-137">如需在 EF6 中的提供者相關的服務的詳細資訊，請參閱[EF6 提供者模型](~/ef6/fundamentals/providers/provider-model.md)一節。</span><span class="sxs-lookup"><span data-stu-id="057c4-137">For more details on provider-related services in EF6 see the [EF6 provider model](~/ef6/fundamentals/providers/provider-model.md) section.</span></span>  

### <a name="systemdataentityinfrastructureimanifesttokenservice"></a><span data-ttu-id="057c4-138">System.Data.Entity.Infrastructure.IManifestTokenService</span><span class="sxs-lookup"><span data-stu-id="057c4-138">System.Data.Entity.Infrastructure.IManifestTokenService</span></span>  

<span data-ttu-id="057c4-139">**版本導入**: EF6.0.0</span><span class="sxs-lookup"><span data-stu-id="057c4-139">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="057c4-140">**傳回物件**： 一項服務，可以產生從連接的提供者資訊清單語彙基元。</span><span class="sxs-lookup"><span data-stu-id="057c4-140">**Object returned**: A service that can generate a provider manifest token from a connection.</span></span> <span data-ttu-id="057c4-141">這項服務通常用於兩種方式。</span><span class="sxs-lookup"><span data-stu-id="057c4-141">This service is typically used in two ways.</span></span> <span data-ttu-id="057c4-142">首先，它可以用來避免 Code First 建立模型時，連接到資料庫。</span><span class="sxs-lookup"><span data-stu-id="057c4-142">First, it can be used to avoid Code First connecting to the database when building a model.</span></span> <span data-ttu-id="057c4-143">第二，它可以用來強制執行 Code First 至強制適用於 SQL Server 2005 的模型，不論是否使用 SQL Server 2008 的有時建置特定的資料庫版本--例如，模型。</span><span class="sxs-lookup"><span data-stu-id="057c4-143">Second, it can be used to force Code First to build a model for a specific database version -- for example, to force a model for SQL Server 2005 even if sometimes SQL Server 2008 is used.</span></span>  

<span data-ttu-id="057c4-144">**物件存留期**： 單一-相同的物件可能會使用多個時間，而且同時由不同的執行緒</span><span class="sxs-lookup"><span data-stu-id="057c4-144">**Object lifetime**: Singleton -- the same object may be used multiple times and concurrently by different threads</span></span>  

<span data-ttu-id="057c4-145">**索引鍵**： 無法使用，將會是 null</span><span class="sxs-lookup"><span data-stu-id="057c4-145">**Key**: Not used; will be null</span></span>  

### <a name="systemdataentityinfrastructureidbproviderfactoryservice"></a><span data-ttu-id="057c4-146">System.Data.Entity.Infrastructure.IDbProviderFactoryService</span><span class="sxs-lookup"><span data-stu-id="057c4-146">System.Data.Entity.Infrastructure.IDbProviderFactoryService</span></span>  

<span data-ttu-id="057c4-147">**版本導入**: EF6.0.0</span><span class="sxs-lookup"><span data-stu-id="057c4-147">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="057c4-148">**傳回物件**： 可以從指定的連接中取得提供者 factory 的服務。</span><span class="sxs-lookup"><span data-stu-id="057c4-148">**Object returned**: A service that can obtain a provider factory from a given connection.</span></span> <span data-ttu-id="057c4-149">在.NET 4.5 提供者是可公開存取的連接。</span><span class="sxs-lookup"><span data-stu-id="057c4-149">On .NET 4.5 the provider is publicly accessible from the connection.</span></span> <span data-ttu-id="057c4-150">在.NET 4 上這項服務的預設實作會使用一些啟發學習法，來尋找相符的提供者。</span><span class="sxs-lookup"><span data-stu-id="057c4-150">On .NET 4 the default implementation of this service uses some heuristics to find the matching provider.</span></span> <span data-ttu-id="057c4-151">如果這些然後失敗，此服務的新實作，都可以註冊以提供適當的解決。</span><span class="sxs-lookup"><span data-stu-id="057c4-151">If these fail then a new implementation of this service can be registered to provide an appropriate resolution.</span></span>  

<span data-ttu-id="057c4-152">**索引鍵**： 無法使用，將會是 null</span><span class="sxs-lookup"><span data-stu-id="057c4-152">**Key**: Not used; will be null</span></span>  

### <a name="funcdbcontext-systemdataentityinfrastructureidbmodelcachekey"></a><span data-ttu-id="057c4-153">L o c k < DbContext、 System.Data.Entity.Infrastructure.IDbModelCacheKey\></span><span class="sxs-lookup"><span data-stu-id="057c4-153">Func<DbContext, System.Data.Entity.Infrastructure.IDbModelCacheKey\></span></span>  

<span data-ttu-id="057c4-154">**版本導入**: EF6.0.0</span><span class="sxs-lookup"><span data-stu-id="057c4-154">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="057c4-155">**傳回物件**： 將會產生指定的內容模型快取索引鍵的處理站。</span><span class="sxs-lookup"><span data-stu-id="057c4-155">**Object returned**: A factory that will generate a model cache key for a given context.</span></span> <span data-ttu-id="057c4-156">根據預設，EF 會快取每個 DbContext 類型，每個提供者的另一個模型。</span><span class="sxs-lookup"><span data-stu-id="057c4-156">By default, EF caches one model per DbContext type per provider.</span></span> <span data-ttu-id="057c4-157">這項服務的不同實作可用來將其他資訊，例如結構描述名稱新增至快取索引鍵。</span><span class="sxs-lookup"><span data-stu-id="057c4-157">A different implementation of this service can be used to add other information, such as schema name, to the cache key.</span></span>  

<span data-ttu-id="057c4-158">**索引鍵**： 無法使用，將會是 null</span><span class="sxs-lookup"><span data-stu-id="057c4-158">**Key**: Not used; will be null</span></span>  

### <a name="systemdataentityspatialdbspatialservices"></a><span data-ttu-id="057c4-159">System.Data.Entity.Spatial.DbSpatialServices</span><span class="sxs-lookup"><span data-stu-id="057c4-159">System.Data.Entity.Spatial.DbSpatialServices</span></span>  

<span data-ttu-id="057c4-160">**版本導入**: EF6.0.0</span><span class="sxs-lookup"><span data-stu-id="057c4-160">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="057c4-161">**傳回物件**: EF 空間加入提供者，支援之基本的 EF 提供者的 geography 和 geometry 空間類型。</span><span class="sxs-lookup"><span data-stu-id="057c4-161">**Object returned**: An EF spatial provider that adds support to the basic EF provider for geography and geometry spatial types.</span></span>  

<span data-ttu-id="057c4-162">**索引鍵**: DbSptialServices 要求有兩種。</span><span class="sxs-lookup"><span data-stu-id="057c4-162">**Key**: DbSptialServices is asked for in two ways.</span></span> <span data-ttu-id="057c4-163">首先，提供者特定的空間服務要求使用 DbProviderInfo 物件 (其中包含非變異名稱和資訊清單語彙基元) 做為索引鍵。</span><span class="sxs-lookup"><span data-stu-id="057c4-163">First, provider-specific spatial services are requested using a DbProviderInfo object (which contains invariant name and manifest token) as the key.</span></span> <span data-ttu-id="057c4-164">第二，DbSpatialServices 可要求沒有索引鍵。</span><span class="sxs-lookup"><span data-stu-id="057c4-164">Second, DbSpatialServices can be asked for with no key.</span></span> <span data-ttu-id="057c4-165">這用來解決 「 全域空間提供者 」 來建立獨立的 DbGeography 或 DbGeometry 型別時使用。</span><span class="sxs-lookup"><span data-stu-id="057c4-165">This is used to resolve the "global spatial provider" which is used when creating stand-alone DbGeography or DbGeometry types.</span></span>  

>[!NOTE]
> <span data-ttu-id="057c4-166">如需在 EF6 中的提供者相關的服務的詳細資訊，請參閱[EF6 提供者模型](~/ef6/fundamentals/providers/provider-model.md)一節。</span><span class="sxs-lookup"><span data-stu-id="057c4-166">For more details on provider-related services in EF6 see the [EF6 provider model](~/ef6/fundamentals/providers/provider-model.md) section.</span></span>  

### <a name="funcsystemdataentityinfrastructureidbexecutionstrategy"></a><span data-ttu-id="057c4-167">L o c k < System.Data.Entity.Infrastructure.IDbExecutionStrategy\></span><span class="sxs-lookup"><span data-stu-id="057c4-167">Func<System.Data.Entity.Infrastructure.IDbExecutionStrategy\></span></span>  

<span data-ttu-id="057c4-168">**版本導入**: EF6.0.0</span><span class="sxs-lookup"><span data-stu-id="057c4-168">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="057c4-169">**傳回物件**： 若要建立服務，可讓提供者實作重試或其他行為，對資料庫執行查詢和命令時的處理站。</span><span class="sxs-lookup"><span data-stu-id="057c4-169">**Object returned**: A factory to create a service that allows a provider to implement retries or other behavior when queries and commands are executed against the database.</span></span> <span data-ttu-id="057c4-170">如果不提供實作，然後 EF 將只執行命令，並傳播擲回任何例外狀況。</span><span class="sxs-lookup"><span data-stu-id="057c4-170">If no implementation is provided, then EF will simply execute the commands and propagate any exceptions thrown.</span></span> <span data-ttu-id="057c4-171">適用於 SQL Server 會提供重試原則，也就是特別有用，例如 SQL Azure 的雲端式資料庫伺服器上執行時使用此服務。</span><span class="sxs-lookup"><span data-stu-id="057c4-171">For SQL Server this service is used to provide a retry policy which is especially useful when running against cloud-based database servers such as SQL Azure.</span></span>  

<span data-ttu-id="057c4-172">**索引鍵**: ExecutionStrategyKey 物件，包含提供者非變異名稱和選擇性的執行策略會使用伺服器名稱。</span><span class="sxs-lookup"><span data-stu-id="057c4-172">**Key**: An ExecutionStrategyKey object that contains the provider invariant name and optionally a server name for which the execution strategy will be used.</span></span>  

>[!NOTE]
> <span data-ttu-id="057c4-173">如需在 EF6 中的提供者相關的服務的詳細資訊，請參閱[EF6 提供者模型](~/ef6/fundamentals/providers/provider-model.md)一節。</span><span class="sxs-lookup"><span data-stu-id="057c4-173">For more details on provider-related services in EF6 see the [EF6 provider model](~/ef6/fundamentals/providers/provider-model.md) section.</span></span>  

### <a name="funcdbconnection-string-systemdataentitymigrationshistoryhistorycontext"></a><span data-ttu-id="057c4-174">L o c k < DbConnection，System.Data.Entity.Migrations.History.HistoryContext 的字串\></span><span class="sxs-lookup"><span data-stu-id="057c4-174">Func<DbConnection, string, System.Data.Entity.Migrations.History.HistoryContext\></span></span>  

<span data-ttu-id="057c4-175">**版本導入**: EF6.0.0</span><span class="sxs-lookup"><span data-stu-id="057c4-175">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="057c4-176">**傳回的物件**： 允許設定至 HistoryContext 對應提供者 factory`__MigrationHistory`使用 EF 移轉的資料表。</span><span class="sxs-lookup"><span data-stu-id="057c4-176">**Object returned**: A factory that allows a provider to configure the mapping of the HistoryContext to the `__MigrationHistory` table used by EF Migrations.</span></span> <span data-ttu-id="057c4-177">HistoryContext 是程式碼的第一個 DbContext，可以使用一般的 fluent API 來變更資料表和資料行對應規格的名稱等項目進行設定。</span><span class="sxs-lookup"><span data-stu-id="057c4-177">The HistoryContext is a Code First DbContext and can be configured using the normal fluent API to change things like the name of the table and the column mapping specifications.</span></span>  

<span data-ttu-id="057c4-178">**索引鍵**： 無法使用，將會是 null</span><span class="sxs-lookup"><span data-stu-id="057c4-178">**Key**: Not used; will be null</span></span>  

>[!NOTE]
> <span data-ttu-id="057c4-179">如需在 EF6 中的提供者相關的服務的詳細資訊，請參閱[EF6 提供者模型](~/ef6/fundamentals/providers/provider-model.md)一節。</span><span class="sxs-lookup"><span data-stu-id="057c4-179">For more details on provider-related services in EF6 see the [EF6 provider model](~/ef6/fundamentals/providers/provider-model.md) section.</span></span>  

### <a name="systemdatacommondbproviderfactory"></a><span data-ttu-id="057c4-180">System.Data.Common.DbProviderFactory</span><span class="sxs-lookup"><span data-stu-id="057c4-180">System.Data.Common.DbProviderFactory</span></span>  

<span data-ttu-id="057c4-181">**版本導入**: EF6.0.0</span><span class="sxs-lookup"><span data-stu-id="057c4-181">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="057c4-182">**傳回物件**: ADO.NET 提供者，以使用指定的提供者非變異名稱。</span><span class="sxs-lookup"><span data-stu-id="057c4-182">**Object returned**: The ADO.NET provider to use for a given provider invariant name.</span></span>  

<span data-ttu-id="057c4-183">**索引鍵**： 包含 ADO.NET 提供者非變異名稱的字串</span><span class="sxs-lookup"><span data-stu-id="057c4-183">**Key**: A string containing the ADO.NET provider invariant name</span></span>  

>[!NOTE]
> <span data-ttu-id="057c4-184">這項服務是不通常直接變更因為預設實作會使用一般的 ADO.NET 提供者註冊。</span><span class="sxs-lookup"><span data-stu-id="057c4-184">This service is not usually changed directly since the default implementation uses the normal ADO.NET provider registration.</span></span> <span data-ttu-id="057c4-185">如需在 EF6 中的提供者相關的服務的詳細資訊，請參閱[EF6 提供者模型](~/ef6/fundamentals/providers/provider-model.md)一節。</span><span class="sxs-lookup"><span data-stu-id="057c4-185">For more details on provider-related services in EF6 see the [EF6 provider model](~/ef6/fundamentals/providers/provider-model.md) section.</span></span>  

### <a name="systemdataentityinfrastructureiproviderinvariantname"></a><span data-ttu-id="057c4-186">System.Data.Entity.Infrastructure.IProviderInvariantName</span><span class="sxs-lookup"><span data-stu-id="057c4-186">System.Data.Entity.Infrastructure.IProviderInvariantName</span></span>  

<span data-ttu-id="057c4-187">**版本導入**: EF6.0.0</span><span class="sxs-lookup"><span data-stu-id="057c4-187">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="057c4-188">**傳回物件**： 一項服務，用來判斷 DbProviderFactory 指定類型的提供者非變異名稱。</span><span class="sxs-lookup"><span data-stu-id="057c4-188">**Object returned**: a service that is used to determine a provider invariant name for a given type of DbProviderFactory.</span></span> <span data-ttu-id="057c4-189">這項服務的預設實作會使用 ADO.NET 提供者註冊。</span><span class="sxs-lookup"><span data-stu-id="057c4-189">The default implementation of this service uses the ADO.NET provider registration.</span></span> <span data-ttu-id="057c4-190">這表示，如果因為 EF 所解析的 DbProviderFactory，ADO.NET 提供者不會以一般方式註冊，則它也會解決這項服務所需的。</span><span class="sxs-lookup"><span data-stu-id="057c4-190">This means that if the ADO.NET provider is not registered in the normal way because DbProviderFactory is being resolved by EF, then it will also be necessary to resolve this service.</span></span>  

<span data-ttu-id="057c4-191">**索引鍵**: DbProviderFactory 的非變異名稱是必要的執行個體。</span><span class="sxs-lookup"><span data-stu-id="057c4-191">**Key**: The DbProviderFactory instance for which an invariant name is required.</span></span>  

>[!NOTE]
> <span data-ttu-id="057c4-192">如需在 EF6 中的提供者相關的服務的詳細資訊，請參閱[EF6 提供者模型](~/ef6/fundamentals/providers/provider-model.md)一節。</span><span class="sxs-lookup"><span data-stu-id="057c4-192">For more details on provider-related services in EF6 see the [EF6 provider model](~/ef6/fundamentals/providers/provider-model.md) section.</span></span>  

### <a name="systemdataentitycoremappingviewgenerationiviewassemblycache"></a><span data-ttu-id="057c4-193">System.Data.Entity.Core.Mapping.ViewGeneration.IViewAssemblyCache</span><span class="sxs-lookup"><span data-stu-id="057c4-193">System.Data.Entity.Core.Mapping.ViewGeneration.IViewAssemblyCache</span></span>  

<span data-ttu-id="057c4-194">**版本導入**: EF6.0.0</span><span class="sxs-lookup"><span data-stu-id="057c4-194">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="057c4-195">**傳回物件**： 包含預先產生的檢視組件快取。</span><span class="sxs-lookup"><span data-stu-id="057c4-195">**Object returned**: a cache of the assemblies that contain pre-generated views.</span></span> <span data-ttu-id="057c4-196">替代項目通常用來讓 EF 知道哪些組件包含預先產生的檢視，而不進行任何探索。</span><span class="sxs-lookup"><span data-stu-id="057c4-196">A replacement is typically used to let EF know which assemblies contain pre-generated views without doing any discovery.</span></span>  

<span data-ttu-id="057c4-197">**索引鍵**： 無法使用，將會是 null</span><span class="sxs-lookup"><span data-stu-id="057c4-197">**Key**: Not used; will be null</span></span>  

### <a name="systemdataentityinfrastructurepluralizationipluralizationservice"></a><span data-ttu-id="057c4-198">System.Data.Entity.Infrastructure.Pluralization.IPluralizationService</span><span class="sxs-lookup"><span data-stu-id="057c4-198">System.Data.Entity.Infrastructure.Pluralization.IPluralizationService</span></span>

<span data-ttu-id="057c4-199">**版本導入**: EF6.0.0</span><span class="sxs-lookup"><span data-stu-id="057c4-199">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="057c4-200">**傳回物件**: EF 用複數化和單數化名稱的服務。</span><span class="sxs-lookup"><span data-stu-id="057c4-200">**Object returned**: a service used by EF to pluralize and singularize names.</span></span> <span data-ttu-id="057c4-201">根據預設會使用英文的複數表示服務。</span><span class="sxs-lookup"><span data-stu-id="057c4-201">By default an English pluralization service is used.</span></span>  

<span data-ttu-id="057c4-202">**索引鍵**： 無法使用，將會是 null</span><span class="sxs-lookup"><span data-stu-id="057c4-202">**Key**: Not used; will be null</span></span>  

### <a name="systemdataentityinfrastructureinterceptionidbinterceptor"></a><span data-ttu-id="057c4-203">System.Data.Entity.Infrastructure.Interception.IDbInterceptor</span><span class="sxs-lookup"><span data-stu-id="057c4-203">System.Data.Entity.Infrastructure.Interception.IDbInterceptor</span></span>  

<span data-ttu-id="057c4-204">**版本導入**: EF6.0.0</span><span class="sxs-lookup"><span data-stu-id="057c4-204">**Version introduced**: EF6.0.0</span></span>

<span data-ttu-id="057c4-205">**傳回物件**： 應該在應用程式啟動時註冊任何攔截器。</span><span class="sxs-lookup"><span data-stu-id="057c4-205">**Objects returned**: Any interceptors that should be registered when the application starts.</span></span> <span data-ttu-id="057c4-206">請注意，這些物件會要求使用 GetServices 呼叫和任何相依性解析程式所傳回的所有攔截器會登錄。</span><span class="sxs-lookup"><span data-stu-id="057c4-206">Note that these objects are requested using the GetServices call and all interceptors returned by any dependency resolver will registered.</span></span>

<span data-ttu-id="057c4-207">**索引鍵**： 無法使用，將會是 null。</span><span class="sxs-lookup"><span data-stu-id="057c4-207">**Key**: Not used; will be null.</span></span>  

### <a name="funcsystemdataentitydbcontext-actionstring-systemdataentityinfrastructureinterceptiondatabaselogformatter"></a><span data-ttu-id="057c4-208">L o c k < System.Data.Entity.DbContext、 動作 < 字串\>，System.Data.Entity.Infrastructure.Interception.DatabaseLogFormatter\></span><span class="sxs-lookup"><span data-stu-id="057c4-208">Func<System.Data.Entity.DbContext, Action<string\>, System.Data.Entity.Infrastructure.Interception.DatabaseLogFormatter\></span></span>  

<span data-ttu-id="057c4-209">**版本導入**: EF6.0.0</span><span class="sxs-lookup"><span data-stu-id="057c4-209">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="057c4-210">**傳回物件**： 將建立資料庫記錄檔格式器將會使用的處理站時所使用的內容。指定的內容上設定 Database.Log 屬性。</span><span class="sxs-lookup"><span data-stu-id="057c4-210">**Object returned**: A factory that will be used to create the database log formatter that will be used when the context.Database.Log property is set on the given context.</span></span>  

<span data-ttu-id="057c4-211">**索引鍵**： 無法使用，將會是 null。</span><span class="sxs-lookup"><span data-stu-id="057c4-211">**Key**: Not used; will be null.</span></span>  

### <a name="funcsystemdataentitydbcontext"></a><span data-ttu-id="057c4-212">L o c k < System.Data.Entity.DbContext\></span><span class="sxs-lookup"><span data-stu-id="057c4-212">Func<System.Data.Entity.DbContext\></span></span>  

<span data-ttu-id="057c4-213">**版本導入**: EF6.1.0</span><span class="sxs-lookup"><span data-stu-id="057c4-213">**Version introduced**: EF6.1.0</span></span>  

<span data-ttu-id="057c4-214">**傳回物件**： 用以建立移轉的內容執行個體，當內容並沒有可存取的無參數建構函式的處理站。</span><span class="sxs-lookup"><span data-stu-id="057c4-214">**Object returned**: A factory that will be used to create context instances for Migrations when the context does not have an accessible parameterless constructor.</span></span>  

<span data-ttu-id="057c4-215">**索引鍵**： 需要原廠的衍生 dbcontext 類型的型別物件。</span><span class="sxs-lookup"><span data-stu-id="057c4-215">**Key**: The Type object for the type of the derived DbContext for which a factory is needed.</span></span>  

### <a name="funcsystemdataentitycoremetadataedmimetadataannotationserializer"></a><span data-ttu-id="057c4-216">L o c k < System.Data.Entity.Core.Metadata.Edm.IMetadataAnnotationSerializer\></span><span class="sxs-lookup"><span data-stu-id="057c4-216">Func<System.Data.Entity.Core.Metadata.Edm.IMetadataAnnotationSerializer\></span></span>  

<span data-ttu-id="057c4-217">**版本導入**: EF6.1.0</span><span class="sxs-lookup"><span data-stu-id="057c4-217">**Version introduced**: EF6.1.0</span></span>  

<span data-ttu-id="057c4-218">**傳回物件**： 用以建立強型別自訂註釋序列化的序列化程式，使它們可以序列化，並且用於 Code First 移轉 desterilized 成 XML 的處理站。</span><span class="sxs-lookup"><span data-stu-id="057c4-218">**Object returned**: A factory that will be used to create serializers for serialization of strongly-typed custom annotations such that they can be serialized and desterilized into XML for use in Code First Migrations.</span></span>  

<span data-ttu-id="057c4-219">**索引鍵**： 正在註釋名稱序列化或還原序列化。</span><span class="sxs-lookup"><span data-stu-id="057c4-219">**Key**: The name of the annotation that is being serialized or deserialized.</span></span>  

### <a name="funcsystemdataentityinfrastructuretransactionhandler"></a><span data-ttu-id="057c4-220">L o c k < System.Data.Entity.Infrastructure.TransactionHandler\></span><span class="sxs-lookup"><span data-stu-id="057c4-220">Func<System.Data.Entity.Infrastructure.TransactionHandler\></span></span>  

<span data-ttu-id="057c4-221">**版本導入**: EF6.1.0</span><span class="sxs-lookup"><span data-stu-id="057c4-221">**Version introduced**: EF6.1.0</span></span>  

<span data-ttu-id="057c4-222">**傳回物件**： 用以建立交易的處理常式，以便可以套用特殊處理，例如處理認可失敗的情況下的處理站。</span><span class="sxs-lookup"><span data-stu-id="057c4-222">**Object returned**: A factory that will be used to create handlers for transactions so that special handling can be applied for situations such as handling commit failures.</span></span>  

<span data-ttu-id="057c4-223">**索引鍵**: ExecutionStrategyKey 物件，包含提供者非變異名稱和選擇性地將使用的交易處理常式的伺服器名稱。</span><span class="sxs-lookup"><span data-stu-id="057c4-223">**Key**: An ExecutionStrategyKey object that contains the provider invariant name and optionally a server name for which the transaction handler will be used.</span></span>  