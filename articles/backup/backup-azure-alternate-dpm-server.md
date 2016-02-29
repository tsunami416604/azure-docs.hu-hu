<properties
    pageTitle="從備份保存庫的另一部 DPM 伺服器復原資料 | Microsoft Azure"
    description="從登錄至 Azure 備份保存庫的任何 DPM 伺服器，復原該保存庫中保護的資料。"
    services="backup"
    documentationCenter=""
    authors="giridharreddy"
    manager="shreeshd"
    editor=""/>

<tags
    ms.service="backup"
    ms.workload="storage-backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/10/2015"
    ms.author="giridham;jimpark"/>

# 從備份保存庫的另一部 DPM 伺服器復原資料
您現在可以從登錄至 Azure 備份保存庫的任何 DPM 伺服器，復原該保存庫中保護的資料。 執行此工作的流程已完全整合至 DPM 管理主控台，且與其他復原工作流程類似。

若要復原另一部 DPM 伺服器必須與備份保存庫中的資料 [System Center Data Protection Manager UR7](https://support.microsoft.com/en-us/kb/3065246) 和 [最新版的 Azure 備份代理程式](http://aka.ms/azurebackup_agent)。

## 從另一部 DPM 伺服器復原資料
從另一部 DPM 伺服器復原資料：

1. 從 **復原** ] 索引標籤的 [DPM 管理主控台中，按一下 [ **新增外部 DPM'** (在左上角的螢幕)。

    ![新增外部 DPM](./media/backup-azure-alternate-dpm-server/add-external-dpm.png)

2. 下載新 **保存庫認證** 從保存庫相關聯 **DPM 伺服器** 正在復原資料，從備份保存庫註冊 DPM 伺服器的清單中的 DPM 伺服器並提供 **加密複雜密碼** 要復原其資料的 DPM 伺服器與相關聯。

    ![外部 DPM 認證](./media/backup-azure-alternate-dpm-server/external-dpm-credentials.png)

    >[AZURE.NOTE] 只有 DPM 伺服器相同的註冊保存庫相關聯可以復原其他人的資料。

    一旦成功新增外部 DPM 伺服器，您可以瀏覽外部 DPM 伺服器和本機 DPM 伺服器的資料 **復原** ] 索引標籤。

3. 瀏覽由外部 DPM 伺服器保護的實際執行伺服器可用清單，並選取適當的資料來源。

    ![瀏覽外部 DPM 伺服器](./media/backup-azure-alternate-dpm-server/browse-external-dpm.png)

4. 選取 **月份和年份** 從 **復原點** 下拉式清單中，選取所需 **復原日期** 的復原點建立時，並選取 **復原時間**。

    檔案和資料夾清單會出現在底部窗格中，方便您瀏覽並復原到任何位置。

    ![外部 DPM 伺服器復原點](./media/backup-azure-alternate-dpm-server/external-dpm-recoverypoint.png)

5. 以滑鼠右鍵按一下適當的項目，按一下 [ **復原**。

    ![外部 DPM 復原](./media/backup-azure-alternate-dpm-server/recover.png)

6. 檢閱 **復原選取項目**。 請確認要復原之備份複本的資料和時間，以及建立備份複本的來源。 如果選取項目不正確，請按一下 [ **取消** 向後巡覽至復原] 索引標籤，選取適當的復原點。 如果選取項目正確，請按一下 [ **下一步**。

    ![外部 DPM 復原摘要](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-summary.png)

7. 選取 **復原到替代位置**。 **瀏覽** 到正確的復原位置。

    ![外部 DPM 復原替代位置](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-alternate-location.png)

8. 選擇相關選項 **建立複本**, ，**略過**, ，或 **覆寫**。
    - **建立複本** 事件沒有名稱衝突，會建立檔案的複本。
    - **略過** 會略過復原檔案，萬一沒有名稱衝突。
    - **覆寫** 將會覆寫現有的複製發生名稱衝突時所指定的位置。

    選擇適當的選項， **還原安全性**。 您可以在建立復原點時，針對將資料復原至其中的目的地電腦，套用其安全性設定，或是套用適用於產品的安全性設定。

    識別是否 **通知** 順利完成復原後，將會傳送。

    ![外部 DPM 復原通知](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-notifications.png)

9.  **摘要** 畫面中會列出目前選擇的選項。 一旦您按一下 **「 復原 」**, ，資料將會還原成內部適當位置。

    ![外部 DPM 復原選項摘要](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-options-summary.png)

    >[AZURE.NOTE] 能夠監視復原工作 **監視** 的 DPM 伺服器] 索引標籤。

    ![監視復原](./media/backup-azure-alternate-dpm-server/monitoring-recovery.png)

10. 您可以按一下 **清除外部 DPM** 上 **復原** 要移除其檢視外部 DPM 伺服器的 DPM 伺服器] 索引標籤。

    ![清除外部 DPM](./media/backup-azure-alternate-dpm-server/clear-external-dpm.png)

## 疑難排解錯誤訊息
|編號 |  錯誤訊息 | 疑難排解步驟 |
| :-------------: |:-------------| :-----|
|1.|        保存庫認證所指定的保存庫中未登錄此伺服器。|  **原因:** 選取保存庫認證檔不屬於與 DPM 伺服器嘗試復原時相關聯的備份保存庫時，會出現這個錯誤。 <br> **解析度:** 下載保存庫認證檔從備份保存庫註冊 DPM 伺服器。|
|2.|        可復原資料無法使用，或選取的伺服器不是 DPM 伺服器。|   **原因:** 與 DPM 2012 R2 UR7 登錄到備份保存庫中，沒有其他 DPM 伺服器或 DPM 伺服器與 DPM 2012 R2 UR7 尚未上傳中繼資料，或選取的伺服器不是 DPM 伺服器 (也稱為 Windows Server 或 Windows 用戶端)。 <br> **解析度:** 如果有備份保存庫登錄其他 DPM 伺服器，請確定 SCDPM 2012 R2 UR7 和最新 Azure 備份代理程式安裝。 <br>如果有其他具有 DPM 2012 R2 UR7 的 DPM 伺服器已向備份保存庫登錄，請在安裝 UR7 後等候一天，再開始復原程序。 夜間工作會針對先前所有受保護的備份，將中繼資料上傳至雲端。 資料將可供復原。|
|3.|        此保存庫未登錄其他 DPM 伺服器。|   **原因:** 有沒有其他 DPM 伺服器與 DPM 2012 R2 UR7 或更高，註冊至保存庫會在嘗試復原。<br>**解決方式:** 備份保存庫登錄其他 DPM 伺服器時，請確定 SCDPM 2012 R2 UR7 和最新的 Azure 備份代理程式安裝。<br>如果與 DPM 2012 R2 UR7 備份保存庫登錄其他 DPM 伺服器，等候一天後安裝 UR7 開始復原程序。 夜間工作會針對先前所有受保護的備份，將中繼資料上傳至雲端。 資料將可供復原。|
|4.|        提供的加密複雜密碼不符合與下列伺服器關聯的複雜密碼: **<server name>**|  **原因:** 進行加密的資料從 DPM 伺服器的資料要復原的程序所使用的加密複雜密碼不符合所提供的加密複雜密碼。 代理程式無法解密資料。 復原失敗，因此。<br>**解決方式:** 請提供完全相同要復原其資料的 DPM 伺服器與相關聯的加密複雜密碼。|

## 常見問題集：
1. **為什麼不能在安裝 UR7 和最新的 Azure Backup Agent 之後，從另一部 DPM 伺服器新增外部 DPM 伺服器？**

    A) 到雲端 (使用保護的更新彙總套件早於更新彙總套件 7) 的資料來源與現有的 DPM 伺服器，您必須等待至少一天後安裝 UR7 和最新的 Azure 備份代理程式啟動 *新增外部 DPM 伺服器*。 這需要將 DPM 保護群組的中繼資料上傳至 Azure。 這項作業會在第一次夜間工作時進行。

2. **Azure Backup Agent 所需的最低版本為何？**

    答) 啟用這項功能的 Azure Backup Agent 最低版本為 2.0.8719.0。  瀏覽至 [控制台]，即可確定 azure 備份代理程式版本 **>** 所有控制台項目 **>** 程式和功能 **>** Microsoft Azure 復原服務代理程式。 如果版本小於 2.0.8719.0，下載 [最新版的 Azure 備份代理程式](https://go.microsoft.com/fwLink/?LinkID=288905) 並安裝。

    ![清除外部 DPM](./media/backup-azure-alternate-dpm-server/external-dpm-azurebackupagentversion.png)

## 後續步驟：
•   [Azure 備份常見問題集](backup-azure-backup-faq.md)

