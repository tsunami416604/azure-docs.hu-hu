<properties 
   pageTitle="Azure 自動化 Webhook"
   description="可讓用戶端透過 HTTP 呼叫在 Azure 自動化中啟動 Runbook 的 Webhook。  本文說明如何建立 Webhook，以及如何進行呼叫以啟動 Runbook。"
   services="automation"
   documentationCenter=""
   authors="SnehaGunda"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/07/2015"
   ms.author="bwren;sngun"/>

# Azure 自動化 Webhook

A *webhook* 可讓您透過單一 HTTP 要求，在 Azure 自動化中啟動特定的 runbook。 這可讓外部服務，例如 Visual Studio Team Services、GitHub 或自訂應用程式啟動 Runbook，而不需使用 Azure 自動化 API 實作完整的解決方案。

![Webhook](media/automation-webhooks/webhooks-overview.png)

您可以比較 webhook 與其他方法啟動 runbook 的 [Azure 自動化中啟動 runbook](automation-starting-a-runbook.md)

## Webhook 的詳細資料

下表描述您必須為 Webhook 設定的屬性。

| 屬性 | 說明 |
|:---|:---|
|名稱 | 您可以為 Webhook 提供任何想要的名稱，因為這並不會向用戶端公開。  該名稱僅供您用來識別 Azure 自動化中的 Runbook。 <br>  最佳做法，您應該給予 webhook 的名稱會用到的用戶端相關。 |
|URL |Webhook 的 URL 是一種唯一性的位址，即用戶端用來呼叫 HTTP POST 以啟動連結至 Webhook的 Runbook。  當您建立 Webhook 時其會自動產生。  您無法指定自訂 URL。 <br> <br>  URL 包含可讓協力廠商系統不需進一步驗證來叫用 runbook 的安全性權杖。 基於這個原因，應該將其視為一種密碼。  基於安全性原因，您僅能於建立 Webhook 時在 Azure preview 入口網站中檢視 URL。 您應該在安全的位置記下 URL 以供日後使用。   |
|到期日期 | 例如憑證，每個 Webhook 都會有一個到期日期，到期後便無法再使用。  建立 Webhook 後無法變更此到期日期，且 Webhook 也無法於到期日期後再次啟用。  在此情況下，您必須建立另一個 Webhook 以取代目前的 Webhook，並更新用戶端以使用新的 Webhook。 |
| 已啟用 | 建立 Runbook 時 Webhook 會預設為啟用。  如果您將其設定為 [停用]，則任何用戶端皆無法使用。  您可以設定 **啟用** 會建立當您建立 webhook 或後的任何時候它的屬性。 |


### 參數
Webhook 可以定義由該 Webhook 啟動 Runbook 時所使用的 Runbook 參數值。 Webhook 必須包含任何 Runbook 的強制參數值，且可能包含選擇性的參數值。  多個 Webhook 連結至單一 Runbook 時，可以個別使用不同的參數值。

當用戶端使用 Webhook 啟動 Runbook 時，其無法覆寫 Webhook 中定義的參數值。  若要從用戶端接收資料，runbook 可以接受單一參數，稱之為 **$WebhookData** 的型別 [object] 將包含用戶端在 POST 要求中包含的資料。 

![Webhookdata](media/automation-webhooks/webhookdata.png)

 **$WebhookData** 物件具有下列屬性 ︰

| 屬性 | 說明 |
|:--- |:---|
| WebhookName | Webhook 的名稱。  |
| RequestHeader | 雜湊表包含傳入 POST 要求的標頭。 |
| RequestBody | 傳入 POST 要求的本文。  這會保留任何的格式，如字串、JSON、XML，或表單已編碼的資料。 Runbook 必須撰寫用來搭配預期的資料格式。|


不支援需要 webhook 的組態 **$WebhookData** 參數，且 runbook 不需要接受其。  若 Runbook 並未定義參數，則會忽略從用戶端所傳送要求的任何詳細資料。

若您在建立 Webhook 時指定 $WebhookData 的值，即使用戶端在要求本文中並未包含任何資料，該值將會在 Webhook 使用用戶端 POST 要求的資料來啟動 Runbook 時遭到覆寫。  若您使用 Webhook 以外的方式啟動具有 $WebhookData 的 Runbook，您可以提供可讓 Runbook 識別的 $WebhookData 值。  這個值應該是具有相同的物件 [屬性](#details-of-a-webhook) 為 $Webhookdata，讓 runbook 可以正確運作與其如同它已由 webhook 所傳遞的實際 Requestbody 處理。

例如，如果要從 Azure 入口網站啟動下列 Runbook，並想要傳遞一些 WebhookData 範例進行測試，因為 WebhookData 為物件，所以應以 UI 中的 JSON 加以傳遞。

![來自 UI 的 WebhookData 參數](media/automation-webhooks/WebhookData-parameter-from-UI.png)

針對上述 Runbook，如果您對 WebhookData 參數具有下列屬性：

1. WebhookName: *MyWebhook*
2. RequestHeader: *= 測試使用者*
3. RequestBody: *["VM1"，"VM2"]*

則應對 WebhookData 參數傳遞下列 UI 中的 JSON 值：  

* {"WebhookName":"MyWebhook"、"RequestHeader": {"From": 「 測試使用者 」}，"RequestBody":"[\"VM1\",\"VM2\"]"}

![啟動來自 UI 的 WebhookData 參數](media/automation-webhooks/Start-WebhookData-parameter-from-UI.png)


>[AZURE.NOTE] 使用 runbook 工作進行記錄所有的輸入參數的值。  這表示，任何由用戶端在 Webhook 要求中提供的輸入將會進行記錄，並可讓任何有自動化工作存取權的人使用。  基於這個原因，您在 Webhook 呼叫中包含機密資訊時應該特別謹慎。

## 安全性

Webhook 的安全性仰賴其 URL 的隱私權，其中包含可允許其接受叫用的安全性權杖。 只要針對正確的 URL 進行要求，Azure 自動化就不會對要求執行任何驗證。 基於這個原因，若不使用驗證要求的替代方式時，Webhooks 不應用於執行高度機密功能的 Runbook。

您可以加入邏輯來判斷它是否藉由呼叫 webhook 的 runbook 內 **WebhookName** $WebhookData 參數的屬性。 Runbook 無法執行進一步的驗證中的特定資訊尋找 **RequestHeader** 或 **RequestBody** 屬性。

另一個策略是當 Runbook 收到 Webhook 要求時，使其執行某些外部條件的驗證。  例如，當 GitHub 儲存機制出現新的認可時，考慮使用由 GitHub 呼叫的 Runbook。  Runbook 可能會連接到 GitHub 以驗證在繼續之前實際上已出現新的認可。

## 建立 Webhook

使用下列程序以在 Azure Preview 入口網站中建立連結至 Runbook 的新 Webhook。

1. 從 **Runbook 刀鋒視窗** 在 Azure 預覽入口網站中，按一下 runbook]，webhook 便會開始檢視其詳細資料] 分頁。 
3. 按一下 [ **Webhook** 頂端的分頁，來開啟 **新增 Webhook** 刀鋒視窗。 <br>
![Webhook 按鈕](media/automation-webhooks/webhooks-button.png)
4. 按一下 [ **建立新的 webhook** 開啟 **建立 webhook 刀鋒視窗**。
5. 指定 **名稱**, ，**到期日** webhook 以及它是否應該啟用。 請參閱 [webhook 的詳細資料](#details-of-a-webhook) 如需這些屬性。
6. 按一下複製圖示，然後按 Ctrl + C 以複製 Webhook 的 URL。  然後將其記錄在安全的地方。  **一旦您建立 webhook，即無法再次擷取 URL。** <br>
![Webhook URL](media/automation-webhooks/copy-webhook-url.png)
3. 按一下 [ **參數** runbook 的參數提供值。  如果 Runbook 有強制參數，除非提供該值，否則您將無法建立 Webhook。
1. 按一下 [ **建立** 來建立 webhook。


## 使用 Webhook

建立 Webhook 之後若要使用它，您的用戶端應用程式必須使用 Webhook 的 URL 來發出 HTTP POST。  Webhook 的語法將遵循以下列格式。

    http://<Webhook Server>/token?=<Token Value>

用戶端將會從 POST 要求中接收下列其中一個傳回代碼。  

| 代碼 | 文字 | 說明 |
|:---|:----|:---|
| 202 | 已接受 | 已接受要求，且 Runbook 已經成功排入佇列。 |
| 400 | 不正確的要求 | 基於下列原因之一不接受此要求。 <ul> <li>Webhook 已過期。</li> <li>Webhook 已停用。</li> <li>在 URL 中的權杖無效。</li>  </ul>|
| 404 | 找不到 |  基於下列原因之一不接受此要求。 <ul> <li>找不到 webhook。</li> <li>找不到 runbook。</li> <li>找不到帳戶。</li>  </ul> |
| 500 | 內部伺服器錯誤 | URL 有效，但發生錯誤。  請重新提交要求。  |

假設要求成功，Webhook 會回應包含 JSON 格式的工作識別碼，如下所示。 它會包含單一的工作識別碼，但是 JSON 格式允許未來可能的增強功能。

    {"JobIds":["<JobId>"]}  

用戶端無法從 Webhook 判斷 Runbook 的工作何時完成或完成狀態。  它可以判斷這項資訊與另一種方法，例如使用工作識別碼 [Windows PowerShell](http://msdn.microsoft.com/library/azure/dn690263.aspx) 或 [Azure 自動化 API](https://msdn.microsoft.com/library/azure/mt163826.aspx)。

### 範例

下列範例使用 Windows PowerShell 搭配 Webhook 來啟動 Runbook。  請注意，任何可以進行 HTTP 要求的語言都可以使用 Webhook；Windows PowerShell 在這裡僅作為範例使用。

Runbook 預期在要求的主體中有 JSON 格式的虛擬機器清單。 我們也在要求的標頭中，列入了有關於啟動 Runbook 的人員、啟動的日期和時間資訊。      

    $uri = "https://s1events.azure-automation.net/webhooks?token=8ud0dSrSo%2fvHWpYbklW%3c8s0GrOKJZ9Nr7zqcS%2bIQr4c%3d"
    $headers = @{"From"="user@contoso.com";"Date"="05/28/2015 15:47:00"}
    
    $vms  = @([pscustomobject]@{Name="vm01";ServiceName="vm01"})
    $vms += @([pscustomobject]@{Name="vm02";ServiceName="vm02"})
    $body = ConvertTo-Json -InputObject $vms 

    $response = Invoke-RestMethod -Method Post -Uri $uri -Headers $headers -Body $body
    $jobid = ConvertFrom-Json $response 


下圖顯示的標頭資訊 (使用 [Fiddler](http://www.telerik.com/fiddler) 追蹤) 從這項要求。 這包括了 HTTP 要求的標準標頭，除此之外，還有我們加入的自訂「日期」和「時間」標頭。  這些值都可以在 runbook **Webhookdata** 屬性 **Requestbody**。 

![Webhook 按鈕](media/automation-webhooks/webhook-request-headers.png)

下圖顯示要求的主體 (使用 [Fiddler](http://www.telerik.com/fiddler) 追蹤) 可用來在 runbook **RequestBody** 屬性 **Requestbody**。 格式化為 JSON 是因為其格式是要求的主體中所包含之格式。     

![Webhook 按鈕](media/automation-webhooks/webhook-request-body.png)

下圖顯示由 Windows PowerShell 送出的要求以及產生的回應。  工作識別碼從回應中擷取，再轉換為字串。

![Webhook 按鈕](media/automation-webhooks/webhook-request-response.png)

下列範例 Runbook 接受之前的範例要求，並啟動要求主體中指定的虛擬機器。

    workflow Test-StartVirtualMachinesFromWebhook
    {
        param ( 
            [object]$WebhookData
        )

        # If runbook was called from Webhook, WebhookData will not be null.
        if ($WebhookData -ne $null) {   
            
            # Collect properties of WebhookData
            $WebhookName    =   $WebhookData.WebhookName
            $WebhookHeaders =   $WebhookData.RequestHeader
            $WebhookBody    =   $WebhookData.RequestBody
            
            # Collect individual headers. VMList converted from JSON.
            $From = $WebhookHeaders.From
            $VMList = ConvertFrom-Json -InputObject $WebhookBody
            Write-Output "Runbook started from webhook $WebhookName by $From."
            
            # Authenticate to Azure resources
            $Cred = Get-AutomationPSCredential -Name 'MyAzureCredential'
            Add-AzureAccount -Credential $Cred
            
            # Start each virtual machine
            foreach ($VM in $VMList)
            {
                $VMName = $VM.Name
                Write-Output "Starting $VMName"
                Start-AzureVM -Name $VM.Name -ServiceName $VM.ServiceName
            }
        }
        else {
            Write-Error "Runbook mean to be started only from webhook." 
        } 
    }


## 啟動 Runbook 以回應 Azure 警示

啟用 Webhook 的 runbook 可用來回應 [Azure 警示](Azure-portal/insights-receive-alert-notifications.md)。 Azure 中的資源可以透過 Azure 警示的協助，藉由收集如效能、可用性和使用方式的統計資料進行監視。 您可以收到根據監視您 Azure 資源的計量或事件的警示，目前自動化帳戶僅支援度量。 指定的度量值超過指定閾值時，或設定的事件會觸發然後若要解決的警示傳送通知給服務系統管理員或共同管理員，如需有關度量和事件，請參閱 [Azure 警示](Azure-portal/insights-receive-alert-notifications.md)。

除了使用 Azure 的警示做為通知系統，也可以使用 Runbook 回應警示。 Azure 自動化可讓您使用 Azure 警示執行啟用 Webhook 的 Runbook。 當計量超過設定的閾值時，警示規則就會變成作用中，並且觸發自動化 Webhook，連帶執行 Runbook。

![Webhook](media/automation-webhooks/webhook-alert.jpg)

### 警示內容

考量如虛擬機器的 Azure 資源，這台電腦的 CPU 使用率是其中一個關鍵效能計量。 如果 CPU 使用率是 100% 或者在一段長時間超過某個量，您可能會想要重新啟動虛擬機器以修正問題。 這可以藉由設定虛擬機器的警示規則來解決，而此規則可以使用 CPU 百分比做為計量。 這裡的 CPU 百分比只是一個範例，但是還有許多您可以對 Azure 資源設定的其他計量，重新啟動虛擬機器是修正此問題所採取的一個動作，您可以設定 Runbook 採取其他動作。

當此警示規則變成作用中並且觸發啟用 Webhook 的 Runbook 時，會傳送警示內容到 Runbook。 [警示內容](Azure-portal/insights-receive-alert-notifications.md) 包含詳細資料包括 **SubscriptionID**, ，**ResourceGroupName**, ，**ResourceName**, ，**ResourceType**, ，**ResourceId** 和 **時間戳記** 所需的 runbook，以辨識的它將會採取動作的資源。 警示內容中的內文部分內嵌 **Requestbody** 物件傳送至 runbook，且可存取與 **Webhook.RequestBody** 屬性


### 範例

建立 Azure 虛擬機器在您的訂閱和關聯 [監視 CPU 百分比度量的警示](Azure-portal/insights-receive-alert-notifications.md)。 建立警示時，請確定以建立 Webhook 時所產生的 Webhook URL 填入 Webhook 欄位。

下列範例 Runbook 會在警示規則變成作用中時觸發，而且會收集 Runbook 識別在上面採取動作的資源所需的警示內容參數。

    workflow Invoke-RunbookUsingAlerts
    {
        param (     
            [object]$WebhookData 
        ) 

        # If runbook was called from Webhook, WebhookData will not be null.
        if ($WebhookData -ne $null) {   
            # Collect properties of WebhookData. 
            $WebhookName    =   $WebhookData.WebhookName 
            $WebhookBody    =   $WebhookData.RequestBody 
            $WebhookHeaders =   $WebhookData.RequestHeader 

            # Outputs information on the webhook name that called This 
            Write-Output "This runbook was started from webhook $WebhookName." 

            
            # Obtain the WebhookBody containing the AlertContext 
            $WebhookBody = (ConvertFrom-Json -InputObject $WebhookBody) 
            Write-Output "`nWEBHOOK BODY" 
            Write-Output "=============" 
            Write-Output $WebhookBody 

            # Obtain the AlertContext     
            $AlertContext = [object]$WebhookBody.context

            # Some selected AlertContext information 
            Write-Output "`nALERT CONTEXT DATA" 
            Write-Output "===================" 
            Write-Output $AlertContext.name 
            Write-Output $AlertContext.subscriptionId 
            Write-Output $AlertContext.resourceGroupName 
            Write-Output $AlertContext.resourceName 
            Write-Output $AlertContext.resourceType 
            Write-Output $AlertContext.resourceId 
            Write-Output $AlertContext.timestamp 

            # Act on the AlertContext data, in our case restarting the VM. 
            # Authenticate to your Azure subscription using Organization ID to be able to restart that Virtual Machine. 
            $cred = Get-AutomationPSCredential -Name "MyAzureCredential" 
            Add-AzureAccount -Credential $cred 
            Select-AzureSubscription -subscriptionName "Visual Studio Ultimate with MSDN" 
          
            #Check the status property of the VM
            Write-Output "Status of VM before taking action"
            Get-AzureVM -Name $AlertContext.resourceName -ServiceName $AlertContext.resourceName
            Write-Output "Restarting VM"

            # Restart the VM by passing VM name and Service name which are same in this case
            Restart-AzureVM -ServiceName $AlertContext.resourceName -Name $AlertContext.resourceName 
            Write-Output "Status of VM after alert is active and takes action"
            Get-AzureVM -Name $AlertContext.resourceName -ServiceName $AlertContext.resourceName
        } 
        else  
        { 
            Write-Error "This runbook is meant to only be started from a webhook."  
        }  
    }

 

## 相關文章

- [啟動 Runbook](automation-starting-a-runbook.md)
- [檢視 Runbook 工作的狀態](automation-viewing-the-status-of-a-runbook-job.md)
- [使用 Azure 自動化對 Azure 警示採取動作](https://azure.microsoft.com/blog/using-azure-automation-to-take-actions-on-azure-alerts/)


