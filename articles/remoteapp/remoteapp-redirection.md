<properties
    pageTitle="在 Azure RemoteApp 中使用重新導向 | Microsoft Azure"
    description="了解如何在 RemoteApp 中設定和使用重新導向"
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/05/2015"
    ms.author="elizapo" />

# 在 Azure RemoteApp 中使用重新導向

裝置重新導向可讓您的使用者使用與其本機電腦、手機或平板電腦連接的裝置，來與遠端應用程式互動。l 比方說，如果您透過 Azure RemoteApp 提供 Skype，您的使用者就必須在電腦安裝相機才能使用 Skype。 印表機、麥克風、監視器以及各種不同以 USB 連接的周邊裝置也是如此。

RemoteApp 會利用遠端桌面通訊協定 (RDP) 與 RemoteFX 來提供重新導向功能。

## 依預設會何種重新導向功能？
當您使用 RemoteApp 時，預設會啟用下列重新導向功能。 括號中的資訊顯示 RDP 設定。

- 本機電腦上播放聲音 (**這台電腦上播放**)。 () audiomode:i:0
- 從本機電腦擷取音訊並傳送到遠端電腦 (**記錄從這部電腦**)。 () audiocapturemode:i:1
- 列印到本機印表機 (redirectprinters:i:1)
- COM 連接埠 (redirectcomports:i:1)
- 智慧卡裝置 (redirectsmartcards:i:1)
- 剪貼簿 (能夠複製和貼上) (redirectclipboard:i:1)
- 清除字型平滑處理 (允許字型平滑處理:i:1)
- 所有支援的隨插即用裝置重新導向。 (devicestoredirect: *)

## 還有其他哪些重新導向功能？
預設會停用兩個重新導向選項：

- 磁碟機重新導向 (磁碟機對應)：您本機電腦的磁碟機會與遠端作業階段中的磁碟機對應。 這可讓您在遠端工作階段工作時，儲存或開啟本機磁碟機中的檔案。
- USB 重新導向：您可以在遠端工作階段期間使用與本機電腦連接的 USB 裝置。

## 在 RemoteApp 中變更重新導向設定
您可以使用 Microsoft Azure PowerShell 搭配 SDK，變更集合的裝置重新導向設定。 安裝新的 PowerShell 與 SDK 之後，第一次設定以管理您的訂閱中所述 [如何安裝和設定 Azure PowerShell](../powershell-install-configure.md)。

接著使用和下面類似的命令來設定自訂 RDP 屬性：

    Set-AzureRemoteAppCollection -CollectionName <collection name>  -CustomRdpProperty "drivestoredirect:s:*`nusbdevicestoredirect:s:*"

(請注意， *' n* 做為個別屬性之間的分隔符號。)

若要取得已設定的自訂 RDP 屬性清單，請執行下列 Cmdlet。 請注意，只會將自訂屬性顯示為輸出，而預設屬性則否：  

    Get-AzureRemoteAppCollection -CollectionName <collection name>

當您設定自訂屬性時，每次都要指定所有自訂屬性，不然就會將設定恢復成停用。   

### 常見範例
使用下列 Cmdlet 可啟用磁碟機重新導向：  

    Set-AzureRemoteAppCollection -CollectionName <collection name>  -CustomRdpProperty "drivestoredirect:s:*”

使用這個 Cmdlet 可同時啟用 USB 與磁碟機重新導向：

    Set-AzureRemoteAppCollection -CollectionName <collection name>  -CustomRdpProperty "drivestoredirect:s:*`nusbdevicestoredirect:s:*"

使用這個 Cmdlet 可停用剪貼簿共用：  

    Set-AzureRemoteAppCollection -CollectionName <collection name>  -CustomRdpProperty "redirectclipboard:i:0”

> [AZURE.IMPORTANT] 請務必完全登出集合中的所有使用者 （和非僅中斷連線） 之前測試變更。 若要確保使用者完全登出，請移至 **工作階段** Azure 入口網站集合中索引標籤，然後登出任何已中斷連線或登入的使用者。 有時候可能需要花費數秒的時間，磁碟機才能顯示在工作階段內的檔案總管中。

## 變更 Windows 用戶端的 USB 重新導向設定

如果您想要在與 RemoteApp 連線的電腦上使用 USB 重新導向，必須進行 2 個動作。 1 - 您的系統管理員必須使用 Azure PowerShell 在集合層級啟用 USB 重新導向。 2 - 在您想要使用 USB 重新導向的每個裝置上，都必須啟用允許重新導向的群組原則。 每個想要使用 USB 重新導向的使用者都必須完成這個步驟。

> [AZURE.NOTE] 使用 Azure RemoteApp 的 USB 重新導向只有在 Windows 電腦上執行。

### 為 RemoteApp 集合啟用 USB 重新導向
使用下列 Cmdlet，在集合層級啟用 USB 重新導向：

    Set-AzureRemoteAppCollection -CollectionName <collection_name> -CustomRdpProperty "nusbdevicestoredirect:s:*"

### 為用戶端電腦啟用 USB 重新導向

若要在電腦設定 USB 重新導向設定：

1. 開啟本機群組原則編輯器 (GPEDIT.MSC)。 (從命令提示字元中執行 gpedit.msc)。
2. 開啟 **電腦設定 \ 原則範本 \windows 元件 \ 遠端桌面服務 \ 遠端桌面連線 remotefx USB 裝置重新導向**。
3. 按兩下 **允許 RDP 重新導向其他支援從這部電腦的 RemoteFX USB 裝置**。
4. 選取 **啟用**, ，然後選取 **系統管理員和使用者在 [RemoteFX USB 重新導向存取權限**。
5. 以系統管理權限開啟命令提示字元，然後執行下列命令：

        gpupdate /force
6. 重新啟動電腦。

您也可以使用群組原則管理工具，為網域中的所有電腦建立和套用 USB 重新導向原則：

1. 以網域管理員的身分登入網域控制站。
2. 開啟 [群組原則管理主控台]。 (按一下 **啟動 > 系統管理工具] > [群組原則管理**。)
3. 瀏覽到您想要建立原則的網域或組織單位。
4. 以滑鼠右鍵按一下 **預設網域原則**, ，然後按一下 [ **編輯**。
5. 開啟 **電腦設定 \ 原則範本 \windows 元件 \ 遠端桌面服務 \ 遠端桌面連線 remotefx USB 裝置重新導向**。
6. 按兩下 **允許 RDP 重新導向其他支援從這部電腦的 RemoteFX USB 裝置**。
7. 選取 **啟用**, ，然後選取 **系統管理員和使用者在 [RemoteFX USB 重新導向存取權限**。
8. 按一下 [ **確定**。  


