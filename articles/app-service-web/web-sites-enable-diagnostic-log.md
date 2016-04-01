<properties
    pageTitle="在 Azure App Service 中針對 Web 應用程式啟用診斷記錄功能。"
    description="了解如何啟用診斷記錄，並在您的應用程式中加入診斷工具，以及如何存取 Azure 所記錄的資訊。"
    services="app-service"
    documentationCenter=".net"
    authors="cephalin"
    manager="wpickett"
    editor="jimbe"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/20/2015"
    ms.author="cephalin"/>

# 在 Azure App Service 中針對 Web 應用程式啟用診斷記錄功能。

## 概觀

Azure 提供內建的診斷功能，可協助您進行偵錯 [App Service web 應用程式](http://go.microsoft.com/fwlink/?LinkId=529714)。 本文將說明如何啟用診斷記錄，並在您的應用程式中加入檢測設備，以及如何存取 Azure 所記錄的資訊。

本文使用 [Azure 入口網站](https://portal.azure.com), ，Azure PowerShell 及 Azure 命令列介面 (Azure CLI) 來處理診斷記錄。 如需處理使用 Visual Studio 的診斷記錄資訊，請參閱 [在 Visual Studio 中疑難排解 Azure](web-sites-dotnet-troubleshoot-visual-studio.md)。

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="whatisdiag"></a>Web 伺服器診斷和應用程式診斷

App Service Web 應用程式會針對來自 Web 伺服器和 Web 應用程式的記錄資訊提供診斷功能。 這些邏輯上可區分為 **web 伺服器診斷** 和 **應用程式診斷**。

### Web 伺服器診斷

您可以啟用或停用下列各種記錄：

- **詳細的錯誤記錄** -對於表示失敗 （狀態碼 400 或以上） 的 HTTP 狀態碼的詳細錯誤資訊。 這當中包含的資訊可協助您判斷為何伺服器傳回錯誤碼。
- **失敗要求追蹤** -關於失敗的要求，包括用來處理要求和每個元件所花費的時間的 IIS 元件追蹤的詳細資訊。 如果您嘗試提升網站效能或是想要從傳回的特定 HTTP 錯誤中找到發生原因，這個方法非常實用。
- **Web 伺服器記錄** -使用的 HTTP 交易相關資訊 [W3C 擴充的記錄檔格式](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx)。 當您需要判斷整體網站指標 (例如，處理的要求數量，或者有多少要求來自特定的 IP 位址) 時，這非常實用。

### 應用程式診斷

應用程式診斷功能可讓您擷取 Web 應用程式所產生的資訊。 ASP.NET 應用程式可以使用 [System.Diagnostics.Trace](http://msdn.microsoft.com/library/36hhw2t6.aspx) 類別將資訊記錄到應用程式診斷記錄。 例如：

    System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");

您可以在執行階段擷取這些記錄檔，以協助疑難排解。 如需詳細資訊，請參閱 [Visual Studio 中的疑難排解 Azure web 應用程式](web-sites-dotnet-troubleshoot-visual-studio.md)。

當您將內容發行至 Web 應用程式時，App Service Web 應用程式也會記錄部署資訊。 此動作會自動發生，因此無須任何組態設定即會記錄部署動作。 部署記錄功能可讓您判斷部署失敗的原因。 例如，如果您是使用自訂的部署指令碼，則您可以使用部署記錄功能來判斷指令碼失敗的原因。

## <a name="enablediag"></a>如何啟用診斷

若要啟用診斷功能 [Azure 入口網站](https://portal.azure.com), ，請移至您的 web 應用程式的刀鋒視窗中，按一下 **設定 > 診斷記錄檔**。

<!-- todo:cleanup dogfood addresses in screenshot -->
![記錄部分](./media/web-sites-enable-diagnostic-log/logspart.png)

當您啟用 **應用程式診斷** 也選擇 **層級**。 此設定可讓您篩選擷取到的資訊 **參考**, ，**警告** 或 **錯誤** 資訊。 這個設定設為 **verbose** 記錄應用程式所產生的所有資訊。

> [AZURE.NOTE] 與變更 web.config 檔案，啟用應用程式診斷或是變更診斷記錄層級不會回收應用程式中執行的應用程式定義域。

在 [傳統入口網站](https://manage.windowsazure.com) Web 應用程式 **設定** 索引標籤上，您可以選取 **儲存體** 或 **檔案系統** 的 **web 伺服器記錄**。 選取 **儲存體** 可讓您選取儲存體帳戶，然後按一下 [記錄檔寫入的 blob 容器。 其他所有記錄 **網站診斷** 會寫入至檔案系統。

 [傳統入口網站](https://manage.windowsazure.com) Web 應用程式 **設定** 索引標籤也有其他應用程式診斷的設定 ︰

* **檔案系統** -將 web 應用程式檔案系統應用程式診斷資訊。 這些檔案可透過 FTP 存取，或是使用 Azure PowerShell 或 Azure 命令列介面 (Azure CLI) 下載為 Zip 封存。
* **資料表儲存體** -將應用程式診斷資訊儲存在指定的 Azure 儲存體帳戶和資料表名稱。
* **Blob 儲存體** -將應用程式診斷資訊儲存至指定的 Azure 儲存體帳戶和 blob 容器中。
* **保留期限** -根據預設，記錄檔不會自動刪除從 **blob 儲存體**。 選取 **設定保留** 輸入保留記錄檔，如果您想要自動刪除記錄的天數。

>[AZURE.NOTE] 如果您 [重新產生儲存體帳戶的存取金鑰](storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys), ，您必須重設使用更新的索引鍵的個別記錄設定。 作法：
>
> 1. 在 **設定** 索引標籤上，將個別的記錄功能設定為 **關閉**。 儲存您的設定。
> 2. 重新啟用記錄至儲存體帳戶 Blob 或資料表。 儲存您的設定。

包括檔案系統、資料表儲存體或是 Blob 儲存體的任意組合都可以同時啟用，並個別具有記錄層級組態。 例如，您也許想要將各種錯誤與警告資訊記錄到 Blob 儲存體做為長期的記錄解決方案，同時啟用詳細資訊層級的檔案系統記錄功能。

三個儲存位置全都提供相同的基本資訊供您記錄事件，而 **資料表儲存體** 和 **blob 儲存體** 記錄其他資訊，例如執行個體識別碼、 執行緒識別碼以及更細緻的時間戳記 （刻度格式） 比 **檔案系統**。

> [AZURE.NOTE] 資訊儲存在 **資料表儲存體** 或 **blob 儲存體** 只能使用儲存體用戶端或應用程式可以直接使用這些儲存系統。 例如，Visual Studio 2013 內含的 [儲存體總管] 可用來探索資料表或 Blob 儲存體，而 HDInsight 則可存取儲存在 Blob 儲存體內的資料。 您也可以撰寫應用程式所使用的其中一個存取 Azure 儲存體 [Azure Sdk](/downloads/#)。

> [AZURE.NOTE] 您也可以從 Azure PowerShell 啟用診斷使用 **Set-azurewebsite** 指令程式。 如果您尚未安裝 Azure PowerShell，或未設定為使用您的 Azure 訂閱，請參閱 [如何使用 Azure PowerShell](/develop/nodejs/how-to-guides/powershell-cmdlets/)。

##<a name="download"></a> 作法：下載記錄

儲存在 Web 應用程式檔案系統中的診斷資訊，可透過 FTP 直接存取。 或是使用 Azure PowerShell 或 Azure 命令列介面下載為 Zip 封存。

儲存這些記錄的目錄結構如下所示：

* **應用程式記錄檔** -/logfiles/application/。 此資料夾內含有一或多個文字檔案，這些檔案涵蓋應用程式記錄所產生的資訊。

* **失敗要求追蹤** -/logfiles/w3svc W3SVC # # # /。 此資料夾內含有一個 XSL 檔案和一或多個 XML 檔案。 請務必將 XSL 檔案下載到 XML 檔案所在的目錄，因為在 Internet Explorer 中檢視 XML 檔案時，XSL 檔案能提供內容格式化和篩選等功能。

* **詳細的錯誤記錄** -/logfiles/detailederrors/。 此資料夾包含一或多個 .htm 檔案，內含已經發生的任何 HTTP 錯誤之詳細資訊。

* **Web 伺服器記錄** -/LogFiles/http/RawLogs。 此資料夾包含一或多個文字檔案格式使用 [W3C 擴充的記錄檔格式](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx)。

* **部署記錄** -/logfiles/git。 此資料夾包含由內部部署處理序所產生，且可供 Azure Web 應用程式運用的記錄，以及適用於 Git 部署的記錄。

### FTP

若要存取使用 FTP 的診斷資訊，請造訪 **儀表板** web 應用程式中的 [傳統入口網站](https://manage.windowsazure.com)。 在 **快速瀏覽** 區段中，使用 **FTP 診斷記錄** 存取使用 FTP 記錄檔的連結。  **部署/FTP User** 項目會列出應該用來存取 FTP 站台的使用者名稱。

> [AZURE.NOTE] 如果 **部署/FTP User** 未設定項目，或您忘記此使用者的密碼，您可以藉由建立新的使用者名稱和密碼 **重設部署認證** 中連結 **快速瀏覽** 區段 **儀表板**。

### 使用 Azure PowerShell 來下載

若要下載記錄檔，請啟動新的 Azure PowerShell 執行個體並使用下列命令：

    Save-AzureWebSiteLog -Name webappname

這會將所指定的 web 應用程式記錄儲存 **-名稱** 參數到名為 **logs.zip** 目前目錄中。

> [AZURE.NOTE] 如果您尚未安裝 Azure PowerShell，或未設定為使用您的 Azure 訂閱，請參閱 [如何使用 Azure PowerShell](/develop/nodejs/how-to-guides/powershell-cmdlets/)。

### 使用 Azure 命列列介面來下載

若要使用 Azure 命令列介面來下載記錄檔案，請開啟新的命令列提示字元、PowerShell、Bash 或終端機工作階段，然後輸入下列命令：

    azure site log download webappname

這會將名為 'webappname' 名為的檔案的 web 應用程式記錄儲存 **diagnostics.zip** 目前目錄中。

> [AZURE.NOTE] 如果您尚未安裝 Azure 命令列介面 (Azure CLI)，或未設定為使用您的 Azure 訂閱，請參閱 [如何使用 Azure CLI](../xplat-cli-install.md)。

## 作法：在 Application Insights 中檢視記錄

Visual Studio Application Insights 提供篩選與搜尋記錄的工具，以及將記錄與要求及其他事件建立相互關聯的工具。

1. 在 Visual Studio 中將 Application Insights SDK 加入至專案。
 * 在 [方案總管] 中，以滑鼠右鍵按一下專案，然後選擇 [加入 Application Insights]。 系統將指導您完成包括建立 Application Insights 資源在內的所有步驟。 [詳細資訊](../application-insights/app-insights-start-monitoring-app-health-usage.md)
2. 將追蹤接聽項封裝新增至專案。
 * 以滑鼠右鍵按一下專案，然後選擇 [管理 NuGet 封裝]。 選取 `Microsoft.ApplicationInsights.TraceListener` [深入](../application-insights/app-insights-asp-net-trace-logs.md)
3. 上傳您的專案並執行，以產生記錄資料。
4. 在 [Azure 入口網站](http://portal.azure.com/), ，瀏覽至新的 Application Insights 資源，並開啟 **搜尋**。 您將會看到您的記錄資料，以及要求、使用情況及其他遙測。 有些遙測可能需要數分鐘才能抵達：按一下 [重新整理]。 [詳細資訊](../application-insights/app-insights-diagnostic-search.md)

[深入了解使用 Application Insights 的效能追蹤](../insights-perf-analytics.md)

##<a name="streamlogs"></a> 作法：串流記錄

開發應用程式時，如果能夠幾近即時地檢視記錄資訊，通常會很實用。 您可以使用 Azure PowerShell 或 Azure 命令列介面，將記錄資訊串流至開發環境來達到這個目的。

> [AZURE.NOTE] 某些記錄緩衝區類型會寫入記錄檔，這可能會導致資料流中的事件順序。 例如，使用者造訪某個網頁所產生的應用程式記錄項目，可能會比頁面要求的對應 HTTP 記錄項目優先顯示在串流中。

> [AZURE.NOTE] 記錄串流也可以串流資訊寫入至文字檔案儲存在 **D:\\home\\LogFiles\\** 資料夾。

### 使用 Azure PowerShell 來串流

若要串流記錄資訊，請啟動新的 Azure PowerShell 執行個體並使用下列命令：

    Get-AzureWebSiteLog -Name webappname -Tail

這會連線至 web 應用程式所指定 **-名稱** 參數並開始記錄事件發生在 web 應用程式串流至 PowerShell 視窗的資訊。 任何寫入副檔名為 .txt、.log 或 .htm 的檔案中並存放在 /LogFiles 目錄 (d:/home/logfiles) 的資訊，都會串流至本機主控台。

若要篩選特定事件，例如錯誤，請使用 **-訊息** 參數。 例如：

    Get-AzureWebSiteLog -Name webappname -Tail -Message Error

若要篩選特定記錄類型，例如 HTTP，使用 **-路徑** 參數。 例如：

    Get-AzureWebSiteLog -Name webappname -Tail -Path http

若要檢視可用的路徑清單，請使用 -ListPath 參數。

> [AZURE.NOTE] 如果您尚未安裝 Azure PowerShell，或未設定為使用您的 Azure 訂閱，請參閱 [如何使用 Azure PowerShell](/develop/nodejs/how-to-guides/powershell-cmdlets/)。

### 使用 Azure 命令列介面來串流

若要串流記錄資訊，請開啟新的命列列提示字元、PowerShell、Bash 或終端機工作階段，然後輸入下列命令：

    azure site log tail webappname

這會連接至名為 'webappname' 的 Web 應用程式，並在該 Web 應用程式產生記錄事件時，開始將資訊串流至視窗。 任何寫入副檔名為 .txt、.log 或 .htm 的檔案中並存放在 /LogFiles 目錄 (d:/home/logfiles) 的資訊，都會串流至本機主控台。

若要篩選特定事件，例如錯誤，請使用 **-篩選** 參數。 例如：

    azure site log tail webappname --filter Error

若要篩選特定記錄類型，例如 HTTP，使用 **-路徑** 參數。 例如：

    azure site log tail webappname --path http

> [AZURE.NOTE] 如果您尚未安裝 Azure 命令列介面，或未設定為使用您的 Azure 訂閱，請參閱 [如何使用 Azure 命令列介面](../xplat-cli-install.md)。

##<a name="understandlogs"></a> 作法：了解診斷記錄

### 應用程式診斷記錄

應用程式診斷功能會根據您將記錄儲存至檔案系統、資料表儲存體或 Blob 儲存體的不同，將 .NET 應用程式的資訊儲存為特定格式。 這三種儲存類型所儲存的資料基底集合全都相同，包括事件發生的日期與時間、產生事件的處理序識別碼、事件類型 (資訊、警告與錯誤)，以及事件訊息。

__檔案系統__

每一行記錄到檔案系統的訊息，或是透過串流方式收到的訊息，都將採下列格式：

    {Date}  PID[{process id}] {event type/level} {message}

例如，錯誤事件可能會類似下列訊息：

    2014-01-30T16:36:59  PID[3096] Error       Fatal error on the page!

記錄到檔案系統可針對三種可用的方法提供最基本的資訊，亦即僅提供時間、處理序識別碼、事件層級與訊息。

__資料表儲存體__

記錄至資料表儲存體時，會使用其他屬性來協助搜尋儲存在資料表裡的資料，以及更精細的事件資訊。 以下內容 (資料行) 會用於資料表中儲存的每個實體 (列)。

屬性名稱|值/格式
---|---
PartitionKey|格式為 yyyyMMddHH 的事件日期/時間
RowKey|可唯一識別此實體的 GUID 值
Timestamp|事件發生的日期與時間
EventTickCount|事件發生的日期與時間 (刻度格式，精準度更高)
ApplicationName|Web 應用程式名稱
等級|事件層級 (例如，錯誤、警告、資訊)
EventId|此事件的事件識別碼<p><p>預設值為 0 (如果未指定的話)
InstanceId|發生事件的 Web 應用程式執行個體
Pid|處理序識別碼
Tid|產生事件的執行緒之執行緒識別碼
訊息|事件詳細資訊訊息

__Blob 儲存體__

登入 Blob 儲存體時，資料會儲存為逗號分隔值 (CSV) 的格式。 其他欄位則會以類似資料表儲存體的作法記錄起來，以提供更精細的事件相關資訊。 CSV 中的每一列會使用以下屬性：

屬性名稱|值/格式
---|---
Date|事件發生的日期與時間
Level|事件層級 (例如，錯誤、警告、資訊)
ApplicationName|Web 應用程式名稱
InstanceId|發生事件的 Web 應用程式執行個體
EventTickCount|事件發生的日期與時間 (刻度格式，精準度更高)
EventId|此事件的事件識別碼<p><p>預設值為 0 (如果未指定的話)
Pid|處理序識別碼
Tid|產生事件的執行緒之執行緒識別碼
訊息|事件詳細資訊訊息

儲存在 Blob 中的資料類似以下內容：

    date,level,applicationName,instanceId,eventTickCount,eventId,pid,tid,message
    2014-01-30T16:36:52,Error,mywebapp,6ee38a,635266966128818593,0,3096,9,An error occurred

> [AZURE.NOTE] 記錄檔的第一行會包含資料行標頭，此範例中所示。

### 失敗要求追蹤

失敗的要求追蹤會儲存在名為 XML 檔案 __fr # # #.xml__。 若要讓您更輕鬆地檢視記錄的資訊，XSL 樣式表名稱為 __freb.xsl__ 所提供的 XML 檔案所在的目錄。 在 Internet Explorer 中開啟其中一個 XML 檔案會使用 XSL 樣式表，提供格式化的追蹤資訊顯示。 此資訊類似以下內容：

![在瀏覽器中檢視的失敗要求](./media/web-sites-enable-diagnostic-log/tws-failedrequestinbrowser.png)

### 詳細的錯誤記錄

詳細的錯誤記錄指的是可針對發生的 HTTP 錯誤提供更詳盡資訊的 HTML 文件。 由於它們都是單純的 HTML 文件，因此可以使用網頁瀏覽器來檢視。

### Web 伺服器記錄

Web 伺服器記錄檔的格式是 [W3C 擴充的記錄檔格式](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx)。 這項資訊可以使用文字編輯器來讀取或剖析使用公用程式，例如 [Log Parser](http://go.microsoft.com/fwlink/?LinkId=246619)。

> [AZURE.NOTE] Azure web 應用程式所產生的記錄並不支援 __s computername__, ，__s ip__, ，或 __cs 版本__ 欄位。

##<a name="nextsteps"></a> 後續步驟

- [如何監視 Web 應用程式](/manage/services/web-sites/how-to-monitor-websites/)
- [在 Visual Studio 中疑難排解 Azure Web App](web-sites-dotnet-troubleshoot-visual-studio.md)
- [在 HDInsight 中分析 Web 應用程式記錄](http://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)

> [AZURE.NOTE] 如果您想要註冊 Azure 帳戶前開始使用 Azure App Service，請移至 [試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751), ，您可以立即建立短期入門 web 應用程式的應用程式服務中。 不需要信用卡；無需承諾。

## 變更的項目
* 如需變更從應用程式服務的網站的指南，請參閱 ︰ [Azure App Service，及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)
* 如需舊入口網站變更為新入口網站的指南，請參閱 ︰ [瀏覽預覽入口網站的參考](http://go.microsoft.com/fwlink/?LinkId=529715)
 


