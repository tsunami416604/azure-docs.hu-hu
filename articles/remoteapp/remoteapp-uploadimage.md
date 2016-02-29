
<properties
    pageTitle="上傳 Azure RemoteApp 的自訂映像 | Microsoft Azure"
    description="了解如何上傳 Azure RemoteApp 的自訂映像"
    services="remoteapp"
    documentationCenter=""
    authors="ericorman"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/05/2015"
    ms.author="ericor" />



# 上傳 Azure RemoteApp 的自訂映像

現在，您已建立自訂範本映像或已更新變更，您必須將該映像上傳至您的 Azure RemoteApp 映像庫。 使用這些步驟。


## 開始之前

1.      Verify your custom image meets the [image requirements](remoteapp-imagereqs.md) and [application requirements](remoteapp-appreqs.md).
2.      Install the [Azure PowerShell module](../install-configure-powershell.md).

## 逐步解說如何上傳自訂映像

1.      Open Azure Management Portal and navigate to the RemoteApp page.
2.      On the **Template images** tab, click **Upload** at the bottom of the page.
4.      Enter a friendly name for your image and specify the storage account location. Ensure the location is the same location as your RemoteApp collection or a location where you want to create one.
5.      When prompted, download the script to your local PC.
6.      Copy the command parameters in the text box to your clipboard.
7.      Open an elevated Windows PowerShell window.
8.      From the elevated Windows PowerShell window, navigate to the same directory where you downloaded the script.
9.      Paste the copied command and press **Enter**.

    系統便會開始上傳程序，持續時間會取決於許多因素，其中包括您的網路速度和影像的大小

11.    如果因為網路中斷或類似原因導致上傳失敗，您可以繼續您已開始的上傳程序。 若要繼續上傳，您可以再次使用相同的命令列來執行指令碼。

> [AZURE.WARNING] 切勿修改上傳指令碼。 已實作可確保映像符合映像需求和應用程式需求的特定檢查。

## 常見問題

- 請確定使用的是 Windows PowerShell，而不是 Azure PowerShell。 您必須安裝 Azure PowerShell 模組，因為上傳過程中會需要某些特定模組。
- 切勿更改指令碼，該指令碼已提供驗證，方便您使用。
- 如果 vhd 檔案在上傳期間遭到鎖定，請將檔案複製或移動到新位置，並再次嘗試上傳。 有些 Windows 程序可能會阻止上傳。  

