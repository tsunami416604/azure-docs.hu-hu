<properties
    pageTitle="為 Azure SQL Database 中的資料庫效能進行疑難排解。"
    description="為資料庫效能進行疑難排解的快速步驟。"
    services="sql-database"
    documentationCenter=""
    authors="v-shysun"
    manager="msmets"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/17/2015"
    ms.author="v-shysun"/>

# 使用 Azure SQL Database 來為資料庫效能進行疑難排解
您可以變更 [服務層](sql-database-service-tiers.md) 的單一資料庫或增加彈性資料庫的 Edtu 集區在任何時間，以改善效能，但您可能想要識別提升，並先最佳化查詢效能的機會。 遺漏索引與查詢最佳化不足是資料庫效能不佳的常見原因。

## 評估和調整資料庫效能的步驟
1.  在 [Azure 入口網站](https://portal.azure.com), ，按一下 [ **SQL 資料庫**, 選取資料庫，然後使用監視圖表來尋找資源接近其上限。 預設會顯示 DTU 耗用量。 按一下 [ **編輯** 來變更所顯示的值和時間範圍。
2.  使用 [查詢效能深入解析](sql-database-query-performance.md) 評估使用 Dtu 的查詢，然後使用 [索引顧問](sql-database-index-advisor.md) 建議和建立索引。
3.  您可以使用動態管理檢視 (DMV)、擴充的事件 (Xevent) 和 SSMS 中的查詢存放區即時取得效能參數。 請參閱 [效能指引主題](sql-database-performance-guidance.md) 取得詳細的監視和微調技巧。

## 使用更多資源提升資料庫效能的步驟
1.  您可以針對單一資料庫， [來變更服務層](sql-database-scale-up.md) 視可改進資料庫效能。
2.  多個資料庫，請考慮使用 [彈性資料庫集區](sql-database-elastic-pool-guidance.md) 自動調整資源。

如果持續發生效能問題，請連絡支援人員開啟支援案例。

