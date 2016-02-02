<properties 
    pageTitle="使用彈性資料庫分割合併工具來縮放 |Microsoft Azure" 
    description="說明如何使用彈性資料庫 API 透過自我託管服務操作分區和移動資料。" 
    services="sql-database" 
    documentationCenter="" 
    manager="jeffreyg" 
    authors="ddove"/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="11/04/2015" 
    ms.author="ddove;sidneyh" />


# 使用彈性資料庫分割合併工具來縮放

[彈性資料庫工具](sql-database-elastic-scale-introduction.md) 包含重新平衡資料分佈及管理作用區分區化應用程式的工具。  **分割合併工具** 管理相應縮小和向外擴充，您可以新增或移除資料庫，從您的分區設定，並使用分割合併工具重新平衡它們之間的 shardlet 分佈。 (關於詞彙定義，請參閱 [Elastic scale 名詞解釋](sql-database-elastic-scale-glossary.md))。

此工具之間不同的資料庫，依需求移動 shardlet，並整合 [分區對應管理](sql-database-elastic-scale-shard-map-management.md) 維護一致的對應。

若要開始，請參閱 [彈性資料庫分割合併工具 ](sql-database-elastic-scale-configure-deploy-split-and-merge.md)。

## 分割合併的新功能

1.1.0 版本的分割合併工具可讓您自動清除已完成要求的中繼資料。 組態選項可控制此中繼資料在移除之前要保留多久時間。

1.0.0 版的分割合併工具提供下列改進:
* 包含 .Net API 來銜接分割合併 – Web 角色現在是選擇性
* 分區化索引鍵現在支援日期和時間類型
* 現在也支援清單分區對應。
* 要求中的範圍界限可以更輕鬆地符合儲存在分區對應中的範圍。
* 為改善可用性，現在也支援多個背景工作角色執行個體。
* 與分割合併作業一起儲存的認證現為靜態加密。

## 如何升級

1. 從 NuGet 下載最新版的分割-合併套件中所述 [下載分割合併封裝](sql-database-elastic-scale-configure-deploy-split-and-merge.md#download-the-Split-Merge-packages)。
2. 變更分割合併部署的雲端服務組態檔，以反映新的組態參數。 新的必要參數是用於加密的憑證相關資訊。 若要這樣做，一個簡單方法是將下載的新組態範本檔案與現有組態進行比較。 請確定您新增 Web 和背景工作角色的 "DataEncryptionPrimaryCertificateThumbprint" 和 "DataEncryptionPrimary" 設定。
3. 將更新部署至 Azure 之前，請確定目前執行的所有分割合併作業都已完成。 作法很簡單，您可以針對進行中的要求，查詢分割合併中繼資料資料庫中的 RequestStatus 和 PendingWorkflows 資料表。
4. 使用新的封裝和更新的服務組態檔，在您的 Azure 訂用帳戶中更新分割合併的現有雲端服務部署。

您無需佈建新的中繼資料資料庫，即可升級分割合併。 新的版本會自動將現有的中繼資料資料庫升級到新的版本。

## 分割合併的案例

應用程式需要靈活地伸展超越單一 Azure SQL DB 資料庫的限制，如下列案例所示：

* **放大容量 – 分割範圍**：在資料層放大彙總容量可解決增加的容量需求。 在此案例中，應用程式將資料分區，並分散到越來越多的資料庫上，直到滿足容量需求為止，以此來提供更多的容量。 Elastic Scale 分割合併服務的「分割」功能可滿足此案例。

* **縮小容量 – 合併範圍**：業務的季節性本質會造成容量波動。 此案例突顯當生意清淡時需要能夠輕易調回到較少的縮放單位。 Elastic Scale 分割合併服務的「合併」功能可滿足此需求。

* **管理作用區 – 移動 Shardlet**：在每個資料庫有多個租用戶的情況下，分區的 Shardlet 配置可能造成某些分區出現容量瓶頸。 這需要重新配置 Shardlet，或將忙碌的 Shardlet 移到新的或較少使用的分區。

接下來，我們依照這些功能，將服務中的任何處理稱為**分割/合併/移動**要求。


圖 1：分割合併的概念式概觀


![概觀][1]


**注意**：並非所有**放大容量**案例都需要分割合併服務。 例如，如果您在環境中定期建立新的分區，以越來越多分區化索引鍵值來儲存新的資料，您可以使用分區對應管理用戶端 API，將新的資料範圍指向新建立的分區。 只有在需要一併移動現有的資料時，才需要分割合併服務。

## 概念和重要功能

**客戶託管服務**：分割合併以客戶託管服務提供。 您必須將服務部署並裝載於 Microsoft Azure 訂用帳戶中。 您從 NuGet 下載的封裝包含設定範本，可加入您特定部署的資訊而使之完備。 請參閱 [分割合併教學課程](sql-database-elastic-scale-configure-deploy-split-and-merge.md) 如需詳細資訊。 因為服務是在您的 Azure 訂用帳戶中執行，您可以控制和設定服務的大部分安全性層面。 預設範本包含設定 SSL、以憑證為基礎的用戶端驗證、加密儲存的認證、DoS 防護及 IP 限制等選項。 您可以在下列文件中找到安全性層面的詳細資訊 [分割合併安全性設定](sql-database-elastic-scale-split-merge-security-configuration.md)。

預設部署服務以一個背景工作角色和一個 Web 角色執行。 各角色在 Azure 雲端服務中使用 A1 VM 大小。 雖然您無法在部署封裝時修改這些設定，但可以在部署成功後在執行中的雲端服務中變更它們 (透過 Azure 入口網站)。 請注意，基於技術原因，不可以將背景工作角色設定多個執行個體。

**分區對應整合**：分割合併服務會與應用程式的分區對應互動。 當使用分割合併服務來分割或合併範圍，或在分區之間移動 Shardlet 時，服務會自動將分區對應保持在最新狀態。 為了這樣做，服務會連接到應用程式的分區對應管理員資料庫，並於分割/合併/移動要求進行時維護範圍和對應。 這可確保當分割合併作業持續進行時，分區對應一律呈現最新的檢視。 分割、合併和 Shardlet 移動作業的實作方式是將一批 Shardlet 從來源分區移至目標分區。 在 Shardlet 移動作業期間，屬於目前批次中的 Shardlet 在分區對應中會標示為離線，也無法供使用 **OpenConnectionForKey** API 的資料相依路由連線使用。

**一致的 Shardlet 連線**：當新一批 Shardlet 的資料移動開始時，分區對應提供指向分區 (儲存 Shardlet) 的任何資料相依路由連線會終止，而當資料移動正在進行時，也會封鎖後續從分區對應 API 至這些 Shardlet 的連線，以避免不一致。 對同一個分區的其他 Shardlet 的連接也會終止，但重試就會立即成功。 移動批次後，目標分區的 Shardlet 會再次標示為線上，並從來源分區移除來源資料。 服務會對每個批次執行這些步驟，直到所有 Shardlet 都已經移動為止。 在整個分割/合併/移動作業的過程中，這將會產生數個連接終止作業。

**管理 Shardlet 可用性**：如上所述，只限制終止目前 Shardlet 批次的連線，可將無法使用的範圍限制在一次一批 Shardlet。 這比分割或合併作業過程中整個分區對其所有 Shardlet 仍保持離線的作法更好。 批次大小 (定義為一次要移動的相異 Shardlet 數目) 是組態參數。 每個分割和合併作業中都可定義它，視應用程式的可用性和效能需求而定。 請注意，分區對應中鎖定的範圍可能大於指定的批次大小。 這是因為服務挑選範圍大小時，都希望資料中的分區化索引鍵值的實際數大約符合批次大小。 對於稀疏填入的分區化索引鍵，尤其要記住這一點。

**中繼資料存放區**：分割合併服務使用資料庫來維護其狀態，並在要求處理期間保留記錄檔。 使用者在其訂用帳戶中建立此資料庫，並在服務部署的組態檔中提供連接字串。 使用者組織的系統管理員也可以連接到此資料庫來檢閱要求進度，並調查關於潛在失敗的詳細資訊。

**分區化感知**：分割合併服務會區別 (1) 分區化資料表、(2) 參考資料表和 (3) 一般資料表。 分割/合併移動作業的語意視使用的資料表類型而定，定義如下：

* **分區化資料表**：分割、合併和移動作業會將 Shardlet 從來源移至目標分區。 成功完成整體要求之後，這些 Shardlet 就不存在於來源。 請注意，目標資料表必須存在於目標分區，而且在處理作業之前不能包含目標範圍中的資料。

* **參考資料表**：對於參考資料表，分割、合併和移動作業會將資料從來源複製到目標分區。 但是請注意，若目標上給定資料表中已存在任何資料列，則此資料表的目標分區不會發生任何變更。 資料表必須是空的，才會處理任何參考資料表複製作業。

* **其他資料表**：分割和合併作業的來源或目標上可以存在其他資料表。 在任何資料移動或複製作業中，分割合併服務會忽略這些資料表。 但是請注意，在有條件約束時，它們會干擾這些作業。

分區對應的 **SchemaInfo** API 提供參考資料表與分區化資料表的比較資訊。 下列範例說明如何在給定的分區對應管理員物件 smm 上使用這些 API：

    // Create the schema annotations 
    SchemaInfo schemaInfo = new SchemaInfo(); 
    
    // Reference tables 
    schemaInfo.Add(new ReferenceTableInfo("dbo", "region")); 
    schemaInfo.Add(new ReferenceTableInfo("dbo", "nation")); 
    
    // Sharded tables 
    schemaInfo.Add(new ShardedTableInfo("dbo", "customer", "C_CUSTKEY")); 
    schemaInfo.Add(new ShardedTableInfo("dbo", "orders", "O_CUSTKEY")); 
    
    // Publish 
    smm.GetSchemaInfoCollection().Add(Configuration.ShardMapName, schemaInfo); 

資料表 '‘region’ 及 ‘nation’ 定義為參考資料表，將透過分割/合併/移動作業來複製。 ‘customer’ 和 ‘orders’ 定義為分區化資料表。 C_CUSTKEY 和 O_CUSTKEY 作為分區化索引鍵。

**參考完整性**：分割合併服務會分析資料表之間的相依性，並使用外部主索引鍵關聯性來接移作業，以移動參考資料表和 Shardlet。 一般而言會先根據相依性順序複製參考資料表，然後在每個批次中再根據相依性順序複製 Shardlet。 這是必要的，當新資料到達時，才能符合目標分區的 FK PK 條件約束。

**分區對應一致性與最終完成**：失敗時，分割合併服務會在任何中斷之後繼續作業，以完成任何進行中的要求。 不過，可能有無法復原的情況，例如，當目標分區遺失或損壞到無法修復的程度。 在這些情況下，原本要移動的一些 Shardlet 可能繼續留在來源分區。 服務可以確保只有當必要的資料成功複製到目標之後，才會更新 Shardlet 對應。 只有當所有資料都已複製到目標，並已成功更新對應的對應之後，才會在來源刪除 Shardlet。 當範圍在目標分區上已上線時，刪除作業會在幕後執行。 分割合併服務永遠確保儲存在分區對應中的對應的正確性。

## 取得服務二進位檔

分割合併服務的二進位檔透過提供 [Nuget](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/)。 請參閱逐步 [分割合併教學課程](sql-database-elastic-scale-configure-deploy-split-and-merge.md) 如需有關下載二進位檔。

## 分割合併使用者介面

除了背景工作角色，分割合併服務套件也包含 Web 角色，可用來以互動方式提交分割合併要求。 使用者介面的主要元件如下：

-    Operation Type: The operation type is a radio button that controls the kind of operation performed by the service for this request. You can choose between the split, merge and move scenarios discussed in Concepts and Key Features. In addition, you can also cancel a previously submitted operation. You can use split, merge and move requests for range shard maps. List shard maps only support move operations.

-    Shard Map: The next section of request parameters cover information about the shard map and the database hosting your shard map. In particular, you need to provide the name of the Azure SQL Database server and database hosting the shardmap, credentials to connect to the shard map database, and finally the name of the shard map. Currently, the operation only accepts a single set of credentials. These credentials need to have sufficient permissions to perform changes to the shard map as well as to the user data on the shards.

-    Source Range (split and merge): A split and merge operation processes a range using its low and high key. To specify an operation with an unbounded high key value, check the “High key is max” check box and leave the high key field empty. The range key values that you specify do not need to precisely match a mapping and its boundaries in your shard map. If you do not specify any range boundaries at all the service will infer the closest range for you automatically. You can use the GetMappings.ps1 PowerShell script to retrieve the current mappings in a given shard map.

-    Split Source Behavior (split): For split operations, you also need to define at which point you want to split the source range. You do this by providing the sharding key where you want the split to occur. Use the radio button next to define whether you want the lower part of the range (excluding the split key) to move, or whether you want the upper part to move (including the split key).

-    Source Shardlet (move): Move operations are different from split or merge operations as they do not require a range to describe the source. A source for move is simply identified by the sharding key value that you plan to move.

-    Target Shard (split): Once you have provided the information on the source of your split operation, you need to define where you want the data to be copied to by providing the Azure SQL Db server and database name for the target.

-    Target Range (merge): Merge operations instead move shardlets to an existing shard. You identify the existing shard by providing the range boundaries of the existing range that you want to merge with.

-    Batch Size: The batch size controls the number of shardlets that will go offline at a time during the data movement. This is an integer value where you can use smaller values when you are sensitive to long periods of downtime for shardlets. Larger values will increase the time that a given shardlet is offline but may improve performance.

-    Operation Id (Cancel): If you have an ongoing operation that is no longer needed, you can cancel the operation by providing its operation ID in this field. You can retrieve the operation ID from the request status table (see Section 8.1) or from the output in the web browser where you submitted the request.



## 需求和限制

目前的分割合併服務實作有下列需求和限制：

* 目前，分區必須存在且在分區對應中註冊，才能對這些分區執行分割合併作業。

* 分割合併服務目前不會在其作業中自動建立資料表或其他任何資料庫物件。 這表示執行任何分割/合併/移動作業之前，所有分區化資料表和參考資料表的結構描述必須存在於目標分區。 尤其，在由分割/合併/移動作業加入新 Shardlet 的範圍中，分區化資料表必須是空的。 否則，作業無法通過目標分區上的初始一致性檢查。 另請注意，只有當參考資料表是空的時，才會複製參考資料，而對於參考資料表上的其他並行寫入作業，也無法保證一致性。 我們建議 – 在執行分割/合併作業時，沒有其他寫入作業在變更參考資料表。

* 服務目前依賴唯一索引或索引鍵 (包含分區化索引鍵) 所建立的資料列識別，以改善大型 Shardlet 的效能和可靠性。 這可讓服務移動比分區化索引鍵值更精細的資料。 這有助於減少作業期間所需的記錄檔空間和鎖定數目上限。 如果您想要透過分割/合併/移動要求來使用特定的資料表，請考慮在此資料表上建立唯一索引或主索引鍵 (包含分區化索引鍵)。 基於效能考量，分區化索引鍵應該為索引鍵或索引中的開頭資料行。

* 要求處理期間，某些 Shardlet 資料可能會同時出現在來源和目標分區。 為了防止 Shardlet 移動期間失敗，這在目前是必要的。 如前所述，分割合併與 Elastic Scale 分區對應的整合，可確保在分區對應上透過資料相依路由 API 使用 **OpenConnectionForKey** 方法來連線時，不會出現任何不一致的過渡狀態。 不過，當連線到來源或目標分區不是使用 **OpenConnectionForKey** 方法時，則在分割/合併/移動要求進行時，可能會出現不一致的過渡狀態。 這些連接可能會顯示不完整或重複的結果，視連接之下的時間或分區而定。 這項限制目前包括 Elastic Scale 多分區查詢所建立的連接。

* 不同角色之間不可共用分割合併服務的中繼資料資料庫。 例如，在預備環境中執行分割合併服務的角色，必須指向與生產角色不同的中繼資料資料庫。


## 計費

分割合併服務是以 Microsoft Azure 訂用帳戶中的雲端服務執行。 因此會對您的服務執行個體收取雲端服務的費用。 除非您經常執行分割/合併/移動作業，否則建議您刪除分割合併雲端服務。 這可以節省執行中或已部署的雲端服務執行個體的成本。 每當您需要執行分割或合併作業時，您可以重新部署和啟動可立即運作的組態。

## 監視

### 狀態資料表

分割合併服務在中繼資料儲存資料庫中提供 **RequestStatus** 資料表，以監視已完成和進行中的要求。 已提交至這個分割合併服務執行個體的每一個分割合併要求，在此資料表中都會列出一個資料列。 它針對每個要求提供以下的資訊：

* **Timestamp**：要求開始的時間和日期。

* **OperationId**：唯一識別要求的 GUID。 這項要求也可用來取消仍在進行中的作業。

* **Status**：要求的目前狀態。 對於持續進行的要求，它也列出要求目前所處的階段。

* **CancelRequest**：旗標，指出是否已取消要求。

* **Progress**：作業完成的估計百分比。 值為 50 表示作業已完成大約 50%。

* **Details**：XML 值，提供更詳細的進度報表。 隨著資料列從來源複製到目標，進度報表會定期更新。 如果發生錯誤或例外狀況，此資料行也包含失敗的詳細資訊。


### Azure 診斷

分割合併服務會根據 Azure SDK 2.5 使用 Azure 診斷來監控與診斷。 您可以如這裡所述控制診斷組態: [在 Azure 雲端服務和虛擬機器中啟用診斷](../cloud-services-dotnet-diagnostics.md)。 下載封裝包含兩個診斷組態 – 一個用於 Web 角色，另一個用於背景工作角色。 這些服務的診斷組態遵循的指引 [Microsoft Azure 中的雲端服務基礎](https://code.msdn.microsoft.com/windowsazure/Cloud-Service-Fundamentals-4ca72649)。 其中包含定義來記錄效能計數器、IIS 記錄檔、Windows 事件記錄檔，以及分割合併應用程式事件記錄檔。

## 部署診斷

針對 NuGet 封裝所提供的 Web 和背景工作角色，若要使用診斷組態啟用監視和診斷，請使用 Azure PowerShell 執行下列命令：

    $storage_name = "<YourAzureStorageAccount>" 
    
    $key = "<YourAzureStorageAccountKey" 
    
    $storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key  
    
    
    $config_path = "<YourFilePath>\SplitMergeWebContent.diagnostics.xml" 
    
    $service_name = "<YourCloudServiceName>" 
    
    Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Production -Role "SplitMergeWeb" 
    
    
    $config_path = "<YourFilePath>\SplitMergeWorkerContent.diagnostics.xml" 
    
    $service_name = "<YourCloudServiceName>" 
    
    Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Production -Role "SplitMergeWorker" 

您可以找到有關如何設定和部署診斷設定的詳細資訊: [在 Azure 雲端服務和虛擬機器中啟用診斷](../cloud-services-dotnet-diagnostics.md)。

## 擷取診斷

從 Visual Studio 伺服器總管，您可以在伺服器總管樹狀目錄的 Azure 部分中輕鬆存取診斷。開啟 Visual Studio 執行個體，在功能表列按一下 [檢視] 和 [伺服器總管]。按一下 [Azure] 圖示，連接至您的 Azure 訂用帳戶。瀏覽至 Azure]-> [儲存體]-> [ <your storage account> ]-> [資料表]-> [WADLogsTable。如需詳細資訊，請參閱 [使用伺服器總管瀏覽儲存體資源](http://msdn.microsoft.com/library/azure/ff683677.aspx)。

![WADLogsTable][2]

在上圖中反白顯示的 WADLogsTable 包含分割合併服務應用程式記錄檔中的詳細事件。 請注意，下載的封裝的預設組態是特別針對生產部署而設計。 因此，從服務執行個體提取記錄檔和計數器的間隔時間很大 (5 分鐘)。 在測試和開發時，請依需要調整 Web 或背景工作角色的診斷設定，以縮短間隔。 請以滑鼠右鍵按一下 Visual Studio 伺服器總管 (請參閱上圖) 中的角色，然後調整 [診斷組態] 設定對話方塊中的 [傳輸期間]：

![組態][3]


## 效能

一般而言，Azure SQL Database 中越高階、越有效能的服務層，預期會有較佳的效能。 越高的服務層使用越高的 IO、CPU 和記憶體配置，有利於分割合併服務使用的大量複製和刪除作業。 基於這個理由，請只針對這些資料庫，在一段已定義的有限期間內增加服務層。

服務在其正常作業中也會執行驗證查詢。 這些驗證查詢會檢查目標範圍中是否存在非預期的資料，並確保任何分割/合併/移動作業是從一致的狀態開始。 這些查詢全部都會檢查作業領域所定義的分區化索引鍵範圍，以及要求定義中所提供的批次大小。 當索引存在且以分區化索引鍵做為開頭資料行時，這些查詢的表現最好。

此外，以分區化索引鍵做為開頭資料行的唯一性屬性，可讓服務使用最佳化方法來限制記錄檔空間和記憶體方面的資源消耗。 移動大型資料時 (通常超過 1 GB) 需要這個唯一性屬性。

## 最佳作法和疑難排解

-    Define a test tenant and exercise your most important split/merge/move operations with the test tenant across several shards. Ensure that all metadata is defined correctly in your shard map and that the operations do not violate constraints or foreign keys.

-    Keep the test tenant data size above the maximum data size of your largest tenant to ensure you are not encountering data size related issues. This helps you assess an upper bound on the time it takes to move a single tenant around. 

-    Make sure that your schema allows deletions. The split-merge service requires the ability to remove data from the source shard once the data has been successfully copied to the target. For example, **delete triggers** can prevent the service from deleting the data on the source and may cause operations to fail.

-    The sharding key should be the leading column in your primary key or unique index definition. That ensures the best performance for the split or merge validation queries, and for the actual data movement and deletion operations which always operate on sharding key ranges.

-    Collocate your split-merge service in the region and data center where your databases reside. 


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

## 參考

* [分割合併教學課程](sql-database-elastic-scale-configure-deploy-split-and-merge.md)

* [Elastic Scale 安全性考量](sql-database-elastic-scale-split-merge-security-configuration.md)





[1]: ./media/sql-database-elastic-scale-overview-split-and-merge/split-merge-overview.png 
[2]: ./media/sql-database-elastic-scale-overview-split-and-merge/diagnostics.png 
[3]: ./media/sql-database-elastic-scale-overview-split-and-merge/diagnostics-config.png 

