<properties
   pageTitle="管理 Azure 資訊安全中心的安全性建議 |Microsoft Azure"
   description="本文件將逐步引導您了解「Azure 資訊安全中心」的建議如何協助您保護 Azure 資源及遵守安全性原則。"
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="StevenPo"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/10/2015"
   ms.author="terrylan"/>

# 管理 Azure 資訊安全中心的安全性建議

本文件將逐步引導您了解「Azure 資訊安全中心」的建議如何協助您保護 Azure 資源。

> [AZURE.NOTE] 這份文件中的資訊適用於 Azure 資訊安全中心的預覽版本。 這是使用範例部署的服務簡介。  這不是逐步指南。

## 什麼是 Azure 資訊安全中心？
Azure 資訊安全中心利用加強對 Azure 資源的能見度及安全性控制權，以預防、偵測並回應威脅。 它提供您訂用帳戶之間的整合式安全性監視和原則管理，協助您偵測可能會忽略的威脅，且適用於廣泛的安全性解決方案生態系統。

## 有哪些安全性建議？
「Azure 資訊安全中心」會定期分析您 Azure 資源的安全性狀態。 當發現潛在的安全性弱點時，就會產生相關建議。 這些建議會引導您完成設定所需控制項的程序。

## 實作安全性建議

### 設定建議

在 [Azure 資訊安全中心 」 中設定安全性原則](security-center-policies.md) 您了解如何:

- 設定安全性原則
- 開啟資料收集
- 選擇要在您的安全性原則中看見的建議

系統更新、 比較基準規則、 防惡意程式周圍的目前原則建議中心 [端點的 Acl](virtual-machines-set-up-endpoints.md), ，[網路安全性群組](virtual-networks-nsg.md) 子網路和網路介面、 SQL Database 稽核、 SQL 資料庫透明資料加密，以及 web 應用程式防火牆上。  [設定安全性原則](security-center-policies.md) 說明的每個建議選項。

### 監視建議

設定安全性原則之後，「Azure 資訊安全中心」會分析您資源的安全性狀態，以識別潛在的弱點。  **建議** 磚 **資訊安全中心** 刀鋒視窗可讓您知道建議由 Azure 資訊安全中心的總數量。

![][2]

查看每個建議的詳細資料：

1. 按一下 [ **建議磚** 上 **資訊安全中心** 刀鋒視窗。  **建議** 分頁隨即開啟。
2. 您可以依狀態和嚴重性篩選呈現給您的建議。 按一下 [ **篩選** 上 **建議** 刀鋒視窗。 即會開啟 [篩選] 刀鋒視窗，您可以選取想要查看的嚴重性和狀態值。
![][3]

3. 如果您判斷建議不適用，您可解除該建議，然後將它從您的檢視中篩除。 解除建議的方式有兩種。 以滑鼠右鍵按一下項目，然後選取 **解除** 或將滑鼠停留在項目，請按一下三個點會出現在右邊，然後選取 [ **解除**。 您可以檢視已解除的建議，藉以 **篩選** ，然後選取 **Dismissed**。
![][4]

系統會以表格格式顯示建議，其中每一行代表一個特定的建議。 這個表格的資料行包括：

- **描述**: 建議和知道要如何處理它的說明
- **資源**: 適用這項建議是哪些資源
- **狀態**: 建議的目前狀態:
  - **開啟**: 沒有尚未處理
  - **在進行中**: 建議目前正在套用至這些資源，您不需要任何動作
  - **判斷是否已解決**: 建議已完成 (已解決狀態時，線條的色彩變成灰色)
- **嚴重性**: 描述該特定建議事項的嚴重性:
  - **高**: 的弱點可能會有意義的資源 (應用程式中，VM 網路安全性群組)，並需要注意
  - **中型**: 非關鍵性或其他步驟才能完成程序或消除弱點
  - **低**: 應該處理，但不需要立即注意的弱點。 (根據預設，則不會提供低建議但如果您選擇檢視它們，您可以篩選低建議)。

請使用下表做為參考，以了解可用的建議，以及如果套用建議，每一個建議將產生的作用：

| 建議 | 說明 |
|----- |-----|
| 啟用訂用帳戶/虛擬機器的資料收集 | 建議您在所選 VM 的「安全性原則」中開啟「資料收集」。 |
| 解決不相符基準規則 | 建議您讓作業系統組態符合建議的基準，例如不允許儲存密碼。 |
| 套用系統更新 | 建議您將遺漏的系統更新部署到 VM (僅適用於 Windows VM)。 |
| 設定端點的 ACL | 建議您設定存取控制清單來限制對 VM 的輸入存取 (僅適用於「傳統 VM」)。 |
| 新增 Web 應用程式防火牆 | 建議您為 Web 端點 (僅適用於「資源管理員 VM」) 部署「Web 應用程式防火牆」(WAF)。 |
| 完成 Web 應用程式防火牆設定 | 若要完成 WAF 組態，必須將流量重新路由至 WAF 設備。 這項建議會完成必要的設定變更。 |
| 啟用反惡意程式碼 | 建議您將反惡意程式碼佈建到 VM (僅適用於 Windows VM)。 |
| 啟用子網路/網路介面上的網路安全性群組 | 建議您啟用子網路和網路介面上的「網路安全性群組」(NSG) (僅適用於「資源管理員 VM」)。  |
| 限制透過公用外部端點的存取 | 建議您為 NSG 設定輸入流量規則。 |
| 啟用伺服器 SQL 稽核 | 建議您針對 Azure SQL 伺服器開啟稽核 (僅適用於 Azure SQL 服務，不包括在您虛擬機器上執行的 SQL)。 |
| 啟用資料庫 SQL 稽核 | 建議您針對 Azure SQL 資料庫開啟稽核 (僅適用於 Azure SQL 服務，不包括在您虛擬機器上執行的 SQL)。 |
| 在 SQL 資料庫上啟用透明資料加密 | 建議您針對 SQL 資料庫啟用加密 (僅適用於 Azure SQL 服務)。 |
| 部署 VM 代理程式 | 可讓您查看哪些 VM 需要「VM 代理程式」。 為了佈建「修補程式掃描」、「基準掃描」及「反惡意程式碼」，必須在 VM 上安裝「VM 代理程式」。 預設會為從 Azure Marketplace 部署的 VM 安裝「VM 代理程式」。 發行項 [VM 代理程式和延伸模組-第 2 部分](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/) 提供如何安裝 VM 代理程式的相關資訊。 |

### 套用建議
檢視所有建議之後，您可以決定應該先套用哪一個建議。 建議使用嚴重性分級做為主要參數，以評估應該先套用哪些建議。
讓我們使用反惡意程式碼建議，來逐步解說有關如何套用建議的範例：

1. 在 **建議** 分頁中，選取 **啟用反惡意程式碼**。
![][5]

2. 在 **安裝反惡意程式碼** 刀鋒視窗中選取清單中的虛擬機器而不需要啟用的反惡意程式碼，然後按一下 [ **安裝反惡意程式碼**。
3.  **新資源** 分頁隨即開啟，讓您選取您想要使用的反惡意程式碼方案。 選取 **Microsoft 反惡意程式碼**。
4. 將會顯示反惡意程式碼解決方案的其他相關資訊。 選取 **建立**。
5. 輸入必要的組態設定上 **加入擴充** 刀鋒視窗中，然後選取 **確定**。
![][6]

[Microsoft 反惡意程式碼](azure-security-antimalware.md) 現在已在選取的虛擬機器上使用。

### 部署建議的合作夥伴解決方案

建議有可能是部署來自 Microsoft 合作夥伴的整合式安全性解決方案。 讓我們逐步解說有關如何進行這項操作的範例：

1. 返回 **建議** 刀鋒視窗。
2.  選取建議 **保護 web 應用程式使用 web 應用程式防火牆**。 這會開啟 **未受保護的 Web 應用程式** 刀鋒視窗。
![][7]
3. 選取 web 應用程式 **加入 Web 應用程式防火牆** 分頁隨即開啟。
4. 選取 **Barracuda Web 應用程式防火牆**。 提供有關的資訊，會開啟一個刀鋒視窗 **Barracuda Web 應用程式防火牆**。
5. 按一下 [ **建立** 資訊刀鋒視窗中。  **新的 Web 應用程式防火牆** 分頁隨即開啟，您可以在其中執行 **VM 組態** 步驟，並提供 **WAF 資訊**。
6. 選取 **VM 組態**。 在 **VM 組態** 刀鋒視窗中輸入註冊將會執行 WAF 的虛擬機器所需的資訊。
![][8]
7. 返回 **新的 Web 應用程式防火牆** 分頁，然後選取 **WAF 資訊**。 在 **WAF 資訊** 設定本身 WAF 的分頁。 步驟 6 可讓您設定將執行 WAF 的虛擬機器，而步驟 7 則可讓您佈建 WAF 本身。

8. 返回 **建議** 刀鋒視窗。  新的項目產生後建立 WAF **完成 web 應用程式防火牆安裝**。 這可讓您知道您需要完成此程序的實際裝設 WAF Azure 虛擬網路內，讓它可以保護應用程式。
![][9]

9. 選取 **完成 web 應用程式防火牆安裝** 和新的刀鋒視窗隨即開啟。 您會看到您有一個需要重新路由流量的 Web 應用程式。
10. 選取該 Web 應用程式，將會開啟一個刀鋒視窗，其中提供完成 Web 應用程式防火牆設定的步驟。 完成的步驟中，按一下 **限制流量**, ，並為您的 Azure 安全性中心完成線路。
![][10]

現在已將來自該 WAF 的記錄完全整合。 「Azure 資訊安全中心」可以開始自動收集並分析記錄，以便對您顯示重要的安全性警示。

## 後續步驟
在本文件中，已向您介紹「Azure 資訊安全中心」的安全性建議。 若要深入了解「Azure 資訊安全中心」，請參閱下列主題：

- [在 Azure 資訊安全中心 」 中設定安全性原則](security-center-policies.md) – 了解如何設定安全性原則
- [Azure 資訊安全中心的安全性健全狀況監視](security-center-monitoring.md) – 了解如何監視您的 Azure 資源的健全狀況
- [管理和 Azure 資訊安全中心的安全性警示回應](security-center-managing-and-responding-alerts.md) -了解如何管理和回應安全性警示
- [Azure 安全性中心常見問題集](security-center-faq.md) – 尋找使用服務的相關的常見問題集
- [Azure 安全性部落格](http://blogs.msdn.com/b/azuresecurity/) – 尋找關於 Azure 安全性與相容性部落格文章

<!--Image references-->
[2]: ./media/security-center-recommendations/recommendations-tile.png
[3]: ./media/security-center-recommendations/filter-recommendations.png
[4]: ./media/security-center-recommendations/dismiss-recommendations.png
[5]: ./media/security-center-recommendations/select-enable-antimalware.png
[6]: ./media/security-center-recommendations/install-antimalware.png
[7]: ./media/security-center-recommendations/secure-web-application.png
[8]: ./media/security-center-recommendations/vm-configuration.png
[9]: ./media/security-center-recommendations/finalize-waf.png
[10]: ./media/security-center-recommendations/restrict-traffic.png

