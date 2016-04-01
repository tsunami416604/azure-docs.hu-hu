<properties
    pageTitle="開始使用 SQL Database 稽核 | Microsoft Azure"
    description="開始使用 SQL Database 稽核"
    services="sql-database"
    documentationCenter=""
    authors="jeffgoll"
    manager="jeffreyg"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="11/12/2015"
    ms.author="jeffreyg; ronitr"/>
 
# 開始使用 SQL Database 稽核
Azure SQL Database 稽核會追蹤資料庫事件，並將稽核事件寫入 Azure 儲存體帳戶中的稽核記錄。 基本、標準和進階服務層皆提供稽核功能。

稽核可以協助您維護法規相符性、 了解資料庫活動，以及深入了解不一致和異常可指出商務考量或疑似安全違規。

稽核工具啟用及推動遵循法規標準，但不保證符合法規。 如需有關支援標準法規的 Azure 程式詳細資訊，請參閱 [Azure 信任中心](http://azure.microsoft.com/support/trust-center/compliance)。

+ [Azure SQL Database 稽核基本概念]
+ [設定資料庫的稽核]
+ [分析稽核記錄和報告]

##<a id="subheading-1"></a>Azure SQL Database 稽核基本概念

下節描述使用 Azure 入口網站進行稽核的設定。 您也可以 [set up auditing for your database using the Classic Azure Classic Portal]。

SQL Database 稽核可讓您：

- **保留** 所選事件的稽核記錄。 您可以定義要稽核的資料庫動作類別。
- **報表** 資料庫活動。 您可以使用預先設定的報告和儀表板，以便快速開始使用活動和事件報告。
- **分析** 報表。 您可以尋找可疑事件、異常活動及趨勢。

> [AZURE.NOTE] 您現在可以接收主動式異常資料庫活動可能表示使用新的潛在安全性威脅的警示 **威脅偵測** 功能，現在處於預覽。 您可以從稽核組態刀鋒視窗內開啟和設定「威脅偵測」。 請參閱 [入門威脅偵測](sql-database-threat-detection-get-started.md) 如需詳細資訊。

您可以設定下列事件類別的稽核：

**一般 SQL** 和 **參數化 SQL** 為其收集的稽核記錄歸類為  

- **存取資料**
- **結構描述變更 (DDL)**
- **資料變更 (DML)**
- **帳戶、角色和權限 (DCL)**
- **預存程序**, ，**登入** ， **交易管理**。

為每個事件類別目錄的稽核 **成功** 和 **失敗** 作業分別設定。

如需已稽核活動和事件的進一步資訊，請參閱[稽核記錄格式參考 (doc 檔案下載)](http://go.microsoft.com/fwlink/?LinkId=506733)。

稽核記錄會儲存在 Azure 儲存體帳戶。 您可以定義稽核記錄保留期間，在此期間之後將會清除舊記錄。

可以針對特定資料庫定義稽核原則，或將稽核原則定義為預設伺服器原則。 預設伺服器稽核原則會套用至伺服器上的所有資料庫，其中該伺服器並沒有定義特定覆寫的資料庫稽核原則。

在設定稽核核取，如果您使用之前 [「 下層用戶端 」](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md)。


##<a id="subheading-2"></a>設定資料庫的稽核

1. 啟動 [Azure 入口網站](https://portal.azure.com) 在 https://portal.azure.com。 或者，您也可以啟動 [傳統的 Azure 傳統入口網站](https://manage.windowsazure.com/) 在 https://manage.windowsazure.com/。 請參閱下列詳細資訊。

2. 瀏覽至您想要稽核的 SQL Database / SQL Server 設定刀鋒視窗。 在 [設定] 刀鋒視窗中選取 **稽核與威脅的偵測**。

    ![導覽窗格][1]

3. 在稽核組態分頁中，開啟 **ON** 稽核。

4. 選取 **儲存體詳細資料** 開啟稽核記錄檔儲存體] 刀鋒視窗。 選取將儲存記錄的 Azure 儲存體帳戶以及保留期間。 **提示 ︰** 充分利用稽核報告範本使用相同的儲存體帳戶，所有稽核的資料庫。

    ![導覽窗格][2]

5. 按一下 [ **稽核事件** 自訂要稽核的事件。 在 **記錄事件** 刀鋒視窗中，按一下 [ **成功** 和 **失敗** 以記錄所有事件，或選擇個別的事件類別。


6. 您可以檢查 **從伺服器繼承稽核設定** 核取方塊來指定此資料庫會根據其伺服器的設定稽核。 選取此選項之後，您會看到一個可讓您從這個內容檢視或修改伺服器稽核設定的連結。

    ![導覽窗格][3]

7. 當您設定您的稽核設定之後時，您可以開啟 **ON** 威脅的偵測及設定電子郵件收到安全性警示。 請參閱 [威脅偵測開始](sql-database-threat-detection-get-started.md) 如需詳細資訊頁面。

8. 按一下 [ **儲存**。



##<a id="subheading-3"></a>分析稽核記錄和報告

稽核記錄檔中使用的存放區資料表的集合彙總 **SQLDBAuditLogs** 您在安裝期間所選擇的 Azure 儲存體帳戶中的前置詞。 您可以使用工具 (例如 [Azure 儲存體總管](http://azurestorageexplorer.codeplex.com/)) 來檢視記錄檔。

預先設定的報表範本為 [可下載的 Excel 試算表](http://go.microsoft.com/fwlink/?LinkId=403540) 可協助您快速分析記錄資料。 若要在稽核記錄上使用範本，您需要 Excel 2013 (或更新版本) 和 Power Query (您可從[此處](http://www.microsoft.com/download/details.aspx?id=39379)下載)。

您可以使用 Power Query 將您的稽核記錄直接從 Azure 儲存體帳戶匯入至 Excel 範本。 然後您可以瀏覽稽核記錄，並根據記錄資料建立儀表板和報告。


![導覽窗格][4]


##<a id="subheading-4"></a>使用 Azure 傳統入口網站設定資料庫的稽核

1. 啟動 [傳統的 Azure 傳統入口網站](https://manage.windowsazure.com/) 在 https://manage.windowsazure.com/。

2. 按一下 [SQL Database / SQL Server 要稽核，然後按一下 [ **稽核與安全性** ] 索引標籤。

3. 若要設定 [稽核 **啟用**。

    ![導覽窗格][5]

4. 編輯 **依事件記錄** 如有需要若要自訂要稽核的事件。

    ![導覽窗格][6]

5. 選取 **儲存體帳戶** 及設定 **保留**。

    ![導覽窗格][7]

6. 按一下 [ **儲存**。




##<a id="subheading-5">生產環境中的使用方式作法</a>
本節的說明與以上的畫面截圖有關。 可以使用 [Azure 入口網站](https://portal.azure.com)或[Azure 傳統入口網站](https://manage.windowsazure.com/)。


##<a id="subheading-6"></a>儲存體金鑰重新產生

在生產中，您可能會定期重新整理儲存體金鑰。 重新整理金鑰時，您需要重新儲存稽核原則。 程序如下：


1. 在稽核組態分頁交換器 **儲存體存取金鑰** 從 *主要* 至 *次要* 按一下 **儲存**。

    ![][8]

2. 移至儲存體組態分頁和 **重新產生**  *主要存取金鑰*。

3. 返回稽核組態分頁中，切換 **儲存體存取金鑰** 從 *次要* 至 *主要* 按一下 **儲存**。

4. 返回儲存體 UI 並 **重新產生**  *次要存取金鑰* （準備進行下一個金鑰重新整理週期）。
  
##<a id="subheading-7"></a>自動化
有數個 PowerShell Cmdlet 可用來設定 Azure SQL Database 中的稽核：

- [Get-AzureRMSqlDatabaseAuditingPolicy](https://msdn.microsoft.com/library/azure/mt603731.aspx)
- [Get-AzureRMSqlServerAuditingPolicy](https://msdn.microsoft.com/library/azure/mt619329.aspx)
- [Remove-AzureRMSqlDatabaseAuditing](https://msdn.microsoft.com/library/azure/mt603796.aspx)
- [Remove-AzureRMSqlServerAuditing](https://msdn.microsoft.com/library/azure/mt603574.aspx)
- [Set-AzureRMSqlDatabaseAuditingPolicy](https://msdn.microsoft.com/library/azure/mt603531.aspx)
- [Set-AzureRMSqlServerAuditingPolicy](https://msdn.microsoft.com/library/azure/mt603794.aspx)
- [Use-AzureRMSqlServerAuditingPolicy](https://msdn.microsoft.com/library/azure/mt619353.aspx)




<!--Anchors-->
[Azure SQL Database Auditing basics]: #subheading-1
[Set up auditing for your database]: #subheading-2
[Analyze audit logs and reports]: #subheading-3
[Set up auditing for your database using the Classic Azure Classic Portal]: #subheading-4
[Practices for usage in production]: #subheading-5
[Storage Key Regeneration]: #subheading-6
[Automation]: #subheading-7


<!--Image references-->
[1]: ./media/sql-database-auditing-get-started/1_auditing_get_started_settings.png
[2]: ./media/sql-database-auditing-get-started/2_auditing_get_started_storage_account.png
[3]: ./media/sql-database-auditing-get-started/3_auditing_get_started_inherit_from_server.png
[4]: ./media/sql-database-auditing-get-started/4_auditing_get_started_report_template.png
[5]: ./media/sql-database-auditing-get-started/5_auditing_get_started_classic_portal_enable.png
[6]: ./media/sql-database-auditing-get-started/6_auditing_get_started_classic_portal_events.png
[7]: ./media/sql-database-auditing-get-started/7_auditing_get_started_classic_portal_storage.png
[8]: ./media/sql-database-auditing-get-started/8_auditing_get_started_storage_key_rotation.png


 


