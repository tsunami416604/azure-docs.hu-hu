<properties 
    pageTitle="使用 PowerShell 來部署和管理通知中樞" 
    description="如何使用 PowerShell 來進行自動化的通知中樞建立和管理" 
    services="notification-hubs" 
    documentationCenter="" 
    authors="wesmc7777" 
    manager="dwrede" 
    editor="" />

<tags 
    ms.service="notification-hubs" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="powershell" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="12/10/2015" 
    ms.author="wesmc"/>


# 使用 PowerShell 來部署和管理通知中樞

## 概觀

本文將說明如何使用 PowerShell 來建立和管理 Azure 通知中樞。 本主題示範下列一般自動化工作。

+ 建立通知中樞
+ 設定認證

如果您還需要建立新的服務匯流排命名空間通知中心，請參閱 [使用 PowerShell 管理服務匯流排](../service-bus/service-bus-powershell-how-to-provision.md)。

Azure PowerShell 隨附的 Cmdlet 無法直接支援「管理通知中樞」。 從 PowerShell 進行的最佳方法，是參考 Microsoft.Azure.NotificationHubs.dll 組件。 組件隨附於 [Microsoft Azure 通知中樞 NuGet 封裝](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)。


## 必要條件

開始閱讀本文之前，您必須符合下列必要條件：

- Azure 訂閱。 Azure 是訂閱型平台。 如需
取得訂閱的相關資訊，請參閱 [購買選項]
[成員優惠] 或 [免費試用版]。

- 具備 Azure PowerShell 的電腦。 如需指示，請參閱 [安裝和設定 Azure PowerShell]。

- 大致了解 PowerShell 指令碼、NuGet 封裝和 .NET Framework。


## 包括對服務匯流排之 .NET 組件的參考

Azure PowerShell 中的 PowerShell Cmdlet 尚未提供「管理 Azure 通知中樞」。 若要佈建通知中樞，您可以使用.NET 用戶端中提供 [Microsoft Azure 通知中樞 NuGet 封裝](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)。

首先，請確定指令碼可以找到 **Microsoft.Azure.NotificationHubs.dll** 組件，其在 Visual Studio 專案中會以 NuGet 套件的形式安裝。 為了要有使用彈性，指令碼會執行這些步驟：

1. 判斷叫用的路徑。
2. 周遊路徑，直到找到名為的資料夾 `封裝`。 當您安裝 Visual Studio 專案的 NuGet 封裝時，會建立這個資料夾。
3. 以遞迴方式搜尋 `封裝` 名為組件的資料夾 **Microsoft.Azure.NotificationHubs.dll**。
4. 參考組件，以供稍後使用這些類型。

以下是如何使用 PowerShell 指令碼實作這些步驟的方式：

``` powershell

try
{
    # WARNING: Make sure to reference the latest version of Microsoft.Azure.NotificationHubs.dll
    Write-Output "Adding the [Microsoft.Azure.NotificationHubs.dll] assembly to the script..."
    $scriptPath = Split-Path (Get-Variable MyInvocation -Scope 0).Value.MyCommand.Path
    $packagesFolder = (Split-Path $scriptPath -Parent) + "\packages"
    $assembly = Get-ChildItem $packagesFolder -Include "Microsoft.Azure.NotificationHubs.dll" -Recurse
    Add-Type -Path $assembly.FullName

    Write-Output "The [Microsoft.Azure.NotificationHubs.dll] assembly has been successfully added to the script."
}

catch [System.Exception]
{
    Write-Error("Could not add the Microsoft.Azure.NotificationHubs.dll assembly to the script. Make sure you build the solution before running the provisioning script.")
}
```

## 建立 NamespaceManager 類別

若要佈建通知中樞，建立的執行個體 [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.azure.notificationhubs.namespacemanager.aspx) SDK 中的類別。

您可以使用 Azure PowerShell 隨附的 [Get-azuresbauthorizationrule] 指令程式來擷取用來提供連接字串的授權規則。 我們將會儲存參考 `NamespaceManager` 執行個體中 `$NamespaceManager` 變數。 我們將使用 `$NamespaceManager` 佈建通知中樞。

``` powershell
$sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
# Create the NamespaceManager object to create the hub
Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
$NamespaceManager=[Microsoft.Azure.NotificationHubs.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."
```


## 佈建新的通知中樞

若要提供新的通知中心，請使用 [通知中樞.NET API]。

您會在指令碼的這個部分設定四個本機變數。

1. `$Namespace` : 設為命名空間名稱您要建立通知中樞。
2. `$Path` : 將此路徑設定為新的通知中心的名稱。 例如，"MyHub"。
3. `$WnsPackageSid` : 將此值設為您的封裝 SID Windows 應用程式從 [Windows 開發人員中心](http://go.microsoft.com/fwlink/p/?linkid=266582&clcid=0x409)。
4. `$WnsSecretkey`: 設為秘密金鑰，從 Windows 應用程式 [Windows 開發人員中心](http://go.microsoft.com/fwlink/p/?linkid=266582&clcid=0x409)。

這些變數可用以連接命名空間，以及建立新的通知中樞，並將其設定為利用 WNS 認證，為 Windows 應用程式處理 Windows Notification Services (WNS) 通知。 如需取得封裝 SID 和秘密金鑰，請參閱 [開始使用通知中心](notification-hubs-windows-store-dotnet-get-started.md) 教學課程。

+ 指令碼片段使用 `NamespaceManager` 物件，若要查看所識別的通知中樞是否 `$Path` 存在。

+ 如果不存在，指令碼會建立 `NotificationHubDescription` 使用 WNS 認證，並傳遞給 `NamespaceManager` 類別 `CreateNotificationHub` 方法。

``` powershell

$Namespace = "<Enter your namespace>
$Path  = "<Enter a name for your notification hub>"
$WnsPackageSid = "<your package sid>"
$WnsSecretkey = "<enter your secret key>"

$WnsCredential = New-Object -TypeName Microsoft.Azure.NotificationHubs.WnsCredential -ArgumentList $WnsPackageSid,$WnsSecretkey

# Query the namespace
$CurrentNamespace = Get-AzureSBNamespace -Name $Namespace

# Check if the namespace already exists
if ($CurrentNamespace)
{
    Write-Output "The namespace [$Namespace] in the [$($CurrentNamespace.Region)] region was found."

    # Create the NamespaceManager object used to create a new notification hub
    $sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
    Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
    $NamespaceManager = [Microsoft.Azure.NotificationHubs.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
    Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."

    # Check to see if the Notification Hub already exists
    if ($NamespaceManager.NotificationHubExists($Path))
    {
        Write-Output "The [$Path] notification hub already exists in the [$Namespace] namespace."  
    }
    else
    {
        Write-Output "Creating the [$Path] notification hub in the [$Namespace] namespace."
        $NHDescription = New-Object -TypeName Microsoft.Azure.NotificationHubs.NotificationHubDescription -ArgumentList $Path;
        $NHDescription.WnsCredential = $WnsCredential;
        $NamespaceManager.CreateNotificationHub($NHDescription);
        Write-Output "The [$Path] notification hub was created in the [$Namespace] namespace."
    }
}
else
{
    Write-Host "The [$Namespace] namespace does not exist."
}
```




## 其他資源

- [使用 PowerShell 管理服務匯流排](../service-bus/service-bus-powershell-how-to-provision.md)
- [如何建立服務匯流排佇列、 主題和訂閱使用 PowerShell 指令碼](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
- [如何建立服務匯流排命名空間與事件中樞使用 PowerShell 指令碼](http://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)

也有一些現成的指令碼可供下載：
- [服務匯流排 PowerShell 指令碼](https://code.msdn.microsoft.com/windowsazure/Service-Bus-PowerShell-a46b7059)



[purchase options]: http://azure.microsoft.com/pricing/purchase-options/ 
[member offers]: http://azure.microsoft.com/pricing/member-offers/ 
[free trial]: http://azure.microsoft.com/pricing/free-trial/ 
[install and configure azure powershell]: ../install-configure-powershell.md 
[.net api for notification hubs]: https://msdn.microsoft.com/library/azure/mt414893.aspx 
[get-azuresbnamespace]: https://msdn.microsoft.com/library/azure/dn495122.aspx 
[new-azuresbnamespace]: https://msdn.microsoft.com/library/azure/dn495165.aspx 
[get-azuresbauthorizationrule]: https://msdn.microsoft.com/library/azure/dn495113.aspx 

