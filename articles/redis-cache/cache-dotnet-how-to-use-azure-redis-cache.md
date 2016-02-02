<properties 
    pageTitle="如何使用 Azure Redis 快取" 
    description="了解如何改善與 Azure Redis 快取的 Azure 應用程式的效能" 
    services="redis-cache,app-service" 
    documentationCenter="" 
    authors="steved0x" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="cache" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="cache-redis" 
    ms.devlang="dotnet" 
    ms.topic="hero-article" 
    ms.date="12/03/2015" 
    ms.author="sdanie"/>


# 如何使用 Azure Redis 快取

> [AZURE.SELECTOR]
- [.Net](cache-dotnet-how-to-use-azure-redis-cache.md)
- [Node.js](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)


本指南示範如何開始使用 **Azure Redis 快取**。 Microsoft Azure Redis 快取是基於受歡迎的開放原始碼 Redis 快取。 它可讓您存取由 Microsoft 管理的安全、專用 Redis 快取。 使用 Azure Redis 快取建立的快取，可透過 Microsoft Azure 內的任何應用程式加以存取。

Microsoft Azure Redis 快取有下列階層：

-   **基本** - 單一節點。 多種大小，最高為 53 GB。
-   **標準** – 兩個節點 (主要/從屬)。 多種大小，最高為 53 GB。99.9% SLA。
-   **進階** – 兩個節點的主要/從屬，最多具有 10 個分區。 從 6 GB 到 530 GB 的多種大小 (如需詳細資訊，請與我們連絡)。 標準層的所有功能以及更多支援 [Redis 叢集](cache-how-to-premium-clustering.md), ，[Redis 持續性](cache-how-to-premium-persistence.md), ，和 [Azure 虛擬網路](cache-how-to-premium-vnet.md)。 99.9% SLA。

每一個階層都有不同的功能和定價。 如需定價資訊，請參閱 [[快取定價詳細資料]][]。

本指南說明如何使用 [StackExchange.Redis []][] 用戶端會使用 C# 程式碼。 涵蓋的案例包括**建立和設定快取**、**設定快取用戶端**，以及**加入和移除快取中的物件**。 如需有關如何使用 Azure Redis 快取的詳細資訊，請參閱 [後續步驟 []][] 一節。

<a name="getting-started-cache-service"></a>
## 開始使用 Azure Redis 快取

開始使用 Azure Redis 快取相當簡單。 若要開始，請佈建並設定快取。 接著，設定快取用戶端，以便它們可以存取快取。 一旦設定了快取用戶端，就可以開始使用它們。

-   [建立快取]][]
-   [設定快取的用戶端]][]

<a name="create-cache"></a>
## 建立快取

若要建立快取，請先登入 [Azure 入口網站 []][], ，然後按一下 **新增**, ，**資料 + 儲存體**, ，**Redis 快取**。

![New cache][newcachemenu]
>[AZURE.NOTE] 如果您沒有 Azure 帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。 如需詳細資訊，請參閱 [Azure 免費試用版 []][]。

在 [新增 Redis 快取]**** 分頁中，指定所需的快取組態。

![Create cache][cachecreate]

-   在 [DNS 名稱]**** 中，輸入要用於快取端點的子網域名稱。 端點必須是介於 6 到 20 個字元之間的字串、僅包含小寫數字和字母，而且必須以字母開頭。
-   針對 [訂閱]****，選取您要用於快取的 Azure 訂閱。 如果您的帳戶僅有一個訂閱，則會自動加以選取，而且不會顯示 [訂閱]**** 下拉式清單。
-   在 [資源群組]**** 中，選取或建立快取的資源群組。 如需詳細資訊，請參閱 [使用資源群組管理您的 Azure 資源 []][]。
-   使用 [位置]**** 來指定管理快取所在的地理位置。 為獲得最佳效能，Microsoft 強烈建議您在與快取用戶端應用程式相同的區域中建立快取。
-   使用 [價格層]**** 來選取需要的快取大小和功能。
-   **Redis 叢集**可讓您建立大於 53 GB 的快取，以及將資料分散於多個 Redis 節點。 如需詳細資訊，請參閱 [如何設定高階 Azure Redis 快取叢集](cache-how-to-premium-clustering.md)。
-   **Redis 持續性**可讓您將您的快取保存至 Azure 儲存體帳戶。 如需設定持續性的指示，請參閱 [如何設定高階 Azure Redis 快取的持續性](cache-how-to-premium-persistence.md)。
-   **虛擬網路**藉由將您的快取存取權限制於指定的 Azure 虛擬網路內的用戶端，以提供增強的安全性和隔離。 您可以使用 VNet 的所有功能，例如子網路、存取控制原則和其他功能，進一步限制對 Redis 的存取權。 如需詳細資訊，請參閱 [如何設定虛擬網路支援高階 Azure Redis 快取](cache-how-to-premium-vnet.md)。
-   使用 [診斷]**** 來指定快取度量的儲存體帳戶。 如需有關設定及檢視快取度量的詳細資訊，請參閱 [如何監視 Azure Redis 快取](cache-how-to-monitor.md)。

一旦設定了新的快取選項，請按一下 [建立新快取]****。 建立快取可能需要數分鐘的時間。 若要檢查狀態，您可以監視開始面板上的進度。 在建立了快取之後，新快取的狀態會是「執行中」****，而且準備好與預設設定搭配使用。

![Cache created][cachecreated]

建立快取之後，您可以從 [瀏覽]**** 分頁加以存取。

![Browse blade][browsecaches]

按一下 [Redis 快取]**** 來檢視您的快取。

![Caches][caches]

<a name="NuGet"></a>
## 設定快取用戶端

使用 Azure Redis 快取建立的快取，可透過任何 Azure 應用程式來存取。 Visual Studio 中開發的 .NET 應用程式可以使用 **StackExchange.Redis** 快取用戶端，該用戶端可使用能簡化快取用戶端應用程式組態的 NuGet 封裝來加以設定。
>[AZURE.NOTE] 如需詳細資訊，請參閱 [StackExchange.Redis []][] github 頁面和 [StackExchange.Redis 快取的用戶端文件 []][]。

若要在 Visual Studio 中使用 StackExchange.Redis NuGet 封裝來設定用戶端應用程式，請在 [方案總管]**** 中的專案上按一下滑鼠右鍵，然後選擇 [管理 NuGet 封裝]****。

![Manage NuGet packages][nugetmenu]

在 [線上搜尋]**** 文字方塊中輸入 **StackExchange.Redis** 或 **StackExchange.Redis.StrongName**，從結果選取需要的版本，然後按一下 [安裝]****。
>[AZURE.NOTE] 如果您偏好使用強式名稱版本的 **StackExchange.Redis** 用戶端程式庫，請選取 **StackExchange.Redis.StrongName**；否則選取 **StackExchange.Redis**。

![StackExchange.Redis NuGet package][stackexchangenuget]

NuGet 封裝會為您的用戶端應用程式下載並加入必要的組件參考，以利用 StackExchange.Redis 快取用戶端來存取 Azure Redis 快取。

一旦設定用戶端專案的快取功能，您就可以使用下列幾節中描述的技術來使用快取。

<a name="working-with-caches"></a>
## 使用快取

本節中的步驟描述如何利用快取執行常見工作。

-   [連接到快取]][]
-   [加入和擷取物件從快取]][]
-   [使用的快取中的.NET 物件](#work-with-net-objects-in-the-cache)

<a name="connect-to-cache"></a>
## 連接到快取

為了能夠以程式設計方式使用快取，您需要快取的參考。 將下面這一行加入至您想要從中使用 StackExchange.Redis 用戶端來存取 Azure Redis 快取之檔案的頂端。

    using StackExchange.Redis;

>[AZURE.NOTE] StackExchange.Redis 用戶端需要 .NET Framework 4 或更高版本。

Azure Redis 快取連線由管理 `ConnectionMultiplexer` 類別。 此類別的設計是要在用戶端應用程式中共用和重複使用，而不需要依據每個作業加以建立。

連接到 Azure Redis 快取，並傳回已連接的執行個體 `ConnectionMultiplexer`, ，呼叫靜態 `連線` 方法並傳入快取端點和金鑰如下列範例所示。 使用從 Azure 入口網站產生的金鑰做為密碼參數。

    ConnectionMultiplexer connection = ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");

>[AZURE.IMPORTANT] 警告：請勿將認證儲存在原始程式碼中。 為了讓這個範例簡單明瞭，我會以原始程式碼來呈現認證內容。 請參閱 [應用程式字串與連接字串的運作 []][] 如需有關如何儲存認證資訊。

如果您不想使用 SSL，請設定 `ssl = false` 或省略 `ssl` 參數。
>[AZURE.NOTE] 預設會為新快取停用非 SSL 連接埠。 如需啟用非 SSL 連接埠的指示，請參閱 [存取連接埠](cache-configure.md#access-ports)...

其中一種方式共用 `ConnectionMultiplexer` 應用程式中的執行個體是靜態屬性傳回已連接的執行個體，類似下列的範例。 這會提供執行緒安全的方式，來初始化只有一個連線 `ConnectionMultiplexer` 執行個體。 這些範例中 `abortConnect` 設為 false，表示將繼續呼叫，即使未建立 Azure Redis 快取的連線。 主要功能之一 `ConnectionMultiplexer` 的原因是它會自動還原連線至快取一次網路問題或其他原因，在解決。

    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        return ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");
    });
    
    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }

如需有關進階連線組態選項，請參閱 [StackExchange.Redis 組態模型 []][]。

快取端點和金鑰可自您快取執行個體的 [Redis 快取]**** 分頁取得。

![Cache properties][cacheproperties]

![Manage keys][managekeys]

一旦建立連線之後，會透過呼叫傳回 redis 快取資料庫的參考 `ConnectionMultiplexer.GetDatabase` 方法。 從傳回的物件 `GetDatabase` 方法是輕量型傳遞物件，並不需要儲存。

    // Connection refers to a property that returns a ConnectionMultiplexer
    // as shown in the previous example.
    IDatabase cache = Connection.GetDatabase();
    
    // Perform cache operations using the cache object...
    // Simple put of integral data types into the cache
    cache.StringSet("key1", "value");
    cache.StringSet("key2", 25);
    
    // Simple get of data types from the cache
    string key1 = cache.StringGet("key1");
    int key2 = (int)cache.StringGet("key2");

現在您知道如何連線至 Azure Redis 快取執行個體，並傳回快取資料庫的參考，讓我們看看如何使用快取。

<a name="add-object"></a>
## 從快取加入和擷取物件

可以在儲存或使用擷取自快取項目 `StringSet` 和 `StringGet` 方法。

    // If key1 exists, it is overwritten.
    cache.StringSet("key1", "value1");
    
    string value = cache.StringGet("key1");

Redis 會將多數資料儲存為 Redis 字串，但這些字串可能包含許多類型的資料，包括序列化的二進位資料 (在快取中儲存 .NET 物件時可能會用到)。

當呼叫 `StringGet`, ，如果物件存在，則會傳回，而且如果沒有出現，請 `null` 會傳回。 在此情況下，您可以從需要的資料來源中擷取值，並將它儲存在快取中供後續使用。 這稱為另行快取模式。

    string value = cache.StringGet("key1");
    if (value == null)
    {
        // The item keyed by "key1" is not in the cache. Obtain
        // it from the desired data source and add it to the cache.
        value = GetValueFromDataSource();
    
        cache.StringSet("key1", value);
    }

若要指定快取中的項目的到期，請使用 `TimeSpan` 參數 `StringSet`。

    cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));

## 使用快取中的 .NET 物件

Azure Redis 快取可以快取 .NET 物件及基本資料類型，但必須先將 .NET 物件序列化，才能加以快取。 這是應用程式開發人員的責任，同時賦與開發人員選擇序列化程式的彈性。

將物件序列化最簡單方式是使用 `JsonConvert` 中的序列化方法 [Newtonsoft.Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/8.0.1-beta1) 和往返於 JSON 序列化。 下列範例示範 get 和 set 使用 `員工` 物件執行個體。


    class Employee
    {
        public int Id { get; set; }
        public string Name { get; set; }
    
        public Employee(int EmployeeId, string Name)
        {
            this.Id = EmployeeId;
            this.Name = Name;
        }
    }
    
    // Store to cache
    cache.StringSet("e25", JsonConvert.SerializeObject(new Employee(25, "Clayton Gragg")));
    
    // Retrieve from cache
    Employee e25 = JsonConvert.DeserializeObject<Employee>(cache.StringGet("e25"));

<a name="next-steps"></a>
## 後續步驟

了解基礎概念之後，請依照下列連結深入了解 Azure Redis 快取。

-   查看 Azure Redis 快取的 ASP.NET 提供者。
    -   [Azure Redis 工作階段狀態提供者](cache-asp.net-session-state-provider.md)
    -   [Azure Redis 快取的 ASP.NET 輸出快取提供者](cache-asp.net-output-cache-provider.md)
-   [啟用快取診斷](cache-how-to-monitor.md#enable-cache-diagnostics) 讓您可以 [監視](cache-how-to-monitor.md) 快取的健全狀況。 您可以在 Azure 入口網站中檢視度量，您也可以 [下載並檢閱](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) 使用您選擇的工具。
-   簽出 [StackExchange.Redis 快取的用戶端文件 []][]。
    -   Azure Redis 快取可以透過許多 Redis 用戶端和開發語言進行存取。 如需詳細資訊，請參閱 [http://redis.io/clients []][] 和 [以 Azure Redis 快取 [] 的其他語言開發][]。
    -   Azure Redis 快取也可以與服務搭配使用 (例如 Redsmin)。 如需詳細資訊，請參閱  [如何擷取 Azure Redis 連接字串，並將它與 redsmin 搭配使用 []][]。
-   請參閱 [redis []][] 文件，並閱讀有關 [redis 資料類型 []][] 和 [15 分鐘簡介 Redis 資料類型 []][]。







[next steps]: #next-steps 
[introduction to azure redis cache (video)]: #video 
[what is azure redis cache?]: #what-is 
[create an azure cache]: #create-cache 
[which type of caching is right for me?]: #choosing-cache 
[prepare your visual studio project to use azure caching]: #prepare-vs 
[configure your application to use caching]: #configure-app 
[get started with azure redis cache]: #getting-started-cache-service 
[create the cache]: #create-cache 
[configure the cache]: #enable-caching 
[configure the cache clients]: #NuGet 
[working with caches]: #working-with-caches 
[connect to the cache]: #connect-to-cache 
[add and retrieve objects from the cache]: #add-object 
[specify the expiration of an object in the cache]: #specify-expiration 
[store asp.net session state in the cache]: #store-session 
[newcachemenu]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-new-cache-menu.png 
[cachecreate]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-cache-create.png 
[stackexchangenuget]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-stackexchange-redis.png 
[nugetmenu]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-manage-nuget-menu.png 
[cacheproperties]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-properties.png 
[managekeys]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-manage-keys.png 
[sessionstatenuget]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-session-state-provider.png 
[browsecaches]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-browse-caches.png 
[caches]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-caches.png 
[cachecreated]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-cache-created.png 
[http://redis.io/clients]: http://redis.io/clients 
[develop in other languages for azure redis cache]: http://msdn.microsoft.com/library/azure/dn690470.aspx 
[how to retrieve an azure redis connection string and use it with redsmin]: https://redsmin.uservoice.com/knowledgebase/articles/485711-how-to-connect-redsmin-to-azure-redis-cache 
[azure redis session state provider]: http://go.microsoft.com/fwlink/?LinkId=398249 
[how to: configure a cache client programmatically]: http://msdn.microsoft.com/library/windowsazure/gg618003.aspx 
[session state provider for azure cache]: http://go.microsoft.com/fwlink/?LinkId=320835 
[azure appfabric cache: caching session state]: http://www.microsoft.com/showcase/details.aspx?uuid=87c833e9-97a9-42b2-8bb1-7601f9b5ca20 
[output cache provider for azure cache]: http://go.microsoft.com/fwlink/?LinkId=320837 
[azure shared caching]: http://msdn.microsoft.com/library/windowsazure/gg278356.aspx 
[team blog]: http://blogs.msdn.com/b/windowsazure/ 
[azure caching]: http://www.microsoft.com/showcase/Search.aspx?phrase=azure+caching 
[how to configure virtual machine sizes]: http://go.microsoft.com/fwlink/?LinkId=164387 
[azure caching capacity planning considerations]: http://go.microsoft.com/fwlink/?LinkId=320167 
[azure caching]: http://go.microsoft.com/fwlink/?LinkId=252658 
[how to: set the cacheability of an asp.net page declaratively]: http://msdn.microsoft.com/library/zd1ysf1y.aspx 
[how to: set a page's cacheability programmatically]: http://msdn.microsoft.com/library/z852zf6b.aspx 
[configure a cache in azure redis cache]: http://msdn.microsoft.com/library/azure/dn793612.aspx 
[stackexchange.redis configuration model]: http://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Configuration.md 
[work with .net objects in the cache]: http://msdn.microsoft.com/library/dn690521.aspx#Objects 
[nuget package manager installation]: http://go.microsoft.com/fwlink/?LinkId=240311 
[cache pricing details]: http://www.windowsazure.com/pricing/details/cache/ 
[azure portal]: https://portal.azure.com/ 
[overview of azure redis cache]: http://go.microsoft.com/fwlink/?LinkId=320830 
[azure redis cache]: http://go.microsoft.com/fwlink/?LinkId=398247 
[migrate to azure redis cache]: http://go.microsoft.com/fwlink/?LinkId=317347 
[azure redis cache samples]: http://go.microsoft.com/fwlink/?LinkId=320840 
[using resource groups to manage your azure resources]: http://azure.microsoft.com/documentation/articles/resource-group-overview/ 
[stackexchange.redis]: http://github.com/StackExchange/StackExchange.Redis 
[stackexchange.redis cache client documentation]: http://github.com/StackExchange/StackExchange.Redis#documentation 
[redis]: http://redis.io/documentation 
[redis data types]: http://redis.io/topics/data-types 
[a fifteen minute introduction to redis data types]: http://redis.io/topics/data-types-intro 
[how application strings and connection strings work]: http://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/ 
[azure free trial]: http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero 

