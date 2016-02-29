<properties 
   pageTitle="設定 StorSimple 裝置的 Web Proxy | Microsoft Azure"
   description="了解如何使用 Windows PowerShell for StorSimple 來設定 StorSimple 裝置的 Web Proxy。"
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/02/2015"
   ms.author="alkohli" />

# 為 StorSimple 裝置設定 Web Proxy

## 概觀

本教學課程描述如何使用 Windows PowerShell for StorSimple 來設定和檢視 StorSimple 裝置的 Web Proxy 設定。 Web Proxy 設定供 StorSimple 裝置與雲端通訊時使用。 Web Proxy 伺服器用來多增加一層安全性、篩選內容、快取以緩解頻寬需求，甚至是協助分析。

Web Proxy 是 StorSimple 裝置的選用設定。 您只能透過 Windows PowerShell for StorSimple 來設定 Web Proxy。 設定分成兩步驟，如下所示：

1. 首先，透過安裝精靈或 Windows PowerShell for StorSimple 指令程式來設定 Web Proxy 設定。

2. 然後，透過 Windows PowerShell for StorSimple 指令程式，啟用已設定的 Web Proxy 設定。

Web Proxy 設定完成之後，您可以在 Microsoft Azure StorSimple Manager 服務和 Windows PowerShell for StorSimple 中檢視已設定的 Web Proxy 設定。 

閱讀本教學課程之後，您將能夠：

- 使用安裝精靈和 Cmdlet 來設定 Web Proxy
- 使用 Cmdlet 來啟用 Web Proxy
- 在 Azure 傳統入口網站中檢視 Web Proxy 設定
- 對 Web Proxy 設定期間的錯誤進行疑難排解


## 透過 Windows PowerShell for StorSimple 來設定 Web Proxy

您可以使用下列其中一項來設定 Web Proxy 設定：

- 安裝精靈來引導您完成設定步驟。

- Windows PowerShell for StorSimple 中的 Cmdlet。

下列各節討論上述每一種方法。

## 透過安裝精靈來設定 Web Proxy

您可以使用安裝精靈引導您完成 Web Proxy 設定的步驟。 執行下列步驟在裝置上設定 Web Proxy。

#### 透過安裝精靈來設定 Web Proxy

1. 在序列主控台功能表中，選擇選項 1 **完整存取權登入** ，並提供 **裝置系統管理員密碼**。 輸入下列命令以啟動安裝精靈工作階段：

    `Invoke-HcsSetupWizard`

2. 如果這是第一次使用安裝精靈來註冊裝置，您必須設定所有必要的網路設定，最後才會進入 Web Proxy 設定。 如果您尚未註冊您的裝置，您可以接受所有已設定的網路設定，直到您到達 web proxy 設定。在安裝精靈] 時提示您設定 web proxy 設定，請輸入 **是**。

3. 如 **Web Proxy URL**, ，指定 web proxy 伺服器的 IP 位址或完整的網域名稱 (FQDN)，以及 TCP 連接埠號碼，您想要您的裝置與雲端通訊時使用。 請使用下列格式：

    `http://<IP address or FQDN of the web proxy server>:<TCP port number>`

    預設會指定 TCP 連接埠號碼 8080。

4. 選擇驗證類型為 **NTLM**, ，**基本**, ，或 **無**。 [基本] 是 Proxy 伺服器設定最不安全的驗證。 NT LAN Manager (NTLM) 是非常安全和複雜的驗證通訊協定，使用三向傳訊系統 (需要更高完整性時，則是四向) 來驗證使用者。 預設驗證為 NTLM。 如需詳細資訊，請參閱 [基本](http://hc.apache.org/httpclient-3.x/authentication.html) 和 [NTLM 驗證](http://hc.apache.org/httpclient-3.x/authentication.html)。 

    > [AZURE.IMPORTANT] **中的 StorSimple Manager 服務裝置監控圖表運作時基本或 NTLM 驗證已啟用裝置的 proxy 伺服器組態中。為了讓監控圖表發揮作用，您必須確保驗證設定為 [無]。**

5. 如果您使用的驗證，提供 **Web Proxy 使用者名稱** 和 **Web Proxy 密碼**。 您也必須確認密碼。

    ![在 StorSimple 裝置 1 設定 Web Proxy](./media/storsimple-configure-web-proxy/IC751830.png)

如果是第一次註冊您的裝置，請繼續註冊。 如果您的裝置已註冊，就會結束精靈。 將會儲存已設定的設定。

現在也會啟用 Web Proxy。 您可以略過 [啟用 web proxy](#enable-web-proxy) 步驟，並直接移至 [Azure 傳統入口網站中檢視 web proxy 設定](#view-web-proxy-settings-in-the-azure-classic-portal)。


## 透過 Windows PowerShell for StorSimple Cmdlet 來設定 Web Proxy

設定 Web Proxy 設定的另一種方法是透過 Windows PowerShell for StorSimple Cmdlet。 執行下列步驟來設定 Web Proxy。

#### 透過 Cmdlet 來設定 Web Proxy

1. 在序列主控台功能表中，選擇選項 1 **完整存取權登入**。 出現提示時，提供 **裝置系統管理員密碼**。 預設密碼是  `Password1`。

2. 在命令提示字元中，輸入：

    `Set-HcsWebProxy -Authentication NTLM -ConnectionURI "<http://<IP address or FQDN of web proxy server>:<TCP port number>" -Username "<Username for web proxy server>"`

    出現提示時，提供並確認密碼，如下所示。

    ![在 StorSimple 裝置 3 設定 Web Proxy](./media/storsimple-configure-web-proxy/IC751831.png)

Web Proxy 現在已設定，必須啟用。

## 啟用 Web Proxy

預設會停用 Web Proxy。 在 StorSimple 裝置上設定 Web Proxy 設定之後，您需要使用 Windows PowerShell for StorSimple 來啟用 Web Proxy 設定。

> [AZURE.NOTE] **不需要此步驟中，如果您使用安裝精靈來設定 web proxy。依預設，安裝精靈工作階段之後會自動啟用 Web Proxy。**

在 Windows PowerShell for StorSimple 中執行下列步驟，在您的裝置上啟用 Web Proxy：

#### 啟用 Web Proxy

1. 在序列主控台功能表中，選擇選項 1 **完整存取權登入**。 出現提示時，提供 **裝置系統管理員密碼**。 預設密碼是  `Password1`。

2. 在命令提示字元中，輸入：

    `Enable-HcsWebProxy`

    您現在已在 StorSimple 裝置上啟用 Web Proxy 設定。

    ![在 StorSimple 裝置 4 設定 Web Proxy](./media/storsimple-configure-web-proxy/IC751832.png)

## 在 Azure 傳統入口網站中檢視 Web Proxy 設定

Web Proxy 設定已透過 Windows PowerShell 介面設定，無法從傳統入口網站內變更。 不過，您可以在傳統入口網站中檢視這些已設定的設定。 執行下列步驟來檢視 Web Proxy。

#### 檢視 Web Proxy 設定
1. 瀏覽至 **StorSimple Manager 服務 > 裝置**。 選取並按一下裝置，然後移至 **設定**。
1. 向下捲動 **設定** 頁面 **Web proxy 設定** 一節。 您可以檢視 StorSimple 裝置上已設定的 Web Proxy 設定，如下所示。

    ![在管理入口網站中檢視 Web Proxy](./media/storsimple-configure-web-proxy/ViewWebProxyPortal_M.png)
 
## 在 Web Proxy 設定期間發生錯誤

如果已正確設定 Web Proxy 設定，Windows PowerShell for StorSimple 中會顯示錯誤訊息給使用者。 下表說明其中部分錯誤訊息、可能原因和建議的動作。

|序號|HRESULT 錯誤碼|可能的根本原因|建議的動作|
|:---|:---|:---|:---|
|1.|0x80070001|命令是從被動控制器執行，無法與主動控制器通訊。|在主動控制器上執行命令。 若要從被動控制器執行命令，您必須將連線從被動控制器修正為主動控制器。 如果此連線已中斷，您必須連絡 Microsoft 支援服務。|
|2.|0x800710dd - 作業識別碼無效|StorSimple 虛擬裝置不支援 Proxy 設定。|StorSimple 虛擬裝置不支援 Proxy 設定。 這些設定只能在 StorSimple 實體裝置上設定。|
|3.|0x80070057 - 無效的參數|針對 Proxy 設定提供的其中一個參數無效。|提供的 URI 不是正確格式。 使用下列格式：`http://<IP address or FQDN of the web proxy server>:<TCP port number>`|
|4.|0x800706ba - RPC 伺服器無法使用|根本原因是下列其中一項:</br></br>叢集未啟動。</br></br>資料路徑服務未執行。</br></br>從被動控制器執行命令並不能與主動控制器通訊。|連絡 Microsoft 支援服務，以確保叢集已啟動，而且資料路徑服務正在執行。</br></br>從主動控制器執行命令。 如果您想要從被動控制器執行命令，就必須確保被動控制器能與主動控制器通訊。 如果此連線已中斷，您必須連絡 Microsoft 支援服務。|
|5.|0x800706be - RPC 呼叫失敗|叢集已關閉。|連絡 Microsoft 支援服務以確定叢集已啟動。|
|6.|0x8007138f - 找不到叢集資源|找不到平台服務叢集資源。 若未正確安裝，即會發生此情況。|您可能需要在裝置上執行原廠重設。您可能需要建立平台資源。 請連絡 Microsoft 支援服務來請示後續步驟。|
|7.|0x8007138c - 叢集資源不在線上|平台或資料路徑叢集資源不在線上。|請連絡 Microsoft 支援服務，以確定資料路徑和平台服務資源都在線上。|

> [AZURE.NOTE] 
> 
> -  上述的錯誤訊息清單不完整。 
> - Web Proxy 設定相關的錯誤不會顯示在 StorSimple Manager 服務的 Azure 傳統入口網站中。 如果有問題 web proxy 組態完成後，裝置狀態會變成 **離線** 在傳統入口網站。 |

## 後續步驟

- 如果您在部署您的裝置，或設定 web proxy 設定時遇到任何問題，請參閱 [疑難排解您的 StorSimple 裝置部署](storsimple-troubleshoot-deployment.md)。

- 若要了解如何使用 StorSimple Manager 服務，請移至 [使用 StorSimple Manager 服務來管理 StorSimple 裝置](storsimple-manager-service-administration.md)。

