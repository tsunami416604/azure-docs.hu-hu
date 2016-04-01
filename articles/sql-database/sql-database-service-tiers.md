<properties
    pageTitle="SQL Database 效能和選項：服務層 | Microsoft Azure"
    description="比較服務層的 SQL Database 效能和商務持續性功能，適當地平衡成本與功能。"
    keywords="database options,database performance,eDTU"
    services="sql-database"
    documentationCenter=""
    authors="rothja"
    manager="jeffreyg"
    editor="monicar"/>

<tags
    ms.service="sql-database"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="data-management"
    ms.date="11/10/2015"
    ms.author="jroth"/>

# SQL Database 選項和效能：了解每個服務層中可用的項目
 

[Azure SQL Database](sql-database-technical-overview.md) 提供多個服務層，以處理不同類型的工作負載。 您可以 [建立單一資料庫](sql-database-get-started.md) 與定義特性和價格。 您可以管理由多個資料庫或者 [建立彈性資料庫集區](sql-database-elastic-pool-portal.md)。 在這兩種情況下，層都包含 **基本**, ，**標準**, ，和 **高階**。 然而這些服務層中的資料庫選項會因為您建立的是個別資料庫，或是在彈性資料庫集區中建立的資料庫，而有所不同。 本文會提供這兩種內容之服務層的概觀。

## 服務層和資料庫選項
基本、標準和高階服務層都具備 99.99% 的執行時間 SLA，並且提供可預測的效能、彈性的商務持續性選項、安全性功能，以及小時計費。 下表提供最適用於不同應用程式工作負載的服務層範例。

| 服務層 | 目標工作負載 |
|---|---|
| **基本** | 最適用於小型資料庫，通常會在指定的時間內支援單一的作用中作業。 範例包括用於開發或測試，或者不常使用之小規模應用程式的資料庫。 |
| **標準** | 適用於大多數雲端應用程式，可支援多個並行查詢。 範例包括工作群組或 Web 應用程式。 |
| **高級** | 針對高交易量而設計，可支援大量並行使用者，且需要最高層級商務持續性功能。 範例包括支援任務關鍵性應用程式的資料庫。 |

>[AZURE.NOTE] Web 和商務版本正逐漸遭到淘汰。 了解如何 [升級 Web 和 Business edition](sql-database-upgrade-new-service-tiers.md)。 請閱讀 [終止常見問題集](http://azure.microsoft.com/pricing/details/sql-database/web-business/) 如果您打算繼續使用 Web 和 Business Edition。

### 單一資料庫服務層和效能等級
單一資料庫的每個服務層內有多個效能層級，您可以彈性選擇最符合您工作負載需求的層級。 如果您需要相應增加或相應減少，可以很輕鬆地在 Azure 傳統入口網站中變更資料庫層，您的應用程式完全不需要停機。 請參閱 [變更資料庫服務層和效能層級](sql-database-scale-up.md) 如需詳細資訊。

此處所列的效能特性會套用至資料庫建立使用 [SQL Database V12](sql-database-v12-whats-new.md)。 如果 Azure 中的基礎硬體裝載了多個 SQL Database，您的資料庫仍然一定會有一組資源，且個別資料庫的預期效能特性不會受到影響。

[AZURE.INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]


進一步了解的 Dtu，請參閱 [DTU 區段](#understanding-dtus) 本主題中。 

>[AZURE.NOTE] 此服務層資料表中的所有資料列的詳細說明，請參閱 [服務層功能和限制](sql-database-performance-guidance.md#service-tier-capabilities-and-limits)。

### eDTU 中的彈性資料庫集區服務層和效能
除了建立及調整單一資料庫，您也可以管理多個資料庫內的 [彈性資料庫集區](sql-database-elastic-pool.md)。 彈性資料庫集區中的所有資料庫會共用一組通用資源。 測量的效能特性 *彈性資料庫交易單位* (Edtu)。 當使用單一資料庫時，彈性資料庫集區有三種服務層 ︰ **基本**, ，**標準**, ，和 **高階**。 彈性資料庫的這三個服務層仍會定義整體效能限制與多項功能。  

彈性資料庫集區可讓這些資料庫共用和取用 DTU 資源，無須指派特定效能層級給集區中的資料庫。 例如，標準集區中的單一資料庫可從使用 0 eDTU 到最大資料庫 eDTU (由服務層定義的 100 eDTU，或是您設定的自訂數字)。 如此可讓多個具有不同工作負載的資料庫有效使用整個集區中的可用 eDTU。 

下表說明彈性資料庫集區服務層的特性。

[AZURE.INCLUDE [SQL DB service tiers table for elastic databases](../../includes/sql-database-service-tiers-table-elastic-db-pools.md)]

集區中的每個資料庫也須遵循該服務層的單一資料庫特性。 例如，標準集區的工作階段數上限為每個集區 2,400 - 28,800 個，但該集區中的個別資料庫的資料庫限制為 300 個工作階段 (如上一節中指定的單一基本資料庫限制)。

### 了解 DTU

[AZURE.INCLUDE [SQL DB DTU description](../../includes/sql-database-understanding-dtus.md)]

## 監視資料庫效能
監視 SQL Database 的效能，必須從監視您選擇之資料庫效能層級相關的資源使用率開始。 此相關資料以下列方式公開：

1.  Microsoft Azure 傳統入口網站。

2.  使用者資料庫，以及包含使用者資料庫之伺服器主資料庫中的動態管理檢視。

在 [Azure 入口網站](https://portal.azure.com/), ，您可以選取您的資料庫，然後按一下監視單一資料庫的使用率 **監視** 圖表。 這會帶出 **度量** ] 視窗中，您可以按一下 **編輯圖表** ] 按鈕。 新增下列度量：

- CPU 百分比
- DTU 百分比
- 資料 IO 百分比
- 儲存空間百分比

一旦您已經新增這些度量，您可以繼續檢視在 **監視** 具有更多詳細資料的圖表上 **度量** 視窗。 所有四個度量顯示平均使用率百分比，相對於 **DTU** 您的資料庫。

![資料庫效能的服務層監視。](./media/sql-database-service-tiers/sqldb_service_tier_monitoring.png)

您也可以在效能度量中設定警示。 按一下 [ **新增警示** 按鈕 **度量** 視窗。 遵循精靈的指示以設定警示。 您可以選擇在度量超出或低於特定臨界值時發出警示。

例如，如果您預期資料庫中的工作負載會成長，可以選擇設定電子郵件警示，以便在資料庫的任何效能度量達到 80% 時收到警示。 您可以使用此警示做為早期警告，協助您判斷何時需要切換至更高的效能層級。

效能度量也可協助您判斷您是否能夠降級至較低的效能層級。 假設您使用標準 S2 資料庫，且所有效能度量皆顯示在指定時間內，資料庫平均使用率沒有超過 10%。 則該資料庫可能在標準 S1 中會運作得不錯。 不過，在您決定將資料庫移至較低效能層級前，請先注意暴增或大幅變動的工作負載。 

在入口網站中公開的相同度量也是可透過系統檢視表 ︰ [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) 中的邏輯 **主要** 您伺服器的資料庫和 [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) 使用者資料庫中 (**sys.dm_db_resource_stats** 會在每個基本、 標準和高階的使用者資料庫中建立。 Web 和 Business Edition 資料庫會傳回空的結果集)。 使用 **sys.resource_stats** 如果您需要一段較長時間監視較不精細的資料。 使用 **sys.dm_db_resource_stats** 如果您需要監視較精細的資料較小的時間範圍內。 如需詳細資訊，請參閱 [Azure SQL Database 效能指引](sql-database-performance-guidance.md#monitoring-resource-use-with-sysresourcestats)。

若為彈性資料庫集區，您可以監視其中的個別資料庫，技巧如本節所述。 但您也可以監視集區整體。 如需資訊，請參閱 [監視和管理彈性資料庫集區](sql-database-elastic-pool-portal.md#monitor-and-manage-an-elastic-database-pool)。

## 後續步驟
進一步了解定價層 [SQL Database 定價](http://azure.microsoft.com/pricing/details/sql-database/)。

如果您有興趣管理多個資料庫，為群組，請考慮 [彈性資料庫集區](sql-database-elastic-pool-guidance.md) 隨著相關聯 [彈性資料庫集區的價格和效能考量](sql-database-elastic-pool-guidance.md)。

既然您了解 SQL Database 層，親身 [免費試用版](http://azure.microsoft.com/pricing/free-trial/) ，並了解 [如何建立您的第一個 SQL database](sql-database-get-started.md)！
 


