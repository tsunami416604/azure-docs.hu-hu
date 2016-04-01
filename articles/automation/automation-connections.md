<properties 
   pageTitle="Azure 自動化中的連接資產 |Microsoft Azure"
   description="Azure 自動化中的連接資產包含從 Runbook 或 DSC 設定連接到外部服務或應用程式所需的資訊。 這篇文章說明連接的詳細資料，以及如何以文字和圖形化編寫形式加以使用。"
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

# Azure 自動化中的連接資產

自動化連接資產包含從 Runbook 或 DSC 設定連接到外部服務或應用程式所需的資訊。 除了連接資訊，例如 URL 或連接埠等，這可能包括驗證所需的資訊，例如使用者名稱和密碼。 連接的值會將連接到特定應用程式的所有屬性放在一個資產中，而不是建立多個變數。 使用者可以在一個地方編輯連接的值，而您可以將連接的名稱在單一參數中傳遞至 Runbook 或 DSC 設定。 連接的屬性可存取 runbook 中使用 DSC 組態 **Get-automationconnection** 活動。

當您建立連接時，您必須指定 *連線類型*。 連接類型是定義一組屬性的範本。 連接會定義在其連接類型中定義的每一個屬性的值。 連接類型是在整合模組中加入 Azure 自動化或使用建立 [Azure 自動化 API](http://msdn.microsoft.com/library/azure/mt163818.aspx)。 當您建立連接時，唯一可用的連接類型是安裝在您的自動化帳戶中的那些。

>[AZURE.NOTE] 在 Azure 自動化中的安全資產包括認證、 憑證、 連接和加密的變數。 這些資產都會經過加密，並使用為每個自動化帳戶產生的唯一索引鍵儲存在 Azure 自動化中。 這個索引鍵是由主要憑證加密，並且儲存在 Azure 自動化中。 儲存安全資產之前，會使用主要憑證解密自動化帳戶的金鑰，然後用來加密資產。

## Windows PowerShell Cmdlet

下表中的 cmdlet 用來建立及管理自動化連接使用 Windows PowerShell 在出貨一部分 [Azure PowerShell 模組](../powershell-install-configure.md) 這是可供在自動化 runbook 和 DSC 組態中使用。

|Cmdlet|說明|
|:---|:---|
|[Get-AzureAutomationConnection](http://msdn.microsoft.com/library/dn921828.aspx)|擷取連接。 包含具有連接欄位值的雜湊表。|
|[New-AzureAutomationConnection](http://msdn.microsoft.com/library/dn921825.aspx)|建立新連接。|
|[Remove-AzureAutomationConnection](http://msdn.microsoft.com/library/dn921827.aspx)|移除現有的連接。|
|[Set-AzureAutomationConnectionFieldValue](http://msdn.microsoft.com/library/dn921826.aspx)|設定現有連接的特定欄位的值。|

## 活動

下表中的活動是用來存取 Runbook 或 DSC 設定中的連接。

|活動|說明|
|---|---|
|Get-AutomationConnection|取得要使用的連接。 傳回具有連線屬性的雜湊表。|

>[AZURE.NOTE] 您應該避免使用變數中的 – Name 參數 **Get-AutomationConnection** 因為這可以在設計階段使得探索 runbook 或 DSC 組態與連接資產之間的相依性。

## 建立新連接

### 使用 Azure 入口網站建立新的連接

1. 從您的自動化帳戶，按一下 [ **資產** 視窗的頂端。
1. 在視窗的底端，按一下 [ **加入設定**。
1. 按一下 [ **加入連接**。
2. 在 **連接類型** 下拉式清單中，選取您想要建立的連接類型。  精靈會顯示該特定類型的屬性。
1. 完成精靈，然後按一下核取方塊以儲存新連接。


### 使用 Azure 預覽入口網站建立新連接

1. 從您的自動化帳戶，按一下 [ **資產** 部分，以開啟 **資產** 刀鋒視窗。
1. 按一下 [ **連線** 部分，以開啟 **連線** 刀鋒視窗。
1. 按一下 [ **加入連接** 刀鋒視窗的頂端。
2. 在 **類型** 下拉式清單中，選取您想要建立的連接類型。  表單會顯示該特定類型的屬性。
1. 完成表單，然後按一下 [ **建立** 以儲存新的連接。



### 使用 Windows PowerShell 建立新連接

建立新的連接使用 Windows PowerShell [New-azureautomationconnection](http://msdn.microsoft.com/library/dn921825.aspx) 指令程式。 此 cmdlet 具有名為 **ConnectionFieldValues** ，預期會有 [雜湊表](http://technet.microsoft.com/en-us/library/hh847780.aspx) 定義每個連線類型所定義之屬性的值。


下列範例命令會建立新連線 [Twilio](http://www.twilio.com) 這是可讓您傳送和接收簡訊的電話語音服務。  包含 Twilio 連接類型的範例整合模組可用於 [指令碼中心](http://gallery.technet.microsoft.com/scriptcenter/Twilio-PowerShell-Module-8a8bfef8)。  這個連接類型會定義帳戶 SID 和授權權杖的屬性，連接到 Twilio 驗證您的帳戶時會需要。  您必須 [下載此模組](http://gallery.technet.microsoft.com/scriptcenter/Twilio-PowerShell-Module-8a8bfef8) 並將它安裝在您的自動化帳戶，此範例程式碼才能運作。

    $AccountSid = "DAf5fed830c6f8fac3235c5b9d58ed7ac5"
    $AuthToken  = "17d4dadfce74153d5853725143c52fd1"
    $FieldValues = @{"AccountSid" = $AccountSid;"AuthToken"=$AuthToken}

    New-AzureAutomationConnection -AutomationAccountName "MyAutomationAccount" -Name "TwilioConnection" -ConnectionTypeName "Twilio" -ConnectionFieldValues $FieldValues


## 在 Runbook 或 DSC 設定中使用連接

擷取 DSC 組態或 runbook 中連接的 **Get-automationconnection** 指令程式。  此活動會擷取連接中不同欄位的值，並傳回其作為 [雜湊表](http://go.microsoft.com/fwlink/?LinkID=324844) 它然後 runbook 或 DSC 組態中的適當命令搭配使用。

### 文字式 Runbook 範例
下列範例命令顯示如何在上述範例中使用 Twilio 連接從 Runbook 傳送簡訊。  這裡使用的 Send-TwilioSMS 活動有兩個參數集，每個使用不同的方法來向 Twilio 服務驗證。  其中一個使用連接物件而另一個對帳戶 SID 和授權權杖使用個別的參數。  在此範例中會顯示這兩種方法。

    $Con = Get-AutomationConnection -Name "TwilioConnection"
    $NumTo = "14255551212"
    $NumFrom = "15625551212"
    $Body = "Text from Azure Automation."

    #Send text with connection object.
    Send-TwilioSMS -Connection $Con -From $NumFrom -To $NumTo -Body $Body

    #Send text with connection properties.
    Send-TwilioSMS -AccountSid $Con.AccountSid -AuthToken $Con.AuthToken $Con -From $NumFrom -To $NumTo -Body $Body

### 圖形化 Runbook 範例

您將加入 **Get-automationconnection** 活動至圖形化 runbook 程式庫] 窗格中的連接，在圖形化編輯器上按一下滑鼠右鍵，然後選取 **加入至畫布**。

![](media/automation-connections/connection-add-canvas.png)

下圖顯示在圖形化 Runbook 中使用連接的範例。  這是如上所述，用於使用 Twilio 傳送簡訊的相同範例。  這個範例會使用 **UseConnectionObject** 參數集 **Useconnectionobject** 使用連接物件向服務驗證的活動。  A [管線連結](automation-graphical-authoring-intro.md#links-and-workflow) 這裡會使用因為 Connection 參數預期的單一物件。

PowerShell 運算式，用於在值的原因 **至** 而不是常值的參數是此參數預期字串陣列值型別，如此您可以傳送給多個號碼。  PowerShell 運算式可讓您提供單一值或陣列。

![](media/automation-connections/get-connection-object.png)

下圖顯示相同的範例，但會使用與以上所述 **SpecifyConnectionFields** 預期 AccountSid 和 AuthToken 參數，來個別指定，而不是使用連接物件來進行驗證的參數集。  在此情況下，會指定連接的欄位，而不是物件本身。  

![](media/automation-connections/get-connection-properties.png)



## 相關文章

- [圖形化編寫中的連結](automation-graphical-authoring-intro.md#links-and-workflow)
 


