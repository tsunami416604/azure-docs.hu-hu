<properties
   pageTitle="開始使用 Azure 資訊安全中心 | Microsoft Azure"
   description="本文帶領您認識安全性監視和原則管理元件，並連結至下一個步驟，協助您快速開始使用 Azure 資訊安全中心。"
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

# 開始使用 Azure 資訊安全中心

本文帶領您認識安全性監視和原則管理元件，並連結至下一個步驟，協助您快速開始使用 Azure 資訊安全中心。

> [AZURE.NOTE] 這份文件中的資訊適用於 Azure 資訊安全中心的預覽版本。 這是使用範例部署的服務簡介。  這不是逐步指南。

## 什麼是 Azure 資訊安全中心？
Azure 資訊安全中心利用加強對 Azure 資源的能見度及安全性控制權，以預防、偵測並回應威脅。 它提供您訂用帳戶之間的整合式安全性監視和原則管理，協助您偵測可能會忽略的威脅，且適用於廣泛的安全性解決方案生態系統。

## 必要條件

若要開始使用 Azure 資訊安全中心，您必須有 Microsoft Azure 訂用帳戶。 Azure 資訊安全中心已經由訂用帳戶啟用。 如果您沒有訂閱，您可以註冊 [免費試用版](https://azure.microsoft.com/pricing/free-trial)。

Azure 資訊安全中心透過 [Azure 入口網站](http://azure.microsoft.com/features/azure-portal/)。 請參閱 [入口網站文件](https://azure.microsoft.com/documentation/services/azure-portal/) 若要深入了。


## 存取 Azure 資訊安全中心

在入口網站中，執行下列步驟來存取 Azure 資訊安全中心：

1. 選取 **瀏覽** ，並捲動至 **資訊安全中心** 選項。
![][1]

2. 選取 **資訊安全中心**。 這會開啟 **資訊安全中心** 刀鋒視窗。
3. 快速存取 **資訊安全中心** 刀鋒視窗中，在未來選取 **Pin] 刀鋒視窗中的儀表板** 選項 （右上方）。
![][2]

## 使用 Azure 資訊安全中心

設定安全性 **原則** 為您的訂閱 ︰

4. 按一下 [ **安全性原則** 磚 **資訊安全中心** 刀鋒視窗。
5. 在 **安全性原則 – 定義每個訂閱的原則** 刀鋒視窗中，選取的訂閱。
6. 在 **安全性原則** 刀鋒視窗中，開啟 **資料收集** 自動收集記錄檔。 開啟 **資料收集** 也會佈建在訂閱中的所有目前和新 Vm 上的監視功能延伸模組。
7. 按一下 [ **選擇儲存體帳戶**。 針對每個有虛擬機器在其中執行的區域，您需選擇儲存體帳戶，以儲存從這些虛擬機器收集到的資料。 如不為每個區域選擇儲存體帳戶，系統會為您建立。 基於安全性考量，收集到的資料在邏輯上會與其他客戶的資料隔離。
8. 開啟 **建議** 您想要查看您的安全性原則的一部分。 範例：

  - 開啟 **系統更新** 會掃描所有支援的虛擬機器缺少 OS 更新。
  - 開啟 **的基準規則** 將掃描支援虛擬機器，以識別任何 OS 組態，可以讓虛擬機器更容易受到攻擊。

![][3]

位址 **建議**:

9. 返回 **資訊安全中心** 分頁，然後按一下 **建議** 並排顯示。
10. Azure 資訊安全中心會定期分析 Azure 資源的安全性狀態。 發現潛在的安全性弱點時，會在這裡顯示建議。
11. 按一下每個建議，檢視詳細資訊及/或採取行動以解決問題。

![][4]

檢視透過資源的健康情況與安全性狀態 **資源健全狀況**:

12. 返回 **資訊安全中心** 刀鋒視窗。
13.  **資源健全狀況** 磚包含安全性狀態的指標 **虛擬機器**, ，**網路**, ，**SQL**, ，和 **應用程式**。
14. 選取 **虛擬機器** 若要檢視的詳細資訊。
15. **虛擬機器** 刀鋒視窗會顯示狀態摘要，包括反惡意程式碼、 系統更新、 重新啟動時，與基準規則，您的虛擬機器。
16. 選取下的一個項目 **預防步驟** 檢視的詳細資訊和/或採取動作以設定所需的控制項。
17. 向下鑽研以檢視特定虛擬機器的其他資訊。

![][5]

位址 **安全性警示**:

19. 返回 **資訊安全中心** 分頁，然後按一下 **安全性警示** 並排顯示。
20. 在 **安全性警示** 刀鋒視窗中，警示清單會顯示。 Azure 資訊安全中心透過對安全性記錄檔和網路活動的分析，偵測到警示。 從整合式的合作夥伴解決方案的警示也會包含。
  ![][6]

21. 按一下警示以檢視其他資訊。

  ![][7]

## 後續步驟
本文介紹了 Azure 資訊安全中心的安全性監視和原則管理元件。 若要深入了解，請參閱下列文章：

- [在 Azure 資訊安全中心 」 中設定安全性原則](security-center-policies.md) – 了解如何設定安全性原則
- [管理 Azure 資訊安全中心的安全性建議](security-center-recommendations.md) – 了解如何建議可協助您保護您的 Azure 資源
- [Azure 資訊安全中心的安全性健全狀況監視](security-center-monitoring.md) – 了解如何監視您的 Azure 資源的健全狀況
- [管理和 Azure 資訊安全中心的安全性警示回應](security-center-managing-and-responding-alerts.md) -了解如何管理和回應安全性警示
- [Azure 安全性中心常見問題集](security-center-faq.md) – 尋找使用服務的相關的常見問題集
- [Azure 安全性部落格](http://blogs.msdn.com/b/azuresecurity/) – 取得最新的 Azure 安全性新聞和資訊

<!--Image references-->
[1]: ./media/security-center-get-started/security-tile.png
[2]: ./media/security-center-get-started/pin-blade.png
[3]: ./media/security-center-get-started/security-policy.png
[4]: ./media/security-center-get-started/recommendations.png
[5]: ./media/security-center-get-started/resources-health.png
[6]: ./media/security-center-get-started/security-alert.png
[7]: ./media/security-center-get-started/security-alert-detail.png


