<properties 
    pageTitle="Azure Redis 快取常見問題集" 
    description="了解 Azure Redis 快取常見問題、模式和最佳作法的答案" 
    services="redis-cache" 
    documentationCenter="" 
    authors="steved0x" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="cache" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="cache-redis" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="12/03/2015" 
    ms.author="sdanie"/>

# Azure Redis 快取常見問題集

了解 Azure Redis 快取常見問題、模式和最佳作法的答案。

<a name="cache-size"></a>
## 應該使用哪個 Redis 快取供應項目和大小？
每個 Azure Redis 快取提供項目提供不同層級的 **大小**, ，**頻寬**, ，**高可用性** 和 **SLA** 選項。

下列是選擇快取供應項目的考量。

-   **記憶體**: 基本和標準層提供 250 MB-53 GB。 「 高階 」 層提供 530 GB 更多有關可用 [要求](mailto:wapteams@microsoft.com?subject=Redis%20Cache%20quota%20increase)。 如需詳細資訊，請參閱 [Azure Redis 快取定價](https://azure.microsoft.com/pricing/details/cache/)。
-   **網路效能**: 如果您的需要高輸送量工作負載 「 高階 」 層提供更多的頻寬，相較於標準或基本。 此外，因為每一層內有裝載快取的基礎 VM，較大型快取還有更大頻寬。 如需詳細資訊，請參閱下列表格。
-   **輸送量**: 「 高階 」 層提供可用的最大輸送量。 如果快取伺服器或用戶端達到頻寬限制，您會在用戶端上收到逾時。 如需詳細資訊，請參閱下列表格。
-   **高的可用性 SLA**: Azure Redis 快取會保證 Standard/Premium 快取將會使用至少 99.9%的時間。 若要深入了解我們的 SLA，請參閱 [Azure Redis 快取定價](https://azure.microsoft.com/support/legal/sla/cache/v1_0/)。 SLA 的範圍僅涵蓋與快取端點的連線。 SLA 未涵蓋資料遺失防護。 建議您使用高階層中的 Redis 資料永續性功能，以提高資料遺失時的復原能力。
-   **Redis 資料持續性**: 「 高階 」 層可讓您快取資料保存在 Azure 儲存體帳戶。 在基本/標準快取中，所有資料都只儲存在記憶體中。 如果基礎結構發生問題，資料可能會遺失。 建議您使用高階層中的 Redis 資料永續性功能，以增加資料遺失時的復原能力。 Azure Redis 快取在 Redis 永續性中提供 RDB 和 AOF (即將推出) 選項。 如需詳細資訊，請參閱 [如何設定高階 Azure Redis 快取的持續性](cache-how-to-premium-persistence.md)。
-   **Redis 叢集**: 如果您想要建立快取大於 53 GB，或想要分區資料跨多個 Redis 節點，您可以使用 Redis 叢集可在 「 高階 」 層。 每個節點均包含一個主要/複本快取組以提供高可用性。 如需詳細資訊，請參閱 [如何設定高階 Azure Redis 快取叢集](cache-how-to-premium-clustering.md)。
-   **增強式安全性及網路隔離**: Azure 虛擬網路 (VNET) 部署提供增強的安全性和隔離您的 Azure Redis 快取，以及子網路，存取控制原則和其他功能來進一步限制存取。 如需詳細資訊，請參閱 [如何設定虛擬網路支援高階 Azure Redis 快取](cache-how-to-premium-vnet.md)。
-   **設定 Redis**: 在標準 」 和 「 高階 」 層次中，您可以設定 Redis Keyspace 通知。
-   **用戶端連線的最大數目**: 「 高階 」 層提供的用戶端可以連線到 Redis，數字的大小較大的快取的連線數目上限。 [定價頁面，如需詳細資訊，請參閱](https://azure.microsoft.com/pricing/details/cache/)。
-   **Redis 伺服器的專用核心**: 「 高階 」 層中所有的快取大小的專用的核心 Redis。 在基本/標準層 C1 和大小上方有 [Redis 伺服器的專用的核心。
-   **Redis 是單一執行緒** 讓兩個以上核心所提供的另一個優點只有兩個核心相同，但較大的 VM 大小通常會有更多的頻寬，相較於較小的大小。 如果快取伺服器或用戶端達到頻寬限制，則您會在用戶端上收到逾時。
-   **效能改進**: 「 高階 」 層中的快取會在有更快的處理器硬體上部署，並提供更好的效能相較於基本或標準層次。 高階層快取的輸送量較高，延遲較低。

下表顯示從 Iaas VM 使用 `redis-benchmark.exe` 對 Azure Redis 快取端點測試各種標準和高階快取大小時觀察到的最大頻寬值。 請注意，這些值並非保證值，亦無關於這些數字的 SLA，但應該是一般情況。 您應該載入測試自己的應用程式，以判斷應用程式的正確快取大小。

我們可以從此表格中獲得下列結論。

-   相較於標準層，高階層中相同大小的快取，其輸送量較高。 例如 6 GB 快取，P1 的輸送量會是 140 K RPS 相較於 49 K 為 C3。
-   使用 Redis 叢集，當您增加叢集中的分區 (節點) 數目時，輸送量會呈線性增加。 例如 如果您建立 10 個分區的 P4 叢集則可以使用輸送量 250k 個 * 10 = 2.5 萬 RPS
-   相較於標準層，高階層中的金鑰大小越大，輸送量就越高。

| 定價層         | 大小   | 可用的頻寬        | 1 KB 金鑰大小                  |
|----------------------|--------|----------------------------|--------------------------------|
| **標準快取大小** | &nbsp;       |**以 MB / 秒 (Mbps) 為單位** | **每秒要求數目 (RPS)**            |
| C0                   | 250 MB | 5                          | 600                            |
| C1                   | 1 GB   | 100                        | 12200                          |
| C2                   | 2.5 GB | 200                        | 24000                          |
| C3                   | 6 GB   | 400                        | 49000                          |
| C4                   | 13 GB  | 500                        | 61000                          |
| C5                   | 26 GB  | 1000                       | 115000                         |
| C6                   | 53 GB  | 2000                       | 150000                         |
| **高階快取大小**  |  &nbsp;      |    &nbsp;                        | **每一分區的每秒要求數目 (RPS)** |
| P1                   | 6 GB   | 1000                       | 140000                         |
| P2                   | 13 GB  | 2000                       | 220000                         |
| P3                   | 26 GB  | 2000                       | 220000                         |
| P4                   | 53 GB  | 4000                       | 250000                         |


如需下載 Redis 工具，例如 `redis-benchmark.exe`, ，請參閱 [如何執行 Redis 命令?](#cache-commands) 一節。

<a name="cache-region"></a>
## 我應該在哪個區域找到快取？

為獲得最佳效能和最低延遲，請在與快取用戶端應用程式相同的區域中找到 Azure Redis 快取。

<a name="cache-billing"></a>
## Azure Redis 快取如何收費？

Azure Redis 快取的價格是 [這裡](http://azure.microsoft.com/pricing/details/cache/)。 定價頁面所列的價格為每小時的費率。 快取是根據從建立快取到刪除快取的時間，以分鐘為單位來收費。 沒有用於停止或暫停快取收費的選項。

<a name="cache-timeouts"></a>
## 為什麼看到逾時？

用來與 Redis 溝通的用戶端發生逾時。 在大多數的情況下，Redis 伺服器不會逾時。 將命令傳送到 Redis 伺服器時，會將命令排入佇列，而且 Redis 伺服器最後會挑選並執行命令。 不過，用戶端可能會在此程序期間逾時，而且，如果是這樣，則會在呼叫端引發例外狀況。 如需疑難排解逾時問題的詳細資訊，請參閱 [調查 StackExchange.Redis 中 Azure Redis 快取的逾時例外狀況](http://azure.microsoft.com/blog/2015/02/10/investigating-timeout-exceptions-in-stackexchange-redis-for-azure-redis-cache/)。

<a name="cache-monitor"></a>
## 如何監視快取的健全狀況和效能？

Microsoft Azure Redis 快取執行個體能夠監視 [Azure 入口網站](https://portal.azure.com)。 您可以檢視度量、將度量圖表釘選到「開始面板」、自訂監視圖表的日期和時間範圍、新增和移除圖表中的度量，以及設定符合特定條件時的警示。 這些工具可讓您監視 Azure Redis 快取執行個體的健全狀況，並協助您管理快取應用程式。 如需監視快取的詳細資訊，請參閱 [監視 Azure Redis 快取](https://msdn.microsoft.com/library/azure/dn763945.aspx)。

<a name="cache-disconnect"></a>
## 我的用戶端為什麼中斷與快取的連線？

下面是快取中斷連線的一些常見原因。

-   用戶端原因
    -   已重新部署用戶端應用程式。
    -   用戶端應用程式已執行調整作業。
        -   如果是雲端服務或 Web Apps，這可能是自動調整所造成。
    -   用戶端上的網路層已變更。
    -   在用戶端或用戶端與伺服器之間的網路節點中，發生暫時性錯誤。
    -   已達頻寬閾值限制。
    -   CPU 繫結作業用了太多時間才完成。
-   伺服器端原因
    -   在標準快取供應項目上，Azure Redis 快取服務會起始從主要節點到次要節點的容錯移轉。
    -   Azure 會修補已部署快取的執行個體
        -   這可能適用於 Redis 伺服器更新或一般 VM 維護。

<a name="cache-configuration"></a>
## StackExchange.Redis 設定選項的作用為何？

StackExchange.Redis NuGet 封裝有許多選項。 本節談論一些常見設定。 如需詳細 StackExchange.Redis 選項的相關資訊，請參閱 [StackExchange.Redis 設定](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Configuration.md)。

ConfigurationOptions|說明|建議
---|---|---
AbortOnConnectFail|設定為 true 時，在網路失敗之後不會重新連線。|設定為 false，並讓 StackExchange.Redis 自動重新連線。
ConnectRetry|初始連線期間的重複連線嘗試次數。||
ConnectTimeout|連線作業的逾時 (毫秒)。|

在大部分情況下，用戶端的預設值就已足夠。 您可以根據工作負載來微調選項。

-   重試
    -   對於 ConnectRetry 和 ConnectTimeout，一般指引是快速失敗，然後再試一次。 這根據您的工作負載以及用戶端發出 Redis 命令以及接收回應所需的平均時間。
    -   讓 StackExchange.Redis 自動重新連線，而不檢查連線狀態，並自行重新連線。 **請避免使用 ConnectionMultiplexer.IsConnected 屬性**。
    -   雪球處理 - 有時，您可能會在重試此雪球時碰到問題，永遠無法復原。 在此情況下您應該考慮使用指數輪詢重試演算法，如下所示 [重試一般指引](https://github.com/mspnp/azure-guidance/blob/master/Retry-General.md) Microsoft 典範與實例群組所發佈。
-   逾時值
    -   請考慮您的工作負載，並據此設定值。 如果您要儲存大的值，請將逾時設定為較高的值。
        -   將 ABortOnConnectFail 設定為 false，並讓 StackExchange.Redis 重新連線。
-   使用應用程式的單一 ConnectionMultiplexer 執行個體。 您可以使用 LazyConnection 建立 Connection 屬性所傳回的單一執行個體中所示 [連接到快取使用 ConnectionMultiplexer 類別](https://msdn.microsoft.com/library/azure/dn690521.aspx#Connect)。
-   將 `ConnectionMultiplexer.ClientName` 屬性設定為應用程式執行個體唯一名稱，以進行診斷。
-   針對自訂工作負載，使用多個 `ConnectionMultiplexer` 執行個體。
    -   如果您的應用程式中有不同的負載，則可以遵循此模型。 例如：
        -   您可以有一個多工器來處理大型索引鍵。 
        -   您可以有一個多工器來處理小型索引鍵。 
        -   您可以設定連線逾時的不同值，以及每個所使用 ConnectionMultiplexer 的重試邏輯。
        -   在每個多工器上設定 `ClientName` 屬性，以協助診斷。 
        -   這會導致每個 `ConnectionMultiplexer` 的更簡化延遲。

<a name="cache-redis-commands"></a>
## 使用常見 Redis 命令時的一些考量為何？

-   如果不了解需要花很長時間才能完成之特定 Redis 命令的影響，則不應該執行這些命令。
    -   例如，請勿執行 [金鑰](http://redis.io/commands/keys) 命令在生產環境中，因為它可能會花很長的時間，以傳回依索引鍵數目而定。 Redis 是單一執行緒伺服器，並且一次處理一個命令。 如果您在 KEYS 之後發出其他命令，則除非 Redis 處理 KEYS 命令，否則不會處理它們。
-   索引鍵大小 - 應該使用較小的索引鍵/值還是較大的索引鍵/值？ 一般而言，這取決於案例。 如果您的案例需要較大的索引鍵，則可以調整 ConnectionTimeout，以及重試值並調整重試邏輯。 從 Redis 伺服器的觀點，觀察到較小的值，即表示有較佳的效能。
    -   這不表示您無法在 Redis 中儲存較大的值；您必須注意下列考量。 延遲會比較高。 如果您有一組更大的資料和一個較小，您可以使用多個 ConnectionMultiplexer 執行個體，如先前所述，每一個設定了一組不同的逾時和重試值 [怎麼 StackExchange.Redis 設定選項的作用](#cache-configuration) 一節。


<a name="cache-ssl"></a>
## 何時應該啟用非 SSL 連接埠來連線至 Redis？

Redis 伺服器不支援非預設 SSL，但是 Azure Redis 快取則支援。 如果您是連線至 Azure Redis 快取，並且您的用戶端支援 SSL (例如 StackExchange.Redis)，則應該使用 SSL。

請注意，預設會停用新 Azure Redis 快取執行個體的非 SSL 連接埠。 如果您的用戶端不支援 SSL，則您必須依照下列中的指示來啟用非 SSL 連接埠 [存取連接埠](https://msdn.microsoft.com/library/azure/dn793612.aspx#AccessPorts) 區段 [Azure Redis 快取中設定快取](https://msdn.microsoft.com/library/azure/dn793612.aspx) 文件。

Redis 工具，例如 `redis-cli` 未使用 SSL 連接埠，但您可以使用公用程式，例如 `stunnel` 工具安全地連線至 SSL 連接埠中的指示，依照 [宣佈 ASP.NET 工作階段狀態提供者 Redis 預覽版本](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) 部落格文章。

如需下載 Redis 工具的指示，請參閱 [如何執行 Redis 命令?](#cache-commands) 一節。

<a name="cache-benchmarking"></a>
## 如何效能評定和測試我快取的效能？

-   [啟用快取診斷](https://msdn.microsoft.com/library/azure/dn763945.aspx#EnableDiagnostics) 讓您可以 [監視](https://msdn.microsoft.com/library/azure/dn763945.aspx) 快取的健全狀況。 您可以在 Azure 入口網站中檢視度量，您也可以 [下載並檢閱](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) 使用您選擇的工具。
-   您可以使用 redis-benchmark.exe 對 Redis 伺服器進行負載測試。
    -   請確定負載測試用戶端與 Redis 快取位於相同的區域。
-   使用 redis-cli.exe，並使用 INFO 命令來監視快取。
    -   如果您的負載造成高記憶體分散，則應該相應增加為較大的快取大小。
-   如需下載 Redis 工具的指示，請參閱 [如何執行 Redis 命令?](#cache-commands) 一節。

<a name="cache-commands"></a>
## 如何執行 Redis 命令？

您可以使用任何命令列在 [Redis 命令](http://redis.io/commands#) 除了列出的命令 [Azure Redis 快取中不支援的 Redis 命令](cache-configure.md#redis-commands-not-supported-in-azure-redis-cache)。 您有幾種方式可以執行 Redis 命令。

-   如果您有標準或高階的快取，您可以執行 Redis 命令使用 [Redis 主控台](cache-configure.md#redis-console)。 這可提供在 Azure 入口網站中執行 Redis 命令的安全方式。
-   您也可以使用 Redis 命令列工具。 若要使用那些工具，請執行下列步驟。
    -   下載 [Redis 命令列工具](https://github.com/MSOpenTech/redis/releases/download/win-2.8.19.1/redis-2.8.19.zip)。
    -   使用 `redis-cli.exe` 連線至快取。 使用-h 參數傳入快取端點，以及使用 -a 傳入索引鍵 (如下列範例所示)。
        -   `redis-cli -h <your cache name>.redis.cache.windows.net -a <key>`
    -   請注意，Redis 命令列工具不適用於 SSL 連接埠，但您可以使用公用程式，例如 `stunnel` 工具安全地連線至 SSL 連接埠中的指示，依照 [宣佈 ASP.NET 工作階段狀態提供者 Redis 預覽版本](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) 部落格文章。

<a name="cache-common-patterns"></a>
## 一些常見的快取模式和考量為何？

-   Microsoft Patterns & Practices 具有下列指引。
    -   [快取指引](https://github.com/mspnp/azure-guidance/blob/master/Caching.md)。
    -   [Azure 雲端應用程式設計和實作指引](https://github.com/mspnp/azure-guidance)
-   [Azure Redis 快取的常見快取模式](cache-howto-common-cache-patterns.md)

<a name="cache-reference"></a>
## Azure Redis 快取為什麼沒有像一些其他 Azure 服務的 MSDN 類別庫參考？

Microsoft Azure Redis 快取是基於受歡迎的開放原始碼 Redis 快取，可讓您存取 Microsoft 所管理的專用安全 Redis 快取。 各種 [Redis 用戶端](http://redis.io/clients) 可供許多程式設計語言。 每個用戶端有它自己的 API，會呼叫 Redis 快取執行個體使用 [Redis 命令](http://redis.io/commands)。

因為每個用戶端都不同，所以 MSDN 上沒有一個集中式類別參考；而是每個用戶端都會維護其專屬的參考文件。 除了參考文件中，有數個教學課程顯示如何開始使用 Azure Redis 快取使用不同語言和快取用戶端上的 Azure.com 上 [Redis 快取文件](http://azure.microsoft.com/documentation/services/redis-cache/) 頁面。


## 我適合使用哪個 Azure 快取服務？

>[AZURE.IMPORTANT] Microsoft 建議所有新的開發使用 Azure Redis 快取。

Azure 快取目前有三個提供項目：

-   Azure Redis 快取
-   Azure 受管理的快取服務
-   Azure In-Role Cache

>[AZURE.IMPORTANT]我們正在宣佈 Azure 受管理快取服務與 Azure 角色中快取 2016 年 11 月 30 日淘汰。 我們建議您移轉至 Azure Redis 快取以為這次淘汰做準備。 
>
>自服務正式運作以來，Azure Redis 快取一直是 Azure 中建議的快取解決方案，而且它現在可在所有的 Azure 區域中使用，包括中國和美國政府。 基於此原因，我們要宣佈即將淘汰受管理的快取服務和 In-Role Cache 服務。 
>
>自 2015 年 11 月 30 日宣佈之後，現有的客戶最多仍可使用受管理的快取服務和 In-Role Cache 服務 12 個月，兩個服務終止的日期將在 2016 年 11 月 30 日結束。 在這個日期之後，將會關閉受管理的快取服務，而且將不再支援 In-Role Cache 服務。 
>
>我們將在 2016 年 2 月 1 日之後發行的第一個 Azure SDK 版本中撤除建立新的角色中快取的支援。 客戶將可以開啟具有角色中快取的現有專案。 
>
>在此期間，我們建議所有現有受管理的快取服務和 In-Role Cache 服務客戶移轉至 Azure Redis 快取。 Azure Redis 快取提供更多功能以及更好的整體價值。 如需有關移轉的詳細資訊，請瀏覽 [從受管理的快取服務的移轉至 Azure Redis 快取](cache-migrate-to-redis.md) 文件網頁。 
>
>如果您有任何問題，請 [與我們連絡](https://azure.microsoft.com/support/options/?WT.mc_id=azurebg_email_Trans_933)。 

### Azure Redis 快取
Azure Redis 快取已正式提供，大小最多 53 GB 和可用性 SLA 99.9%。 新 [「 高階 」 層](cache-premium-tier.md) 提供大小高達 530 GB 和支援叢集 VNET 和持續性，具有 99.9 %sla。

Azure Redis Cache 可以讓客戶使用 Microsoft 所管理的安全、專用 Redis 快取。 使用這項提供項目，您可以利用 Redis 提供的豐富功能集和生態系統，並使用 Microsoft 提供的可靠託管及監控服務。

不同於僅處理金鑰-值組的傳統快取，Redis 受到歡迎是因為其高效能資料類型。 Redis 也支援在這些類型上執行不可部分完成的作業，例如附加至字串、在雜湊中遞增值、推入至清單、計算集合交集、聯集和差異，或取得已排序集合中最高排名的成員。 其他功能包括支援交易、發行/訂用、Lua 指令碼撰寫、存活時間有限的索引鍵及組態設定，可讓 Redis 的行為更像傳統快取。

Redis 成功的另一個重要層面是建置健全、有活力的開放原始碼生態系統。 這會反映在跨多種語言可用的各式各樣 Redis 用戶端。 您在 Azure 內建置的任何工作負載，幾乎都可使用它。 

如需開始使用 Azure Redis 快取的詳細資訊，請參閱 [如何使用 Azure Redis 快取](cache-dotnet-how-to-use-azure-redis-cache.md) 和 [Azure Redis 快取文件](https://azure.microsoft.com/documentation/services/redis-cache/)。

### 受管理的快取服務
若您現為 Azure 受管理快取服務的客戶，可選擇繼續使用現有服務，或是移轉成 Azure Redis 快取來充分利用其豐富的功能集。 Azure 受管理的快取服務已正式運作，同時提供 99.9% 的可用性 SLA。

### 角色中快取
如果您使用 In-Role Cache 自我裝載，您可以繼續這麼做。 因為 In-Role Cache 是一種自我裝載的軟體元件，而不是 Microsoft 託管服務，所以它不提供任何 SLA。 In-Role Cache 使用者可以選擇移轉至 Azure Redis 快取，來運用它的豐富功能集並取得 SLA。

