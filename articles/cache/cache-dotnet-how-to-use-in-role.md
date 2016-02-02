<properties 
    pageTitle="如何使用 In-Role Cache (.NET) | Microsoft Azure" 
    description="了解如何使用 Azure In-Role Cache。這些範例均以 C# 程式碼撰寫，並使用 .NET API。" 
    services="cache" 
    documentationCenter=".net" 
    authors="steved0x" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="cache" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="12/03/2015" 
    ms.author="sdanie"/>







# 如何使用 Azure 快取的角色中快取

本指南說明如何開始使用 
這些範例均以 C\# 程式碼撰寫並
使用 .NET API。

如需

>[AZURE.IMPORTANT]我們現在宣布將在 2016 年 11 月 30 日淘汰「Azure 受管理的快取服務」和 Azure In-Role Cache。 我們建議您移轉到 Azure Redis Cache 以為這次淘汰做準備。

<a name="what-is"></a>
## 何謂角色中快取？

角色中快取可對 Azure 應用程式提供快取層。




-   


-   

-   


In-Role Cache 會使用託管 Azure 雲端服務 (又稱託管服務) 中的角色執行個體之虛擬機器的部分記憶體執行快取。 您會有更為彈性的部署選項可作選擇，快取的大小不僅可以非常大，而且沒有快取特有的配額限制。
>[AZURE.IMPORTANT] 從 Azure SDK 2.6 版開始，In-Role Cache 使用 Microsoft Azure 儲存體 SDK 4.3 版。 在 Azure SDK 舊版中，In-Role Cache 使用 Azure 儲存體 SDK 1.7。 Azure 儲存體版本 2011-08-18 即將於 2016 年 8 月 1 日解除委任，使用採用 Azure SDK 2.6 之前版本之 In-Role Cache 的應用程式請儘早移轉至 Azure SDK 2.6。

角色執行個體快取有下列優點：

-   不需為快取額外支付費用。 只需為主控快取的運算資源付費。
-   消除快取配額和節流。
-   提供更大的控制和隔離能力。
-   增進效能。
-   在角色放大或縮小時自動調整快取大小。 在新增或移除角色執行個體時，有效地調整可用於快取的記憶體。
-   提供不失真的開發時期偵錯。
-   支援記憶體快取通訊協定。

此外，角色執行個體快取也提供這些可配置選項：

-   設定專用快取角色，或在現有角色共置快取。
-   使快取可供相同雲端服務部署中的多個用戶端使用。
-   以不同屬性建立多個具名快取。
-   選擇性地對個別快取設定高可用性。
-   使用擴充的快取功能，例如區域、標記和通知。

本指南提供開始使用角色中快取的概觀。

<a name="getting-started-cache-role-instance"></a>
## 開始使用角色中快取

角色中快取提供一種方法，可透過主控角色執行個體的虛擬機器上的記憶體來啟用快取。 主控快取的角色執行個體也稱為「快取叢集」****。 角色執行個體快取部署技術有兩種：

-   **專用角色**快取 - 角色執行個體專門用於快取。
-   **共置角色**快取 - 快取與應用程式共用虛擬機器資源 (頻寬、CPU 和記憶體)。

若要對角色執行個體使用快取，您需要設定快取叢集，然後設定快取用戶端，以便它們可以存取快取叢集。

-   
-   

<a name="enable-caching"></a>
## 設定快取叢集

若要設定**共置角色**快取叢集，請選取您要用來主控快取叢集的角色。 在 [方案總管]**** 的角色屬性上按一下滑鼠右鍵，然後選擇 [屬性]****。

![RoleCache1][rolecache1]

切換至 [**快取**] 索引標籤、核取 [**啟用快取**] 核取方塊，然後指定所需的快取選項。 在 [背景工作角色]**** 或 [ASP.NET Web 角色]**** 中啟用快取時，預設組態為「共置角色」****快取，並將 30% 的角色執行個體記憶體配置給快取。 系統會自動設定預設快取，但如果想要的話，您也可以建立其他具名快取，這些快取將會共用配置的記憶體。

![RoleCache2][rolecache2]

若要設定「**專用角色**」快取叢集，請將「**快取背景工作角色**」新增至您的專案。

![RoleCache7][rolecache7]

當「**快取背景工作角色**」新增至專案時，預設組態為「**專用角色**」快取。

![RoleCache8][rolecache8]

一旦啟用快取，就可以設定快取叢集儲存體帳戶。 角色中快取需要 Azure 儲存體帳戶。 此儲存體帳戶會用來保留關於快取叢集的相關組態資料，而此快取叢集可從構成快取叢集的所有虛擬機器進行存取。 此儲存體帳戶是在快取叢集角色屬性頁面的 [**快取**] 索引標籤上指定，就在 [**Named Cache Settings**] 正上方。

![RoleCache10][rolecache10]

>如果未設定此儲存體帳戶，角色將無法啟動。

快取大小由以下幾項共同決定：角色的虛擬機器大小、角色的執行個體計數，以及快取叢集是設定為專用角色還是共置角色快取叢集。

>本節提供如何設定快取大小的簡化概觀。

若要設定虛擬機器大小和角色執行個體數目，請在 [**方案總管**] 的角色屬性上按一下滑鼠右鍵，然後選擇 [**屬性**]。

![RoleCache1][rolecache1]

切換至 [**組態**] 索引標籤。 預設 [執行個體計數]**** 為 1，而預設 [VM 大小]**** 為 [小型]****。

![RoleCache3][rolecache3]

VM 大小的記憶體總計如下：

-   **小型**：1.75 GB
-   **中型**：3.5 GB
-   **大型**：7 GB
-   **特大型**：14 GB


> 這些記憶體大小代表 VM 的可用記憶體總數，並由 OS、快取程序、快取資料和應用程式共用。  請注意，特大型**** VM 大小不支援快取。

指定 [共置角色]**** 快取時，會以虛擬機器記憶體的指定百分比來決定快取大小。 指定 [專用角色]**** 快取時，則會將虛擬機器的所有可用記憶體都用於快取。 如果設定兩個角色執行個體，則會使用相加在一起的虛擬機器記憶體。 這會構成快取叢集，可用快取記憶體會分配給多個角色執行個體，但以單一資源的樣貌呈現給快取的用戶端們。 設定其他角色執行個體會以相同方式增加快取大小。

一旦設定了快取叢集，您可以設定快取用戶端，以允許存取快取。

<a name="NuGet"></a>
## 設定快取用戶端

若要存取「角色中快取」的快取，用戶端必須位於相同部署內。 如果快取叢集為專用角色快取叢集，則用戶端為部署中的其他角色。  系統會提供一個 NuGet 套件，可用來設定每一個存取快取的用戶端角色。 若要將角色設定為使用 Caching NuGet 套件存取快取叢集，請在 [**方案總管**] 中的角色專案上按一下滑鼠右鍵，然後選擇 [**Manage NuGet Packages**]。

![RoleCache4][rolecache4]

選取 [**In-Role Cache**]、按一下 [**安裝**]，然後按一下 [**I Accept**]。

>如果 [**In-Role Cache**] 未出現在清單中，請將 **WindowsAzure.Caching** 鍵入 [**線上搜尋**] 文字方塊中，然後從結果中進行選取。

![RoleCache5][rolecache5]

NuGet 會執行數項工作：將必要的組態新增至角色的組態檔、將快取用戶端診斷層級設定新增至 Azure 應用程式的 ServiceConfiguration.cscfg 檔案，以及新增必要的組件參考。

>針對 ASP.NET Web 角色，Caching NuGet 封裝也會將兩個已標成註解的區段新增至 web.config 中。 第一個區段可讓工作階段儲存在快取中，第二個區段則可讓 ASP.NET 頁面輸出快取處理。

NuGet 封裝會將下列組態元素新增至角色的 web.config 或 app.config 中。 **dataCacheClients** 區段和 **cacheDiagnostics** 區段會新增至 **configSections** 元素之下。 如果沒有 **configSections** 元素，則會建立一個，作為 **configuration** 元素的子項。

    <configSections>
      
    
      <section name="dataCacheClients" 
               type="Microsoft.ApplicationServer.Caching.DataCacheClientsSection, Microsoft.ApplicationServer.Caching.Core" 
               allowLocation="true" 
               allowDefinition="Everywhere" />
    
      <section name="cacheDiagnostics" 
               type="Microsoft.ApplicationServer.Caching.AzureCommon.DiagnosticsConfigurationSection, Microsoft.ApplicationServer.Caching.AzureCommon" 
               allowLocation="true" 
               allowDefinition="Everywhere" />
    </configSections>

這些新的區段包括 **dataCacheClients** 元素和 **cacheDiagnostics** 元素的參考。 這些元素也會新增至 **configuration** 元素。

    <dataCacheClients>
      <dataCacheClient name="default">
        <autoDiscover isEnabled="true" identifier="[cache cluster role name]" />
        
      </dataCacheClient>
    </dataCacheClients>
    <cacheDiagnostics>
      <crashDump dumpLevel="Off" dumpStorageQuotaInMB="100" />
    </cacheDiagnostics>

在新增組態之後，請將 **[cache cluster role name]** 取代為主控快取叢集的角色名稱。

>如果未將 **[cache cluster role name]** 取代為主控快取叢集的角色名稱，則存取快取時，將擲回 **TargetInvocationException**，並隨附內部 **DatacacheException** 和訊息 "No such role exists"。

NuGet 封裝也會將 **ClientDiagnosticLevel** 設定新增至 ServiceConfiguration.cscfg 中快取用戶端角色的 **ConfigurationSettings** 中。 下列範例是 ServiceConfiguration.cscfg 檔案中的 **WebRole1** 區段，該檔案的 **ClientDiagnosticLevel** 為 1，亦即預設的 **ClientDiagnosticLevel**。

    <Role name="WebRole1">
      <Instances count="1" />
      <ConfigurationSettings>
        
        <Setting name="Microsoft.WindowsAzure.Plugins.Caching.ClientDiagnosticLevel" 
                 value="1" />
      </ConfigurationSettings>
    </Role>

>角色中快取同時提供快取伺服器和快取用戶端診斷層級。 診斷層級為單一設定，用來設定針對快取收集的診斷資訊層級。

NuGet 套件也會新增下列組件的參考：

-   Microsoft.ApplicationServer.Caching.Client.dll
-   Microsoft.ApplicationServer.Caching.Core.dll
-   Microsoft.WindowsFabric.Common.dll
-   Microsoft.WindowsFabric.Data.Common.dll
-   Microsoft.ApplicationServer.Caching.AzureCommon.dll
-   Microsoft.ApplicationServer.Caching.AzureClientHelper.dll

如果您的角色為 ASP.NET Web 角色，則也會新增下列組件參考：

-   Microsoft.Web.DistributedCache.dll

一旦設定用戶端專案的快取功能，您就可以使用下列幾節中描述的技術來使用快取。

<a name="working-with-caches"></a>
## 使用快取

本節中的步驟描述如何利用快取執行常見工作。

-   
-   
-   
-   
-   

<a name="create-cache-object"></a>
## 做法：建立 DataCache 物件

為了能夠以程式設計方式使用快取，您需要快取的參考。 將下列程式碼新增至您想要從中使用
In-Role Cache：

    using Microsoft.ApplicationServer.Caching;

>如果在安裝了會加入必要參考的 Caching NuGet 封裝之後，Visual Studio 還是無法辨識 using
>陳述式中的類型，請確定專案的目標
>

有兩種方式可建立 **DataCache** 物件。 第一種方式為僅建立 **DataCache**，並傳入所需快取的名稱。

    DataCache cache = new DataCache("default");

一旦 **DataCache** 具現化，您就可以依下列幾節所述使用它，與快取互動。

若要使用第二種方式，請使用預設建構函式，在應用程式中建立新的 **DataCacheFactory** 物件。 這會導致快取用戶端使用組態檔中的設定。 呼叫新 **DataCacheFactory** 執行個體的 **GetDefaultCache** 方法，傳回 **DataCache** 物件，或呼叫 **GetCache** 方法，並傳入快取的名稱。 這些方法會傳回 **DataCache** 物件，然後可以用來以程式設計方式存取快取。

    // Cache client configured by settings in application configuration file.
    DataCacheFactory cacheFactory = new DataCacheFactory();
    DataCache cache = cacheFactory.GetDefaultCache();
    // Or DataCache cache = cacheFactory.GetCache("MyCache");
    // cache can now be used to add and retrieve items. 

<a name="add-object"></a>
## 做法：從快取新增和擷取物件





    // Add the string "value" to the cache, keyed by "item"
    cache.Add("item", "value");




> 
> 索引鍵已存在的物件。 快取將只會接受
> 物件的唯一索引鍵值。

若要擷取具有特定索引鍵的物件，可以使用 **Get** 方法。


    // Add the string "value" to the cache, keyed by "key"
    object result = cache.Get("Item");
    if (result == null)
    {
        // "Item" not in cache. Obtain it from specified data source
        // and add it.
        string value = GetItemValue(...);
        cache.Add("item", value);
    }
    else
    {
        // "Item" is in cache, cast result to correct type.
    }




    // Add the string "value" to the cache, keyed by "item". If it exists,
    // replace it.
    cache.Put("item", "value");

<a name="specify-expiration"></a>
## 做法：指定快取中物件的到期時間

依預設，快取中的項目會在放入快取十分鐘後到期。 您可在 [**存留時間 (分鐘)**] 設定中設定此值，該設定位於主控快取叢集之角色的角色屬性中。

![RoleCache6][rolecache6]

總共有三種類型的**到期類型**：**無**，**絕對**，和**滑動視窗**。 這些類型會設定如何使用 [存留時間 (分鐘)]**** 來決定到期時間。 預設 [到期類型]**** 為 [絕對]****，表示當項目放入快取時，項目到期時間的倒數計時器即會開始倒數。 一旦過了項目的指定時間量，項目即到期。 如果指定 [滑動視窗]****，則每次存取快取中的項目，就會重設項目的到期倒數時間，而且項目將不會到期，直到自前次存取後已過了指定的時間量。 如果指定 [無]****，則 [存留時間 (分鐘)]**** 必須設為 **0**，項目將不會到期，而且只要它們留在快取中就會一直有效。


在



    // Add the string "value" to the cache, keyed by "item"
    cache.Add("item", "value", TimeSpan.FromMinutes(30));






    // Get a DataCacheItem object that contains information about
    // "item" in the cache. If there is no object keyed by "item" null
    // is returned. 
    DataCacheItem item = cache.GetCacheItem("item");
    TimeSpan timeRemaining = item.Timeout;

<a name="store-session"></a>
## 做法：將 ASP.NET 工作階段狀態儲存在快取中




若要使用快取工作階段
在安裝 Caching NuGet 套件時，它會在 web.config 新增已標成註解的區段，其中包含讓 ASP.NET 應用程式對角色中快取使用工作階段狀態提供者所需的組態。


>

若要對角色中快取啟用工作階段狀態提供者，請將指定的區段取消註解。 預設快取是在提供的片段中指定。 若要使用不同快取，請在 **cacheName** 屬性中指定所需的快取。




<a name="store-page"></a>
## 做法：將 ASP.NET 頁面輸出快取儲存在快取中

角色中快取的輸出快取提供者為輸出快取資料的程序外儲存體機制。

收件人
在安裝 Caching NuGet 套件時，它會在 web.config 新增下列已標成註解的區段，其中包含讓 ASP.NET 應用程式對角色中快取使用輸出快取提供者所需的組態。


>

若要對角色中快取啟用輸出快取提供者，請將指定的區段取消註解。 預設快取是在提供的片段中指定。 若要使用不同快取，請在 **cacheName** 屬性中指定所需的快取。

將 **OutputCache** 指示詞新增至每一個您要快取輸出的頁面。

    <%@ OutputCache Duration="60" VaryByParam="*" %>

在此範例中，已快取的頁面資料會留在快取中 60 秒，而且會對每一個參數組合快取不同版本的頁面。



<a name="next-steps"></a>
## 後續步驟

現在，您已了解 In-Role Cache 的基本概念，
請循著下列連結以了解如何執行更複雜的快取工作。

-   
-   
-   
-   



[next steps]: #next-steps 
[what is in-role cache?]: #what-is 
[create an azure cache]: #create-cache 
[which type of caching is right for me?]: #choosing-cache 
[getting started with the in-role cache service]: #getting-started-cache-service 
[prepare your visual studio project to use in-role cache]: #prepare-vs 
[configure your application to use caching]: #configure-app 
[getting started with in-role cache]: #getting-started-cache-role-instance 
[configure the cache cluster]: #enable-caching 
[configure the desired cache size]: #cache-size 
[configure the cache clients]: #NuGet 
[working with caches]: #working-with-caches 
[how to: create a datacache object]: #create-cache-object 
[how to: add and retrieve an object from the cache]: #add-object 
[how to: specify the expiration of an object in the cache]: #specify-expiration 
[how to: store asp.net session state in the cache]: #store-session 
[how to: store asp.net page output caching in the cache]: #store-page 
[target a supported .net framework profile]: #prepare-vs-target-net 
[rolecache1]: ./media/cache-dotnet-how-to-use-in-role/cache8.png 
[rolecache2]: ./media/cache-dotnet-how-to-use-in-role/cache9.png 
[rolecache3]: ./media/cache-dotnet-how-to-use-in-role/cache10.png 
[rolecache4]: ./media/cache-dotnet-how-to-use-in-role/cache11.png 
[rolecache5]: ./media/cache-dotnet-how-to-use-in-role/cache12.png 
[rolecache6]: ./media/cache-dotnet-how-to-use-in-role/cache13.png 
[rolecache7]: ./media/cache-dotnet-how-to-use-in-role/cache14.png 
[rolecache8]: ./media/cache-dotnet-how-to-use-in-role/cache15.png 
[rolecache10]: ./media/cache-dotnet-how-to-use-in-role/cache17.png 
[how to configure virtual machine sizes]: http://go.microsoft.com/fwlink/?LinkId=164387 
[how to: configure a cache client programmatically]: http://msdn.microsoft.com/library/windowsazure/gg618003.aspx 
[how to: set a page's cacheability programmatically]: http://msdn.microsoft.com/library/z852zf6b.aspx 
[how to: set the cacheability of an asp.net page declaratively]: http://msdn.microsoft.com/library/zd1ysf1y.aspx 
[in-role cache capacity planning considerations]: http://go.microsoft.com/fwlink/?LinkId=252651 
[in-role cache samples]: http://msdn.microsoft.com/library/jj189876.aspx 
[in-role cache]: http://go.microsoft.com/fwlink/?LinkId=252658 
[in-role cache]: http://www.microsoft.com/showcase/Search.aspx?phrase=azure+caching 
[maximum performance: accelerate your cloud services applications with azure caching]: http://channel9.msdn.com/Events/TechEd/NorthAmerica/2013/WAD-B326#fbid=kmrzkRxQ6gU 
[migrate to in-role cache]: http://msdn.microsoft.com/library/hh914163.aspx 
[nuget package manager installation]: http://go.microsoft.com/fwlink/?LinkId=240311 
[output cache provider for in-role cache]: http://msdn.microsoft.com/library/windowsazure/gg185662.aspx 
[outputcache directive]: http://go.microsoft.com/fwlink/?LinkId=251979 
[overview of in-role cache]: http://go.microsoft.com/fwlink/?LinkId=254172 
[session state provider for in-role cache]: http://msdn.microsoft.com/library/windowsazure/gg185668.aspx 
[team blog]: http://blogs.msdn.com/b/windowsazure/ 
[troubleshooting and diagnostics for in-role cache]: http://msdn.microsoft.com/library/windowsazure/hh914135.aspx 
[azure appfabric cache: caching session state]: http://www.microsoft.com/showcase/details.aspx?uuid=87c833e9-97a9-42b2-8bb1-7601f9b5ca20 
[azure shared caching]: http://msdn.microsoft.com/library/windowsazure/gg278356.aspx 
[which azure cache offering is right for me?]: cache-faq.md#which-azure-cache-offering-is-right-for-me 

