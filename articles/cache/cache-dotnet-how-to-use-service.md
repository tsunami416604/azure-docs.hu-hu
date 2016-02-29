<properties 
    pageTitle="如何使用 Azure 受管理快取服務" 
    description="了解如何改善與 Azure 受管理快取服務的 Azure 應用程式的效能" 
    services="cache" 
    documentationCenter="" 
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

# 如何使用 Azure 受管理快取服務

本指南說明如何開始使用 
**Azure 受管理的快取服務**。 這些範例均以 C\# 程式碼撰寫並
使用 .NET API。 涵蓋的案例包括 **建立和設定快取**, ，**設定快取用戶端**, ，**新增和移除
從快取中，將 ASP.NET 工作階段狀態儲存在快取物件**,，
和 **啟用 ASP.NET 頁面輸出快取使用快取**。 如需
有關使用 Azure 快取，請參閱 [後續步驟] [] 一節。

>[AZURE.IMPORTANT]我們正在宣佈 Azure 受管理快取服務與 Azure 角色中快取 2016 年 11 月 30 日淘汰。 我們建議您移轉至 Azure Redis 快取以為這次淘汰做準備。 如需有關日期和移轉指導方針的詳細資訊，請參閱 [哪個 Azure 快取服務是最適合我?](../redis-cache/cache-faq.md#which-azure-cache-offering-is-right-for-me)

<a name="what-is"></a>
## 何謂 Azure 受管理快取服務？

Azure 受管理快取服務是一種分散式、記憶體內的可擴充解決方案，藉由提供超快速的資料存取，可讓您建置可高度擴充和回應的應用程式。

Azure 受管理快取服務包括下列
功能:

-   工作階段狀態及頁面輸出預先建置的 ASP.NET 提供者
    快取，讓 web 應用程式加速，卻不必
    修改應用程式程式碼。
-   快取任何可序列化的受管理的物件-例如: CLR 物件、 資料列、 XML，
    二進位資料。
-   Azure 和 Windows 的一致的開發模型
    Server AppFabric。

受管理的快取服務可讓您存取由 Microsoft 管理的專用安全快取。 您可從 Azure 網站、Web 和背景工作角色以及虛擬機器上執行的 Azure 應用程式，存取使用受管理的快取服務建立的快取。

受管理的快取服務有三個階層：

-   基本 - 大小從 128MB 到 1GB 的快取
-   標準 - 大小從 1GB 到 10GB 的快取
-   高級 - 大小從 5GB 到 150GB 的快取

每一個階層都有不同的功能和定價。 探討功能稍後在本指南中，如需定價的詳細資訊，請參閱 [快取定價詳細資料] []。

本指南提供開始使用受管理的快取服務的概觀。 如需詳細資訊，功能的討論範圍內的這個快速入門指南，請參閱 [Azure 受管理快取服務概觀] []。

<a name="getting-started-cache-service"></a>
## 開始使用快取服務

開始使用受管理的快取服務相當簡單。 若要開始，請佈建並設定快取。 接著，設定快取用戶端，以便它們可以存取快取。 一旦設定了快取用戶端，就可以開始使用它們。

-   [建立快取][]
-   [設定快取][]
-   [設定快取用戶端][]

<a name="create-cache"></a>
## 建立快取

您可以使用 PowerShell Cmdlet，在受管理的快取服務中建立快取執行個體。 

>一旦使用 PowerShell cmdlet 來檢視和設定 [Azure 傳統入口網站] [] 中建立受管理的快取服務執行個體。

若要建立受管理的快取服務執行個體，請開啟 Azure PowerShell 命令視窗。

>如需安裝和使用 Azure PowerShell 的指示，請參閱 < 如何安裝和設定 Azure PowerShell] []。

叫用 [Add-azureaccount] [] 指令程式，並輸入的電子郵件地址和您的帳戶相關聯的密碼。 訂用帳戶預設會選擇之後，並顯示您叫用 [Add-azureaccount] [] 指令程式。 若要變更訂閱，請叫用 [Select-azuresubscription] [] 指令程式。

>如果您已使用帳戶的憑證來設定 Azure PowerShell，那麼就可以跳過此步驟。 如需 Azure PowerShell 與 Azure 帳戶連接的詳細資訊，請參閱 [如何安裝和設定 Azure PowerShell] []。

預設會選擇並顯示一個訂用帳戶。 若要變更訂閱，請叫用 [Select-azuresubscription] [] 指令程式。

叫用 [New-azuremanagedcache] [] cmdlet，並指定名稱、 地區、 快取提供項目，以及快取的大小。

如 **名稱**, ，輸入要用於快取端點的子網域名稱。 端點必須是介於 6 到 20 個字元之間的字串、僅包含小寫數字和字母，而且必須以字母開頭。

如 **位置**, ，指定快取的區域。 為獲得最佳效能，請在與快取用戶端應用程式相同的區域中建立快取。

**Sku** 和 **記憶體** 一起運作，以判斷快取的大小。 受管理的快取服務共有下列三個階層：

-   基本 - 大小從 128MB 到 1GB 的快取 (每次增量 128MB)，具有一個預設具名快取
-   標準 - 大小從 1GB 到 10GB 的快取 (每次增量 1GB)，支援通知和最多 10 個具名快取
-   高級 - 大小從 5GB 到 150GB 的快取 (每次增量 5GB)，支援通知、高可用性和最多 10 個具名快取

選擇 **Sku** 和 **記憶體** 符合您的應用程式的需求。 請注意，有些快取功能 (例如通知和高可用性) 只有特定快取提供項目才會提供。 如需選擇應用程式最適用的快取提供項目和大小的詳細資訊，請參閱 [快取提供項目][]。

 在下列範例中，基本 128MB 快取是透過位於美國中南部地理區域、名稱為 contosocache 所建立。

    New-AzureManagedCache -Name contosocache -Location "South Central US" -Sku Basic -Memory 128MB

>參數和值可以在建立快取時使用的完整清單，請參閱 [New-azuremanagedcache] [] cmdlet 文件。

叫用 PowerShell Cmdlet 之後，可能需要數分鐘的時間建立快取。 建立快取之後，新快取有 `Running` 狀態並可提供使用預設設定，並可以檢視和 [Azure 傳統入口網站] [] 中設定。 若要自訂您的快取的設定，請參閱下列的 [設定快取] [] 一節。

您可以在 Azure PowerShell 視窗中監視建立進度。 一旦快取可供使用，則 [New-azuremanagedcache] [] 指令程式會顯示快取資訊，如下列範例所示。

    PS C:\> Add-AzureAccount
    VERBOSE: Account "user@domain.com" has been added.
    VERBOSE: Subscription "MySubscription" is selected as the default subscription.
    VERBOSE: To view all the subscriptions, please use Get-AzureSubscription.
    VERBOSE: To switch to a different subscription, please use Select-AzureSubscription.
    PS C:\> New-AzureManagedCache -Name contosocache -Location "South Central US" -Sku Basic -Memory 128MB
    VERBOSE: Intializing parameters...
    VERBOSE: Creating prerequisites...
    VERBOSE: Verify cache service name...
    VERBOSE: Creating cache service...
    VERBOSE: Waiting for cache service to be in ready state...


    Name     : contosocache
    Location : South Central US
    State    : Active
    Sku      : Basic
    Memory   : 128MB



    PS C:\>




<a name="enable-caching"></a>
## 設定快取

 **設定** 索引標籤上，供 Azure 傳統入口網站中的快取的快取設定的選項。 每個快取都有 **預設** 具名快取，而且標準和進階版快取提供項目支援最多 9 個額外的具名快取，總共 10。 每個具名快取都有自己的選項集，可讓您以高度彈性方式設定快取。

![NamedCaches][NamedCaches]

若要建立具名快取，輸入新的快取的名稱 **名稱** ，指定所需的選項，按一下 **儲存**, ，然後按一下 **是** 確認。 若要取消任何變更，請按一下 [ **捨棄**。

## 到期原則和時間 (分鐘) ##

 **到期原則** 搭配 **時間 (分鐘)** 設定，以判斷快取項目何時到期。 有三種類型的到期原則: **絕對**, ，**滑動**, ，和 **永不**。 

當 **絕對** 指定時，所指定的到期間隔 **時間 (分鐘)** 開始項目新增至快取。 所指定的間隔之後 **時間 (分鐘)** 結束項目即到期。 

當 **滑動** 指定時，所指定的到期間隔 **時間 (分鐘)** 即會重設每次存取快取中項目。 項目不會過期之前所指定的間隔 **時間 (分鐘)** 從前次存取項目後經過了。

當 **永不** 指定，則 **時間 (分鐘)** 必須設為 **0**, ，並不會過期的項目。

**絕對** 為預設到期原則，10 分鐘的預設設定 **時間 (分鐘)**。 到期原則都固定的具名快取中，每個項目，但 **時間 (分鐘)** 可以自訂每個項目，使用 **新增** 和 **放** 超載來採用逾時參數。

如需收回和到期原則的詳細資訊，請參閱 [到期和收回]。

## 通知 ##

快取通知可讓應用程式在快取叢集發生各種快取作業時，收到非同步通知。 快取通知也提供使本機快取的物件自動失效的功能。 如需詳細資訊，請參閱 [通知] []。

>標準和高級快取提供項目才會提供通知功能，基本快取提供項目並不會提供。 如需詳細資訊，請參閱 [快取提供項目] []。

## 高可用性 ##

啟用高可用性時，會由每一個新增至快取的項目組成備份複本。 如果項目的主要複本發生非預期的失敗，則仍有備份複本可用。

依定義，使用高可用性時，每個快取項目所需的記憶體數量會翻倍。 請在進行容量計劃時將此記憶體影響納入考量。 如需詳細資訊，請參閱 [高可用性] []。

>高級快取提供項目才會提供高可用性功能，基本或標準快取提供項目並不會提供。 如需詳細資訊，請參閱 [快取提供項目] []。

## 收回 ##

為了保持快取內可用的記憶體容量，因此會支援最近最少使用的 (LRU) 收回。 當記憶體耗用量超出記憶體臨界值時，無論物件是否過期，都會從記憶體收回物件，直到記憶體壓力舒緩為止。
依預設會啟用收回功能。 如果停用收回功能，則在到達容量上限時，將不會從快取中收回項目，Put 和 Add 作業將會因此失敗。

如需收回和到期原則的詳細資訊，請參閱 [到期和收回]。

一旦設定了快取，您可以設定快取用戶端，以允許存取快取。

<a name="NuGet"></a>
## 設定快取用戶端

您可從 Azure 網站、Web 和背景工作角色以及虛擬機器上執行的 Azure 應用程式，存取使用受管理的快取服務建立的快取。 我們有提供 NuGet 套件，此套件可簡化快取用戶端應用程式的組態作業。 

若要設定用戶端應用程式使用快取 NuGet 封裝，以滑鼠右鍵按一下專案中的 **方案總管] 中** 選擇 **管理 NuGet 封裝**。 

![NuGetPackageMenu][NuGetPackageMenu]

型別 **WindowsAzure.Caching** 到 **線上搜尋** 文字方塊，然後選取 **Windows**  
**Azure** **快取** 的結果。 按一下 [ **安裝**, ，然後按一下 [ **我接受**。

![NuGetPackage][NuGetPackage]

NuGet 會執行數項工作：它會將必要的組態新增至應用程式的組態檔，以及新增必要的組件參考。 若為雲端服務專案，也會將快取用戶端診斷層級設定新增至雲端服務的 ServiceConfiguration.cscfg 檔案。

>針對 ASP.NET Web 專案，Cache NuGet 封裝也會將兩個已標成註解的區段新增至 web.config 中。 第一個區段可讓工作階段儲存在快取中，第二個區段則可讓 ASP.NET 頁面輸出快取處理。 如需詳細資訊，請參閱 [How To: 將 ASP.NET 工作階段狀態儲存在快取] 和 [How To: 將 ASP.NET 頁面輸出快取中快取] []。

NuGet 封裝會將下列組態元素新增至應用程式的 web.config 或 app.config 中。 A **dataCacheClients** 區段和 **cacheDiagnostics** 區段會新增在 **configSections** 項目。 如果沒有任何 **configSections** 元素，則會建立一個子系為 **組態** 項目。

    <configSections>
      <!-- Existing sections omitted for clarity. -->
      <section name="dataCacheClients" 
        type="Microsoft.ApplicationServer.Caching.DataCacheClientsSection,
              Microsoft.ApplicationServer.Caching.Core" 
        allowLocation="true" 
        allowDefinition="Everywhere" />
  
    <section name="cacheDiagnostics" 
        type="Microsoft.ApplicationServer.Caching.AzureCommon.DiagnosticsConfigurationSection,
              Microsoft.ApplicationServer.Caching.AzureCommon" 
        allowLocation="true" 
        allowDefinition="Everywhere" />


這些新的區段包含參考 **dataCacheClients** 項目，也會加入至 **組態** 項目。

    <dataCacheClients>
      <dataCacheClient name="default">
        <!--To use the in-role flavor of Azure Caching, 
            set identifier to be the cache cluster role name -->
        <!--To use the Azure Caching Service,
            set identifier to be the endpoint of the cache cluster -->
        <autoDiscover isEnabled="true" identifier="[Cache role name or Service Endpoint]" />
        <!--<localCache isEnabled="true" sync="TimeoutBased" objectCount="100000" ttlValue="300" />-->
        <!--Use this section to specify security settings for connecting to your cache. 
            This section is not required if your cache is hosted on a role that is a part
            of your cloud service. -->
        <!--<securityProperties mode="Message" sslEnabled="false">
          <messageSecurity authorizationInfo="[Authentication Key]" />
        </securityProperties>-->
      </dataCacheClient>
    </dataCacheClients>


在新增組態後，請在剛新增的組態中取代下列兩個項目。

1. 取代 **[快取角色名稱或服務端點]** 有端點，其中會顯示在 Azure 傳統入口網站的儀表板。

    ![端點][Endpoint]

2. 取消註解 securityProperties 區段，並取代 **[Authentication Key]** 以驗證金鑰，這可在 Azure 傳統入口網站按一下 **管理金鑰** 快取儀表板中。

    ![AccessKeys][AccessKeys]

>必須適當地配置這些設定，否則用戶端將無法存取快取。

雲端服務專案，NuGet 套件也會新增 **ClientDiagnosticLevel** 設為 [ **ConfigurationSettings** ServiceConfiguration.cscfg 中快取用戶端角色。 下列範例是 **WebRole1** ServiceConfiguration.cscfg 檔案中的一節 **ClientDiagnosticLevel** 為 1，這是預設值 **ClientDiagnosticLevel**。

    <Role name="WebRole1">
      <Instances count="1" />
      <ConfigurationSettings>
        <!-- Existing settings omitted for clarity. -->
        <Setting name="Microsoft.WindowsAzure.Plugins.Caching.ClientDiagnosticLevel" 
                 value="1" />
      </ConfigurationSettings>
    </Role>

>用戶端診斷層級會設定為每一個快取用戶端收集的快取診斷資訊的層級。 如需詳細資訊，請參閱 [疑難排解和診斷]

NuGet 套件也會新增下列組件的參考：

-   Microsoft.ApplicationServer.Caching.Client.dll
-   Microsoft.ApplicationServer.Caching.Core.dll
-   Microsoft.WindowsFabric.Common.dll
-   Microsoft.WindowsFabric.Data.Common.dll
-   Microsoft.ApplicationServer.Caching.AzureCommon.dll
-   Microsoft.ApplicationServer.Caching.AzureClientHelper.dll

如果您的專案為 Web 專案，則也會新增下列組件參考：

-   Microsoft.Web.DistributedCache.dll

一旦設定用戶端專案的快取功能，您就可以使用下列幾節中描述的技術來使用快取。

<a name="working-with-caches"></a>
## 使用快取

本節中的步驟描述如何利用快取執行常見工作。

-   [如何: 建立 DataCache 物件][]
-   [如何: 新增和擷取物件從快取][]
-   [如何: 指定快取中物件的到期時間][]
-   [如何: 將 ASP.NET 工作階段狀態儲存在快取][]
-   [如何: 將 ASP.NET 頁面輸出快取中快取儲存][]

<a name="create-cache-object"></a>
## 做法：建立 DataCache 物件

為了能夠以程式設計方式使用快取，您需要快取的參考。 將下列程式碼新增至您想要從中使用
Azure 快取：

    using Microsoft.ApplicationServer.Caching;

>如果在安裝了會加入必要參考的 Caching NuGet 封裝之後，Visual Studio 還是無法辨識 using
確認目標即使在安裝 Caching NuGet 套件，以將必要的參考之後, 的陳述式
設定檔的專案為.NET Framework 4 或更高版本，並務必選取其中一個設定檔未指定 **用戶端設定檔**。 如需設定快取用戶端的指示，請參閱 [設定快取用戶端] []。

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
**DataCacheException** 就會擲回並出現下列訊息:

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

依預設，快取中的項目會在放入快取十分鐘後到期。 這可設定在 **時間 (分鐘)** Azure 傳統入口網站中快取設定] 索引標籤上設定。

![NamedCaches][NamedCaches]

有三種類型的 **到期原則**: **永不**, ，**絕對**, ，和 **滑動**。 這些設定如何 **時間 (分鐘)** 用來決定到期時間。 預設 **到期類型** 是 **絕對**, ，表示項目的到期倒數計時器即會開始項目放入快取時。 一旦過了項目的指定時間量，項目即到期。 如果 **滑動** 已指定，則項目的到期倒數會重設每次存取快取中而且指定的時間量經過自前次存取之前，不會過期的項目。 如果 **永不** 指定了， **時間 (分鐘)** 必須設為 **0**, ，項目將不會到期，而且只要它們是在快取會維持有效。

新增或更新快取中使用的項目時，如果需要較長或較短的逾時間隔比快取屬性中設定，則可以指定特定持續時間
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

Azure 快取的工作階段狀態提供者
ASP.NET 應用程式的程序外儲存體機制。 此提供者
可讓您在 Azure 快取中儲存工作階段狀態，而不是
記憶體中或 SQL Server 資料庫中。 若要使用快取工作階段
狀態提供者，先設定您的快取，然後再設定 ASP.NET 應用程式使用快取 NuGet 封裝 [開始使用受管理快取服務] [] 中所述的快取。 在安裝 Caching NuGet 套件時，它會在 web.config 新增已標成註解的區段，其中包含讓 ASP.NET 應用程式對 Azure 快取使用工作階段狀態提供者所需的組態。

    <!--Uncomment this section to use Azure Caching for session state caching
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

>如果您 web.config 未包含這個已標成註解的區段在安裝 Caching NuGet 套件之後，請確定已安裝最新的 NuGet 封裝管理員從 [NuGet Package Manager 安裝] []，然後解除安裝並重新安裝套件。

若要對 Azure 快取啟用工作階段狀態提供者，請將指定的區段取消註解。 預設快取是在提供的片段中指定。 若要使用不同的快取，指定所需的快取中 **cacheName** 屬性。

如需使用受管理快取服務工作階段狀態的詳細資訊
提供者，請參閱 [Azure 快取工作階段狀態提供者] []。

<a name="store-page"></a>
## 做法：將 ASP.NET 頁面輸出快取儲存在快取中

Azure 快取的輸出快取提供者為輸出快取資料的程序外儲存體機制。 這項資料是特別適用於完整 HTTP
回應 (頁面輸出快取)。 提供者會插入新的輸出
ASP.NET 4 中引進快取提供者擴充點。 收件人
使用輸出快取提供者，先設定快取叢集，然後再設定 ASP.NET 應用程式快取使用快取 NuGet 封裝，[開始使用受管理快取服務] [] 中所述。 在安裝 Caching NuGet 套件時，它會在 web.config 新增下列已標成註解的區段，其中包含讓 ASP.NET 應用程式對 Azure 快取使用輸出快取提供者所需的組態。

    <!--Uncomment this section to use Azure Caching for output caching
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

>如果您 web.config 未包含這個已標成註解的區段在安裝 Caching NuGet 套件之後，請確定已安裝最新的 NuGet 封裝管理員從 [NuGet Package Manager 安裝] []，然後解除安裝並重新安裝套件。

若要對 Azure 快取啟用輸出快取提供者，請將指定的區段取消註解。 預設快取是在提供的片段中指定。 若要使用不同的快取，指定所需的快取中 **cacheName** 屬性。

新增 **OutputCache** 指示詞加入每個您要快取輸出的頁面。

    <%@ OutputCache Duration="60" VaryByParam="*" %>

在此範例中，已快取的頁面資料會留在快取中 60 秒，而且會對每一個參數組合快取不同版本的頁面。 如需有關可用選項的詳細資訊，請參閱 [OutputCache 指示詞] []。

如需 Azure 快取使用輸出快取提供者的詳細資訊，請參閱 [Azure 快取輸出快取提供者] []。

<a name="next-steps"></a>
## 後續步驟

既然您已了解基本的受管理快取服務，
請循著下列連結以了解如何執行更複雜的快取工作。

-   請參閱 MSDN 參考: [受管理快取服務]]
-   了解如何移轉至受管理快取服務: [移轉至受管理快取服務]]
-   查看範例: [受管理快取服務範例]]

<!-- INTRA-TOPIC LINKS -->
[Next Steps]: #next-steps
[What is Azure Managed Cache Service?]: #what-is
[Create an Azure Cache]: #create-cache
[Which type of caching is right for me?]: #choosing-cache
[Prepare Your Visual Studio Project to Use Azure Caching]: #prepare-vs
[Configure Your Application to Use Caching]: #configure-app
[Getting Started with Managed Cache Service]: #getting-started-cache-service
[Create the cache]: #create-cache
[Configure the cache]: #enable-caching
[Configure the cache clients]: #NuGet
[Working with Caches]: #working-with-caches
[How To: Create a DataCache Object]: #create-cache-object
[How To: Add and Retrieve an Object from the Cache]: #add-object
[How To: Specify the Expiration of an Object in the Cache]: #specify-expiration
[How To: Store ASP.NET Session State in the Cache]: #store-session
[How To: Store ASP.NET Page Output Caching in the Cache]: #store-page
[Target a Supported .NET Framework Profile]: #prepare-vs-target-net
  
<!-- IMAGES -->
[NewCacheMenu]: ./media/cache-dotnet-how-to-use-service/CacheServiceNewCacheMenu.png

[QuickCreate]: ./media/cache-dotnet-how-to-use-service/CacheServiceQuickCreate.png

[Endpoint]: ./media/cache-dotnet-how-to-use-service/CacheServiceEndpoint.png

[AccessKeys]: ./media/cache-dotnet-how-to-use-service/CacheServiceManageAccessKeys.png

[NuGetPackage]: ./media/cache-dotnet-how-to-use-service/CacheServiceManageNuGetPackage.png

[NuGetPackageMenu]: ./media/cache-dotnet-how-to-use-service/CacheServiceManageNuGetPackagesMenu.png

[NamedCaches]: ./media/cache-dotnet-how-to-use-service/CacheServiceNamedCaches.jpg
  
   
<!-- LINKS -->
[Azure Classic Portal]: https://manage.windowsazure.com/
[How to: Configure a Cache Client Programmatically]: http://msdn.microsoft.com/library/windowsazure/gg618003.aspx
[Session State Provider for Azure Cache]: http://go.microsoft.com/fwlink/?LinkId=320835
[Azure AppFabric Cache: Caching Session State]: http://www.microsoft.com/showcase/details.aspx?uuid=87c833e9-97a9-42b2-8bb1-7601f9b5ca20
[Output Cache Provider for Azure Cache]: http://go.microsoft.com/fwlink/?LinkId=320837
[Azure Shared Caching]: http://msdn.microsoft.com/library/windowsazure/gg278356.aspx
[Team Blog]: http://blogs.msdn.com/b/windowsazure/
[Azure Caching]: http://www.microsoft.com/showcase/Search.aspx?phrase=azure+caching
[How to Configure Virtual Machine Sizes]: http://go.microsoft.com/fwlink/?LinkId=164387
[Azure Caching Capacity Planning Considerations]: http://go.microsoft.com/fwlink/?LinkId=320167
[Azure Caching]: http://go.microsoft.com/fwlink/?LinkId=252658
[How to: Set the Cacheability of an ASP.NET Page Declaratively]: http://msdn.microsoft.com/library/zd1ysf1y.aspx
[How to: Set a Page's Cacheability Programmatically]: http://msdn.microsoft.com/library/z852zf6b.aspx
[Overview of Azure Managed Cache Service]: http://go.microsoft.com/fwlink/?LinkId=320830
[Managed Cache Service]: http://go.microsoft.com/fwlink/?LinkId=320830
[OutputCache Directive]: http://go.microsoft.com/fwlink/?LinkId=251979
[Troubleshooting and Diagnostics]: http://go.microsoft.com/fwlink/?LinkId=320839
[NuGet Package Manager Installation]: http://go.microsoft.com/fwlink/?LinkId=240311
[Cache Pricing Details]: http://www.windowsazure.com/pricing/details/cache/
[Cache offerings]: http://go.microsoft.com/fwlink/?LinkId=317277
[Capacity planning]: http://go.microsoft.com/fwlink/?LinkId=320167
[Expiration and Eviction]: http://go.microsoft.com/fwlink/?LinkId=317278
[High Availability]: http://go.microsoft.com/fwlink/?LinkId=317329
[Notifications]: http://go.microsoft.com/fwlink/?LinkId=317276
[Migrate to Managed Cache Service]: http://go.microsoft.com/fwlink/?LinkId=317347
[Managed Cache Service Samples]: http://go.microsoft.com/fwlink/?LinkId=320840
[New-AzureManagedCache]: http://go.microsoft.com/fwlink/?LinkId=400495
[Azure Managed Cache Cmdlets]: http://go.microsoft.com/fwlink/?LinkID=398555
[How to install and configure Azure PowerShell]: http://go.microsoft.com/fwlink/?LinkId=400494
[Add-AzureAccount]: http://msdn.microsoft.com/library/dn495128.aspx
[Select-AzureSubscription]: http://msdn.microsoft.com/library/dn495203.aspx

[Which Azure Cache offering is right for me?]: cache-faq.md#which-azure-cache-offering-is-right-for-me
 
