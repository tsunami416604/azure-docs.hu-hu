<properties 
    pageTitle="如何使用 In-Role Cache (.NET) | Microsoft Azure" 
    description="了解如何使用 Azure In-Role Cache。 這些範例均以 C# 程式碼撰寫，並使用 .NET API。" 
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
**Azure 快取的角色中快取**。 這些範例均以 C\# 程式碼撰寫並
使用 .NET API。 涵蓋的案例包括 **設定快取叢集**, ，**設定快取用戶端**, ，**新增和移除
從快取中，將 ASP.NET 工作階段狀態儲存在快取物件**,，
和 **啟用 ASP.NET 頁面輸出快取使用快取**。 如需
有關使用角色中快取，請參閱 [下一步][] 一節。

>[AZURE.IMPORTANT]我們正在宣佈 Azure 受管理快取服務與 Azure 角色中快取 2016 年 11 月 30 日淘汰。 我們建議您移轉至 Azure Redis 快取以為這次淘汰做準備。 如需有關日期和移轉指導方針的詳細資訊，請參閱 [哪個 Azure 快取服務是最適合我？](../redis-cache/cache-faq.md#which-azure-cache-offering-is-right-for-me)

<a name="what-is"></a>
## 何謂角色中快取？

角色中快取可對 Azure 應用程式提供快取層。 暫時儲存資訊在記憶體內快取增進效能
其他後端來源，並可減少與資料庫相關聯的成本
在雲端中的交易。 包括下列項目角色中快取
功能 ︰

-   工作階段狀態及頁面輸出預先建置的 ASP.NET 提供者
    快取，讓 web 應用程式加速，卻不必
    修改應用程式程式碼。
-   快取任何可序列化的受管理的物件-例如 ︰ CLR 物件、 資料列、 XML，
    二進位資料。
-   Azure 和 Windows 的一致的開發模型
    Server AppFabric。

In-Role Cache 會使用託管 Azure 雲端服務 (又稱託管服務) 中的角色執行個體之虛擬機器的部分記憶體執行快取。 您會有更為彈性的部署選項可作選擇，快取的大小不僅可以非常大，而且沒有快取特有的配額限制。

>[AZURE.IMPORTANT] 從 Azure SDK 2.6 開始，角色中快取使用 Microsoft Azure 儲存體 SDK 4.3 版。 在 Azure SDK 舊版中，In-Role Cache 使用 Azure 儲存體 SDK 1.7。 Azure 儲存體版本 2011-08-18 即將於 2016 年 8 月 1 日解除委任，使用採用 Azure SDK 2.6 之前版本之 In-Role Cache 的應用程式請儘早移轉至 Azure SDK 2.6。 如需詳細資訊，請參閱 [Azure SDK 2.6 版本資訊角色中快取](../azure-sdk-dotnet-release-notes-2_6.md#in-role-cache-updates) 和 [Microsoft Azure 儲存體服務版本移除更新 ︰ 延伸至 2016年](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/10/19/microsoft-azure-storage-service-version-removal-update-extension-to-2016.aspx)。

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

本指南提供開始使用角色中快取的概觀。 如需詳細討論本入門指南的範圍內的這些功能的詳細資訊，請參閱 [角色中快取概觀][]。

<a name="getting-started-cache-role-instance"></a>
## 開始使用角色中快取

角色中快取提供一種方法，可透過主控角色執行個體的虛擬機器上的記憶體來啟用快取。 您的快取都稱為的主控角色執行個體 **快取叢集**。 角色執行個體快取部署技術有兩種：

-   **專用角色** 快取-的角色執行個體專門用於快取。
-   **共置角色** 快取-快取與應用程式共用虛擬機器資源 （頻寬、 CPU 和記憶體）。

若要對角色執行個體使用快取，您需要設定快取叢集，然後設定快取用戶端，以便它們可以存取快取叢集。

-   [設定快取叢集][]
-   [設定快取用戶端][]

<a name="enable-caching"></a>
## 設定快取叢集

若要設定 **共置角色** 快取叢集，請選取您要用來主控快取叢集的角色。 以滑鼠右鍵按一下角色屬性中的 **方案總管] 中** 選擇 **屬性**。

![RoleCache1][RoleCache1]

切換至 **快取** ] 索引標籤，核取 **啟用快取** 核取方塊，並指定所需的快取選項。 中的快取啟用時 **背景工作角色** 或 **ASP.NET Web 角色**, ，預設設定是 **共置角色** 快取，並將 30%的記憶體配置給快取的角色執行個體。 系統會自動設定預設快取，但如果想要的話，您也可以建立其他具名快取，這些快取將會共用配置的記憶體。

![RoleCache2][RoleCache2]

若要設定 **專用角色** 快取叢集，新增 **快取背景工作角色** 至您的專案。

![RoleCache7][RoleCache7]

當 **快取背景工作角色** 」 新增至專案時，預設組態為 **專用角色** 快取。

![RoleCache8][RoleCache8]

一旦啟用快取，就可以設定快取叢集儲存體帳戶。 角色中快取需要 Azure 儲存體帳戶。 此儲存體帳戶會用來保留關於快取叢集的相關組態資料，而此快取叢集可從構成快取叢集的所有虛擬機器進行存取。 此儲存體帳戶上指定 **快取** 正上方的快取叢集角色屬性] 索引標籤頁面 **Named Cache Settings**。

![RoleCache10][RoleCache10]

>如果未設定此儲存體帳戶，角色將無法啟動。 

快取大小由以下幾項共同決定：角色的虛擬機器大小、角色的執行個體計數，以及快取叢集是設定為專用角色還是共置角色快取叢集。

>本節提供如何設定快取大小的簡化概觀。 如需有關快取大小和其他容量規劃考量的詳細資訊，請參閱 [角色中快取容量規劃考量][]。

若要設定虛擬機器大小和角色執行個體數目，以滑鼠右鍵按一下角色屬性中的 **方案總管] 中** 選擇 **屬性**。

![RoleCache1][RoleCache1]

切換至 **組態** ] 索引標籤。 預設 **執行個體計數** 為 1，而預設 **VM 大小** 是 **小**。

![RoleCache3][RoleCache3]

VM 大小的記憶體總計如下： 

-   **小型**: 1.75 GB
-   **中型**: 3.5 GB
-   **大型**: 7 GB
-   **超大型**: 14 GB


> 這些記憶體大小代表 VM 的可用記憶體總數，並由 OS、快取程序、快取資料和應用程式共用。 如需有關如何設定虛擬機器大小的詳細資訊，請參閱 [如何設定虛擬機器大小][]。 請注意，快取不支援 **特大型** VM 大小。

當 **共置角色** 快取已指定，來指定的虛擬機器記憶體的百分比決定快取大小。 當 **專用角色** 快取指定，則所有的虛擬機器的可用記憶體都用於快取。 如果設定兩個角色執行個體，則會使用相加在一起的虛擬機器記憶體。 這會構成快取叢集，可用快取記憶體會分配給多個角色執行個體，但以單一資源的樣貌呈現給快取的用戶端們。 設定其他角色執行個體會以相同方式增加快取大小。 若要決定佈建所需大小的快取所需的設定，您可以使用容量規劃試算表中有 [角色中快取容量規劃考量][]。

一旦設定了快取叢集，您可以設定快取用戶端，以允許存取快取。

<a name="NuGet"></a>
## 設定快取用戶端

若要存取「角色中快取」的快取，用戶端必須位於相同部署內。 如果快取叢集為專用角色快取叢集，則用戶端為部署中的其他角色。 如果快取叢集為共置角色快取叢集，則用戶端可以在部署中，其他角色或自行主控快取叢集的角色。 系統會提供一個 NuGet 套件，可用來設定每一個存取快取的用戶端角色。 若要設定角色來存取快取叢集，使用快取 NuGet 封裝，以滑鼠右鍵按一下中的角色專案 **方案總管] 中** 選擇 **管理 NuGet 封裝**。 

![RoleCache4][RoleCache4]

選取 **角色中快取**, ，按一下 [ **安裝**, ，然後按一下 [ **我接受**。

>如果 **角色中快取** 不會出現在清單中 **WindowsAzure.Caching** 到 **線上搜尋** 文字方塊，然後從結果選取它。

![RoleCache5][RoleCache5]

NuGet 會執行數項工作：將必要的組態新增至角色的組態檔、將快取用戶端診斷層級設定新增至 Azure 應用程式的 ServiceConfiguration.cscfg 檔案，以及新增必要的組件參考。

>針對 ASP.NET Web 角色，Caching NuGet 封裝也會將兩個已標成註解的區段新增至 web.config 中。 第一個區段可讓工作階段儲存在快取中，第二個區段則可讓 ASP.NET 頁面輸出快取處理。 如需詳細資訊，請參閱 [How To: Store ASP.NET Session State in the Cache] 和 [How To ︰ 將 ASP.NET 頁面輸出快取中快取][]。

NuGet 封裝會將下列組態元素新增至角色的 web.config 或 app.config 中。 A **dataCacheClients** 區段和 **cacheDiagnostics** 區段會新增在 **configSections** 項目。 如果沒有任何 **configSections** 元素，則會建立一個子系為 **組態** 項目。

    <configSections>
      <!-- Existing sections omitted for clarity. -->

      <section name="dataCacheClients" 
               type="Microsoft.ApplicationServer.Caching.DataCacheClientsSection, Microsoft.ApplicationServer.Caching.Core" 
               allowLocation="true" 
               allowDefinition="Everywhere" />
    
      <section name="cacheDiagnostics" 
               type="Microsoft.ApplicationServer.Caching.AzureCommon.DiagnosticsConfigurationSection, Microsoft.ApplicationServer.Caching.AzureCommon" 
               allowLocation="true" 
               allowDefinition="Everywhere" />
    </configSections>

這些新的區段包含參考 **dataCacheClients** 項目和 **cacheDiagnostics** 項目。 這些項目也會新增至 **組態** 項目。

    <dataCacheClients>
      <dataCacheClient name="default">
        <autoDiscover isEnabled="true" identifier="[cache cluster role name]" />
        <!--<localCache isEnabled="true" sync="TimeoutBased" objectCount="100000" ttlValue="300" />-->
      </dataCacheClient>
    </dataCacheClients>
    <cacheDiagnostics>
      <crashDump dumpLevel="Off" dumpStorageQuotaInMB="100" />
    </cacheDiagnostics>

新增組態之後，請將 **[快取叢集角色名稱]** 主控快取叢集之角色的名稱。

>如果 **[快取叢集角色名稱]** 不取代主控快取叢集之角色的名稱則 **TargetInvocationException** 隨附內部存取快取時將會擲回 **DatacacheException** "No such role exists"訊息。

NuGet 套件也會新增 **ClientDiagnosticLevel** 設為 [ **ConfigurationSettings** ServiceConfiguration.cscfg 中快取用戶端角色。 下列範例是 **WebRole1** ServiceConfiguration.cscfg 檔案中的一節 **ClientDiagnosticLevel** 為 1，這是預設值 **ClientDiagnosticLevel**。

    <Role name="WebRole1">
      <Instances count="1" />
      <ConfigurationSettings>
        <!-- Existing settings omitted for clarity. -->
        <Setting name="Microsoft.WindowsAzure.Plugins.Caching.ClientDiagnosticLevel" 
                 value="1" />
      </ConfigurationSettings>
    </Role>

>角色中快取同時提供快取伺服器和快取用戶端診斷層級。 診斷層級為單一設定，用來設定針對快取收集的診斷資訊層級。 如需詳細資訊，請參閱 [疑難排解和診斷角色中快取][]

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

-   [做法：建立 DataCache 物件][]
-   [做法：從快取新增和擷取物件][]
-   [做法：指定快取中物件的到期時間][]
-   [做法：將 ASP.NET 工作階段狀態儲存在快取中][]
-   [做法：將 ASP.NET 頁面輸出快取儲存在快取中][]

<a name="create-cache-object"></a>
## 做法：建立 DataCache 物件

為了能夠以程式設計方式使用快取，您需要快取的參考。 將下列程式碼新增至您想要從中使用
In-Role Cache：

    using Microsoft.ApplicationServer.Caching;

>如果在安裝了會加入必要參考的 Caching NuGet 封裝之後，Visual Studio 還是無法辨識 using
陳述式中的類型，請確定專案的目標
設定檔的專案為.NET Framework 4.0 或更高版本，並務必選取其中一個設定檔未指定 **用戶端設定檔**。 如需設定快取用戶端的指示，請參閱 [設定快取用戶端][]。

有兩種方式來建立 **DataCache** 物件。 第一種方式為僅建立 **DataCache**, ，並傳入所需的快取名稱。

    DataCache cache = new DataCache("default");

一次 **DataCache** 是具現化，您可以使用它，互動與快取中，下列各節中所述。

若要使用第二種方式，建立新 **DataCacheFactory** 應用程式中使用預設建構函式的物件。 這會導致快取用戶端使用組態檔中的設定。 呼叫 **Datacachefactory** 的新方法 **DataCacheFactory** 執行個體傳回 **DataCache** 物件，或 **GetCache** 方法，並傳入快取的名稱。 這些方法會傳回 **DataCache** 物件，然後可以用來以程式設計方式存取快取。

    // Cache client configured by settings in application configuration file.
    DataCacheFactory cacheFactory = new DataCacheFactory();
    DataCache cache = cacheFactory.GetDefaultCache();
    // Or DataCache cache = cacheFactory.GetCache("MyCache");
    // cache can now be used to add and retrieve items. 

<a name="add-object"></a>
## 做法：從快取新增和擷取物件

若要將項目新增至快取， **新增** 方法或 **放** 方法
可以使用。  **新增** 方法快取中，將指定的物件
做為索引鍵的索引鍵參數值。

    // Add the string "value" to the cache, keyed by "item"
    cache.Add("item", "value");

如果具有相同索引鍵的物件已經在快取中，
**DataCacheException** 就會擲回並出現下列訊息 ︰

> Substatus: 正在嘗試建立的物件
> 索引鍵已存在的物件。 快取將只會接受
> 物件的唯一索引鍵值。

若要以特定的索引鍵擷取物件 **取得** 方法可用。 如果物件存在，則
傳回，如果不存在，會傳回 null。

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

 **放** 方法會將物件與指定之索引鍵的快取
如果它不存在，或取代物件，如果檔案存在。

    // Add the string "value" to the cache, keyed by "item". If it exists,
    // replace it.
    cache.Put("item", "value");

<a name="specify-expiration"></a>
## 做法：指定快取中物件的到期時間

依預設，快取中的項目會在放入快取十分鐘後到期。 這可設定在 **存留時間 （分鐘）** 設定位於主控快取叢集之角色的角色屬性中。

![RoleCache6][RoleCache6]

有三種類型的 **到期類型**: **無**, ，**絕對**, ，和 **滑動視窗**。 這些設定如何 **存留時間 （分鐘）** 用來決定到期時間。 預設 **到期類型** 是 **絕對**, ，表示項目的到期倒數計時器即會開始項目放入快取時。 一旦過了項目的指定時間量，項目即到期。 如果 **滑動視窗** 已指定，則項目的到期倒數會重設每次存取快取中而且指定的時間量經過自前次存取之前，不會過期的項目。 如果 **無** 指定了， **存留時間 （分鐘）** 必須設為 **0**, ，項目將不會到期，而且只要它們是在快取會維持有效。

新增或更新快取中使用的項目時，如果需要較長或較短的逾時間隔比角色屬性中設定，則可以指定特定持續時間
多載 **新增** 和 **放** 採用 **TimeSpan** 參數。 在
下列範例中，字串 **值** 新增至快取，做為索引鍵
**項目**, ，逾時值為 30 分鐘的時間。

    // Add the string "value" to the cache, keyed by "item"
    cache.Add("item", "value", TimeSpan.FromMinutes(30));

若要檢視快取中項目的剩餘逾時間隔
**GetCacheItem** 方法可以用來擷取 **DataCacheItem**
物件包含快取中項目的相關資訊，包括
剩餘的逾時間隔。

    // Get a DataCacheItem object that contains information about
    // "item" in the cache. If there is no object keyed by "item" null
    // is returned. 
    DataCacheItem item = cache.GetCacheItem("item");
    TimeSpan timeRemaining = item.Timeout;

<a name="store-session"></a>
## 做法：將 ASP.NET 工作階段狀態儲存在快取中

角色中快取的工作階段狀態提供者
ASP.NET 應用程式的程序外儲存體機制。 此提供者
可讓您在 Azure 快取中儲存工作階段狀態，而不是
記憶體中或 SQL Server 資料庫中。 若要使用快取工作階段
狀態提供者，先設定快取叢集，然後再設定 ASP.NET 應用程式快取中所述，使用快取 NuGet 封裝 [開始使用角色中快取][]。 在安裝 Caching NuGet 套件時，它會在 web.config 新增已標成註解的區段，其中包含讓 ASP.NET 應用程式對角色中快取使用工作階段狀態提供者所需的組態。

    <!--Uncomment this section to use In-Role Cache for session state caching
    <system.web>
      <sessionState mode="Custom" customProvider="AFCacheSessionStateProvider">
        <providers>
          <add name="AFCacheSessionStateProvider" 
            type="Microsoft.Web.DistributedCache.DistributedCacheSessionStateStoreProvider,
                  Microsoft.Web.DistributedCache" 
            cacheName="default" 
            dataCacheClientName="default"/>
        </providers>
      </sessionState>
    </system.web>-->

>如果您 web.config 未包含這個已標成註解的區段在安裝 Caching NuGet 套件之後，請確定最新的 NuGet 封裝管理員從已安裝的 [NuGet Package Manager 安裝][], ，然後解除安裝並重新安裝套件。

若要對角色中快取啟用工作階段狀態提供者，請將指定的區段取消註解。 預設快取是在提供的片段中指定。 若要使用不同的快取，指定所需的快取中 **cacheName** 屬性。

如需使用快取服務工作階段狀態的詳細資訊
提供者，請參閱 [角色中快取的工作階段狀態提供者][]。

<a name="store-page"></a>
## 做法：將 ASP.NET 頁面輸出快取儲存在快取中

角色中快取的輸出快取提供者為輸出快取資料的程序外儲存體機制。 這項資料是特別適用於完整 HTTP
回應 （頁面輸出快取）。 提供者會插入新的輸出
ASP.NET 4 中引進快取提供者擴充點。 收件人
使用輸出快取提供者，先設定快取叢集，然後再設定 ASP.NET 應用程式快取使用快取 NuGet 封裝中所述 [開始使用角色中快取][]。 在安裝 Caching NuGet 套件時，它會在 web.config 新增下列已標成註解的區段，其中包含讓 ASP.NET 應用程式對角色中快取使用輸出快取提供者所需的組態。

    <!--Uncomment this section to use In-Role Cache for output caching
    <caching>
      <outputCache defaultProvider="AFCacheOutputCacheProvider">
        <providers>
          <add name="AFCacheOutputCacheProvider" 
            type="Microsoft.Web.DistributedCache.DistributedCacheOutputCacheProvider,
                  Microsoft.Web.DistributedCache" 
            cacheName="default" 
            dataCacheClientName="default" />
        </providers>
      </outputCache>
    </caching>-->

>如果您 web.config 未包含這個已標成註解的區段在安裝 Caching NuGet 套件之後，請確定最新的 NuGet 封裝管理員從已安裝的 [NuGet Package Manager 安裝][], ，然後解除安裝並重新安裝套件。

若要對角色中快取啟用輸出快取提供者，請將指定的區段取消註解。 預設快取是在提供的片段中指定。 若要使用不同的快取，指定所需的快取中 **cacheName** 屬性。

新增 **OutputCache** 指示詞加入每個您要快取輸出的頁面。

    <%@ OutputCache Duration="60" VaryByParam="*" %>

在此範例中，已快取的頁面資料會留在快取中 60 秒，而且會對每一個參數組合快取不同版本的頁面。 如需可用選項的詳細資訊，請參閱 [Outputcache][]。

如需角色中快取使用輸出快取提供者的詳細資訊，請參閱 [角色中快取的輸出快取提供者][]。

<a name="next-steps"></a>
## 後續步驟

現在，您已了解 In-Role Cache 的基本概念，
請循著下列連結以了解如何執行更複雜的快取工作。

-   請參閱 MSDN 參考 ︰ [角色中快取][]
-   了解如何移轉至角色中快取 ︰ [移轉至角色中快取][]
-   查看範例 ︰ [角色中快取範例][]
-   監看式 [最大效能 ︰ 加速雲端服務應用程式與 Azure 快取][] TechEd 2013 在角色中快取的工作階段

<!-- INTRA-TOPIC LINKS -->
[Next Steps]: #next-steps
[What is In-Role Cache?]: #what-is
[Create an Azure Cache]: #create-cache
[Which type of caching is right for me?]: #choosing-cache
[Getting Started with the In-Role Cache Service]: #getting-started-cache-service
[Prepare Your Visual Studio Project to Use In-Role Cache]: #prepare-vs
[Configure Your Application to Use Caching]: #configure-app
[Getting Started with In-Role Cache]: #getting-started-cache-role-instance
[Configure the cache cluster]: #enable-caching
[Configure the desired cache size]: #cache-size
[Configure the cache clients]: #NuGet
[Working with Caches]: #working-with-caches
[How To: Create a DataCache Object]: #create-cache-object
[How To: Add and Retrieve an Object from the Cache]: #add-object
[How To: Specify the Expiration of an Object in the Cache]: #specify-expiration
[How To: Store ASP.NET Session State in the Cache]: #store-session
[How To: Store ASP.NET Page Output Caching in the Cache]: #store-page
[Target a Supported .NET Framework Profile]: #prepare-vs-target-net
 
<!-- IMAGES --> 
[RoleCache1]: ./media/cache-dotnet-how-to-use-in-role/cache8.png
[RoleCache2]: ./media/cache-dotnet-how-to-use-in-role/cache9.png
[RoleCache3]: ./media/cache-dotnet-how-to-use-in-role/cache10.png
[RoleCache4]: ./media/cache-dotnet-how-to-use-in-role/cache11.png
[RoleCache5]: ./media/cache-dotnet-how-to-use-in-role/cache12.png
[RoleCache6]: ./media/cache-dotnet-how-to-use-in-role/cache13.png
[RoleCache7]: ./media/cache-dotnet-how-to-use-in-role/cache14.png
[RoleCache8]: ./media/cache-dotnet-how-to-use-in-role/cache15.png
[RoleCache10]: ./media/cache-dotnet-how-to-use-in-role/cache17.png
  
<!-- LINKS -->
[How to Configure Virtual Machine Sizes]: http://go.microsoft.com/fwlink/?LinkId=164387
[How to: Configure a Cache Client Programmatically]: http://msdn.microsoft.com/library/windowsazure/gg618003.aspx
[How to: Set a Page's Cacheability Programmatically]: http://msdn.microsoft.com/library/z852zf6b.aspx
[How to: Set the Cacheability of an ASP.NET Page Declaratively]: http://msdn.microsoft.com/library/zd1ysf1y.aspx
[In-Role Cache Capacity Planning Considerations]: http://go.microsoft.com/fwlink/?LinkId=252651
[In-Role Cache Samples]: http://msdn.microsoft.com/library/jj189876.aspx
[In-Role Cache]: http://go.microsoft.com/fwlink/?LinkId=252658
[In-Role Cache]: http://www.microsoft.com/showcase/Search.aspx?phrase=azure+caching
[Maximum Performance: Accelerate Your Cloud Services Applications with Azure Caching]: http://channel9.msdn.com/Events/TechEd/NorthAmerica/2013/WAD-B326#fbid=kmrzkRxQ6gU
[Migrate to In-Role Cache]: http://msdn.microsoft.com/library/hh914163.aspx
[NuGet Package Manager Installation]: http://go.microsoft.com/fwlink/?LinkId=240311
[Output Cache Provider for In-Role Cache]: http://msdn.microsoft.com/library/windowsazure/gg185662.aspx
[OutputCache Directive]: http://go.microsoft.com/fwlink/?LinkId=251979
[Overview of In-Role Cache]: http://go.microsoft.com/fwlink/?LinkId=254172
[Session State Provider for In-Role Cache]: http://msdn.microsoft.com/library/windowsazure/gg185668.aspx
[Team Blog]: http://blogs.msdn.com/b/windowsazure/
[Troubleshooting and Diagnostics for In-Role Cache]: http://msdn.microsoft.com/library/windowsazure/hh914135.aspx
[Azure AppFabric Cache: Caching Session State]: http://www.microsoft.com/showcase/details.aspx?uuid=87c833e9-97a9-42b2-8bb1-7601f9b5ca20
[Azure Shared Caching]: http://msdn.microsoft.com/library/windowsazure/gg278356.aspx

[Which Azure Cache offering is right for me?]: cache-faq.md#which-azure-cache-offering-is-right-for-me
 


