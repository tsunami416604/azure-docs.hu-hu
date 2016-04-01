<properties 
    pageTitle="開始使用 SQL 資料倉儲資料庫稽核 | Microsoft Azure" 
    description="開始使用 SQL 資料倉儲資料庫稽核" 
    services="sql-data-warehouse" 
    documentationCenter="" 
    authors="twounder" 
    manager="" 
    editor=""/>

<tags 
    ms.service="sql-data-warehouse" 
    ms.workload="data-management" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/06/2015" 
    ms.author="twounder"/>
 
# 開始使用 SQL 資料倉儲資料庫稽核 
<p> Azure SQL 資料倉儲稽核會追蹤資料庫事件，並將稽核事件寫入 Azure 儲存體帳戶中的稽核記錄。

稽核可以協助您維護法規相符性、 了解資料庫活動，以及深入了解不一致和異常可指出商務考量或疑似安全違規。 

稽核工具啟用及推動遵循法規標準，但不保證符合法規。 如需有關支援標準法規的 Azure 程式詳細資訊，請參閱 <a href="http://azure.microsoft.com/support/trust-center/compliance/" target="_blank">Azure 信任中心</a>。

+ [資料庫稽核基本概念] 
+ [設定資料庫的稽核]
+ [分析稽核記錄和報告]

##<a id="subheading-1"></a>Azure SQL 資料倉儲資料庫稽核基本概念


SQL 資料倉儲資料庫稽核可讓您：

- **保留** 所選事件的稽核記錄。 您可以定義要稽核的資料庫動作的類別。
- **報表** 資料庫活動。 您可以使用預先設定的報告和儀表板，以便快速開始使用活動和事件報告。
- **分析** 報表。 您可以尋找可疑事件、異常活動及趨勢。

您可以設定下列事件類別的稽核：

**一般 SQL** 和 **參數化 SQL** 為其收集的稽核記錄歸類為  

- **存取資料**
- **結構描述變更 (DDL)**
- **資料變更 (DML)**
- **帳戶、角色和權限 (DCL)**
- **預存程序**, ，**登入** ， **交易管理**。

為每個事件類別目錄的稽核 **成功** 和 **失敗** 作業分別設定。

如需已稽核活動和事件的進一步資訊，請參閱<a href="http://go.microsoft.com/fwlink/?LinkId=506733" target="_blank">稽核記錄格式參考 (doc 檔案下載)</a>。 

稽核記錄會儲存在 Azure 儲存體帳戶。 您可以定義稽核記錄保留期間。

可以針對特定資料庫定義稽核原則，或將稽核原則定義為預設伺服器原則。 預設伺服器稽核原則會套用至伺服器上的所有資料庫，其中該伺服器並沒有定義特定覆寫的資料庫稽核原則。

設定稽核核取，如果您使用的稽核前 [「 下層用戶端 」](sql-data-warehouse-auditing-downlevel-clients.md)。


##<a id="subheading-2"></a>設定資料庫的稽核

1. 啟動 <a href="https://portal.azure.com" target="_blank">Azure 入口網站</a>。

2. 瀏覽至您要稽核的 SQL 資料倉儲資料庫 / SQL Server 組態刀鋒視窗。 按一下 [ **設定** 按鈕在最上面，然後在 [設定] 刀鋒視窗，然後選取 **稽核**。

    ![][1]

3. 在稽核組態分頁中，首先取消選取 **繼承稽核伺服器上設定** 核取方塊。 這可讓您指定特定資料庫的設定。
    
    ![][2]

4. 接下來，啟用 [稽核]，即可 **ON** ] 按鈕。

    ![][3]

5. 在稽核組態分頁中，選取 **儲存體詳細資料** 開啟稽核記錄檔儲存體] 刀鋒視窗。 選取將儲存記錄的 Azure 儲存體帳戶以及保留期間。 **提示 ︰** 充分利用預先設定的報告範本使用相同的儲存體帳戶，所有稽核的資料庫。

    ![][4]

6. 按一下 [ **確定** ] 按鈕以儲存儲存體詳細資料設定。


7. 在 **依事件記錄**, ，按一下 [ **成功** 和 **失敗** 以記錄所有事件，或選擇個別的事件類別。


8. 如果您正在為資料庫設定稽核，您可能需要變更用戶端的連接字串，以確保系統會正確擷取資料稽核。 檢查 [修改連接字串中的伺服器 FDQN](sql-data-warehouse-auditing-downlevel-clients.md) 下層用戶端連線的主題。

9. 按一下 [ **確定**。


##<a id="subheading-3">分析稽核記錄和報告</a>

稽核記錄檔中使用的存放區資料表的集合彙總 **SQLDBAuditLogs** 您在安裝期間所選擇的 Azure 儲存體帳戶中的前置詞。 您可以使用工具 (例如 <a href="http://azurestorageexplorer.codeplex.com/" target="_blank">Azure 儲存體總管</a>) 來檢視記錄檔。

預先設定的儀表板報告範本會以<a href="http://go.microsoft.com/fwlink/?LinkId=403540" target="_blank">可下載的 Excel 試算表</a>形式提供，以協助您快速分析記錄資料。 若要在稽核記錄上使用範本，您需要 Excel 2013 (或更新版本) 和 Power Query (您可從<a href="http://www.microsoft.com/download/details.aspx?id=39379">此處</a>下載)。 

範本中包含虛構的範例資料，您可以設定 Power Query 直接從 Azure 儲存體帳戶匯入稽核記錄。 

如需有關使用報告範本的詳細指示，請閱讀<a href="http://go.microsoft.com/fwlink/?LinkId=506731">如何 (doc 下載)</a>。

![][5]


##<a id="subheading-4">生產環境中的使用方式作法</a>
本節的說明與以上的畫面截圖有關。 可以使用 <a href="https://portal.azure.com" target="_blank">Azure 入口網站</a>或<a href= "https://manage.windowsazure.com/" target="_bank">Azure 傳統入口網站</a>。
 

##<a id="subheading-5"></a>儲存體金鑰重新產生

在生產中，您可能會定期重新整理儲存體金鑰。 重新整理金鑰時，您需要重新儲存該原則。 程序如下：


1. 在稽核組態分頁中 （如以上所述設定稽核一節） 切換 **儲存體存取金鑰** 從 *主要* 至 *次要* 和 **儲存**。
![][4]
2. 移至儲存體組態分頁和 **重新產生**  *主要存取金鑰*。

3. 返回稽核組態分頁中，切換 **儲存體存取金鑰** 從 *次要* 至 *主要* 按 **儲存**。

4. 返回儲存體 UI 並 **重新產生**  *次要存取金鑰* （為下一個金鑰重新整理週期。
  
##<a id="subheading-6"></a>自動化
有數個 PowerShell 指令程式可用來設定 Azure SQL Database 中的稽核。 若要存取稽核 Cmdlet，您必須在 [Azure 資源管理員] 模式中執行 PowerShell。

> [AZURE.NOTE]   [Azure 資源管理員](https://msdn.microsoft.com/library/dn654592.aspx) 模組目前為預覽狀態。 它可能沒有如 Azure 模組所提供的相同管理功能。

在 [Azure 資源管理員] 模式中，您可以執行 `Get-Command *AzureSql*` 來列出可用的 Cmdlet。


<!--Anchors-->
[Database Auditing basics]: #subheading-1
[Set up auditing for your database]: #subheading-2
[Analyze audit logs and reports]: #subheading-3


<!--Image references-->
[1]: ./media/sql-data-warehouse-auditing-get-started/sql-data-warehouse-auditing.png
[2]: ./media/sql-data-warehouse-auditing-get-started/sql-data-warehouse-auditing-inherit.png
[3]: ./media/sql-data-warehouse-auditing-get-started/sql-data-warehouse-auditing-enable.png
[4]: ./media/sql-data-warehouse-auditing-get-started/sql-data-warehouse-auditing-storage-account.png
[5]: ./media/sql-data-warehouse-auditing-get-started/sql-data-warehouse-auditing-dashboard.png


<!--Link references-->


 


