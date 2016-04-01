<properties
    pageTitle="建立和匯出 Azure SQL Database 的 BACPAC"
    description="建立和匯出 Azure SQL Database 的 BACPAC"
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


# 建立和匯出 Azure SQL Database 的 BACPAC

**單一資料庫**

> [AZURE.SELECTOR]
- [Azure 入口網站](sql-database-export.md)
- [PowerShell](sql-database-export-powershell.md)

本文提供的指示，說明如何匯出您的 Azure SQL database 與 BACPAC [Azure 入口網站](https://portal.azure.com)。

A [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) 是.bacpac 檔案，其中包含資料庫結構描述和資料。 BACPAC 是資料庫伺服器移動到另一個，為主要使用案例 [本機資料庫移轉至雲端](sql-database-cloud-migrate.md), ，和保存現有的資料庫，以開放式格式。

> [AZURE.NOTE] Bacpac 不打算要用於備份和還原作業。 Azure SQL Database 會自動為每個使用者資料庫建立備份。 如需詳細資訊，請參閱 [業務持續性概觀](sql-database-business-continuity.md)。


BACPAC 會匯出至 Azure 儲存體 Blob 容器，以供您在作業順利完成之後下載。

若要完成本文，您需要下列項目：

- Azure 訂閱。 如果您需要 Azure 訂用帳戶，可以按一下 **免費試用版** 頂端的這個頁面上，然後再回來完成這篇文章。
- Azure SQL Database。 如果您沒有 SQL 資料庫，建立一個遵循本文中的步驟 ︰ [建立您的第一個 Azure SQL Database](sql-database-get-started.md)。
-  [Azure 儲存體帳戶](storage-create-storage-account.md) 與 blob 容器來儲存 BACPAC。 目前的儲存體帳戶必須使用傳統部署模型選擇這樣做的話 **傳統** 時建立儲存體帳戶。 


## 匯出您的資料庫

開啟欲匯出資料庫的 [SQL Database] 刀鋒視窗。

> [AZURE.IMPORTANT] 若要保證交易上一致的 BACPAC 檔案您應該先 [建立您的資料庫副本](sql-database-copy.md) ，然後匯出資料庫複本。 

1.  移至 [Azure 入口網站](https://portal.azure.com)。
2.  按一下 [ **全部瀏覽**。
3.  按一下 [ **SQL 資料庫**。
2.  按一下您想要匯出為 BACPAC 的資料庫。
3.  在 [SQL Database] 分頁中按一下 [ **匯出** 開啟 **匯出資料庫** 刀鋒視窗中 ︰

    ![匯出按鈕][1]

1.  按一下 [ **儲存體** ，然後選取要儲存 BACPAC 儲存體帳戶和 blob 容器 ︰

    ![匯出資料庫][2]

1.  輸入 **伺服器管理員登入** 和 **密碼** 包含您要匯出之資料庫的 Azure SQL server。
1.  按一下 [ **建立** 匯出資料庫。

按一下 **建立** 建立匯出資料庫要求，並將它提交至服務。 視資料庫大小而定，匯出作業可能需要一些時間才能完成。

## 監視匯出作業的進度

2.  按一下 [ **全部瀏覽**。
3.  按一下 [ **SQL 伺服器**。
2.  按一下包含您剛才匯出的原始 (來源) 資料庫的伺服器。
3.  在 [SQL server] 刀鋒視窗中按一下 [ **匯入/匯出記錄**:

    ![匯入匯出記錄][3]
    ![匯入匯出記錄][4]

## 確認 BACPAC 位於儲存體容器中

2.  按一下 [ **全部瀏覽**。
3.  按一下 [ **儲存體帳戶 （傳統）**。
2.  按一下您用來儲存 BACPAC 的儲存體帳戶。
3.  按一下 [ **容器** ，然後選取 [匯出到資料庫，如需詳細資訊 （您可以下載並儲存從這裡 BACPAC） 的容器。

    ![.bacpac 檔案詳細資料][5]  


## 後續步驟

- [匯入 Azure SQL Database](sql-database-import.md)



## 其他資源

- [業務續航力概觀](sql-database-business-continuity.md)
- [災害復原詳細資訊](sql-database-disaster-recovery-drills.md)
- [SQL Database 文件](https://azure.microsoft.com/documentation/services/sql-database/)


<!--Image references-->
[1]: ./media/sql-database-export/export.png
[2]: ./media/sql-database-export/export-blade.png
[3]: ./media/sql-database-export/export-history.png
[4]: ./media/sql-database-export/export-status.png
[5]: ./media/sql-database-export/bacpac-details.png


