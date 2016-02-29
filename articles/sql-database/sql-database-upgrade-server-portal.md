<properties 
    pageTitle="使用 Azure 入口網站升級至 Azure SQL Database V12 | Microsoft Azure" 
    description="說明如何使用 Azure 入口網站升級至 Azure SQL Database V12，包括如何升級 Web 和商務資料庫，以及如何將 V11 伺服器的資料庫直接移轉至彈性資料庫集區來升級 V11 伺服器。" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jeffreyg"
    editor=""/>

<tags 
    ms.service="sql-database" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="data-management" 
    ms.date="12/01/2015" 
    ms.author="sstein"/>


# 使用 Azure 入口網站升級至 Azure SQL Database V12


> [AZURE.SELECTOR]
- [Azure 入口網站](sql-database-upgrade-server-portal.md)
- [PowerShell](sql-database-upgrade-server-powershell.md)


SQL Database V12 是最新的版本，因此建議升級至 SQL Database V12。
SQL Database V12 新增了許多 [優於舊版](sql-database-v12-whats-new.md) 包括:

- 提升與 SQL Server 的相容性。
- 提供改進的高階效能和新的效能等級。
- [彈性資料庫集區](sql-database-elastic-pool.md)。

本文提供將現有的 SQL Database V11 伺服器和資料庫升級至 SQL Database V12 的說明。 

在升級至 V12 的過程中，您將會把所有 Web 和商務資料庫都升級至新的服務層級，因此本文也包含了升級 Web 和商務資料庫的說明。 

此外，移轉至 [彈性資料庫集區](sql-database-elastic-pool.md) 更符合成本效益比單一資料庫的升級 (定價層) 的個別效能層級。 集區也可以簡化資料庫管理，因為您只需要管理集區的效能設定，而不需分開管理個別資料庫的效能等級。 如果您的資料庫位於多部伺服器上，請考慮將它們移到相同的伺服器，並利用將它們放入集區所帶來的優點。 您可以輕鬆地 [自動移轉資料庫從 V11 伺服器直接將彈性資料庫集區使用 PowerShell](sql-database-upgrade-server.md)。 您也可以使用入口網站將 V11 資料庫移轉至集區，但在入口網站中，您必須已經備妥 V12 伺服器來建立集區。 稍後建立集區伺服器升級之後，如果您有這篇文章會提供指示 [受益於集區的資料庫](sql-database-elastic-pool-guidance.md)。

請注意，您的資料庫會維持在線上，並且在整個升級作業中都會繼續保持運作。 在實際轉換到新的效能等級時，資料庫連線可能會暫時中斷一段非常短的時間，通常約 90 秒，但最長可達 5 分鐘。 如果您的應用程式具有 [暫時性錯誤處理連線終止](sql-database-connect-central-recommendations.md) 就足以保護在升級結束時中斷連線。 

升級至 SQL Database V12 後即無法復原。 在升級之後，即無法將伺服器還原至 V11。 

在升級至 V12 之後, [服務層建議](sql-database-service-tier-advisor.md) 和 [彈性集區建議](sql-database-elastic-pool-portal.md#step-2-choose-a-pricing-tier) 立即之前將無法使用的服務有時間來評估您的工作負載，新的伺服器上。 V11 伺服器建議記錄不適用於 V12 伺服器，因此不會保留。  


## 準備升級

- **將所有的 Web 和 Business 資料庫升級**: 請參閱 [將所有的 Web 和 Business 資料庫升級](sql-database-upgrade-server-portal.md#upgrade-all-web-and-business-databases) 下面章節，或使用 [來升級資料庫和伺服器的 PowerShell](sql-database-upgrade-server-powershell.md)。
- **檢閱並暫停異地複寫**: 如果您的 Azure SQL database 設定異地複寫，您應該記錄其目前的設定和 [停止地理複寫](sql-database-geo-replication-portal.md#remove-secondary-database)。 在升級完成之後，請重新設定資料庫的異地複寫。
- **如果您在 Azure VM 上的用戶端開啟這些連接埠**: 如果您的用戶端執行 Azure 的虛擬機器 (VM) 上時，用戶端程式會連接到 SQL Database V12，您必須開啟連接埠範圍 11000-11999 和 VM 上的 14000 14999。 如需詳細資訊，請參閱 [連接埠的 SQL Database V12](sql-database-develop-direct-route-ports-adonet-v12.md)。



## 開始升級

1. 在 [Azure 入口網站](http://portal.azure.com/) 瀏覽至您想要升級選取的伺服器 **全部瀏覽** > **SQL server**, ，然後選取您要的伺服器。
2. 選取 **的最新的 SQL database update**, ，然後選取 **升級此伺服器**。

      ![升級伺服器][] 1

## 升級所有 Web 和商務資料庫

如果您的伺服器內有任何 Web 或 Business 資料庫，就必須將這些資料庫升級。 在升級至 SQL Database V12 的過程中，您將會把所有 Web 和商務資料庫更新至新的服務層級。    

為了協助您完成升級，SQL Database 服務建議為每個資料庫選擇適當的服務層和效能層級 (定價層)。 服務會透過分析您資料庫過去的使用情況，建議最適合用於執行您現有資料庫工作負載的層。 
    
3. 在 **升級此伺服器** 分頁中，選取每個資料庫，並選取 [建議的定價層升級。 您隨時都可以瀏覽各種可用的定價層，並選取最符合您環境的選項。


     ![databases][2]


7. 按一下建議的服務層後您會看到 **選擇價格層** 刀鋒視窗中，您可以在其中選取層級，然後按一下 **選取** 按鈕變更為該層。 為每個 Web 或商務資料庫選取新的層。

    請特別注意，SQL Database 不會鎖定至任何特定的服務層級或效能等級，因此當您的資料庫需求變更時，您可以在可用的服務層級和效能等級之間輕鬆進行變更。 事實上，基本、標準和高階 SQL Database 是以小時計費，您可以在 24 小時內向上或向下調整每個資料庫 4 次。

    ![建議][] 6


伺服器的所有資料庫都符合資格之後，您就可以準備開始升級

## 確認升級

3. 當伺服器上的所有資料庫都適合您需要的升級 **類型的伺服器名稱** 以確認您要執行升級，然後按一下 [ **確定**。 

    ![確認升級][] 3


4. 升級隨即開始，過程中也會顯示進度通知。 升級程序即會啟動。 視特定資料庫的詳細資料而定，升級至 V12 可能需要一些時間。 在這段期間，伺服器上的所有資料庫將維持線上狀態，但伺服器和資料庫的管理動作將受到限制。

    ![升級進行中][] 4

    在實際轉換到新的效能層級時，資料庫連線可能會暫時中斷一小段時間 (通常以秒計算)。 若應用程式對於連線終止出現暫時性的錯誤處理 (重試邏輯)，在升級結束時連線將不會中斷。 

5. 升級作業完成後 **最新更新** ] 刀鋒視窗會顯示 **啟用**。 

    ![啟用 V12][] 5  

## 將資料庫移至彈性資料庫集區

在 [Azure 入口網站](https://portal.azure.com/) 瀏覽至 V12 伺服器，然後按一下 [ **新增集區**。

-或-

如果您看到訊息指出 **按一下這裡以檢視此伺服器的建議的彈性資料庫集區**, ，按一下以輕鬆地建立最適合您的伺服器資料庫的集區。 如需詳細資訊，請參閱 [建議的彈性資料庫集區](sql-database-elastic-pool-portal.md#recommended-elastic-database-pools)。

![新增到伺服器集區][] 7
   
請遵照 [建立彈性資料庫集區](sql-database-elastic-pool.md) 文件，以完成建立您的集區。

## 在升級至 SQL Database V12 後監視資料庫


升級之後，建議您主動監視資料庫，以確保應用程式達到所需的執行效能，並視需要將使用情況調整到最佳狀態。 

除了監視個別資料庫中，您可以監視彈性資料庫集區 [使用入口網站](sql-database-elastic-pool-portal.md#monitor-and-manage-an-elastic-database-pool) 或 [PowerShell](sql-database-elastic-pool-powershell.md#monitoring-elastic-databases-and-elastic-database-pools) 


**資源耗用量資料:** 若是基本、 標準和高階資料庫資源耗用資料是透過 [sys.dm_ db_ resource_stats](http://msdn.microsoft.com/library/azure/dn800981.aspx) DMV 使用者資料庫中的。 此 DMV 以 15 秒的間隔提供幾乎即時的前一小時作業資源耗用量資訊。 某一間隔的 DTU 百分比耗用量會以 CPU、IO 及記錄檔方面的最大百分比耗用量來計算。 下列是計算前一小時之平均 DTU 百分比耗用量的查詢：

    SELECT end_time
         , (SELECT Max(v)
             FROM (VALUES (avg_cpu_percent)
                         , (avg_data_io_percent)
                         , (avg_log_write_percent)
           ) AS value(v)) AS [avg_DTU_percent]
    FROM sys.dm_db_resource_stats
    ORDER BY end_time DESC;

其他監視資訊：

- [單一資料庫的 azure SQL Database 效能指南](http://msdn.microsoft.com/library/azure/dn369873.aspx)。
- [彈性資料庫集區的價格和效能考量](sql-database=elastic-pool-guidance.md)。
- [使用動態管理檢視監視 Azure SQL Database](sql-database-monitoring-with-dmvs.md)




**警示:** 設定 「 警示 」 Azure 入口網站中已升級之資料庫的 DTU 耗用量接近特定的高層級時通知您。 您可以在 Azure 入口網站中為各種效能計量 (例如 DTU、CPU、IO 及記錄檔) 設定資料庫警示。 瀏覽至您的資料庫，然後選取 **警示規則** 中 **設定** 刀鋒視窗。

例如，您可以設定若過去 5 分鐘的平均 DTU 百分比值超出 75% 則發出「 DTU 百分比 」電子郵件警示。 請參閱 [接收警示通知](insights-receive-alert-notifications.md) 若要深入了解如何設定警示通知。





## 後續步驟

- [如需彈性資料庫集區建議檢查](sql-database-elastic-pool-portal.md#recommended-elastic-database-pools)。
- [建立彈性資料庫集區](sql-database-elastic-pool-portal.md) 並將部分或所有的資料庫加入至集區。
- [變更您的資料庫服務層和效能層級](sql-database-scale-up.md)。



## 相關連結

- [SQL Database V12 新功能](sql-database-v12-whats-new.md)
- [規劃和準備升級至 SQL Database V12](sql-database-v12-plan-prepare-upgrade.md)


<!--Image references-->
[1]: ./media/sql-database-upgrade-server-portal/latest-sql-database-update.png
[2]: ./media/sql-database-upgrade-server-portal/upgrade-server2.png
[3]: ./media/sql-database-upgrade-server-portal/upgrade-server3.png
[4]: ./media/sql-database-upgrade-server-portal/online-during-upgrade.png
[5]: ./media/sql-database-upgrade-server-portal/enabled.png
[6]: ./media/sql-database-upgrade-server-portal/recommendations.png
[7]: ./media/sql-database-upgrade-server-portal/new-elastic-pool.png





