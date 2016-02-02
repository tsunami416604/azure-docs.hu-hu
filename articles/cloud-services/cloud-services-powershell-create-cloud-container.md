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

1. 安裝 Microsoft Azure PowerShell cmdlet [下載 Azure PowerShell](http://go.microsoft.com/?linkid=9811175&clcid=0x409)。如需進一步的指示安裝 Azure PowerShell cmdlet 及連接至您的 Azure 訂用帳戶，請參閱 [如何安裝和設定 Azure PowerShell](../powershell-install-configure.md)。

2. **New-AzureService** 是用來建立空白雲端服務容器的 Cmdlet。

    ```
    New-azureservice [-ServiceName] <String> [-AffinityGroup] <String> [[-標籤] <String>] [[-描述] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
    New-azureservice [-ServiceName] <String> [-位置] <String> [[-標籤] <String>] [[-描述] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
```

   An example invoking the cmdlet is:
```
New-AzureService -ServiceName "mytestcloudservice" -Location "North Central US" -Label "mytestcloudservice"
```

3. For more information about creating Azure Cloud Service, please run:
```
Get-help New-AzureService
```

4. 後續步驟：

   - 若要管理雲端服務部署，請參閱 [Get-azureservice](https://msdn.microsoft.com/library/azure/dn495131.aspx), ，[Remove-azureservice](https://msdn.microsoft.com/library/azure/dn495120.aspx), ，和 [Set-azureservice](https://msdn.microsoft.com/library/azure/dn495242.aspx) 命令。 另請參閱 [如何設定雲端服務](cloud-services-how-to-configure.md)

    - 若要將您的雲端服務專案發佈至 Azure，請參閱 **PublishCloudService.ps1** 程式碼範例從 [Azure 中雲端服務的連續傳遞](cloud-services-dotnet-continuous-delivery.md)






