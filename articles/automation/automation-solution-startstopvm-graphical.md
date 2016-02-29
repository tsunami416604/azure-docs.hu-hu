<properties 
    pageTitle="啟動和停止虛擬機器 - Graph | Microsoft Azure"
    description="Azure 自動化解決方案的 PowerShell 工作流程版本，包含可啟動和停止傳統虛擬機器的 Runbook。"
    services="automation"
    documentationCenter=""
    authors="bwren"
    manager="stevenka"
    editor="tysonn" />
<tags 
    ms.service="automation"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="09/25/2015"
    ms.author="bwren" />

# Azure 自動化解決方案 - 啟動和停止虛擬機器

此 Azure 自動化解決方案包含可啟動和停止傳統虛擬機器的 Runbook。  此解決方案可作為下列任何用途：  

- 在您自己的環境中不修改而直接使用 Runbook。 
- 修改 Runbook 來執行自訂的功能。  
- 在整個解決方案中從另一個 Runbook 呼叫 Runbook。 
- 將 Runbook 當作教學課程來了解 Runbook 撰寫概念。 

> [AZURE.SELECTOR]
- [圖形化](automation-solution-startstopvm-graphical.md)
- [PowerShell 工作流程](automation-solution-startstopvm-psworkflow.md)

這是此解決方案的圖形化 Runbook 版本。 也可使用 [PowerShell 工作流程 runbook](automation-solutions-startstopvm-psworkflow.md)。

## 取得解決方案

此解決方案包含兩個可以從下列連結下載的圖形化 Runbook。  請參閱 [PowerShell 工作流程版本](automation-solutions-startstopvm-psworkflow.md) 此解決方案的 PowerShell 工作流程 runbook 的連結。


| Runbook | 連結 | 類型 | 說明 |
|:---|:---|:---|:---|
| StartAzureClassicVM | [啟動 Azure 傳統 VM 圖形化 Runbook](https://gallery.technet.microsoft.com/scriptcenter/Start-Azure-Classic-VM-c6067b3d) | 圖形化 | 啟動 Azure 訂用帳戶中的所有傳統虛擬機器，或具有特定服務名稱的所有虛擬機器。 |
| StopAzureClassicVM | [停止 Azure 傳統 VM 圖形化 Runbook](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-Classic-VM-397819bd) | 圖形化 | 停止自動化帳戶中的所有虛擬機器，或具有特定服務名稱的所有虛擬機器。  |


## 安裝解決方案

### 1.安裝 Runbook

下載 runbook 之後, 您可以匯入它們使用中的程序 [圖形化 runbook 程序](automation-graphical-authoring-intro.md#graphical-runbook-procedures)。

### 2.檢閱描述和需求
Runbook 包含的活動 **讀我檔案** ，包含描述及所需的資產。  您可以選取檢視這項資訊 **讀我檔案** 活動，然後 **工作流程指令碼** 參數。  您也可以從這份文件取得相同的資訊。 

### 3.設定資產
Runbook 需要下列資產，您必須建立這些資產並填入適當的值。  名稱是預設值。  您可以使用資產有不同的名稱，如果您指定這些名稱在 [輸入參數](#using-the-solution) 當您啟動 runbook。

|資產類型 |預設名稱 |描述 |
|:---|:---|:---|:---|
| [認證](automation-credentials.md) |AzureCredential |包含具有啟動和停止 Azure 訂用帳戶中的虛擬機器的權限的帳戶認證。  |
| [變數](automation-variables.md) |AzureSubscriptionId |包含您的 Azure 訂閱的訂閱識別碼。 |

## 使用解決方案

### 參數

每個 runbook 有下列 [輸入參數](automation-starting-a-runbook#runbook-parameters)。  您必須提供任何必要參數的值，另外可根據您的需求，選擇性地提供其他參數的值。

| 參數 | 類型 | 強制 | 說明 |
|:---|:---|:---|:---|
| ServiceName | 字串 | 否 | 如果提供一個值，則會啟動或停止具有該服務名稱的所有虛擬機器。  如果不提供任何值，則會啟動或停止 Azure 訂用帳戶中的所有傳統虛擬機器。 |
| AzureSubscriptionIdAssetName | 字串 | 否 | 包含名稱的 [變數資產](#installing-the-solution) ，其中包含您的 Azure 訂閱的訂閱識別碼。  如果您未指定值， *AzureSubscriptionId* 用。  |
| AzureCredentialAssetName | 字串 | 否 | 包含名稱的 [認證資產](#installing-the-solution) ，其中包含要使用的 runbook 的認證。  如果您未指定值， *AzureCredential* 用。  |

### 啟動 Runbook

您可以使用任何一種方法在 [Azure 自動化中啟動 runbook](automation-starting-a-runbook.md) 其中一個 runbook 啟動此解決方案中。

下列範例命令會使用 Windows PowerShell 執行 **StartAzureClassicVM** 啟動的所有虛擬機器與服務名稱 *MyVMService*。

    $params = @{"ServiceName"="MyVMService"}
    Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "StartAzureClassicVM" –Parameters $params

### 輸出

Runbook 將 [輸出一則訊息](automation-runbook-output-and-messages.md) ，指出是否已成功提交開始或停止指示每個虛擬機器。  您可以在輸出中尋找特定的字串，以判斷每一個 Runbook 的結果。  下表列出可能的輸出字串。

| Runbook | 條件 | 訊息 |
|:---|:---|:---|
| StartAzureClassicVM | 虛擬機器已在執行中  | MyVM 已在執行中 |
| StartAzureClassicVM | 成功提交虛擬機器的啟動要求 | MyVM 已啟動 |
| StartAzureClassicVM | 虛擬機器的啟動要求失敗  | MyVM 無法啟動 |
| StopAzureClassicVM | 虛擬機器已在執行中  | MyVM 已停止 |
| StopAzureClassicVM | 成功提交虛擬機器的啟動要求 | MyVM 已啟動 |
| StopAzureClassicVM | 虛擬機器的啟動要求失敗  | MyVM 無法啟動 |


以下是使用的映像 **StartAzureClassicVM** 為 [子 runbook](automation-child-runbooks.md) 範例圖形化 runbook 中。  這使用下表中的條件式連結。

| 連結 | 準則 |
|:---|:---|
| 成功連結 | $ActivityOutput ['StartAzureClassicVM']-例如"\ * 已啟動 」    |
| 錯誤連結   | $ActivityOutput ['StartAzureClassicVM']-notlike"\ * 已啟動 」 |

![子 Runbook 範例](media/automation-solution-startstopvm/graphical-childrunbook-example.png)


## 詳細分解圖

以下是此解決方案中所有 Runbook 的詳細分解圖。  您可以使用這項資訊來自訂 Runbook，或只是從中學習撰寫您自己的解決方案。
 

### 驗證

![驗證](media/automation-solution-startstopvm/graphical-authentication.png)

Runbook 活動設定以開始 [認證](automation-configuring.md#configuring-authentication-to-azure-resources) 和 Azure 訂用帳戶將用於 runbook 的其餘部分。

前兩個活動， **取得訂閱識別碼** 和 **取得 Azure 認證**, ，擷取 [資產](#installing-the-solution) ，由兩個活動。  這些活動可以直接指定資產，但需要資產名稱。  因為我們會讓使用者能夠指定這些名稱在 [輸入參數](#using-the-solution), ，我們需要這些活動的輸入參數所指定的名稱與擷取資產。

**Add-azureaccount** 設定將用於 runbook 的其餘部分的認證。  它會從擷取認證資產 **取得 Azure 認證** 必須能夠存取啟動和停止 Azure 訂用帳戶中的虛擬機器。  會選取 [訂用帳戶 **Select-azuresubscription** 使用訂用帳戶 Id，以及從 **取得訂閱識別碼**。

### 取得虛擬機器

![取得 VM](media/automation-solution-startstopvm/graphical-getvms.png)

Runbook 必須決定將會使用的虛擬機器，以及這些虛擬機器是否已啟動或停止 (取決於 Runbook)。   這兩個活動中，其中一個會擷取 VM。  **服務中取得 Vm** 會執行 *ServiceName* runbook 的輸入的參數包含的值。  **取得所有 Vm** 會執行 *ServiceName* runbook 的輸入的參數未包含的值。  每個活動之前的條件式連結會執行此邏輯。

這兩個活動會使用 **Get-azurevm** 指令程式。  **取得所有 Vm** 使用 **ListAllVMs** 參數設定為傳回的所有虛擬機器。  **服務中取得 Vm** 使用 **GetVMByServiceAndVMName** 參數集合，並提供 **ServiceName** 輸入的參數 **ServiceName** 參數。  

### 合併 VM

![合併 VM](media/automation-solution-startstopvm/graphical-mergevms.png)

 **合併 Vm** 活動所提供的輸入 **Start-azurevm** 而且需要名稱和啟動 vm 的服務名稱。  輸入可以來自任何 **取得所有 Vm** 或 **服務中取得 Vm**, ，但 **Start-azurevm** 只能指定一個為其輸入的活動。   

解決方法是建立 **合併 Vm** 執行 **Write-output** 指令程式。   **InputObject** 指令程式的參數是 PowerShell 運算式，結合了先前兩個活動的輸入。  其中只有一個活動會執行，因此只會有一組輸出。  **Start-azurevm** 可以使用該輸出，其輸入參數。 

### 啟動/停止虛擬機器

![啟動 VM](media/automation-solution-startstopvm/graphical-startvm.png) ![停止 VM](media/automation-solution-startstopvm/graphical-stopvm.png)

下一個活動嘗試啟動或停止 runbook 中使用 runbook，根據 **Start-azurevm** 或 **Stop-azurevm**。  活動已加上管線連結，因為它會執行一次傳回的每個物件 **合併 Vm**。  條件式連結會使活動才會執行 *RunningState* 的虛擬機器是 *已停止* 的 **Start-azurevm** 和 *已啟動* 的 **Stop-azurevm**。 如果這不符合條件，然後 **通知已經開始** 或 **通知已經停止** 用來傳送訊息的執行 **Write-output**。

### 傳送輸出

![通知啟動 VM](media/automation-solution-startstopvm/graphical-notifystart.png) ![通知停止 VM](media/automation-solution-startstopvm/graphical-notifystop.png)

Runbook 的最後一個步驟是傳送輸出，而不論是否成功提交每個虛擬機器的啟動或停止要求。 沒有個別 **寫入輸出** 活動，並判斷哪一個值來執行條件式連結。  **通知 VM 啟動** 或 **通知 VM 已停止** 執行時如果 *OperationStatus* 是 *成功*。  如果 *OperationStatus* 是任何其他值，則 **通知失敗，若要啟動** 或 **通知無法停止** 執行。


## 相關文章

- [Azure 自動化中的圖形化編寫](automation-graphical-authoring-intro.md)
- [Azure 自動化中的子 Runbook](automation-child-runbooks.md) 
- [Azure 自動化中的 Runbook 輸出與訊息](automation-runbook-output-and-messages.md)
