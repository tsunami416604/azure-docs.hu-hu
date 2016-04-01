<properties
 pageTitle="排解雲端服務部署問題 | Microsoft Azure"
 description="將雲端服務部署至 Azure 時，可能會發生幾個常見的問題。 本文章提供其中部分問題的解決方案。"
   services="cloud-services"
   documentationCenter=""
   authors="dalechen"
   manager="msmets"
   editor=""
   tags="top-support-issue"/>
<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="12/01/2015"
   ms.author="daleche" />

# 如何疑難排解您可能會碰到的雲端服務部署問題

當您部署至 Azure 雲端服務應用程式封裝時，您可以取得部署相關的資訊從 **屬性** Azure 傳統入口網站中的窗格。 您可以利用此窗格中的詳細資料來排解雲端服務的問題，您也可以在開啟新的支援要求時將這項資訊提供給 Azure 支援。

> [AZURE.NOTE] 您可以複製到剪貼簿的 [屬性] 窗格的內容窗格右上角的圖示，即可。

## 連絡 Azure 客戶支援

如果您需要更多協助，在本文中的任何時間點，您可以與 Azure 專家 [MSDN Azure 和堆疊溢位論壇](http://azure.microsoft.com/support/forums/)。

或者，您也可以提出 Azure 支援事件。 移至 [Azure 支援網站](http://azure.microsoft.com/support/options/) ，然後按一下 **取得支援**。 使用 Azure 支援的相關資訊，請參閱 [Microsoft Azure 支援常見問題集](http://azure.microsoft.com/support/faq/)。



## 問題：即使我的部署已啟動，且所有角色執行個體皆已就緒，我仍然無法存取我的網站

入口網站中顯示的網站 URL 連結未包含連接埠。 網站的預設連接埠為 80。 不過，如果您的應用程式設定為執行於不同的連接埠，您必須在存取網站時將正確的連接埠新增至 URL。

1. 在 Azure 傳統入口網站中，按一下您的雲端服務部署。
2. 在 **屬性** 窗格 Azure 傳統入口網站，檢查角色執行個體 （在下方輸入端點） 的連接埠。
3. 如果沒有該連接埠 *80*, ，為 URL 加入正確的連接埠值，當您存取應用程式。 若要指定非預設連接埠，請輸入 URL，後面依序加上冒號 (:) 和不含空格的連接埠號碼。

## 問題：我的角色執行個體無故自行重新啟動

當 Azure 偵測到有問題的節點，並將角色執行個體移至新節點時，會自動進行服務修復。 發生此情況時，您可能會看見角色執行個體自動重新啟動。 若要確認是否在執行服務修復：

1. 在 Azure 傳統入口網站中，按一下您的雲端服務部署。
2. 在 **屬性** 窗格 Azure 傳統入口網站，檢視資訊並判斷是否發生服務修復期間觀察到角色重新啟動。

在主機 OS 和客體 OS 升級期間，角色大約每個月會重新啟動一次。  
如需詳細資訊，請參閱部落格文章 [角色執行個體重新啟動因作業系統升級而](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx)

## 問題：我無法進行 VIP 交換並收到錯誤訊息

在部署更新進行期間不允許 VIP 交換。 部署更新可能在下列時間點自動執行：

* 新的客體作業系統可供使用時，和您設定要自動更新時
* 服務修復執行時

若要確認自動升級是否使您無法進行 VIP 交換：

1. 在 Azure 傳統入口網站中，按一下您的雲端服務部署。
2. 在 **屬性** 窗格中的 Azure 傳統入口網站，查看值的 **狀態**。 如果是 **準備**, ，然後檢查 **前一個作業** 查看如果其中一個最近發生的可能無法進行 VIP 交換。
3. 為生產部署重複步驟 1 和 2。
4. 如果自動更新正在進行中，請等候它完成再嘗試進行 VIP 交換。

## 問題：角色執行個體在 [已啟動]、[初始化中]、[忙碌] 和 [已停止] 之間循環

這種情況可能表示應用程式的程式碼、封裝或組態檔發生問題。 如果為 true，則您應該可以看到每隔幾分鐘就變更的狀態和 Azure 傳統入口網站可會說明類似 **回收**, ，**忙碌**, ，或 **初始化**。 這表示應用程式發生了某些錯誤，導致角色執行個體無法執行。

如需有關如何疑難排解此問題的詳細資訊，請參閱部落格文章 [Azure PaaS Compute Diagnostics Data] 和 [常見的問題導致角色回收](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md)

## 問題：我的應用程式停止運作

1. 在 Azure 傳統入口網站中，按一下 [角色執行個體]。
2. 在 **屬性** 窗格 Azure 傳統入口網站，請考慮下面狀況來解決您的問題 ︰
   * 如果最近停止角色執行個體 (您可以檢查的值 **中止計數**)，無法更新部署。 請等候並觀察角色執行個體是否會自行恢復運作。
   * 如果角色執行個體為 「 忙碌 」，請檢查您的應用程式程式碼，看看是否 [StatusCheck](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.statuscheck) 處理事件。 您可能需要新增或修正處理此事件的程式碼。
   * 瀏覽的診斷資料及疑難排解案例部落格文章 [Azure PaaS Compute Diagnostics Data]。

>[AZURE.WARNING] 如果您重新啟動您的雲端服務，您重設部署的內容有效地清除了原始問題的資訊。

## 後續步驟

檢視更多 [疑難排解文章](..\?tag=top-support-issue&service=cloud-services) 雲端服務。


[Azure PaaS Compute Diagnostics Data]: http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx


