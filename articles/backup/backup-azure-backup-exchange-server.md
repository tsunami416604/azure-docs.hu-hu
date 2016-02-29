<properties
    pageTitle="使用 System Center 2012 R2 DPM 將 Exchange Server 備份至 Azure 備份 | Microsoft Azure"
    description="了解如何使用 System Center 2012 R2 DPM 將 Exchange Server 備份至 Azure 備份"
    services="backup"
    documentationCenter=""
    authors="Jim-Parker"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="backup"
    ms.workload="storage-backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/08/2015"
    ms.author="jimpark;delhan"/>


# 使用 System Center 2012 R2 DPM 將 Exchange Server 備份至 Azure 備份
這篇文章說明如何將 System Center 2012 R2 Data Protection Manager (DPM) 伺服器設定為將 Microsoft Exchange server 備份至 Azure 備份。  

## 更新
若要在 Azure 備份中成功註冊 DPM 伺服器，您必須安裝 System Center 2012 R2 DPM 的最新更新彙總套件和 Azure 備份代理程式的最新版本。 取得從最新的更新彙總套件 [Microsoft 目錄](http://catalog.update.microsoft.com/v7/site/Search.aspx?q=System%20Center%202012%20R2%20Data%20protection%20manager)。

>[AZURE.NOTE] 這篇文章中的範例，安裝 Azure 備份代理程式的版本 2.0.8719.0，以及 System Center 2012 R2 DPM 上安裝更新彙總套件 6。

## 先決條件
在繼續之前，請確定所有 [必要條件](backup-azure-dpm-introduction.md#prerequisites) 已符合使用 Microsoft Azure 備份保護工作負載。 這些先決條件包含下列各項：

- 已在 Azure 網站上建立備份保存庫。
- 代理程式和保存庫認證已下載至 DPM 伺服器。
- 代理程式已安裝於 DPM 伺服器。
- 保存庫認證已用來註冊 DPM 伺服器。

## DPM 保護代理程式  
若要在 Exchange Server 上安裝 DPM 保護代理程式，請遵循下列步驟：

1. 請確定已正確設定防火牆。 請參閱 [設定代理程式的防火牆例外](https://technet.microsoft.com/library/Hh758204.aspx)。

2. 在 Exchange 伺服器上安裝代理程式，藉以 **管理 > 代理程式 > 安裝** DPM 系統管理員主控台。 請參閱 [安裝 DPM 保護代理程式](https://technet.microsoft.com/library/hh758186.aspx?f=255&MSPPError=-2147217396) 如需詳細步驟。

## 建立 Exchange Server 的保護群組

1. 在 [DPM 系統管理員主控台按一下 **保護**, ，然後按一下 [ **新增** 工具功能區，以開啟 **建立新保護群組** 精靈。

2. 在 **歡迎** 畫面精靈按一下 **下一步**。

3. 在 **選取保護群組類型** 畫面上，選取 **伺服器** 按一下 **下一步**。

4. 選取您想要保護，並按一下 [Exchange server 資料庫 **下一步**。

    >[AZURE.NOTE] 如果您要保護 Exchange 2013，請檢查 [Exchange 2013 先決條件](https://technet.microsoft.com/library/dn751029.aspx)。

    下例中選取了Exchange 2010 資料庫。

    ![選擇群組成員](./media/backup-azure-backup-exchange-server/select-group-members.png)

5. 選取資料保護方式。

    替保護群組命名，然後選取下列兩個選項：

    - 我想要使用磁碟進行短期保護。
    - 我想要線上保護。

6. 按一下 [ **下一步**。

7. 選取 **執行 Eseutil 以檢查資料完整性** 選項，如果您想要檢查 Exchange Server 資料庫的完整性。

    選取此選項之後，備份一致性檢查會執行 DPM 伺服器，以避免產生所執行的 I/O 流量上 **eseutil** Exchange 伺服器上的命令。

    >[AZURE.NOTE] 若要使用此選項，您必須將 Ese.dll 和 Eseutil.exe 檔案複製到 DPM 伺服器上的 C:\Program Files\Microsoft System Center 2012 R2\DPM\DPM\bin 目錄。 否則，就會觸發下列錯誤:  
    ![eseutil 錯誤](./media/backup-azure-backup-exchange-server/eseutil-error.png)

8. 按一下 [ **下一步**。

9. 選取的資料庫 **複製備份**, ，然後按一下 [ **下一步**。

    >[AZURE.NOTE] 如果您未選取至少一個 DAG 複製資料庫的 「 完整備份 」，並不會截斷記錄檔。

10. 設定的目標 **短期備份**, ，然後按一下 [ **下一步**。

11. 檢閱可用的磁碟空間，然後按一下 **下一步**。

12. 選取的 DPM 伺服器將建立的初始複寫，並按一下時間 **下一步**。

13. 選取的一致性檢查選項，然後再按 **下一步**。

14. 選擇您想要備份至 Azure，然後按一下 [資料庫 **下一步**。 例如：

    ![指定線上保護資料](./media/backup-azure-backup-exchange-server/specify-online-protection-data.png)

15. 定義排程 **Azure Backup**, ，然後按一下 [ **下一步**。 例如：

    ![指定線上備份排程](./media/backup-azure-backup-exchange-server/specify-online-backup-schedule.png)

    >[AZURE.NOTE] 請注意，線上復原點根據快速完整的復原點。 因此，您必須將線上復原點排程在針對快速完整復原點指定的時間之後。

16. 設定的保留原則 **Azure Backup**, ，然後按一下 [ **下一步**。

17. 選擇線上複寫選項並按一下 **下一步**。

    如果您有大型資料庫，則透過網路建立初始備份所需的時間很長。 若要避免這個問題，您可以建立離線備份。  

    ![指定線上保留期原則](./media/backup-azure-backup-exchange-server/specify-online-retention-policy.png)

18. 確認設定，然後按一下 **建立群組**。

19. 按一下 [ **關閉**。

## 復原 Exchange 資料庫

1. 若要復原 Exchange 資料庫，請按一下 [ **復原** DPM 系統管理員主控台。

2. 找出您要復原的 Exchange 資料庫。

3. 選取 [從線上復原點 *復原時間* 下拉式清單。

4. 按一下 [ **復原** 啟動 **復原精靈]**。

線上復原點有五種復原類型：

- **復原到原始 Exchange Server 位置:** 資料將會還原成原始的 Exchange 伺服器。
- **復原到 Exchange 伺服器上的另一個資料庫:** ，資料將會還原到另一部 Exchange 伺服器上的其他資料庫。
- **復原到復原資料庫:** 資料復原到 Exchange 復原資料庫 (RDB)。
- **複製到網路資料夾:** ，資料將會還原到網路資料夾。
- **複製到磁帶:** 的復原點如果您有任何磁帶媒體櫃或獨立磁帶機連接和 DPM 伺服器上設定，將會複製到可用的磁帶。

    ![選擇線上複寫](./media/backup-azure-backup-exchange-server/choose-online-replication.png)

## 後續步驟

- [Azure 備份常見問題集](backup-azure-backup-faq.md)

