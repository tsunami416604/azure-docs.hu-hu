
<properties
    pageTitle="Azure RemoteApp 有哪些新功能？ | Microsoft Azure"
    description="深入了解對 Azure RemoteApp 所做的變更和改進"
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
    ms.date="10/23/2015"
    ms.author="elizapo" />



# Azure RemoteApp 有哪些新功能？

Azure RemoteApp 的優點之一是我們不斷地努力改進 RemoteApp。 每當 RemoteApp 有新功能時 ，我們便會在這裡發表變更。

## 2015 年 9 月
- 已將 Infopath 新增至 Microsoft Office 365 範本和資源庫映像。 如果您想要共用 Infopath，請務必使用最新的映像來更新您的集合。
- 用戶端更新：
    - 更新 Windows 用戶端讓使用者分享意見，特別是關於連線問題。
    - 更新 iOS 用戶端以修正錯誤訊息和修正您的認證比預期提早過期的問題。
- 我們正在努力測試 Office 2016 支援。 完成時，請尋找更新的映像。
- 新的發行項發行有關 [雲端和混合式集合之間的差異](remoteapp-collections.md) -這可協助您選擇最適合您的應用程式-僅限雲端、 定域機組 + VNET 或混合式集合型別。
- 想要分享使用 Azure RemoteApp 的 QuickBooks 卻不知道步驟嗎？ 簽出 [Eric 的新發行項](remoteapp-quickbooks.md) 告知您要做。

## 2015 年 8 月
8 月的重大變更 - 以下是重點：

- 您現在可以使用 Azure VNET 搭配雲端集合！ 簽出 [定域機組建立指示](remoteapp-create-cloud-deployment.md) 新步驟。
- 讓新增應用程式至 Windows RemoteApp 用戶端的 [開始] 功能表成為可能。 應用程式會顯示在應用程式清單中，您可以將它們釘選至 Windows 中的 [開始] 功能表。
- 將新的映像新增至 Azure VM 資源庫 - Windows Server 遠端桌面工作階段主機與 Microsoft Office 365 ProPlus。
- 修正 Mac 用戶端，讓具有強制回應視窗的應用程式停止凍結。
- 說明如何使用您 [訂閱 Office 365 ProPlus](remoteapp-officesubscription.md) Azure remoteapp。
- 詳述如何 [保護應用程式和資料](remoteapp-secure.md) Azure RemoteApp 集合中。

## 2015 年 7 月

7 月設定即將在 8 月推出之變更的階段，因此現在沒有很多可談，而且大部分都是文件更新。 以下是最新的變更：

- 加入 **支援** 入口網站，就能更輕鬆地存取支援資源，像是論壇] 索引標籤。
- 已修改建立混合式集合的疑難排解資訊。 簽出 [最新和最大](remoteapp-hybridtrouble.md) 如需疑難排解秘訣，像是如何識別正確的連接埠設定的 VNET。
- 記載如何 [使用者資料](remoteapp-upd.md) 建立並儲存在 Azure RemoteApp 中。
- 說明如何以 [應用程式鎖定](remoteapp-secure.md)。
- 發行 [Azure RemoteApp cmdlet](https://msdn.microsoft.com/library/mt428031.aspx)。
- 最後，我們開始就術語與某些 Azure RemoteApp 使用者交談。 尋找我們參考不同集合選項的方式有何變更。

## 2015 年 6 月

變更十分多！ 小組在6 月極為忙碌：

- 重新設計 Azure RemoteApp [登陸頁面](https://www.remoteapp.windowsazure.com/) -請查看 ！
- 更新可為您訂用帳戶一部分之所有映像中的軟體。
- 改善混合式集合，包括強制通道支援，以及先檢查 IP 子網路大小，再嘗試建立集合。
- 發現 * 萬用字元不適用於網路攝影機。 而是，您需要指定執行個體識別碼或 GUID。 我們將更新重新導向資訊以反映該資訊。
- 這樣做，以從 Azure 資源庫建立範本映像時，將自訂防毒軟體新增到您的映像。

我們已在 7 月累積其他變更，因此很快就會提供另一個更新。

## 2015 年 5 月

從第一次建立本主題之後，已新增許多資訊，因此清單很大而且是從 3 月開始累積到 5 月。 請了解這些新功能：

- 自動化各個項目-Azure RemoteApp 現在具有 [Azure PowerShell 模組中的 cmdlet](remoteapp-tutorial-arawithpowershell.md)。
- [從 Azure 的虛擬機器建立 Azure RemoteApp 映像](remoteapp-image-on-azurevm.md)。 將自訂映像上傳至 Azure 的速度更為快速。
- 使用 Azure VNET (非 RemoteApp VNET)，以將公司網路資源連線到 Azure。 我們已更新 [混合式集合指示](remoteapp-create-hybrid-deployment.md) 來逐步引導您建立 Azure VNET （它是步驟 1）。
- 對於 Vnet，請參閱 [新指引](remoteapp-vnetsizing.md) 有關 VNET 大小限制和限制。
- 而且對於限制，就是什麼是 [服務限制和預設值](remoteapp-servicelimits.md)嗎？

想要深入了解 Azure RemoteApp 嗎？ RemoteApp 小組在數週之前已致力於 Ignite。 請參閱 Eric 的影片， [的 Microsoft Azure RemoteApp 管理基礎](http://channel9.msdn.com/Events/Ignite/2015/BRK3868)。

需要查看 Azure RemoteApp 在真實世界的樣子嗎？ 簽出 [任何地方在任何裝置上執行任何應用程式](remoteapp-anyapp.md) 教學課程-示範您如何共用您的使用者，包括共用資料庫檔案的存取。 我們也有教學課程，說明有關 [Office 365](remoteapp-tutorial-o365anywhere.md) 任何裝置上執行相同。

感謝您持續關注我們 - 下個月還會有其他更新。


### 幫我們來協助您
您知道除了評比這篇文章以及在下面留言以外，您可以變更文件本身嗎？ 有所遺漏？ 有所錯誤？ 我是否撰寫了令人混淆的內容？ 向上捲動，然後按一下 [ **GitHub 上的編輯** 若要變更-供檢閱，這些會變成給我們，接著，我們登入它們，就會看到您的變更和改進這裡。


