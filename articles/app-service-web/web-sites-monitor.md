<properties
    pageTitle="監視 Azure 應用程式服務中的 Web 應用程式"
    description="了解如何使用管理入口網站來監視 Azure App Service 中的 Web 應用程式。"
    services="app-service"
    documentationCenter=""
    authors="cephalin"
    manager="wpickett"
    editor="mollybos"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/14/2015"
    ms.author="byvinyal"/>

#<a name="howtomonitor"></a>監視 Azure 應用程式服務中的 Web 應用程式

[App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) 針對標準與高階應用程式服務計劃透過 [監視] 管理頁面提供監視功能。 [監視] 管理頁面可提供 Web 應用程式的效能統計資料，如下文所述。

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

##度量保留原則

>[AZURE.NOTE] 應用程式度量的保留原則會因資料粒度。

- **分鐘** 資料粒度度量資訊會保留 **24 小時**
- **小時** 資料粒度度量資訊會保留 **7 天**
- **天** 資料粒度度量資訊會保留 **30 天**

##<a name="websitemetrics"></a>如何: 新增 web 應用程式度量

1. 在 [傳統入口網站](https://manage.windowsazure.com), ，從 web 應用程式] 頁面上，按一下 [ **監視** 索引標籤，顯示 **監視** 管理頁面。 依預設在圖表上 **監視** 頁面會顯示相同的度量圖表 **儀表板** 頁面。

2. 若要檢視 web 應用程式的其他度量，請按一下 [ **加入度量** 底部的頁面，即可顯示 **選擇度量** 對話方塊。

3. 按一下以選取其他度量顯示在 **監視** 頁面。

4. 選取您想要加入的度量之後 **監視** 頁面上，按一下底部的核取記號。

5. 加入度量之後 **監視** 頁面中，按一下以啟用 / 停用從頁面頂端的圖表中移除度量資訊或將每個度量旁的圓形核取方塊。

6. 若要將度量移除 **監視** 頁面上，選取您想要移除，然後按一下度量 **Delete Metric** 在頁面底部的圖示。



##<a name="howtoreceivealerts"></a>如何: 接收來自 web 應用程式度量的警示

在 **標準** web 應用程式模式中，您可以接收 web 應用程式監視度量為基礎的警示。 警示功能需要您先設定 web 端點監視，您可以在 **監視** 區段 **設定** 頁面。 您也可以選擇在所選擇的度量達到所指定的值時傳送電子郵件。 如需詳細資訊，請參閱 [How to: 接收警示通知及管理在 Azure 中的警示規則](http://go.microsoft.com/fwlink/?LinkId=309356)。  

##<a name="howtoviewusage"></a>如何: 檢視 web 應用程式的使用量配額

Web 應用程式可以設定為在 [ **共用** 或 **標準** 從 web 應用程式的模式 **延展** ] 管理頁面 [傳統入口網站](https://manage.windowsazure.com)。 每個 Azure 訂閱都能存取針對每個區域執行最多 100 個 web 應用程式提供的資源集區 **共用** 模式。 此項目的每個 web 應用程式訂用帳戶可用的資源集區由其他 web 應用程式共用相同地理區域中，設定為在 **共用** 模式。 由於這些資源需與其他 Web 應用程式共用，因此所有訂用帳戶在使用這些資源時均會受到限制。 套用這些資源的訂用帳戶的使用限制會以每個 web 應用程式的使用量概觀] 區段底下所列的使用量配額來表示 **儀表板** 管理頁面。

>[AZURE.NOTE] 當 web 應用程式設定為在執行 **標準** 模式中，系統會配置專用的資源相當於 **小** (預設值)， **媒體** 或 **大** 在表格中的虛擬機器大小 [虛擬機器和雲端服務大小][vmsizes]。 訂閱可以使用在執行 web 應用程式所需的資源而毫不受限 **標準** 模式。 不過，數目 **標準** 模式 web 應用程式，可以在每個區域建立為 500。

### 作法：檢視設定為共用模式的 Web 應用程式的使用量配額 ###
若要判斷 Web 應用程式影響資源使用量配額的程度，請遵循以下步驟：

1. 開啟 web 應用程式的 **儀表板** ] 管理頁面 [傳統入口網站](https://manage.windowsazure.com)。
2. 在 **使用量概觀** 區段程式各自的使用量配額 [應用程式服務](http://go.microsoft.com/fwlink/?LinkId=529714) 計劃會顯示，它是下列子集:
    -   **資料輸出**, ，**CPU 時間**, ，和 **記憶體** -若超出配額，Azure 會停止目前的配額間隔的剩餘的 web 應用程式。 Azure 會在下一個配額間隔開始時啟動 Web 應用程式。
    -   **檔案系統儲存體** -當到達此配額，檔案系統儲存體仍可進行讀取作業，但是所有寫入作業，包括所需的一般 web 應用程式活動，會遭到封鎖。 當您降低檔案使用量或將 Web 應用程式移到配額較高的應用程式服務方案時，將會繼續進行寫入作業。
    -   **連結的資源** -web 應用程式，例如資料庫或儲存體的任何連結資源的配額也會顯示如下。

    有些配額可以依據 Web 裝載計畫套用，但有些配額則可依據網站套用。 每個 Web 主控方案的使用量配額的詳細資訊，請參閱 [網站限制](azure-subscription-service-limits.md#websiteslimits)。

##<a name="resourceusage"></a> 如何: 避免超出配額

配額與效能或成本無關，但這是 Azure 管理在多租用戶環境中資源使用量的方法，以免租用戶過度使用共用資料。 因為超過配額即表示您的 Web 應用程式停機或功能降低，如果您希望在即將達到配額時讓網站持續運作，請考慮下列各項：

- 將 Web 應用程式移到較高層級的應用程式服務方案，以利用較大的配額。 例如，唯一配額 **基本** 和 **標準** 計劃是檔案系統儲存體。
- 隨著 Web 應用程式的執行個體數目增加，超過共用資源配額的可能性也會隨之提高。 如果適當，請考慮在超出共用資源配額時調降 Web 應用程式的其他執行個體。

##<a name="howtoconfigdiagnostics"></a>如何: 設定診斷功能及下載 web 應用程式記錄

啟用診斷功能 **設定** ] 索引標籤中的 web 應用程式 [傳統入口網站](https://manage.windowsazure.com)。 有兩種類型的診斷: **應用程式診斷** 和 **網站診斷**。

#### 應用程式診斷 ####

 **應用程式診斷** 區段 **設定** 管理頁面控制項之資訊的記錄所產生狀況中很有用的應用程式記錄應用程式中發生的事件。 例如，當應用程式內部發生錯誤時，您也許會希望能向使用者呈現易於理解的錯誤，意即在記錄中寫入更詳細的錯誤資訊，以供日後分析之用。

您可以啟用或停用以下應用程式診斷：

- **Application Logging (File System)** -開啟應用程式所產生之資訊的記錄。  **記錄層級** 欄位決定要記錄錯誤、 警告或資訊層級的資訊。 您也可以選取 [詳細資訊] 來記錄應用程式產生的所有資訊。

    此設定所產生的記錄檔儲存在 web 應用程式的檔案系統，可以使用中的步驟來下載 **下載 web 應用程式的記錄檔** 下一節。

- **Application Logging (Table Storage)** -開啟應用程式，類似於 [Application Logging (File System)] 選項所產生之資訊的記錄。 不過記錄資訊是以資料表形式儲存在 Azure 儲存體帳戶中。

    若要指定 Azure 儲存體帳戶和資料表，請選擇 [ **上**, ，請選取 **記錄層級**, ，然後選擇 [ **管理資料表儲存體**。 指定要使用的儲存體帳戶和資料表，或建立新的資料表。

    您可以使用 Azure 儲存體用戶端來存取儲存於資料表中的記錄資訊。

- **應用程式記錄 (Blob 儲存體)** -開啟應用程式，類似於 [Application Logging (Table Storage)] 選項所產生之資訊的記錄。 不過記錄資訊是以 Blob 形式儲存在 Azure 儲存體帳戶中。

    若要指定 Azure 儲存體帳戶和 blob，請選擇 [ **上**, ，請選取 **記錄層級**, ，然後選擇 [ **管理 Blob 儲存體**。 指定要使用的儲存體帳戶、Blob 容器及 Blob 名稱，或建立新的容器和 Blob。

如需 Azure 儲存體帳戶的詳細資訊，請參閱 [如何管理儲存體帳戶](/manage/services/storage/how-to-manage-a-storage-account/)。

> [AZURE.NOTE] .NET 應用程式中的資料表或 blob 儲存體的應用程式記錄功能僅支援。

由於儲存體的應用程式記錄功能須使用儲存體用戶端來檢視記錄資料，因此當您計劃使用了解如何直接讀取及處理 Azure 資料表或 Blob 儲存體之資料的服務或應用程式時，這項功能最為實用。 檔案系統的記錄功能會產生可透過 FTP 或其他公用程式下載到本機電腦的檔案，如本節後續內容所述。

**應用程式診斷 (檔案系統)**, ，**應用程式診斷 (資料表儲存體)**, ，和 **應用程式診斷 (blob 儲存體)** 都可以啟用一次，並個別具有記錄層級組態。 例如，您也許會想要將錯誤和警告記錄在儲存體並做為長期的記錄解決方案，同時在檢測應用程式程式碼後啟用檔案系統記錄並搭配詳細資訊層級，以供疑難排解問題之用。

您也可以從 Azure PowerShell 啟用診斷使用 **Set-azurewebsite** 指令程式。 如果您尚未安裝 Azure PowerShell，或未設定為使用您的 Azure 訂閱，請參閱 [如何使用 Azure PowerShell](/develop/nodejs/how-to-guides/powershell-cmdlets/)。

> [AZURE.NOTE] 應用程式記錄功能仰賴應用程式所產生的記錄資訊。 用來產生記錄資訊的方法和資訊的格式取決於應用程式的撰寫語言。 如需有關使用應用程式記錄功能的語言特有資訊，請參閱以下文章：
>
> - **.NET** - [疑難排解 Azure App Service 使用 Visual Studio 中的 web 應用程式](web-sites-dotnet-troubleshoot-visual-studio.md)
> - **Node.js** - [如何偵錯在 Azure 網站中的 Node.js 應用程式](web-sites-nodejs-debug.md)
>
> 只有 .NET 應用程式才支援資料表或 Blob 儲存體的應用程式記錄功能。

#### 網站診斷 ####

 **網站診斷** 區段 **設定** 會由 web 伺服器，例如 web 要求、 記錄提供頁面提供所需的時間失敗的記錄檔的管理頁面控制項。 您可以啟用或停用以下選項：

- **Web 伺服器記錄** -開啟 Web 伺服器記錄功能，以將 web 應用程式記錄使用 W3C 擴充的記錄檔案格式儲存。 Web 伺服器記錄功能會產生涵蓋所有傳入 Web 應用程式之要求的記錄，其中包括用戶端 IP 位址、要求的 URI、回應的 HTTP 狀態碼及用戶端的使用者代理程式字串等資訊。 您可以將記錄儲存在 Azure 儲存體帳戶或檔案系統中。

 若要將 web 伺服器記錄儲存至 Azure 儲存體帳戶中，選擇 [ **儲存體**, ，然後選擇 [ **管理儲存體** 指定儲存體帳戶和用來保存記錄檔的 Azure Blob 容器。 如需 Azure 儲存體帳戶的詳細資訊，請參閱 [如何管理儲存體帳戶](/manage/services/storage/how-to-manage-a-storage-account/)。

   若要將 web 伺服器記錄儲存至檔案系統中，選擇 [ **檔案系統**。 這可讓 **配額** 方塊，您可以設定的最大記錄檔的磁碟空間量。 大小的下限為 25MB，上限為 100MB。 預設大小為 35MB。

 依預設，系統永遠不會刪除 Web 伺服器記錄。 若要指定一段時間之後，將會自動刪除記錄，請選取 **設定保留** 並輸入記錄的保存天數 **保留期限** 方塊。 此項設定適用於 Azure 儲存體和檔案系統選項。

- **詳細的錯誤訊息** -開啟詳細錯誤記錄到記錄有關 HTTP 錯誤 (大於 400 狀態碼) 的其他資訊。

- **失敗要求追蹤** -開啟失敗的要求追蹤功能可擷取的資訊失敗的用戶端要求，如 400 系列的 HTTP 狀態碼。  失敗要求追蹤功能會產生 XML 文件，其中包括要求在 IIS 中經過哪些模組的追蹤、模組傳回的詳細資料及叫用模組的時間。 這些資訊可用來隔離出發生失敗的元件。


啟用 web 應用程式的診斷之後，按一下 [ **儲存** 底部的圖示 **設定** 管理頁面，以套用您設定的選項。

> [AZURE.IMPORTANT] 詳細錯誤訊息和失敗要求追蹤大增的 web 應用程式。 建議您在重製欲疑難排解的問題後關閉這些功能。

### 進階組態 ###

新增索引鍵/值配對可以進一步修改診斷功能 **應用程式設定** 區段 **設定** 管理頁面。 可以設定下列設定，從 **應用程式設定**:

**DIAGNOSTICS_TEXTTRACELOGDIRECTORY**

- 儲存應用程式記錄的位置，相對於 Web 根目錄。

- 預設值:...\\..\\LogFiles\\Application

**DIAGNOSTICS_TEXTTRACEMAXBUFFERSIZEBYTES**

- 擷取應用程式記錄時，使用的緩衝區大小上限。 剛開始時，系統會先將資訊寫入緩衝區，然後再排清到檔案或儲存體。 如果系統將新資訊寫入緩衝區，然後再予以排清，您便會遺失先前記錄的資訊。 如果應用程式突然產生大量的記錄資訊，請考慮增加緩衝區的大小。

- 預設值：10MB

**DIAGNOSTICS_TEXTTRACEMAXLOGFOLDERSIZEBYTES**

- 大小上限 **應用程式** 儲存應用程式的診斷資訊寫入檔案的資料夾。

- 預設值：1MB

###下載 Web 應用程式的記錄檔

您可以使用 FTP、Azure PowerShell 或 Azure CLI 來下載記錄檔。

**FTP**

1. 開啟 web 應用程式的 **儀表板** ] 管理頁面 [傳統入口網站](https://manage.windowsazure.com) ，並記下所列的 FTP 站台 **診斷記錄檔** 列示的帳戶和 **部署使用者**。 FTP 站台是記錄檔的所在位置，而列示於 [Deployment User] 下的帳戶可用來通過 FTP 站台的驗證。
2. 如果您尚未建立部署認證，帳戶會列示於 **部署使用者** 列為 **未設定**。 在這種情況下，您必須建立部署認證 (如 [儀表板] 的 [Reset Deployment Credentials] 區段所述)，因為您必須使用這些認證來通過儲存記錄檔之 FTP 站台的驗證。 Azure 不支援使用 Live ID 認證來通過此 FTP 站台的驗證。
3. 請考慮使用 FTP 用戶端，例如 [FileZilla][fzilla] 連線到 FTP 站台。 與瀏覽器普遍的功能相比，FTP 用戶端更能簡化認證的指定和 FTP 站台資料夾的檢視。
4. 請將 FTP 站台上的記錄檔複製到本機電腦。

**Azure PowerShell**

1. 從 **[開始] 畫面** 或 **[開始] 功能表**, ，搜尋 **PowerShell**。 以滑鼠右鍵按一下 **PowerShell** 項目，然後選取 **系統管理員身分執行**。

    > [AZURE.NOTE] 如果 **PowerShell** 是未安裝，請參閱 [開始使用 Azure PowerShell Cmdlet](http://msdn.microsoft.com/library/windowsazure/jj554332.aspx) 取得安裝和組態資訊。

2. 在 Azure PowerShell 提示字元中使用下列命令來下載記錄檔：

        Save-AzureWebSiteLog -Name webappname

    這會下載所指定的 web 應用程式記錄檔 **webappname** 並將它們儲存到 **log.zip** 目前目錄中的檔案。

    您也可以使用以下命令來檢視記錄事件的即時資料流：

        Get-AzureWebSiteLog -Name webappname -Tail

    如此可讓記錄資訊在發生時顯示於 Azure PowerShell 提示字元中。

**Azure CLI**

開啟新的命令提示字元、PowerShell、Bash 或終端機工作階段，然後使用以下命令來下載記錄檔：

    azure site log download webappname

這會下載所指定的 web 應用程式記錄檔 **webappname** 並將它們儲存到 **log.zip** 目前目錄中的檔案。

您也可以使用以下命令來檢視記錄事件的即時資料流：

    azure site log tail webappname

如此可將記錄資訊顯示在執行命令的命令提示字元、PowerShell、Bash 或終端機工作階段中。

> [AZURE.NOTE] 如果 **azure** 命令尚未安裝，請參閱 [如何使用 Azure CLI](../virtual-machines-command-line-tools.md) 取得安裝和組態資訊。

### 讀取記錄檔 ###

在啟用Web 應用程式記錄和/或追蹤之後產生的記錄檔，會因為您在 Web 應用程式之 [設定] 管理頁面中所設定的記錄/追蹤層級而有所差異。 以下是記錄檔的位置和記錄檔的分析方式：

**記錄檔類型：應用程式記錄**

- 位置：/LogFiles/Application/。 此資料夾內含有一或多個文字檔案，這些檔案涵蓋應用程式記錄所產生的資訊。 所記錄的資訊包括日期和時間、應用程式的處理序識別碼 (PID)，以及應用程式檢測所產生的值。

- 檔案讀取方法：了解應用程式產生之值的文字編輯器或剖析器。

**記錄檔類型：失敗要求追蹤**

- 位置：/LogFiles/W3SVC#########/。 此資料夾內含有一個 XSL 檔案和一或多個 XML 檔案。 請務必將 XSL 檔案下載到 XML 檔案所在的目錄，因為在 Internet Explorer 中檢視 XML 檔案時，XSL 檔案能提供內容格式化和篩選等功能。

- 檔案讀取方法：Internet Explorer

**記錄檔類型：詳細錯誤記錄**

- 位置：/LogFiles/DetailedErrors/。 /LogFiles/DetailedErrors/ 資料夾內含有一或多個 .htm 檔案，這些檔案能提供任何已發生之 HTTP 錯誤的廣泛資訊。

- 檔案讀取方法：Web 瀏覽器

.htm 檔案包括以下區段：

- **詳細錯誤資訊:** 包含錯誤的相關資訊，例如 <em>模組</em>, ，<em>處理常式</em>, ，<em>錯誤碼</em>, ，和 <em>要求的 URL</em>。

- **最可能的原因:** 列出幾個可能的原因的錯誤。

- **您可以嘗試的動作:** 列出可能可以解決錯誤回報之問題的解決方案。

- **連結和詳細資訊**: 提供有關錯誤的其他摘要資訊，而且也可以包含其他資源，例如 Microsoft 知識庫文件的連結。

**記錄檔類型：Web 伺服器記錄**

- 位置：/LogFiles/http/RawLogs。 將資訊儲存在檔案格式使用 [W3C 擴充的記錄格式](http://go.microsoft.com/fwlink/?LinkID=90561)。 Azure Web 應用程式未使用 s-computername、s-ip 及 cs-version 等欄位。

- 檔案讀取方法：記錄檔剖析器。 用來剖析及查詢 IIS 記錄檔。 Log Parser 2.2 可在 Microsoft 下載中心取得： <a href="http://go.microsoft.com/fwlink/?LinkId=246619">http://go.microsoft.com/fwlink/?LinkId=246619</a>.


##<a name="webendpointstatus"></a> 如何: 監視 web 端點狀態

這項功能，提供 **標準** 模式，可讓您監視從最多 3 個地理位置最多 2 個端點。

端點監視能讓您設定從不同地理位置執行，且用來測試 Web URL 之回應時間和運作時間的 Web 測試。 這項測試會針對 Web URL 執行 HTTP get 操作，以便從每個位置判斷回應時間和運作時間。 各個已設定的位置會每隔五分鐘執行一次測試。

運作時間是透過 HTTP 回應碼來加以監視，而回應時間的測量單位是毫秒。 當回應時間少於 30 秒且 HTTP 狀態碼小於 400 時，運作時間可視為 100%。 當回應時間大於 30 秒或 HTTP 狀態碼大於 400 時，運作時間則為 0%。

在設定端點監視後，您可以從每個測試位置向下鑽研各個端點，以便檢視監視間隔內詳細的回應時間和運作時間狀態。 您也可以設定警示規則 (例如，當端點的回應時間太長時)。

**設定端點監視：**

1.  開啟 **Web 應用程式**。 按一下要設定的 Web 應用程式名稱。
2.  按一下 [ **設定** ] 索引標籤。
3.     Go to the **Monitoring** section to enter your endpoint settings.
4.  輸入端點的名稱。
5.  輸入要監視之 Web 應用程式組件的 URL。 例如， [http://contoso.azurewebsites.net/archive](http://contoso.azurewebsites.net/archive)。
6.  在清單中選取一或多個地理位置。
7.  您也可以選擇重複先前的步驟來建立第二個端點。
8.  按一下 [ **儲存**。 它可能需要一些時間 web 端點監視資料會出現在 **儀表板** 和 **監視** 索引標籤。

    若要建立電子郵件規則，請執行下列動作：

9.  在服務列在最左邊，按一下 [ **管理服務**。
10. 按一下 [ **新增規則** 底部。
11. 在 **服務類型**, ，請選取 **Web 應用程式**, ，然後選取 [web 應用程式，您先前設定端點監視。 按一下 [ **下一步**。
12. 在 **度量**, ，您現在可以選取其他度量，為您設定的端點。 例如: **回應時間 (首頁 / 美國: IL-芝加哥)**。 選取 [回應時間] 度量並中輸入 3 **臨界值** 指定 3 秒的臨界值。
13. 選取 **傳送電子郵件給服務管理員和共同管理員**。 按一下 [ **完整**。

    Azure 現在會主動監視端點，並且在端點的回覆時間超過 3 秒時傳送點子郵件通知。

如需 Web 應用程式端點監視的詳細資訊，請觀看以下影片：

- [Scott Guthrie introduces Azure Web Sites and sets up Endpoint Monitoring](/documentation/videos/websites-and-endpoint-monitoring-scottgu/)

- [Keeping Azure Web Sites up plus Endpoint Monitoring - with Stefan Schackow](/documentation/videos/azure-web-sites-endpoint-monitoring-and-staying-up/)

>[AZURE.NOTE] 如果您想要註冊 Azure 帳戶前開始使用 Azure App Service，請移至 [試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751), ，您可以立即建立短期入門 web 應用程式的應用程式服務中。 不需要信用卡；沒有承諾。

## 變更的項目
* 如需變更從應用程式服務的網站的指南，請參閱: [Azure App Service，及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)
* 如需 Azure 入口網站變更為 Azure 預覽入口網站的指南，請參閱: [瀏覽預覽入口網站的參考](http://go.microsoft.com/fwlink/?LinkId=529715)

[fzilla]:http://go.microsoft.com/fwlink/?LinkId=247914
[vmsizes]:http://go.microsoft.com/fwlink/?LinkID=309169
 

