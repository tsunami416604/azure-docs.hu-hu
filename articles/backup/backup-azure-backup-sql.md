<properties
    pageTitle="使用 DPM 進行 SQL Server 工作負載的 Azure 備份 | Microsoft Azure"
    description="使用 Azure 備份服務來備份 SQL Server 資料庫的簡介"
    services="backup"
    documentationCenter=""
    authors="giridharreddy"
    manager="shreeshd"
    editor=""/>

<tags
    ms.service="backup 」
    ms.workload= 「 儲存體的備份復原的 」
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="11/26/2015 」
    ms.author="aashishr"，"giridham";「 jimpark 」 / >


# 使用 DPM 進行 SQL Server 工作負載的 Azure 備份

本文將引導您完成使用 Azure 備份來備份 SQL Server 資料庫的設定步驟。

若要將 SQL Server 資料庫備份至 Azure，您需要 Azure 帳戶。 如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。 如需詳細資料，請參閱 [Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。

將 SQL Server 資料庫備份至 Azure 及從 Azure 復原的管理作業包含三個步驟：

1. 建立備份原則以在 Azure 保護 SQL Server 資料庫。
2. 在 Azure 建立隨選備份複本。
3. 從 Azure 復原資料庫。

## 開始之前
在開始之前，請確定所有 [必要條件](../backup-azure-dpm-introduction/#prerequisites) 已符合使用 Microsoft Azure 備份保護工作負載。 必要條件涵蓋下列類似工作：建立備份保存庫、下載保存庫認證、安裝 Azure Backup Agent，以及向保存庫註冊伺服器。

## 建立備份原則以在 Azure 保護 SQL Server 資料庫

1. 在 DPM 伺服器中，設定新的 SQL Server 資料庫的備份原則來建立新 **保護群組**。 按一下 [ **保護** 工作區。

2. 按一下 [ **新增** 來建立新的保護群組。

    ![建立保護群組](./media/backup-azure-backup-sql/protection-group.png)

3. DPM 會顯示 [開始] 畫面的指引，建立 **保護群組**。 按一下 [ **下一步**。

4. 選取 **伺服器**。

    ![選取保護群組類型 - 伺服器](./media/backup-azure-backup-sql/pg-servers.png)

5. 展開要備份之資料庫所在的 SQL Server 電腦。 DPM 顯示可從該伺服器備份的各種資料來源。 展開 **所有 SQL 共用** ，然後選取要備份資料庫 (在此情況下我們選取 ReportServer$ MSDPM2012 和 ReportServer$ MSDPM2012TempDB)。 按一下 [ **下一步**。

    您會發現類似以下的畫面。

    ![選取 SQL DB](./media/backup-azure-backup-sql/pg-databases.png)

6. 提供您即將建立之保護群組的名稱。 請確定您選取 「**我想要線上保護**」 選項。

    ![資料保護方式 - 短期磁碟和線上 Azure](./media/backup-azure-backup-sql/pg-name.png)

7. 在 **指定短期目標** 畫面上，包括必要的輸入，來建立磁碟的備份點。

    我們這裡看到 **保留範圍** 設為 *5 天*, ，**同步處理頻率** 設定一次為每個 *15 分鐘* 即執行備份的頻率。 **快速完整備份** 設為 *下午 8:00*。

    ![短期目標](./media/backup-azure-backup-sql/pg-shortterm.png)

    >[AZURE.NOTE] 在下午 8:00 (根據螢幕上輸入) 的備份點會建立每一天前一天的下午 8:00 備份點從傳輸已修改的資料。 此程序稱為 **快速完整備份**。 雖然交易記錄檔每隔 15 分鐘同步處理一次，但如果有需要在下午 9:00 復原資料庫，則此點可藉由重新執行最後一個快速完整備份點 (在本例中為下午 8:00) 的記錄檔來建立。

8. 按一下 [ **下一步**

    DPM 會顯示可用的整體儲存空間和潛在的磁碟空間使用量。

    ![磁碟配置](./media/backup-azure-backup-sql/pg-storage.png)

    DPM 會為每一個資料來源 (SQL Server 資料庫) 建立一個磁碟區，以供建立初始備份複本。 透過這個方法，邏輯磁碟管理員 (LDM) 會限制 DPM 最多只能保護 300 個資料來源 (SQL Server 資料庫)。 為了避免這種情形，DPM 已實作另一種方法，其對多個資料來源使用單一磁碟區。 啟用此選項使用 **DPM 存放集區中的資料共置**。 DPM 可以使用這種方法來保護多達 2000 個 SQL Database。

    DPM 可以備份磁碟區增加實際執行資料成長時，如果負責 **自動擴大磁碟區** 已選取。 如果您取消選取 **自動擴大磁碟區**, ，它會限制用來備份保護群組中的資料來源的備份存放區。

9. 系統管理員可選擇手動 (關閉網路) 傳輸此初始備份，以避免頻寬壅塞或透過網路。 他們也可以設定可發生初始傳輸的時間。 按一下 [ **下一步**。

    ![初始複寫方法](./media/backup-azure-backup-sql/pg-manual.png)

    初始備份複本要求將整個資料來源 (SQL Server 資料庫) 從生產伺服器 (SQL Server 電腦) 傳輸到 DPM 伺服器。 此資料有時可能非常大，透過網路傳輸此資料可能會超出頻寬。 因此，系統管理員可以選擇是否要傳送此初始備份上的 **手動** 避免頻寬繼續，或他們想要這種情況， **自動** 在網路上。 此外，當系統管理員選擇 **網路** 他們可以選擇是否要建立初始備份複本 **現在** 或 **稍後**, ，在指定的時間。

    一旦完成初始備份，其餘的備份會是初始備份複本的增量備份，一般而言都非常小並且會在網路上傳輸。

10. 選擇要執行，然後按一下將一致性檢查 **下一步**。

    ![一致性檢查](./media/backup-azure-backup-sql/pg-consistent.png)

    DPM 可以執行一致性檢查來檢查備份點的完整性。 它會計算生產伺服器 (在此案例中為 SQL Server 電腦) 上備份檔案的總和檢查碼以及 DPM 上該檔案的已備份資料。 在衝突的情況下，假設 DPM 上的備份檔案已損毀。 DPM 藉由傳送對應至總和檢查碼不符的區塊，改正已備份的資料。。 由於一致性檢查是效能密集作業，所以系統管理員可選擇排程或自動執行此作業。

11. 若要指定資料來源的線上保護，選取要保護至 Azure，然後按一下資料庫 **下一步**。

    ![選取資料來源](./media/backup-azure-backup-sql/pg-sqldatabases.png)

12. 系統管理員可以選擇備份排程以及符合其組織原則的保留原則。

    ![排程和保留](./media/backup-azure-backup-sql/pg-schedule.png)

    在此範例中，每天下午 12:00 和下午 8:00 會進行一次備份 (螢幕的下半部)

    >[AZURE.NOTE] 它是很好的做法，以便快速復原短期磁碟上有幾個復原點。 這稱之為「操作復原」。 Azure 可做為具有較高 SLA 和保證可用性的良好離站位置。

    **最佳作法**: 請確定排程 Azure 備份時，會使用 DPM 的本機磁碟備份完成後。 這可讓最新的磁碟備份複製到 Azure。

13. 選擇保留原則排程。 保留原則的運作方式的詳細資料會提供 [使用 Azure 備份來取代您的磁帶基礎結構文章](backup-azure-backup-cloud-as-tape.md)。

    ![保留原則](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    在此範例中：

    - 每天下午 12:00 和下午 8:00 會進行一次備份 (螢幕的下半部) 並保留 180 天。
    - 星期六下午 12:00 備份 保留 104 週
    - 最後一個星期六下午 12:00 備份 會保留 60 個月
    - 最後一個星期六的下午 12:00 年 3 月備份 會保留 10 年

14. 按一下 [ **下一步** ，然後選取適當的選項，傳送至 Azure 的初始備份複本。 您可以選擇 **自動透過網路** 或 **離線備份**。

    - **自動透過網路** 會傳送至 Azure 的備份資料根據選擇的備份排程。
    - 如何 **離線備份** works 會說明在 [Azure 備份中的離線備份工作流程](backup-azure-backup-import-export.md)。

    選擇初始備份複本傳送至 Azure，然後按一下相關的傳輸機制 **下一步**。

15. 一旦您檢閱原則詳細資料，在 **摘要** 畫面上，按一下 [ **建立群組** 按鈕以完成工作流程。 您可以按一下 **關閉** 按鈕，並監視在監視工作區中的工作進度。

    ![保護群組的建立正在進行中](./media/backup-azure-backup-sql/pg-summary.png)

## SQL Server 資料庫的隨選備份
雖然先前的步驟建立了備份原則，但只有在第一次備份發生時，才會建立「復原點」。 不需等待排程器開始作用，下列步驟將會手動觸發復原點的建立。

1. 等到保護群組狀態顯示 **確定** 資料庫前建立的復原點。

    ![保護群組成員](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)

2. 以滑鼠右鍵按一下資料庫，然後選取 **建立復原點**。

    ![建立線上復原點](./media/backup-azure-backup-sql/sqlbackup-createrp.png)

3. 選擇 **線上保護** 下拉式清單中，然後按一下 **確定**。 這會在 Azure 中開始建立復原點。

    ![建立復原點](./media/backup-azure-backup-sql/sqlbackup-azure.png)

4. 您可以檢視中的工作進度 **監視** 工作區，您可以在其中找到正在進行中作業一屬像下圖。

    ![監視主控台](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## 從 Azure 復原 SQL Server 資料庫
以下是從 Azure 復原受保護的實體 (SQL Server 資料庫) 所需的步驟。

1. 開啟 [DPM 伺服器管理主控台]。 瀏覽至 **復原** 工作區中，您可以在此處查看的伺服器備份的 DPM。 瀏覽所需的資料庫 (在此例中為 ReportServer$MSDPM2012)。 選取 **從復原** 時間以 **線上**。

    ![選取復原點](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)

2. 以滑鼠右鍵按一下資料庫名稱，按一下 [ **復原**。

    ![從 Azure 復原](./media/backup-azure-backup-sql/sqlbackup-recover.png)

3. DPM 會顯示復原點的詳細資料。 按一下 [ **下一步**。 選取復原類型 **復原到原始 SQL Server 執行個體**。 這樣會覆寫資料庫。 按一下 [ **下一步**。

    ![復原到原始位置](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    在本例中，DPM 可讓資料庫復原至另一個 SQL server 執行個體或獨立的網路資料夾。

4. 在 **指定復原選項** ] 畫面中，您可以選取像是網路頻寬使用節流的復原選項，復原所使用頻寬節流處理。 按一下 [ **下一步**。

5. 在 **摘要** ] 畫面中，您會看到提供到目前為止的所有復原組態。 按一下 [ **復原**。

    [復原狀態] 會顯示正在復原的資料庫。 您可以按一下 **關閉** 以關閉精靈並檢視中的進度 **監視** 工作區。

    ![起始復原程序](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    一旦完成復原，還原的資料庫複本會是應用程式保持一致複本。

### 後續步驟：

•   [Azure 備份常見問題集](backup-azure-backup-faq.md)

