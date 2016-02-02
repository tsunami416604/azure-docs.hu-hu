<properties
   pageTitle="使用 PowerShell 來管理服務匯流排和事件中樞資源"
   description="使用 PowerShell 來建立及管理服務匯流排和事件中樞資源"
   services="service-bus"
   documentationCenter=".NET"
   authors="sethmanheim"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/09/2015"
   ms.author="sethm"/>


# 使用 PowerShell 來管理服務匯流排和事件中樞資源

Microsoft Azure PowerShell 是一種指令碼環境，可讓您用來控制及自動化 Azure 服務的部署和管理。 本文說明如何使用 PowerShell，透過本機 Azure PowerShell 主控台來佈建及管理服務匯流排實體，例如命名空間、佇列和事件中樞。

## 必要條件

在開始之前，您將需要下列項目：

- Azure 訂用帳戶。 Azure 是訂閱型平台。 如需
取得訂閱的相關資訊，請參閱 [購買選項 []][], ，[成員優惠 []][], ，或 [[免費試用]][]。

- 具備 Azure PowerShell 的電腦。 如需指示，請參閱 [安裝及設定 Azure PowerShell]][]。

- 大致了解 PowerShell 指令碼、NuGet 封裝和 .NET Framework。

## 包括對服務匯流排之 .NET 組件的參考

可用於管理服務匯流排的 PowerShell Cmdlet 數目有限。 若要佈建不透過現有 cmdlet 公開的實體，您可以使用 PowerShell 中，從服務匯流排.NET 用戶端藉由參考 [服務匯流排 NuGet 封裝]。

首先，請確定指令碼可以找到與 NuGet 封裝一起安裝的 **Microsoft.ServiceBus.dll** 組件。 為了要有使用彈性，指令碼會執行這些步驟：

1. 判斷叫用的路徑。
2. 周遊路徑，直到找到名為的資料夾 `封裝`。 當您安裝 NuGet 封裝時，會建立這個資料夾。
3. 以遞迴方式搜尋 `封裝` 名為組件的資料夾 **Microsoft.ServiceBus.dll**。
4. 參考組件，以供稍後使用這些類型。

以下是如何使用 PowerShell 指令碼實作這些步驟的方式：

```powershell

try
{
    # IMPORTANT: Make sure to reference the latest version of Microsoft.ServiceBus.dll
    Write-Output "Adding the [Microsoft.ServiceBus.dll] assembly to the script..."
    $scriptPath = Split-Path (Get-Variable MyInvocation -Scope 0).Value.MyCommand.Path
    $packagesFolder = (Split-Path $scriptPath -Parent) + "\packages"
    $assembly = Get-ChildItem $packagesFolder -Include "Microsoft.ServiceBus.dll" -Recurse
    Add-Type -Path $assembly.FullName

    Write-Output "The [Microsoft.ServiceBus.dll] assembly has been successfully added to the script."
}

catch [System.Exception]
{
    Write-Error("Could not add the Microsoft.ServiceBus.dll assembly to the script. Make sure you build the solution before running the provisioning script.")
}
```

## 佈建服務匯流排命名空間

當使用服務匯流排命名空間，有兩個指令程式，而不是.NET SDK，您可以使用: [Get-azuresbnamespace] 和 [New-azuresbnamespace]。

本範例會建立幾個區域變數中指令碼。 `$Namespace` 和 `$Location`。

- `$Namespace` 名稱我們想要使用之服務匯流排命名空間。
- `$Location` 識別資料中心中的我們將會佈建命名空間。
- `$CurrentNamespace` 儲存我們擷取 (或建立) 的參考命名空間。

在實際的指令碼， `$Namespace` 和 `$Location` 可以當做參數傳遞。

這部分的指令碼會執行下列作業：

1. 嘗試擷取具有所提供之名稱的服務匯流排命名空間。
2. 如果找到命名空間，它會回報找到的項目。
3. 如果找不到命名空間，它會建立命名空間，然後擷取新建立的命名空間。

    ``` powershell
    $Namespace = "MyServiceBusNS"
    $Location = "West US"

    # Query to see if the namespace currently exists
    $CurrentNamespace = Get-AzureSBNamespace -Name $Namespace

    # Check if the namespace already exists or needs to be created
    if ($CurrentNamespace)
    {
        Write-Output "The namespace [$Namespace] already exists in the [$($CurrentNamespace.Region)] region."
    }
    else
    {
        Write-Host "The [$Namespace] namespace does not exist."
        Write-Output "Creating the [$Namespace] namespace in the [$Location] region..."
        New-AzureSBNamespace -Name $Namespace -Location $Location -CreateACSNamespace false -NamespaceType Messaging
        $CurrentNamespace = Get-AzureSBNamespace -Name $Namespace
        Write-Host "The [$Namespace] namespace in the [$Location] region has been successfully created."
    }
    ```
若要佈建其他服務匯流排實體，建立的執行個體 `NamespaceManager` SDK 中的物件。 您可以使用 [Get-azuresbauthorizationrule] 指令程式來擷取用來提供連接字串的授權規則。 這個範例會將儲存的參考 `NamespaceManager` 執行個體中 `$NamespaceManager` 變數。 稍後會使用指令碼 `$NamespaceManager` 來佈建其他實體。

    ``` powershell
    $sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
    # Create the NamespaceManager object to create the Event Hub
    Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
    $NamespaceManager = [Microsoft.ServiceBus.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
    Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."
    ```

## 佈建其他服務匯流排實體

若要佈建其他實體，例如佇列、 主題和事件中樞，您可以使用 [服務匯流排.NET API]。 本文結尾處會參考更多詳細的範例 (包括其他實體)。

### 建立事件中心

這部分的指令碼會再建立四個區域變數。 這些變數可用來具現化 `EventHubDescription` 物件。 指令碼會執行下列作業：

1. 使用 `NamespaceManager` 物件，請查看所識別的事件中樞是否 `$Path` 存在。
2. 如果不存在，建立 `EventHubDescription` 並傳遞給 `NamespaceManager` 類別 `CreateEventHubIfNotExists` 方法。
3. 判斷事件中樞可用之後, 建立消費者群組使用 `ConsumerGroupDescription` 和 `NamespaceManager`。

    ``` powershell
    $Path  = "MyEventHub"
    $PartitionCount = 12
    $MessageRetentionInDays = 7
    $UserMetadata = $null
    $ConsumerGroupName = "MyConsumerGroup"

    # Check if the Event Hub already exists
    if ($NamespaceManager.EventHubExists($Path))
    {
        Write-Output "The [$Path] event hub already exists in the [$Namespace] namespace."  
    }
    else
    {
        Write-Output "Creating the [$Path] event hub in the [$Namespace] namespace: PartitionCount=[$PartitionCount] MessageRetentionInDays=[$MessageRetentionInDays]..."
        $EventHubDescription = New-Object -TypeName Microsoft.ServiceBus.Messaging.EventHubDescription -ArgumentList $Path
        $EventHubDescription.PartitionCount = $PartitionCount
        $EventHubDescription.MessageRetentionInDays = $MessageRetentionInDays
        $EventHubDescription.UserMetadata = $UserMetadata
        $EventHubDescription.Path = $Path
        $NamespaceManager.CreateEventHubIfNotExists($EventHubDescription);
        Write-Output "The [$Path] event hub in the [$Namespace] namespace has been successfully created."
    }

    # Create the consumer group if it doesn't exist
    Write-Output "Creating the consumer group [$ConsumerGroupName] for the [$Path] event hub..."
    $ConsumerGroupDescription = New-Object -TypeName Microsoft.ServiceBus.Messaging.ConsumerGroupDescription -ArgumentList $Path, $ConsumerGroupName
    $ConsumerGroupDescription.UserMetadata = $ConsumerGroupUserMetadata
    $NamespaceManager.CreateConsumerGroupIfNotExists($ConsumerGroupDescription);
    Write-Output "The consumer group [$ConsumerGroupName] for the [$Path] event hub has been successfully created."
    ```

### 建立佇列

若要建立佇列或主題，請執行上一節的命名空間檢查。

    if ($NamespaceManager.QueueExists($Path))
    {
        Write-Output "The [$Path] queue already exists in the [$Namespace] namespace."
    }
    else
    {
        Write-Output "Creating the [$Path] queue in the [$Namespace] namespace..."
        $QueueDescription = New-Object -TypeName Microsoft.ServiceBus.Messaging.QueueDescription -ArgumentList $Path
        if ($AutoDeleteOnIdle -ge 5)
        {
            $QueueDescription.AutoDeleteOnIdle = [System.TimeSpan]::FromMinutes($AutoDeleteOnIdle)
        }
        if ($DefaultMessageTimeToLive -gt 0)
        {
            $QueueDescription.DefaultMessageTimeToLive = [System.TimeSpan]::FromMinutes($DefaultMessageTimeToLive)
        }
        if ($DuplicateDetectionHistoryTimeWindow -gt 0)
        {
            $QueueDescription.DuplicateDetectionHistoryTimeWindow = [System.TimeSpan]::FromMinutes($DuplicateDetectionHistoryTimeWindow)
        }
        $QueueDescription.EnableBatchedOperations = $EnableBatchedOperations
        $QueueDescription.EnableDeadLetteringOnMessageExpiration = $EnableDeadLetteringOnMessageExpiration
        $QueueDescription.EnableExpress = $EnableExpress
        $QueueDescription.EnablePartitioning = $EnablePartitioning
        $QueueDescription.ForwardDeadLetteredMessagesTo = $ForwardDeadLetteredMessagesTo
        $QueueDescription.ForwardTo = $ForwardTo
        $QueueDescription.IsAnonymousAccessible = $IsAnonymousAccessible
        if ($LockDuration -gt 0)
        {
            $QueueDescription.LockDuration = [System.TimeSpan]::FromSeconds($LockDuration)
        }
        $QueueDescription.MaxDeliveryCount = $MaxDeliveryCount
        $QueueDescription.MaxSizeInMegabytes = $MaxSizeInMegabytes
        $QueueDescription.RequiresDuplicateDetection = $RequiresDuplicateDetection
        $QueueDescription.RequiresSession = $RequiresSession
        if ($EnablePartitioning)
        {
            $QueueDescription.SupportOrdering = $False
        }
        else
        {
            $QueueDescription.SupportOrdering = $SupportOrdering
        }
        $QueueDescription.UserMetadata = $UserMetadata
        $NamespaceManager.CreateQueue($QueueDescription);
    }

### 建立主題

    if ($NamespaceManager.TopicExists($Path))
    {
        Write-Output "The [$Path] topic already exists in the [$Namespace] namespace."
    }
    else
    {
        Write-Output "Creating the [$Path] topic in the [$Namespace] namespace..."
        $TopicDescription = New-Object -TypeName Microsoft.ServiceBus.Messaging.TopicDescription -ArgumentList $Path
        if ($AutoDeleteOnIdle -ge 5)
        {
            $TopicDescription.AutoDeleteOnIdle = [System.TimeSpan]::FromMinutes($AutoDeleteOnIdle)
        }
        if ($DefaultMessageTimeToLive -gt 0)
        {
            $TopicDescription.DefaultMessageTimeToLive = [System.TimeSpan]::FromMinutes($DefaultMessageTimeToLive)
        }
        if ($DuplicateDetectionHistoryTimeWindow -gt 0)
        {
            $TopicDescription.DuplicateDetectionHistoryTimeWindow = [System.TimeSpan]::FromMinutes($DuplicateDetectionHistoryTimeWindow)
        }
        $TopicDescription.EnableBatchedOperations = $EnableBatchedOperations
        $TopicDescription.EnableExpress = $EnableExpress
        $TopicDescription.EnableFilteringMessagesBeforePublishing = $EnableFilteringMessagesBeforePublishing
        $TopicDescription.EnablePartitioning = $EnablePartitioning
        $TopicDescription.IsAnonymousAccessible = $IsAnonymousAccessible
        $TopicDescription.MaxSizeInMegabytes = $MaxSizeInMegabytes
        $TopicDescription.RequiresDuplicateDetection = $RequiresDuplicateDetection
        if ($EnablePartitioning)
        {
            $TopicDescription.SupportOrdering = $False
        }
        else
        {
            $TopicDescription.SupportOrdering = $SupportOrdering
        }
        $TopicDescription.UserMetadata = $UserMetadata
        $NamespaceManager.CreateTopic($TopicDescription);
    }

## 後續步驟

此文章會提供您使用PowerShell 佈建服務匯流排實體的基本大綱。 雖然可用來管理服務匯流排訊息實體的 PowerShell Cmdlet 數目有限，但是藉由參考 Microsoft.ServiceBus.dll assembly 組件，您也可以在 PowerShell 指令碼中，執行可使用 .NET 用戶端程式庫執行的幾乎所有作業。

這些部落格文章中有更多詳細的範例可用：

- [如何建立服務匯流排佇列、 主題和訂閱使用 PowerShell 指令碼](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
- [如何建立服務匯流排命名空間與事件中樞使用 PowerShell 指令碼](http://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)

也有一些現成的指令碼可供下載：

- [服務匯流排 PowerShell 指令碼](https://code.msdn.microsoft.com/Service-Bus-PowerShell-a46b7059)




[purchase options]: http://azure.microsoft.com/pricing/purchase-options/ 
[member offers]: http://azure.microsoft.com/pricing/member-offers/ 
[free trial]: http://azure.microsoft.com/pricing/free-trial/ 
[service bus nuget package]: http://www.nuget.org/packages/WindowsAzure.ServiceBus/ 
[get-azuresbnamespace]: https://msdn.microsoft.com/library/azure/dn495122.aspx 
[new-azuresbnamespace]: https://msdn.microsoft.com/library/azure/dn495165.aspx 
[get-azuresbauthorizationrule]: https://msdn.microsoft.com/library/azure/dn495113.aspx 
[.net api for service bus]: https://msdn.microsoft.com/en-us/library/azure/mt419900.aspx 
[install and configure azure powershell]: ../install-configure-powershell.md 

