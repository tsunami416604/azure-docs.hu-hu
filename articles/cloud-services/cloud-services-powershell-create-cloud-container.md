<properties
   pageTitle="如何使用 Azure PowerShell 命令來建立空白的雲端服務容器"
   description="本文說明如何使用 PowerShell 指令碼建立雲端服務容器，以及與執行雲端服務相關的管理作業"
   services="cloud-services"
   documentationCenter=".net"
   authors="cawaMS"
   manager="paulyuk" 
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="powershell"
   ms.workload="na"
   ms.date="10/14/2015"
   ms.author="cawa"/>

# 如何使用 Azure PowerShell 命令來建立空白的雲端服務容器
1. 安裝 Microsoft Azure PowerShell cmdlet [下載 Azure PowerShell](http://go.microsoft.com/?linkid=9811175&clcid=0x409)。 如需進一步的指示安裝 Azure PowerShell cmdlet 及連接至您的 Azure 訂用帳戶，請參閱 [如何安裝和設定 Azure PowerShell](../powershell-install-configure.md)。

2. **New-azureservice** 是用來建立空白的雲端服務容器的 cmdlet。

    ```
    New-AzureService [-ServiceName] <String> [-AffinityGroup] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
    New-AzureService [-ServiceName] <String> [-Location] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
```

   An example invoking the cmdlet is:
```
New-AzureService -ServiceName "mytestcloudservice" -Location "North Central US" -Label "mytestcloudservice"
```

3. For more information about creating Azure Cloud Service, please run:
```
Get-help New-AzureService
```

4. Next steps:

   - To manage the Cloud Service deployment, please refer to [Get-AzureService](https://msdn.microsoft.com/library/azure/dn495131.aspx), [Remove-AzureService](https://msdn.microsoft.com/library/azure/dn495120.aspx), and [Set-AzureService](https://msdn.microsoft.com/library/azure/dn495242.aspx) commands. Also please refer to [How to Configure Cloud Services](cloud-services-how-to-configure.md)

    - To publish your Cloud Service project to Azure, please refer to **PublishCloudService.ps1** code sample from [Continuous Delivery for Cloud Service in Azure](cloud-services-dotnet-continuous-delivery.md)
 

