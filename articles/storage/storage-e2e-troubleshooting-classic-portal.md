<properties 
    pageTitle="使用 Azure 儲存體度量和記錄、AzCopy 和 Message Analyzer 進行端對端疑難排解 | Microsoft Azure" 
    description="示範使用 Azure Storage Analytics、AzCopy 和 Microsoft Message Analyzer 進行端對端疑難排解的教學課程" 
    services="storage" 
    documentationCenter="dotnet" 
    authors="tamram" 
    manager="adinah"/>

<tags 
    ms.service="storage" 
    ms.workload="storage" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="12/01/2015" 
    ms.author="tamram"/>

# 使用 Azure 儲存體度量和記錄、AzCopy 和 Message Analyzer 進行端對端疑難排解 

[AZURE.INCLUDE [storage-selector-portal-e2e-troubleshooting](../../includes/storage-selector-portal-e2e-troubleshooting.md)]

## 概觀

診斷與疑難排解是透過 Microsoft Azure 儲存體建置和支援用戶端應用程式的關鍵技術。 由於 Azure 應用程式的分散式本質，診斷和疑難排解錯誤和效能問題可能會比在傳統環境中更為複雜。

在本教學課程中，我們將示範如何識別可能會影響效能的用戶端特定錯誤，以及使用 Microsoft 提供的工具和 Azure 儲存體來從端對端排解這些錯誤，以便最佳化用戶端應用程式。 

本教學課程提供端對端疑難排解案例的實際操作探勘。 疑難排解 Azure 儲存體應用程式的深度概念性指南，請參閱 [監視、 診斷與疑難排解儲存體](storage-monitoring-diagnosing-troubleshooting.md)。 

## 對 Azure 儲存體應用程式進行疑難排解的工具

若要使用 Microsoft Azure 儲存體進行用戶端應用程式的疑難排解，您可以使用一些工具組合來判斷何時發生問題和問題的可能原因。 這些工具包括：

- **Azure 儲存體分析**。 [Azure 儲存體分析](http://msdn.microsoft.com/library/azure/hh343270.aspx) 提供度量和記錄 Azure 儲存體。
    - **儲存體度量** 追蹤交易度量和儲存體帳戶的容量度量。 您可以使用度量，判斷如何根據各種不同的量值來執行您的應用程式。 請參閱 [儲存體分析度量資料表結構描述](http://msdn.microsoft.com/library/azure/hh343264.aspx) 如需儲存體分析所追蹤的度量類型詳細資訊。 

    - **儲存體記錄** 每項要求記錄到 Azure 儲存體服務，以伺服器端記錄檔。 記錄檔可追蹤每項要求的詳細資料，包括執行的作業、作業的狀態及延遲資訊。 請參閱 [儲存體分析記錄格式](http://msdn.microsoft.com/library/azure/hh343259.aspx) 如需有關儲存體分析寫入記錄檔的要求和回應資料。

- **Azure 傳統入口網站**。 您可以在儲存體帳戶設定的度量和記錄 [Azure 傳統入口網站](manage.windowsazure.com)。 您也可以檢視圖表和圖形 (其中顯示應用程式在一段時間內的執行狀況)，並設定警示，通知應用程式是否對指定的度量以超乎預期的方式執行。 
    
    請參閱 [如何監視儲存體帳戶](storage-monitor-storage-account.md) 如需設定 Azure 傳統入口網站中的監視資訊。

- **AzCopy**。 Azure 儲存體的伺服器記錄檔會儲存為 Blob，因此您可以使用 AzCopy，將記錄檔 Blob 複製到本機目錄，以便使用 Microsoft Message Analyzer 分析。 請參閱 [如何搭配使用 AzCopy 與 Microsoft Azure 儲存體](storage-use-azcopy.md) 如需 AzCopy 的詳細資訊。

- **Microsoft Message Analyzer**。 Message Analyzer 是一種可取用記錄檔並以視覺化格式顯示記錄檔資料的工具，讓您輕鬆地篩選、搜尋記錄檔資料並分組為實用的資料集，以便用來分析錯誤和效能問題。 請參閱 [Microsoft Message Analyzer 操作指南](http://technet.microsoft.com/library/jj649776.aspx) 如需 Message Analyzer 的詳細資訊。

## 關於範例案例

在本教學課程中，我們將檢驗一個案例，其中的 Azure 儲存體度量表示應用程式呼叫 Azure 儲存體的成功率很低。 低百分比成功速率度量 (顯示為 **PercentSuccess** 在 Azure 傳統入口網站和度量資料表中) 會追蹤成功的作業，但是會傳回大於 299 的 HTTP 狀態碼。 在伺服器端儲存體記錄檔中，這些作業會記錄交易狀態為 **ClientOtherErrors**。 如需低成功百分比度量的詳細資訊，請參閱 [度量顯示低 PercentSuccess 或分析記錄檔項目的交易狀態為 ClientOtherErrors 的作業](storage-monitoring-diagnosing-troubleshooting.md#metrics-show-low-percent-success)。

Azure 儲存體作業可能會傳回大於 299 的 HTTP 狀態碼為其正常功能的一部分。 但是在某些情況下，這些錯誤表示您可能可以最佳化您的用戶端應用程式，以改善效能。 

在此案例中，我們會將低百分比成功率視為任何低於 100%的項目。 不過，您可以根據自己的需求，選擇不同的度量層級。 我們建議您在應用程式的測試期間，建立關鍵效能指標的基準容錯。 例如，您可能會根據測試，決定您的應用程式應具有 90% 或 85% 的穩定成功率。 如果度量資料顯示應用程式偏離該數字，您可以調查造成增加的可能原因。 

在我們的範例案例中，一旦建立了低於 100% 的百分比成功率度量，我們就會檢查記錄檔，以找出度量相關的錯誤，並利用它們來找出造成較低百分比成功率的原因。 我們會特別關注 400 範圍中的錯誤。 然後我們將更仔細調查 404 (找不到) 錯誤。

### 400 範圍錯誤的某些原因

下列範例顯示 Azure Blob 儲存體要求的某些 400 範圍錯誤的取樣，以及其可能原因。 任何這些錯誤，以及 300 範圍和 500 範圍中的錯誤，都可能導致低百分比成功率。 

請注意，下面所列並不完整。 請參閱 [狀態和錯誤碼](http://msdn.microsoft.com/library/azure/dd179382.aspx) msdn 如需一般 Azure 儲存體錯誤和每項儲存體服務特定錯誤的詳細資訊。

**狀態碼 404 (找不到) 範例**

發生於因為找不到 Blob 或容器而使對容器或 Blob 的讀取作業失敗時。

- 如果在其他用戶端此要求前刪除了容器或 Blob，就會發生。 
- 如果您在檢查容器或 Blob 是否存在之後，使用可建立容器或 Blob 的 API 呼叫，就會發生。 CreateIfNotExists API 會先進行 HEAD 呼叫，以檢查容器或 Blob 是否存在；如果不存在，寔會傳回 404 錯誤，然後進行第二次 PUT 呼叫，以寫入容器或 Blob。

**狀態碼 409 (衝突) 範例**

- 如果您使用建立 API 來建立新的容器或 Blob，但未先檢查其存在與否，而且已存在具有該名稱的容器或 Blob，就會發生。 
- 如果正在刪除容器，而您嘗試在刪除作業完成之前，使用相同名稱建立新的容器，就會發生。
- 如果您在容器或 Blob 上指定租用，而且已經有租用存在，就會發生。
 
**狀態碼 412 (先決條件失敗) 範例**

- 發生於不符合條件式標頭指定的條件時。
- 發生於指定的租用識別碼與容器或 Blob 上的租用識別碼不相符時。

## 產生記錄檔以供分析

在本教學課程中，我們將使用 Message Analyzer 來處理三種不同類型的記錄檔，雖然您可以選擇使用任何其中一種：

-  **伺服器記錄檔**, ，當您啟用 Azure 儲存體記錄建立。 伺服器記錄檔包含對其中一項 Azure 儲存體服務 (Blob、佇列、資料表和檔案) 呼叫的每項作業的相關資料。 伺服器記錄檔指出已呼叫的作業和傳回的狀態碼，以及有關要求和回應的其他詳細資料。
-  **.NET 用戶端記錄檔**, ，當您在.NET 應用程式中啟用用戶端記錄時建立。 用戶端記錄檔包含用戶端如何準備要求及如何接收和處理回應的詳細資訊。 
-  **HTTP 網路追蹤記錄檔**, ，用以收集 HTTP/HTTPS 要求和回應資料，包括對 Azure 儲存體作業。 在本教學課程中，我們將透過 Message Analyzer 產生網路追蹤。

### 設定伺服器端記錄和度量

首先，我們必須設定 Azure 儲存體記錄和度量，如此我們才有用戶端應用程式提供的資料可分析。 您可以透過設定記錄和度量以各種方式- [Azure 傳統入口網站](manage.windowsazure.com), ，使用 PowerShell，或以程式設計的方式。 請參閱 [啟用儲存體度量和檢視度量資料](http://msdn.microsoft.com/library/azure/dn782843.aspx) 和 [啟用儲存體記錄和存取記錄檔資料](http://msdn.microsoft.com/library/azure/dn782840.aspx) msdn 如需設定記錄和度量的詳細資訊。

**透過 Azure 傳統入口網站**

若要設定您使用入口網站的儲存體帳戶的記錄和度量，請依照下列指示 [如何監視儲存體帳戶](storage-monitor-storage-account.md)。

> [AZURE.NOTE] 您不可能使用 Azure 傳統入口網站設定分鐘度量。 不過，我們建議您不要為了本教學課程的目的，以及為了調查您的應用程式的效能問題，而設定這類度量。 您可以使用 PowerShell (如下所示)，或以程式設計方式，或透過 Azure 傳統入口網站設定分鐘度量。
>
> 請注意，Azure 傳統入口網站無法顯示分鐘度量，只能顯示每小時度量。 

**透過 PowerShell**

若要開始使用 azure PowerShell，請參閱 [如何安裝和設定 Azure PowerShell](../install-configure-powershell.md)。

1. 使用 [Add-azureaccount](http://msdn.microsoft.com/library/azure/dn722528.aspx) cmdlet 將您的 Azure 使用者帳戶新增至 PowerShell 視窗:

    ```
    Add-AzureAccount
    ```

2. 在 **登入 Microsoft Azure** ] 視窗中，輸入電子郵件地址和您的帳戶相關聯的密碼。 Azure 會驗證並儲存認證資訊，然後關閉視窗。
3. 在 PowerShell 視窗中執行下列命令，將預設儲存體帳戶設定為您在本教學課程中使用的儲存體帳戶：

    ```
    $SubscriptionName = 'Your subscription name'
    $StorageAccountName = 'yourstorageaccount' 
    Set-AzureSubscription -CurrentStorageAccountName $StorageAccountName -SubscriptionName $SubscriptionName 
    ```

4. 啟用 Blob 服務的儲存體記錄： 
 
    ```
    Set-AzureStorageServiceLoggingProperty -ServiceType Blob -LoggingOperations Read,Write,Delete -PassThru -RetentionDays 7 -Version 1.0 
    ```
5. 啟用 Blob 服務，並確認其設定的儲存體度量 **-MetricsType** 至 `Minute`:

    ```
    Set-AzureStorageServiceMetricsProperty -ServiceType Blob -MetricsType Minute -MetricsLevel ServiceAndApi -PassThru -RetentionDays 7 -Version 1.0 
    ```

### 設定 .NET 用戶端記錄

若要設定 .NET 應用程式的用戶端的記錄，請在應用程式的組態檔 (web.config 或 app.config) 中啟用 .NET 診斷。 請參閱 [用戶端使用儲存體用戶端程式庫記錄](http://msdn.microsoft.com/library/azure/dn782839.aspx) 和 [用戶端記錄與 Microsoft Azure Storage SDK for Java](http://msdn.microsoft.com/library/azure/dn782844.aspx) msdn 如需詳細資訊。

用戶端記錄檔包含用戶端如何準備要求及如何接收和處理回應的詳細資訊。

用戶端記錄設定於您的應用程式的 app.config 或 web.config 檔案中。 如需詳細資訊，請參閱 [用戶端使用儲存體用戶端程式庫記錄](http://msdn.microsoft.com/library/azure/dn782839.aspx) MSDN 上。

儲存體用戶端程式庫會將用戶端記錄檔資料儲存在應用程式的組態檔 (web.config 或 app.config) 中指定的位置。 

### 收集網路追蹤

您可以使用 Message Analyzer，在用戶端應用程式執行時收集 HTTP/HTTPS 網路追蹤。 訊息分析師會使用 [Fiddler](http://www.telerik.com/fiddler) 在後端。 收集網路追蹤之前，我們建議您設定 Fiddler 以記錄未加密的 HTTPS 流量：

1. 安裝 [Fiddler](http://www.telerik.com/download/fiddler)。
2. 啟動 Fiddler。
2. 選取 **工具 |Fiddler 選項**。
3. 在 [選項] 對話方塊中，確定 **擷取 HTTPS Connect** 和 **解密 HTTPS 流量** 都已選取，如下所示。

![設定 Fiddler 選項](./media/storage-e2e-troubleshooting-classic-portal/fiddler-options-1.png)

在本教學課程中，先在 Message Analyzer 中收集並儲存網路追蹤，然後建立分析工作階段以分析追蹤和記錄檔。 若要在 Message Analyzer 中收集網路追蹤：

1. 在 Message Analyzer 中，選取 **檔案 |[快速追蹤 |未加密的 HTTPS**。
2. 追蹤會立即開始。 選取 **停止** 以停止追蹤，我們可以將它設定為只追蹤儲存體流量。
3. 選取 **編輯** 以編輯追蹤工作階段。
4. 選取 **設定** 的右邊連結 **Microsoft-Pef-webproxy** ETW 提供者。
5. 在 **進階設定** ] 對話方塊中，按一下 [ **提供者** ] 索引標籤。
6. 在 **主機名稱篩選條件** 欄位中，指定您的儲存體端點，並以空格分隔。 例如，您可以如下指定您的端點；將 `storagesample` 變更為儲存體帳戶的名稱：
    
    ``` 
    storagesample.blob.core.windows.net storagesample.queue.core.windows.net storagesample.table.core.windows.net 
    ```

7. 結束對話方塊，然後按一下 [ **重新啟動** 開始收集追蹤與主機名稱篩選條件，以便追蹤中包含唯一的 Azure 儲存體網路流量。

>[AZURE.NOTE] 完成網路追蹤收集之後，我們強烈建議您還原的設定，您可能已經變更 Fiddler 以解密 HTTPS 流量。 在 [Fiddler 選項] 對話方塊中，取消選取 **擷取 HTTPS Connect** 和 **解密 HTTPS 流量** 核取方塊。

請參閱 [使用網路追蹤功能](http://technet.microsoft.com/library/jj674819.aspx) technet 如需詳細資訊。

## 在 Azure 傳統入口網站中檢閱度量資料

一旦您的應用程式已執行了一段時間，您即可檢閱 Azure 傳統入口網站中顯示的度量圖表，以觀察您服務的執行狀況。 首先，我們將新增 **成功百分比** 至 [監控] 頁面的度量:

1. 瀏覽至您的儲存體帳戶中的儀表板 [Azure 傳統入口網站](manage.windowsazure.com), ，然後選取 **監視** 檢視監控頁面。
2. 按一下 [ **加入度量** 顯示 **選擇度量** ] 對話方塊。
3. 向下尋找捲動 **成功百分比** 群組，加以展開，然後選取 **彙總**, ，如下圖所示。 此度量會彙總所有 Blob 作業的成功百分比資料。

![選擇度量](./media/storage-e2e-troubleshooting-classic-portal/choose-metrics-portal-1.png)

在 Azure 傳統入口網站，您會立即看到 **成功百分比** 在監控圖表，以及任何其他度量您所新增 (最多六個可以顯示在圖表上一次)。 在下圖中，您可以看到百分比成功率稍微低於 100%，這就是我們接下來將透過在 Message Analyzer 分析中記錄檔來調查的案例：

![入口網站中的度量圖表](./media/storage-e2e-troubleshooting-classic-portal/portal-metrics-chart-1.png)

如需將度量新增至 [監控] 頁面的詳細資訊，請參閱 [How to: 將度量新增至度量表](storage-monitor-storage-account.md#addmonitoringmetrics)。

> [AZURE.NOTE] 它可能需要一些時間才會出現在 Azure 傳統入口網站啟用儲存體度量之後，度量資料。 這是因為過了這個小時，前一個小時的每小時度量才會顯示在 Azure 傳統入口網站中。 此外，分鐘度量不會顯示在 Azure 傳統入口網站中。 因此視您啟用度量的時間而定，有可能需要兩個小時才看得見度量資料。

## 使用 AzCopy 將伺服器記錄檔複製到本機目錄

Azure 儲存體會將伺服器記錄檔資料寫入至 Blob，而度量會寫入至資料表。 記錄檔 Blob 位於您儲存體帳戶已知名稱的 `$logs` 容器中。 記錄檔 Blob 會以階層方式依照年、月、日和小時命名，讓您輕鬆地找出您要調查的時間範圍。 例如，在 `storagesample` 帳戶中，01/02/2015 上午 8-9 點的記錄檔 Blob 容器為 `https://storagesample.blob.core.windows.net/$logs/blob/2015/01/08/0800`。 此容器中的個別 Blob 會循序命名，並以 `000000.log` 開頭。

您可以使用 AzCopy 命令列工具，將這些伺服器端記錄檔下載至您在本機電腦上選擇的位置。 例如，您可以使用下列命令，將發生於 2015 年 1 月 2 日的 Blob 作業的記錄檔下載至 `C:\Temp\Logs\Server` 資料夾；以您的儲存體帳戶名稱取代 `<storageaccountname>`，並以您的帳戶存取金鑰取代 `<storageaccountkey>`：

    AzCopy.exe /Source:http://<storageaccountname>.blob.core.windows.net/$logs /Dest:C:\Temp\Logs\Server /Pattern:"blob/2015/01/02" /SourceKey:<storageaccountkey> /S /V

AzCopy 可供下載 [Azure 下載](http://azure.microsoft.com/downloads/) 頁面。 如需使用 AzCopy 的詳細資訊，請參閱 [如何搭配使用 AzCopy 與 Microsoft Azure 儲存體](storage-use-azcopy.md)。

如需下載伺服器端記錄檔的詳細資訊，請參閱 [啟用儲存體記錄和存取記錄檔資料](http://msdn.microsoft.com/library/azure/dn782840.aspx#DownloadingStorageLogginglogdata)。 

## 使用 Microsoft Message Analyzer 來分析記錄檔資料

Microsoft Message Analyzer 是在疑難排解與診斷案例中用於擷取、顯示及分析通訊協定訊息流量、事件和其他系統或應用程式訊息的工具。 Message Analyzer 也可讓您載入、彙總和分析記錄檔和儲存的追蹤檔案中的資料。 如需 Message Analyzer 的詳細資訊，請參閱 [Microsoft Message Analyzer 操作指南](http://technet.microsoft.com/library/jj649776.aspx)。

Message Analyzer 包含 Azure 儲存體資產，可協助您分析伺服器、用戶端和網路記錄檔。 在本節中，我們將討論如何使用這些工具來解決儲存記錄檔中低成功百分比的問題。

### 下載並安裝 Message Analyzer 和 Azure 儲存體資產

1. 下載 [Message Analyzer](http://www.microsoft.com/download/details.aspx?id=44226) 從 Microsoft 下載中心，然後執行安裝程式。
2. 啟動 Message Analyzer。
3. 在 **啟動** 頁面上，瀏覽至 **下載**, ，接著篩選 **Azure 儲存體**。 您會看到 Azure 儲存體資產，如下圖所示。
4. 按一下 [ **同步處理所有顯示的項目** 安裝 Azure 儲存體資產。 可用的資產包括： 
    - **Azure 儲存體色彩規則:** Azure 儲存體色彩規則可讓您定義特殊篩選條件，使用色彩、 文字和字型樣式來反白顯示包含在追蹤中的特定資訊的訊息。
    - **Azure 儲存體圖表:** Azure 儲存體圖表是圖形伺服器記錄資料的預先定義的圖表。 請注意，若要在此時使用 Azure 儲存體圖表，您可能只要將伺服器記錄檔載入到 [分析方格]。
    - **Azure 儲存體剖析器:** Azure 儲存體剖析器剖析 Azure 儲存體用戶端、 伺服器和 HTTP 記錄檔以便在分析方格中顯示。
    - **Azure 儲存體篩選條件:** Azure 儲存體篩選條件是預先定義的準則，您可以使用查詢分析方格中的資料。
    - **Azure 儲存體檢:** Azure 儲存體檢視版面配置是預先定義的資料行版本配置和分析方格中的群組。
4. 在您安裝資產之後，請重新啟動 Message Analyzer。

![Message Analyzer 起始頁](./media/storage-e2e-troubleshooting-classic-portal/mma-start-page-1.png)

> [AZURE.NOTE] 安裝所有的 Azure 儲存體資產，本教學課程的目的。

### 將記錄檔匯入 Message Analyzer 中

您可以將所有已儲存的記錄檔 (伺服器端、用戶端和網路) 匯入 Microsoft Message Analyzer 的單一工作階段中進行分析。

1. 在 **檔案** 功能表 Microsoft Message Analyzer 中，按一下 **新的工作階段**, ，然後按一下 [ **空白工作階段**。 在 **新的工作階段** ] 對話方塊中，輸入您的分析工作階段的名稱。 在 **工作階段詳細資料** 面板中，按一下 **檔案** ] 按鈕。 
1. 若要載入 Message Analyzer 所產生的網路追蹤資料，請按一下 **加入檔案**, ，瀏覽至您的 web 追蹤工作階段從儲存.matp 檔案所在的位置，選取.matp 檔案，然後按一下 **開啟**。 
1. 若要載入伺服器端記錄檔資料，請按一下 **加入檔案**, 、 瀏覽到下載伺服器端記錄的位置、 選取您想要分析，並按一下 [時間範圍內的記錄檔 **開啟**。 然後，在 **工作階段詳細資料** 面板中，將 **文字記錄檔設定** 下拉式清單，為每個伺服器端記錄檔 **AzureStorageLog** 以確保 Microsoft Message Analyzer 可以正確剖析記錄檔。
1. 若要載入用戶端記錄檔資料，請按一下 **加入檔案**, 、 瀏覽至儲存您的用戶端記錄檔的位置、 選取您想要分析和按一下的記錄檔 **開啟**。 然後，在 **工作階段詳細資料** 面板中，將 **文字記錄檔設定** 下拉式清單，為每個用戶端記錄檔 **AzureStorageClientDotNetV4** 以確保 Microsoft Message Analyzer 可以正確剖析記錄檔。
1. 按一下 [ **啟動** 中 **新的工作階段** ] 對話方塊，以載入和剖析記錄檔資料。 記錄檔資料會顯示在 Message Analyzer 分析方格中。

下圖顯示使用伺服器、用戶端與網路追蹤記錄檔設定的範例工作階段。

![設定 Message Analyzer 工作階段](./media/storage-e2e-troubleshooting-classic-portal/configure-mma-session-1.png)

請注意，Message Analyzer 會將記錄檔載入記憶體中。 如果您有大量記錄檔資料，您會想要進行篩選，以便 Message Analyzer 發揮最佳效能。

首先，判斷您有興趣檢閱的時間範圍，而此時間範圍越小越好。 在許多情況下，您會想檢閱數分鐘或數小時 (最多) 的期間。 匯入可符合您需求的最小記錄檔集合。

如果您仍有大量的記錄檔資料，您可能會想要指定工作階段篩選條件，以在載入記錄檔資料前進行篩選。 在 **工作階段篩選條件** 方塊中，選取 **程式庫** 按鈕來選擇預先定義的篩選條件; 例如，選擇 **全域時間篩選 I** 從 Azure 儲存體篩選條件以篩選時間間隔。 然後，您可以編輯篩選準則，針對您要查看的間隔指定開始和結束時間戳記。 您也可以篩選特定狀態碼 ；例如，您可以選擇只載入狀態碼為 404 的記錄檔項目。

如需有關記錄檔資料匯入 Microsoft Message Analyzer 的詳細資訊，請參閱 [擷取訊息資料](http://technet.microsoft.com/library/dn772437.aspx) TechNet 上。

### 使用用戶端要求識別碼讓記錄檔資料相互關聯

Azure 儲存體用戶端程式庫會自動為每一項要求產生唯一的用戶端要求識別碼。 這個值會寫入至用戶端記錄檔、伺服器記錄檔和網路追蹤，以便您使用它讓 Message Analyzer 內全部三個記錄檔的資料相互關聯。 請參閱 [用戶端要求 ID](storage-monitoring-diagnosing-troubleshooting.md#client-request-id) 如需有關用戶端要求識別碼。

下列各節說明如何使用預先設定和自訂的版面配置檢視，根據用戶端要求識別碼讓資料相互關聯和分組。

### 選取要顯示在分析方格中的檢視版本配置

適用於 Message Analyzer 的儲存體資產包括 Azure 儲存體檢視版面配置，這些是預先設定的檢視，可用來顯示在不同案例中具有實用群組和資料行的資料。 您也可以建立自訂檢視版面配置並儲存起來以便重複使用。 

下的圖顯示 **檢視版面配置** ] 功能表上，即可選取 **檢視版面配置** 從工具列功能區中。 Azure 儲存體的檢視版本配置之下 **Azure 儲存體** 功能表中的節點。 您可以在 [搜尋] 方塊中搜尋 `Azure Storage`，只篩選 Azure 儲存體檢視版面配置。 您也可以選取檢視版面配置旁邊的星號，使其成為我的最愛並顯示在功能表的頂端。

![檢視版面配置功能表](./media/storage-e2e-troubleshooting-classic-portal/view-layout-menu.png)

一開始先選取 **依 ClientRequestID 和模組分組**。 此檢視版面配置記錄檔資料分組來自所有三個記錄檔先依用戶端要求 ID，再依來源記錄檔 (或 **模組** Message Analyzer 中)。 與此檢視中，您可以向下切入到特定用戶端要求識別碼，並查看所有三個記錄檔中該用戶端要求識別碼的資料。

下圖顯示套用至範例記錄檔資料的這個版面配置檢視，其中顯示部分的資料行。 您可以看見針對特定用戶端要求識別碼，分析方格會顯示來自用戶端記錄檔、伺服器記錄檔和網路追蹤的資料。

![Azure 儲存體檢視版面配置](./media/storage-e2e-troubleshooting-classic-portal/view-layout-client-request-id-module.png)

>[AZURE.NOTE] 不同的記錄檔有不同的資料行，所以分析方格中顯示多個記錄檔中的資料時，某些資料行可能包含給定的資料列的任何資料。 例如，在上圖中， 
用戶端記錄檔資料列不會顯示任何資料 **時間戳記**, ，**TimeElapsed**, ，**來源**, ，和 **目的地** 資料行，因為這些資料行不存在於用戶端記錄檔，但存在於網路追蹤。 同樣地， **時間戳記** 資料行會顯示伺服器記錄檔中，時間戳記資料，但會顯示任何資料，如 **TimeElapsed**, ，**來源**, ，和 **目的地** 資料行，並不屬於伺服器記錄檔。 

除了使用 Azure 儲存體檢視版面配置，您也可以定義和儲存自己的檢視版面配置。 您可以選取其他所需的欄位來進行資料分組，並將群組儲存在您自訂的版面配置中。 

### 將色彩規則套用至分析方格

儲存體資產也包含色彩規則，以便以視覺化方式識別分析方格中不同類型的的錯誤。 預先定義的色彩規則會套用至 HTTP 錯誤，所以只會針對伺服器記錄檔和網路追蹤顯示。

若要套用色彩規則，請選取 **色彩規則** 從工具列功能區中。 您會在功能表中看到 Azure 儲存體色彩規則。 教學課程中，選取 **用戶端錯誤 (400 與 499 之間的 StatusCode)**, ，如下圖所示。

![Azure 儲存體檢視版面配置](./media/storage-e2e-troubleshooting-classic-portal/color-rules-menu.png)

除了使用 Azure 儲存體色彩規則，您也可以定義並儲存自己的色彩規則。

### 群組及篩選記錄檔資料以尋找 400 範圍的錯誤

接下來，我們將群組及篩選記錄檔資料，以找出 400 範圍內的所有錯誤。

1. 找出 **StatusCode** 資料行在分析方格中，以滑鼠右鍵按一下欄標題，並選取 **群組**。
2. 接下來，群組對象 **ClientRequestId** 資料行。 您會看到分析方格中的資料現在依狀態碼和用戶端要求識別碼排列。
1. 如果 [檢視篩選條件] 工具視窗尚未顯示，請予以顯示 。 在工具列功能區中，選取 **工具視窗**, ，然後 **檢視篩選條件**。
2. 若要篩選記錄資料，而僅顯示 400 範圍錯誤，加入下列篩選準則來 **檢視篩選條件** 視窗，然後按一下 **套用**:

        (AzureStorageLog.StatusCode >= 400 && AzureStorageLog.StatusCode <=499) || (HTTP.StatusCode >= 400 && HTTP.StatusCode <= 499)

下圖顯示這個群組和篩選的結果。 展開 **ClientRequestID** 狀態碼 409，群組下方的欄位，例如顯示導致該狀態碼的作業。

![Azure 儲存體檢視版面配置](./media/storage-e2e-troubleshooting-classic-portal/400-range-errors1.png)

套用此篩選條件之後, 您會看到用戶端記錄檔資料列遭到排除，因為用戶端記錄檔不包含 **StatusCode** 資料行。 首先，我們會檢閱伺服器和網路追蹤記錄檔以找出 404 錯誤，然後我們會返回用戶端記錄檔，以檢查導致這些錯誤的用戶端作業。

>[AZURE.NOTE] 您可以篩選 **StatusCode** 資料行，仍然繼續顯示來自所有三個記錄檔，包括用戶端記錄檔中，如果您將運算式加入篩選，其中包含記錄項目，其中的狀態碼為 null 的資料。 若要建構此篩選運算式，請使用：
>
> <code>& #42。StatusCode > = 400 或! & #42。StatusCode</code> 
>
> 此篩選條件會傳回用戶端記錄檔的所有資料列，以及僅限狀態碼大於 400 之伺服器記錄檔和 HTTP 記錄檔的資料列。 如果您將此篩選條件套用到依用戶端要求識別碼和模組分組的檢視版面配置，即可搜尋或捲視所有記錄檔項目，以尋找所有三個記錄檔表示的地方。   

### 篩選記錄檔資料以尋找 404 錯誤

儲存體資產包括預先定義的篩選條件，您可用來縮小記錄檔資料的範圍，以尋找您所尋找的錯誤或趨勢。 接下來，我們將會套用兩個預先定義的篩選條件：一個篩選 404 錯誤的伺服器和網路追蹤記錄檔，一個篩選指定時間範圍內的資料。

1. 如果 [檢視篩選條件] 工具視窗尚未顯示，請予以顯示 。 在工具列功能區中，選取 **工具視窗**, ，然後 **檢視篩選條件**。
2. 在 [檢視篩選器] 視窗中，選取 **程式庫**, ，然後搜尋 `Azure Storage` 以尋找 Azure 儲存體篩條件。 選取篩選器 **所有記錄檔中的 404 (找不到) 訊息**。
3. 顯示 **程式庫** 功能表，然後找出並選取 **全域時間篩選條件**。
4. 將篩選條件中顯示的時間戳記編輯成您想要檢視的範圍。 這有助於縮小要分析的資料範圍。
5. 您的篩選條件看起來應類似下列範例。 按一下 [ **套用** 篩選條件套用至分析方格。

        ((AzureStorageLog.StatusCode == 404 || HTTP.StatusCode == 404)) And 
        (#Timestamp >= 2014-10-20T16:36:38 and #Timestamp <= 2014-10-20T16:36:39)

![Azure 儲存體檢視版面配置](./media/storage-e2e-troubleshooting-classic-portal/404-filtered-errors1.png)

### 分析記錄檔資料

您現已將資料分組並進行篩選，即可檢查產生 404 錯誤的個別要求詳細資料。 在目前的檢視版面配置中，資料會依用戶端要求識別碼分組，再依記錄檔來源分組。 因為我們是在篩選 StatusCode 欄位包含 404 的要求，所以只會看到伺服器和網路追蹤資料，而不會看到用戶端記錄檔資料。

下圖顯示「取得 Blob」作業因為 Blob 不存在而產生 404 的特定要求。 請注意，某些資料行已從標準檢視中移除，以便顯示相關資料。

![已篩選的伺服器和網路追蹤記錄檔](./media/storage-e2e-troubleshooting-classic-portal/server-filtered-404-error.png)

接下來，我們會讓此用戶端要求識別碼與用戶端記錄檔資料相互關聯，以查看用戶端在錯誤發生時所採取的動作。 您可以顯示此工作階段的新分析方格檢視，以檢視在第二個索引標籤中開啟的用戶端記錄檔資料：

1. 首先，將複製的值 **ClientRequestId** 欄位到剪貼簿。 您可以選取任一個資料列、 找出 **ClientRequestId** 欄位，以滑鼠右鍵按一下資料值，然後選擇 **複製 'ClientRequestId'**。 
1. 在工具列功能區中，選取 **新的檢視器**, ，然後選取 **分析方格** 開啟新索引標籤。 新索引標籤會顯示您記錄檔中的所有資料，而不分組、篩選或套用色彩規則。 
2. 在工具列功能區中，選取 **檢視版面配置**, ，然後選取 **所有.NET 用戶端的資料行** 下 **Azure 儲存體** 一節。 此檢視版面配置會顯示用戶端記錄檔中的資料，以及伺服器和網路追蹤記錄檔中的資料。 依預設一定會排序在 **MessageNumber** 資料行。
3. 接下來，搜尋用戶端記錄檔中的用戶端要求識別碼。 在工具列功能區中，選取 **尋找郵件**, ，然後在用戶端要求 ID 來指定自訂篩選器 **尋找** 欄位。 將此語法用於篩選，並指定自己的用戶端要求識別碼：

        *ClientRequestId == "398bac41-7725-484b-8a69-2a9e48fc669a"

Message Analyzer 會找出並選取搜尋準則符合用戶端要求識別碼的第一個記錄檔項目。 在用戶端記錄檔中，有數個項目，每個用戶端要求識別碼，因此您可以將它們群組在 **ClientRequestId** ，方便您更輕鬆地一起查看這些欄位。 下圖顯示用戶端記錄檔中指定之用戶端要求識別碼的所有訊息。 

![顯示 404 錯誤的用戶端記錄檔](./media/storage-e2e-troubleshooting-classic-portal/client-log-analysis-grid1.png)

使用這兩個索引標籤的檢視版面配置中顯示的資料，即可分析要求資料，以判斷造成錯誤的可能原因。 您也可以查看這一個要求之前的要求，看看前一個事件是否可能造成 404 錯誤。 例如，您可以檢閱此用戶端要求識別碼之前的用戶端記錄檔項目，以判斷是否可能已刪除 Blob，或是否因為用戶端應用程式在容器或 Blob 上呼叫 CreateIfNotExists API 而造成。 在用戶端記錄檔中，您可以找到 blob 的位址，在 **描述** 欄位; 在伺服器和網路追蹤記錄檔中，這項資訊會出現在 **摘要** 欄位。

一旦知道造成 404 錯誤之 Blob 的位址，即可進一步調查。 如果您在記錄檔項目中搜尋其他與相同 Blob 上的作業相關聯的訊息，您可以檢查用戶端先前是否刪除了實體。 

## 分析其他類型的儲存體錯誤

您現已熟悉使用 Message Analyzer 來分析記錄檔資料，便可利用檢視版面配置、色彩規則及搜尋/篩選功能，分析其他類型的錯誤。 下表列出一些您可能會遇到的問題以及您可用來尋找問題的篩選準則。 如需建構篩選條件與 Message Analyzer 篩選語言的詳細資訊，請參閱 [篩選訊息資料](http://technet.microsoft.com/library/jj819365.aspx)。

|    若要調查...                                                                                               |    使用篩選運算式…                                                                                                                                                                                                                                        |    運算式套用到記錄檔 (用戶端、 伺服器、 網路、 全部)    |
|------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------|
|    佇列上未預期的訊息傳遞延遲                                                              |    AzureStorageClientDotNetV4.Description 包含 「 正在重試失敗的作業 」。                                                                                                                                                                                |    用戶端                                                        |
|    PercentThrottlingError 的 HTTP 增加                                                                       |    HTTP。Response.StatusCode = = 500 #124; & #124;HTTP。Response.StatusCode = = 503                                                                                                                                                                                          |    網路                                                       |
|    PercentTimeoutError 增加                                                                               |    HTTP。Response.StatusCode = = 500                                                                                                                                                                                                                             |    網路                                                       |
|    PercentTimeoutError 增加 (全部)                                                                         |    * StatusCode = = 500                                                                                                                                                                                                                                          |    全部                                                           |
|    Percentnetworkerror 增加                                                                               |    AzureStorageClientDotNetV4.EventLogEntry.Level < 2                                                                                                                                                                                                          |    用戶端                                                        |
|    HTTP 403 (禁止) 訊息                                                                                 |    HTTP。Response.StatusCode = = 403                                                                                                                                                                                                                             |    網路                                                       |
|    HTTP 404 (找不到) 訊息                                                                                 |    HTTP。Response.StatusCode = = 404                                                                                                                                                                                                                             |    網路                                                       |
|    404 (全部)                                                                                                     |    * StatusCode = = 404                                                                                                                                                                                                                                          |    全部                                                           |
|    共用存取簽章 (SAS) 授權問題                                                             |    作業 AzureStorageLog.RequestStatus = ="SASAuthorizationError"                                                                                                                                                                                                     |    網路                                                       |
|    HTTP 409 (衝突) 訊息                                                                                  |    HTTP。Response.StatusCode = = 409                                                                                                                                                                                                                             |    網路                                                       |
|    409 (全部)                                                                                                     |    * StatusCode = = 409                                                                                                                                                                                                                                          |    全部                                                           |
|    低 PercentSuccess 或分析記錄項目內含具有 ClientOtherErrors 的交易狀態作業    |    作業 AzureStorageLog.RequestStatus = ="ClientOtherError"                                                                                                                                                                                                         |    伺服器                                                        |
|    Nagle 警告                                                                                               |    ((AzureStorageLog.EndToEndLatencyMS-AzureStorageLog.ServerLatencyMS) > (AzureStorageLog.ServerLatencyMS * 1.5)) 和 (AzureStorageLog.RequestPacketSize < 1460年) 和 (AzureStorageLog.EndToEndLatencyMS-AzureStorageLog.ServerLatencyMS > = 200)        |    伺服器                                                        |
|    在伺服器和網路中的時間範圍內的記錄                                                                    |    #時間戳記 > = 2014年-10-20T16:36:38 和 #Timestamp < = 2014年-10-20T16:36:39                                                                                                                                                                                     |    伺服器、網路                                               |
|    伺服器記錄檔中的時間範圍                                                                                |    AzureStorageLog.Timestamp > = 2014年-10-20T16:36:38 和 AzureStorageLog.Timestamp < = 2014年-10-20T16:36:39                                                                                                                                                     |    伺服器                                                        |


## 後續步驟

如需在 Azure 儲存體中進行端對端案例疑難排解的詳細資訊，請參閱下列資源：

- [監控、診斷及疑難排解儲存體](storage-monitoring-diagnosing-troubleshooting.md)
- [儲存體分析](http://msdn.microsoft.com/library/azure/hh343270.aspx)
- [如何監控儲存體帳戶](storage-monitor-storage-account.md)
- [使用 AzCopy 命令列公用程式傳輸資料](storage-use-azcopy)
- [Microsoft Message Analyzer 操作指南](http://technet.microsoft.com/library/jj649776.aspx)
 
 

