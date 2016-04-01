<properties
   pageTitle="使用 Powershell 在 Azure RemoteApp 中開始使用 | Microsoft Azure"
   description="了解如何使用 Powershell 在 Azure RemoteApp 中開始使用"
   services="remoteapp"
   documentationCenter=""
   authors="guscatalano"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="12/05/2015"
   ms.author="guscatal;spatnaik;elizapo"/>



# 使用 Powershell 在 Azure RemoteApp 中開始使用
=====================================


## 取得 Cmdlet
-------------
首先我們要下載 Azure Powershell cmdlet [這裡](http://go.microsoft.com/?linkid=9811175), ，RemoteApp 包含在其中。

請參閱 Azure RemoteApp 的 cmdlet 說明 [這裡](https://msdn.microsoft.com/library/mt428031.aspx)。

## 設定 Azure Cmdlet 以使用訂用帳戶
------------------
請依照下列 [本指南](../powershell-install-configure.md) 讓您可以使用 cmdlet，針對您的 Azure 訂閱。

## 建立雲端收藏
--------------------
十分簡單，只要執行下列命令：

    New-AzureRemoteAppCollection -Collectionname RAppO365Col1 -ImageName "Office 365 ProPlus (Subscription required)" -Plan Basic -Location "West US" - Description "Office 365 Collection."

上述命令會自動發佈 Microsoft Office 365 應用程式 (Excel、OneNote、Outlook、PowerPoint、Visio 和 Word)。

建立集合可能需要 30 分鐘或更久的時間才能完成。 因此，此命令會傳回可使用的追蹤識別碼，如下所示：


    Get-AzureRemoteAppOperationResult -TrackingId xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx

完成集合之後，您可以使用下列命令，將使用者新增至集合：

    Add-AzureRemoteAppUser -CollectionName RAppO365Col1 -Type microsoftAccount -UserUpn someone@domain.com

大功告成！ 該使用者應該能夠連線到應用程式使用 Azure RemoteApp 用戶端找到 [這裡](https://www.remoteapp.windowsazure.com/)。

## 可用的 Cmdlet
我們還有一些其他命令，將會簡短予以說明：

基本 RemoteApp 集合 cmdlet:

- New-AzureRemoteAppCollection
- Get-AzureRemoteAppCollection
- Set-AzureRemoteAppCollection
- Update-AzureRemoteAppCollection
- Remove-AzureRemoteAppCollection
- Add-AzureRemoteAppUser
- Get-AzureRemoteAppUser
- Remove-AzureRemoteAppUser
- Get-AzureRemoteAppSession
- Disconnect-AzureRemoteAppSession
- Invoke-AzureRemoteAppSessionLogoff
- Send-AzureRemoteAppSessionMessage
- Get-AzureRemoteAppProgram
- Get-AzureRemoteAppStartMenuProgram
- Publish-AzureRemoteAppProgram
- Unpublish-AzureRemoteAppProgram
- Get-AzureRemoteAppCollectionUsageDetails
- Get-AzureRemoteAppCollectionUsageSummary
- Get-AzureRemoteAppPlan

RemoteApp virtual network cmdlets:

- New-AzureRemoteAppVNet
- Get-AzureRemoteAppVNet
- Set-AzureRemoteAppVNet
- Remove-AzureRemoteAppVNet
- Get-AzureRemoteAppVpnDevice
- Get-- AzureRemoteAppVpnDeviceConfigScript
- Reset-AzureRemoteAppVpnSharedKey

RemoteApp template image cmdlets:

- New-AzureRemoteAppTemplateImage
- Get-AzureRemoteAppTemplateImage
- Rename-AzureRemoteAppTemplateImage
- Remove-AzureRemoteAppTemplateImage

Other RemoteApp cmdlets:

- Get-AzureRemoteAppLocation
- Get-AzureRemoteAppWorkspace
- Set-AzureRemoteAppWorkspace
- Get-AzureRemoteAppOperationResult


