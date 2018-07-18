---
title: Entity Framework-EF6 的個案研究
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: cd5d3ae3-717d-4095-a2ef-0e8fd72b1a2f
caps.latest.revision: 3
ms.openlocfilehash: 27c911799f957dd81a1866a3fd49e7f6cfa0059b
ms.sourcegitcommit: 390f3a37bc55105ed7cc5b0e0925b7f9c9e80ba6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2018
ms.locfileid: "39120487"
---
# <a name="microsoft-case-studies-for-entity-framework"></a><span data-ttu-id="b867c-102">Entity Framework 的 Microsoft 案例研究</span><span class="sxs-lookup"><span data-stu-id="b867c-102">Microsoft Case Studies for Entity Framework</span></span>
<span data-ttu-id="b867c-103">案例研究，在此頁面上反白顯示都利用 Entity Framework 的幾個實際生產環境專案。</span><span class="sxs-lookup"><span data-stu-id="b867c-103">The case studies on this page highlight a few real-world production projects that have employed Entity Framework.</span></span>
> [!NOTE]
> <span data-ttu-id="b867c-104">這些案例研究的詳細的版本已不再從 Microsoft 網站上取得。</span><span class="sxs-lookup"><span data-stu-id="b867c-104">The detailed versions of these case studies are no longer available on the Microsoft website.</span></span> <span data-ttu-id="b867c-105">因此已移除的連結。</span><span class="sxs-lookup"><span data-stu-id="b867c-105">Therefore the links have been removed.</span></span>

## <a name="epicor"></a><span data-ttu-id="b867c-106">Epicor</span><span class="sxs-lookup"><span data-stu-id="b867c-106">Epicor</span></span>
<span data-ttu-id="b867c-107">Epicor 是 （有超過 400 個開發人員） 在超過 150 個國家/地區的公司開發企業資源規劃 (ERP) 解決方案的大型的全球軟體公司。</span><span class="sxs-lookup"><span data-stu-id="b867c-107">Epicor is a large global software company (with over 400 developers) that develops Enterprise Resource Planning (ERP) solutions for companies in more than 150 countries.</span></span>
<span data-ttu-id="b867c-108">其旗艦產品 Epicor 9，以在服務導向架構 (SOA) 使用.NET Framework 為基礎。</span><span class="sxs-lookup"><span data-stu-id="b867c-108">Their flagship product, Epicor 9, is based on a Service-Oriented Architecture (SOA) using the .NET Framework.</span></span>
<span data-ttu-id="b867c-109">若要升級至 Visual Studio 2010 和.NET Framework 4.0，面臨許多的客戶要求，以支援 Language Integrated Query (LINQ)，和也想要降低後端 SQL 伺服器上的負載，該團隊決定。</span><span class="sxs-lookup"><span data-stu-id="b867c-109">Faced with numerous customer requests to provide support for Language Integrated Query (LINQ), and also wanting to reduce the load on their back-end SQL Servers, the team decided to upgrade to Visual Studio 2010 and the .NET Framework 4.0.</span></span>
<span data-ttu-id="b867c-110">它們使用 Entity Framework 4.0，才能夠達成這些目標，也可大幅簡化開發與維護。</span><span class="sxs-lookup"><span data-stu-id="b867c-110">Using the Entity Framework 4.0, they were able to achieve these goals and also greatly simplify development and maintenance.</span></span>
<span data-ttu-id="b867c-111">特別的是，Entity Framework 的豐富的 T4 支援能讓他們全面掌控其產生的程式碼，並會自動建置效能節約功能，例如預先編譯的查詢和快取中。</span><span class="sxs-lookup"><span data-stu-id="b867c-111">In particular, the Entity Framework’s rich T4 support allowed them to take full control of their generated code and automatically build in performance-saving features such as pre-compiled queries and caching.</span></span>

> <span data-ttu-id="b867c-112">「 我們進行一些效能測試，最近與現有的程式碼，而且我們能夠減少要求至 SQL Server 90%。</span><span class="sxs-lookup"><span data-stu-id="b867c-112">“We conducted some performance tests recently with existing code, and we were able to reduce the requests to SQL Server by 90 percent.</span></span>
<span data-ttu-id="b867c-113">這是因為 ADO.NET Entity Framework 4。 」</span><span class="sxs-lookup"><span data-stu-id="b867c-113">That is because of the ADO.NET Entity Framework 4.”</span></span> <span data-ttu-id="b867c-114">– Erik Johnson，副總裁產品研究</span><span class="sxs-lookup"><span data-stu-id="b867c-114">– Erik Johnson, Vice President, Product Research</span></span>  

## <a name="veracity-solutions"></a><span data-ttu-id="b867c-115">真實性解決方案</span><span class="sxs-lookup"><span data-stu-id="b867c-115">Veracity Solutions</span></span>
<span data-ttu-id="b867c-116">需要取得事件計劃的軟體系統，很難維護及擴充長期來看，真實性解決方案可以用 Visual Studio 2010 來重新撰寫功能強大且容易使用的豐富網際網路應用程式建立的 Silverlight 4。</span><span class="sxs-lookup"><span data-stu-id="b867c-116">Having acquired an event-planning software system that was going to be difficult to maintain and extend over the long-term, Veracity Solutions used Visual Studio 2010 to re-write it as a powerful and easy-to-use Rich Internet Application built on Silverlight 4.</span></span>
<span data-ttu-id="b867c-117">使用.NET RIA Services 時，他們能夠快速建置 Entity Framework，避免程式碼重複，並允許進行通用驗證，並驗證邏輯層之上的服務層級。</span><span class="sxs-lookup"><span data-stu-id="b867c-117">Using .NET RIA Services, they were able to quickly build a service layer on top of the Entity Framework that avoided code duplication and allowed for common validation and authentication logic across tiers.</span></span>  

> <span data-ttu-id="b867c-118">「 我們所銷售的 Entity Framework 時引進，和 Entity Framework 4 已證實是更好。</span><span class="sxs-lookup"><span data-stu-id="b867c-118">“We were sold on the Entity Framework when it was first introduced, and the Entity Framework 4 has proven to be even better.</span></span>
<span data-ttu-id="b867c-119">工具也有所提升，並更輕鬆地操作定義概念模型、 儲存體模型和這些模型之間對應的.edmx 檔案...使用 Entity Framework，我可以取得一天內使用該資料存取層，並建置它，我進行。</span><span class="sxs-lookup"><span data-stu-id="b867c-119">Tooling is improved, and it’s easier to manipulate the .edmx files that define the conceptual model, storage model, and mapping between those models... With the Entity Framework, I can get that data access layer working in a day—and build it out as I go along.</span></span>
<span data-ttu-id="b867c-120">Entity Framework 是我們的實際資料存取層;我不知道為什麼任何人都不會使用它。 」</span><span class="sxs-lookup"><span data-stu-id="b867c-120">The Entity Framework is our de facto data access layer; I don’t know why anyone wouldn’t use it.”</span></span> <span data-ttu-id="b867c-121">– Joe McBride 資深開發人員</span><span class="sxs-lookup"><span data-stu-id="b867c-121">– Joe McBride, Senior Developer</span></span>

## <a name="nec-display-solutions-of-america"></a><span data-ttu-id="b867c-122">America 專屬的 NEC 顯示解決方案</span><span class="sxs-lookup"><span data-stu-id="b867c-122">NEC Display Solutions of America</span></span>
<span data-ttu-id="b867c-123">NEC 想要輸入數位位置為基礎的廣告，廣告商和網路擁有者中獲益，並提升自己的營收的解決方案的市場。</span><span class="sxs-lookup"><span data-stu-id="b867c-123">NEC wanted to enter the market for digital place-based advertising with a solution to benefit advertisers and network owners and increase its own revenues.</span></span>
<span data-ttu-id="b867c-124">若要這樣做，就會啟動一組自動化手動程序需要在傳統的廣告活動中的 web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="b867c-124">In order to do that, it launched a pair of web applications that automate the manual processes required in a traditional ad campaign.</span></span>
<span data-ttu-id="b867c-125">使用 ASP.NET、 Silverlight 3、 AJAX 和 WCF，以及資料存取層中的 Entity Framework，與 SQL Server 2008 所建置的網站。</span><span class="sxs-lookup"><span data-stu-id="b867c-125">The sites were built using ASP.NET, Silverlight 3, AJAX and WCF, along with the Entity Framework in the data access layer to talk to SQL Server 2008.</span></span>

> <span data-ttu-id="b867c-126">「 SQL server，我們認為我們可以取得我們提供廣告商和網路服務與即時及可靠性，以協助確保一律會使用我們的任務關鍵性應用程式中的資訊中的資訊時所需的輸送量 」-Mike Corcoran協理 IT</span><span class="sxs-lookup"><span data-stu-id="b867c-126">“With SQL Server, we felt we could get the throughput we needed to serve advertisers and networks with information in real time and the reliability to help ensure that the information in our mission-critical applications would always be available”- Mike Corcoran, Director of IT</span></span>

## <a name="darwin-dimensions"></a><span data-ttu-id="b867c-127">達爾文港維度</span><span class="sxs-lookup"><span data-stu-id="b867c-127">Darwin Dimensions</span></span>
<span data-ttu-id="b867c-128">使用 Microsoft 廣泛的技術，達爾文港的小組著手建立 Evolver-取用者可以用來建立用於令人讚嘆、 lifelike 虛擬人偶遊戲、 動畫和社交網路的頁面中的線上虛擬人偶入口網站。</span><span class="sxs-lookup"><span data-stu-id="b867c-128">Using a wide range of Microsoft technologies, the team at Darwin set out to create Evolver - an online avatar portal that consumers could use to create stunning, lifelike avatars for use in games, animations, and social networking pages.</span></span>
<span data-ttu-id="b867c-129">使用 Entity Framework 和 Windows Workflow Foundation (WF) 和 Windows Server AppFabric （高度可擴充的記憶體內部應用程式快取） 等元件中提取的生產力優勢，小組就可以令人讚嘆的產品提供 35%較少開發時間。</span><span class="sxs-lookup"><span data-stu-id="b867c-129">With the productivity benefits of the Entity Framework, and pulling in components like Windows Workflow Foundation (WF) and Windows Server AppFabric (a highly-scalable in-memory application cache), the team was able to deliver an amazing product in 35% less development time.</span></span>
<span data-ttu-id="b867c-130">儘管團隊成員分成多個國家/地區，小組遵循敏捷式開發程序，並提供每週的版本。</span><span class="sxs-lookup"><span data-stu-id="b867c-130">Despite having team members split across multiple countries, the team following an agile development process with weekly releases.</span></span>

 > <span data-ttu-id="b867c-131">「 我們試著不要建立技術的目的的技術。</span><span class="sxs-lookup"><span data-stu-id="b867c-131">“We try not to create technology for technology’s sake.</span></span> <span data-ttu-id="b867c-132">為啟動時，很重要，我們運用技術，可以節省時間與金錢。</span><span class="sxs-lookup"><span data-stu-id="b867c-132">As a startup, it is crucial that we leverage technology that saves time and money.</span></span>
 <span data-ttu-id="b867c-133">.NET 是快速且符合成本效益的開發選擇。 」</span><span class="sxs-lookup"><span data-stu-id="b867c-133">.NET was the choice for fast, cost-effective development.”</span></span> <span data-ttu-id="b867c-134">– Zachary Olsen，架構設計人員</span><span class="sxs-lookup"><span data-stu-id="b867c-134">– Zachary Olsen, Architect</span></span>  

## <a name="silverware"></a><span data-ttu-id="b867c-135">銀器</span><span class="sxs-lookup"><span data-stu-id="b867c-135">Silverware</span></span>
<span data-ttu-id="b867c-136">15 年以上的開發小型和中型的餐廳群組的銷售點 (POS) 解決方案的經驗，開發小組在銀器著手來增強他們的產品，具有更多的企業層級功能以吸引更大餐廳鏈結。</span><span class="sxs-lookup"><span data-stu-id="b867c-136">With more than 15 years of experience in developing point-of-sale (POS) solutions for small and midsize restaurant groups, the development team at Silverware set out to enhance their product with more enterprise-level features in order to attract larger restaurant chains.</span></span>
<span data-ttu-id="b867c-137">使用最新版的 Microsoft 開發工具，它們能夠建置新的方案四倍速度。</span><span class="sxs-lookup"><span data-stu-id="b867c-137">Using the latest version of Microsoft’s development tools, they were able to build the new solution four times faster than before.</span></span>
<span data-ttu-id="b867c-138">索引鍵的新功能，例如 LINQ 和 Entity Framework，更輕鬆地從 Crystal Reports 移轉至 SQL Server 2008 和 SQL Server Reporting Services (SSRS) 來儲存其資料和報告需求。</span><span class="sxs-lookup"><span data-stu-id="b867c-138">Key new features like LINQ and the Entity Framework made it easier to move from Crystal Reports to SQL Server 2008 and SQL Server Reporting Services (SSRS) for their data storage and reporting needs.</span></span>

> <span data-ttu-id="b867c-139">「 有效的資料管理是機碼的銀器 – 成功而這就是為什麼我們決定採用 SQL Reporting。 」</span><span class="sxs-lookup"><span data-stu-id="b867c-139">“Effective data management is key to the success of SilverWare – and this is why we decided to adopt SQL Reporting.”</span></span> <span data-ttu-id="b867c-140">-Nicholas Romanidis，協理 IT / 軟體工程</span><span class="sxs-lookup"><span data-stu-id="b867c-140">- Nicholas Romanidis, Director of IT/Software Engineering</span></span>