<properties
    pageTitle="變更 Azure SQL Database 的服務層級和效能等級"
    description="變更 Azure SQL Database 的服務層級和效能等級說明如何相應增加或減少您的 SQL Database。 變更 Azure SQL Database 的定價層。"
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jeffreyg"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="12/01/2015"
    ms.author="sstein"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>


# 變更 SQL Database 的服務層級和效能等級 (定價層)

**單一資料庫**

> [AZURE.SELECTOR]
- [Azure 入口網站](sql-database-scale-up.md)
- [PowerShell](sql-database-scale-up-powershell.md)

本文將說明如何變更與您的 SQL database 服務層和效能層級 [Azure 入口網站](https://portal.azure.com)。 

使用中的資訊 [SQL Database Web/Business 資料庫升級至新的服務層](sql-database-upgrade-new-service-tiers.md) 和 [Azure SQL Database 服務層和效能層級](sql-database-service-tiers.md) 來判斷適當的服務層和效能層級為您的 Azure SQL 資料庫。

> [AZURE.IMPORTANT] 變更 SQL 資料庫的服務層和效能層級是一項線上作業。 這表示您的資料庫在整個作業過程中，將維持在線上可供使用的狀態而不需停機。

- 若要將資料庫降級，資料庫應該小於目標服務層允許的大小上限。 
- 升級的資料庫時 [標準異地複寫](https://msdn.microsoft.com/library/azure/dn758204.aspx) 或 [作用中地理複寫](https://msdn.microsoft.com/library/azure/dn741339.aspx) 啟用，您必須先升級次要資料庫所需的效能層然後再升級主要資料庫。
- 從高階服務層降級時，您必須先終止所有的「異地複寫」關聯性。 您可以依照所述的步驟 [終止連續複製關聯性](https://msdn.microsoft.com/library/azure/dn741323.aspx) 停止主要資料庫與作用中次要資料庫之間的複寫程序主題。
- 還原服務會針對各種服務層提供不同的選項。 降級後您可能會無法還原至某個時間點，或具有較短的備份保留期限。 如需詳細資訊，請參閱 [Azure SQL Database 備份和還原](https://msdn.microsoft.com/library/azure/jj650016.aspx)。
- 您可以在 24 小時期間內，變更最多四個個別的資料庫 (服務層或效能等級)。
- 完成變更之前，不會將新屬性套用至資料庫。


**若要完成本文，您需要下列項目：**

- Azure 訂閱。 如果您需要 Azure 訂用帳戶，可以按一下 **免費試用版** 頂端的這個頁面上，然後再回來完成這篇文章。
- Azure SQL Database。 如果您沒有 SQL 資料庫，建立一個遵循本文中的步驟 ︰ [建立您的第一個 Azure SQL Database](sql-database-get-started.md)。


## 變更您資料庫的服務層級和效能等級


開啟您想要相應增加或減少之資料庫的 [SQL Database] 刀鋒視窗：

1.  移至 [Azure 入口網站](https://portal.azure.com)。
2.  按一下 [ **全部瀏覽**。
3.  按一下 [ **SQL 資料庫**。
2.  按一下您想要變更的資料庫。
3.  在 [SQL Database] 分頁中按一下 [ **定價層** 並排顯示 ︰

    ![定價磚][1]

1.  選取新的階層，然後按一下 [ **選取**:

    按一下 **選取** 送出變更資料庫層級規模的要求。 視資料庫大小而定，調整作業需要一些時間才能完成。 按一下 [通知] 以取得調整作業的詳細資料和狀態。 

    ![選取定價層][2]

3.  在左側功能區按一下 **通知**:

    ![通知][3]

## 確認資料庫位在所選取的定價層

   調整作業完成之後，檢查及確認資料庫位在想要的階層：

2.  按一下 [ **全部瀏覽**。
3.  按一下 [ **SQL 資料庫**。
2.  按一下您更新的資料庫。
3.  檢查 **定價層** 並排顯示，並確認其設定為正確的層次。

    ![新的價格][4] 


## 後續步驟

- [相應放大和縮小](sql-database-elastic-scale-get-started.md)
- [使用 SSMS 連接和查詢 SQL Database](sql-database-connect-query-ssms.md)
- [匯出 Azure SQL Database](sql-database-export.md)

## 其他資源

- [業務續航力概觀](sql-database-business-continuity.md)
- [SQL Database 文件](https://azure.microsoft.com/documentation/services/sql-database/)


<!--Image references-->
[1]: ./media/sql-database-scale-up/pricing-tile.png
[2]: ./media/sql-database-scale-up/choose-tier.png
[3]: ./media/sql-database-scale-up/scale-notification.png
[4]: ./media/sql-database-scale-up/new-tier.png


