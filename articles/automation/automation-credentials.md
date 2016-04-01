<properties 
   pageTitle="Azure 自動化中的認證資產 | Microsoft Azure"
   description="Azure 自動化中的認證資產包含可用來驗證由 Runbook 或 DSC 設定存取資源的安全性認證。 本文說明如何建立認證資產和在 Runbook 或 DSC 設定中使用它們。"
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
   ms.date="10/23/2015"
   ms.author="bwren" />

# Azure 自動化中的認證資產

自動化認證資產會保存 [PSCredential](http://msdn.microsoft.com/library/system.management.automation.pscredential)  物件，其中包含安全性認證，例如使用者名稱和密碼。 Runbook 和 DSC 設定可以使用可接受 PSCredential 物件進行驗證的 Cmdlet，否則可能會擷取 PSCredential 物件的使用者名稱和密碼，以對需要驗證的一些應用程式或服務提供。 認證的屬性會安全地儲存在 Azure 自動化中，並可存取 DSC 組態或 runbook [Get-automationpscredential](http://msdn.microsoft.com/library/system.management.automation.pscredential.aspx) 活動。

>[AZURE.NOTE] 在 Azure 自動化中的安全資產包括認證、 憑證、 連接和加密的變數。 這些資產都會經過加密，並使用為每個自動化帳戶產生的唯一索引鍵儲存在 Azure 自動化中。 這個索引鍵是由主要憑證加密，並且儲存在 Azure 自動化中。 儲存安全資產之前，會使用主要憑證解密自動化帳戶的金鑰，然後用來加密資產。 

## Windows PowerShell Cmdlet

下表中的 Cmdlet 是用來透過 Windows PowerShell 建立和管理自動化認證資產。  其隨附於 [Azure PowerShell 模組](../powershell-install-configure.md) 這是可供在自動化 runbook 和 DSC 組態中使用。

|Cmdlet|說明|
|:---|:---|
|[Get-AzureAutomationCredential](http://msdn.microsoft.com/library/dn913781.aspx)|擷取認證資產的相關資訊。 您可以只擷取認證本身從 **Get-automationcredential** 活動。|
|[New-AzureAutomationCredential](http://msdn.microsoft.com/library/azure/jj554330.aspx)|建立新的自動化認證。|
|[Remove-AzureAutomationCredential](http://msdn.microsoft.com/library/azure/jj554330.aspx)|移除自動化認證。|
|[Set-AzureAutomationCredential](http://msdn.microsoft.com/library/azure/jj554330.aspx)|設定現有自動化認證的屬性。|

## Runbook 活動

下表中的活動用來存取中 Runbook 和 DSC 設定的認證。

|活動|說明|
|:---|:---|
|Get-AutomationPSCredential|取得要在 Runbook 或 DSC 設定中使用的認證。 傳回 [System.Management.Automation.PSCredential](http://msdn.microsoft.com/library/system.management.automation.pscredential) 物件。|

>[AZURE.NOTE] 使用變數，您應該避免在 Get-automationpscredential 因為這可以使得探索 runbook 或 DSC 組態之間的相依性，並在設計階段認證資產的 – Name 參數中。

## 建立新認證


### 使用 Azure 入口網站建立新的變數

1. 從您的自動化帳戶，按一下 [ **資產** 視窗的頂端。
1. 在視窗的底端，按一下 [ **加入設定**。
1. 按一下 [ **加入認證**。
2. 在 **認證類型** 下拉式清單中，選取 **PowerShell 認證**。
1. 完成精靈，然後按一下核取方塊以儲存新認證。


### 使用 Azure 預覽入口網站建立新認證

1. 從您的自動化帳戶，按一下 [ **資產** 部分，以開啟 **資產** 刀鋒視窗。
1. 按一下 [ **認證** 部分，以開啟 **認證** 刀鋒視窗。
1. 按一下 [ **新增認證** 刀鋒視窗的頂端。
1. 完成表單，然後按一下 [ **建立** 以儲存新的認證。


### 使用 Windows PowerShell 建立新的 PowerShell 認證

下列範例命令顯示如何建立新的自動化認證。 PSCredential 物件是先建立了名稱和密碼，然後用來建立認證資產。 或者，您可以使用 **Get-credential** cmdlet 來提示您輸入的名稱和密碼。

    $user = "MyDomain\MyUser"
    $pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
    $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
    New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred

## 使用 PowerShell 認證

擷取 DSC 組態或 runbook 中的認證資產 **Get-automationpscredential** 活動。 這會傳回 [PSCredential 物件](http://msdn.microsoft.com/library/system.management.automation.pscredential.aspx) ，您可以在活動或需要 PSCredential 參數的 cmdlet 搭配使用。 您也可以擷取要個別使用的認證物件的屬性。 物件的屬性，使用者名稱和安全的密碼，或是您可以使用 **GetNetworkCredential** 方法來傳回 [NetworkCredential](http://msdn.microsoft.com/library/system.net.networkcredential.aspx) 將提供的密碼不安全的版本的物件。

### 文字式 Runbook 範例

下列範例命令顯示如何在 Runbook 中使用 PowerShell 認證。 在此範例中，會擷取認證及指派給變數的使用者名稱和密碼。

    $myCredential = Get-AutomationPSCredential -Name 'MyCredential'
    $userName = $myCredential.UserName
    $securePassword = $myCredential.Password
    $password = $myCredential.GetNetworkCredential().Password


### 圖形化 Runbook 範例

您將加入 **Get-automationpscredential** 活動至圖形化 runbook 的圖形化編輯器 [文件庫] 窗格中的認證上按一下滑鼠右鍵，然後選取 **加入至畫布**。


![加入認證至畫布](media/automation-credentials/credential-add-canvas.png)

下圖顯示在圖形化 Runbook 中使用認證的範例。  在此情況下，它用來提供 runbook 的 Azure 資源的驗證中所述 [設定對 Azure 資源驗證](#automation-configuring.md)。  第一個活動會擷取可存取 Azure 訂用帳戶的認證。   **Add-azureaccount** 活動會使用此認證來為隨後的任何活動提供驗證。  A [管線連結](automation-graphical-authoring-intro.md#links-and-workflow) 因為 **Get-automationpscredential** 預期單一物件。  

![加入認證至畫布](media/automation-credentials/get-credential.png)

## 在 DSC 中使用 PowerShell 認證
雖然在 Azure 自動化 DSC 組態可以參考使用的認證資產 **Get-automationpscredential**, ，認證資產，也可以傳遞中透過參數，如有需要。 如需詳細資訊，請參閱 [編譯中 Azure 自動化 DSC 組態](automation-dsc-compile.md#credential-assets)。

## 相關文章

- [圖形化編寫中的連結](automation-graphical-authoring-intro.md#links-and-workflow)

 


