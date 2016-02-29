<properties
   pageTitle="開始使用 Azure 資訊安全中心 | Microsoft Azure"
   description="本文件可協助您在「Azure 資訊安全中心」設定安全性原則。"
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/16/2015"
   ms.author="yurid"/>

#在 Azure 資訊安全中心設定安全性原則
這份文件透過帶領您完成執行這項工作的必要步驟，協助您設定 Azure 資訊安全中心的安全性原則。

> [AZURE.NOTE] 這份文件中的資訊適用於 Azure 資訊安全中心的預覽版本。

## 什麼是 Azure 資訊安全中心？
Azure 資訊安全中心利用加強對 Azure 資源的能見度及安全性控制權，以預防、偵測並回應威脅。 它提供您訂用帳戶之間的整合式安全性監視和原則管理，協助您偵測可能會忽略的威脅，且適用於廣泛的安全性解決方案生態系統。

##什麼是安全性原則？
安全性原則定義了針對指定之訂用帳戶內的資源建議的一組控制項。 在「Azure 資訊安全中心」中，您可以根據公司安全性需求，以及每個訂用帳戶中應用程式的類型或資料的敏感性，為您的 Azure 訂用帳戶定義原則。
 
例如，用於開發或測試的資源與用於實際執行應用程式的資源，兩者的安全性需求可能不同。 同樣地，具有 PII (個人識別資訊) 這類規範資料的應用程式可能會需要較高層級的安全性。 「Azure 資訊安全中心」中啟用的安全性原則將會決定安全性建議與監視，以協助您識別可能的弱點並減輕威脅。

##設定安全性原則

安全性原則是針對每個訂用帳戶設定的。 若要修改安全性原則，您必須是該訂用帳戶的「擁有者」或「參與者」。 請依照下列步驟在「Azure 資訊安全中心」設定安全性原則：

1. 按一下 [ **安全性原則** Azure 資訊安全中心儀表板中並排顯示。
 
2. 在 **安全性原則-定義每個訂閱的原則** 開啟刀鋒視窗右上角，選取您想要啟用安全性原則的訂用帳戶。

    ![啟用資料收集](./media/security-center-policies/security-center-policies-fig0.png)

3.  **安全性原則**  該訂用帳戶的刀鋒視窗會開啟一組選項類似如下所示:

    ![啟用資料收集](./media/security-center-policies/security-center-policies-fig1.png)

    
4. 請確定 **從虛擬機器收集資料** 選項是 **上**。 此選項可啟用現有資源與新資源的自動記錄收集。 

5. 如果尚未設定儲存體帳戶，您可能會看到當您開啟下圖中所示範的類似警告 **安全性原則**:

    ![儲存體選擇](./media/security-center-policies/security-center-policies-fig2.png) 

6. 如果您看到這個警告，請按一下此選項並選取區域，如下圖所示：

    ![儲存體選擇](./media/security-center-policies/security-center-policies-fig3.png) 

7. 針對每個有虛擬機器在其中執行的區域，請選擇儲存體帳戶，以儲存從這些虛擬機器收集到的資料。 這可讓您針對隱私權和資料主權的考量，輕鬆將資料保留在相同的地理區域。 您決定將使用哪一個區域之後，請選取該區域，然後選取儲存體帳戶。

8. 在 **選擇儲存體帳戶** 刀鋒視窗中按一下 **確定**。

    > [AZURE.NOTE] 如果想要的話，您可以從一個集中的儲存體帳戶中的不同區域的虛擬機器來彙總資料。 請參閱 [Azure 安全性中心常見問題集](security-center-faq.md) 如需詳細資訊

9. 在 **安全性原則** 刀鋒視窗中按一下 **上** 啟用您想要使用此訂用帳戶的安全性建議。 請使用下表做為參考，以了解每個選項會執行的動作：  

    |原則 |在狀態 |
|----- |-----|
|系統更新 |在您的 Windows 虛擬機器上安裝的擷取從 Windows Update 或 WSUS，視哪一項服務的可用更新清單已針對該虛擬機器，每隔 12 小時和遺失更新的建議。 |
|基準規則 |會分析所有支援的虛擬機器每隔 12 小時，以識別任何 OS 組態，可以讓虛擬機器更容易受到攻擊，並建議解決這些弱點的組態變更。 請參閱 [清單的建議基準](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) 如需有關受監視的特定設定。 |
|反惡意程式碼 |建議的反惡意程式碼來協助識別和移除病毒、 間諜軟體及其他惡意軟體的所有 Windows 虛擬機器佈都建。 |
|在端點上存取控制清單 |建議 [存取控制清單](virtual-machines-set-up-endpoints.md) (ACL) 設定來限制存取傳統虛擬機器端點。 這通常是用來確保只有連線到公司網路的使用者才能存取虛擬機器。 |
|網路子網路和網路介面上的安全性群組 |建議 [網路安全性群組](virtual-networks-nsg.md) (Nsg) 設定來控制傳入和傳出流量子網路及網路資源管理員的虛擬機器的介面。 除非另有指定，否則所有虛擬機器網路介面都會繼承為子網路設定的 NSG。 除了檢查是否已設定 NSG 之外，還會評估「輸入安全性規則」來識別允許「任何」連入流量的規則。 |
|Web 應用程式防火牆 |建議您在資源管理員的虛擬機器上佈建 Web 應用程式防火牆時: [執行個體層級公用 IP](virtual-networks-instance-level-public-ip.md) (ILPIP) 會使用和相關聯的 NSG 輸入安全性規則都設定為允許存取連接埠 80/443。 使用負載平衡 IP (VIP)，而且相關聯的負載平衡以及傳入的 NAT 規則都設定為允許存取連接埠 80/443 (如需詳細資訊，請參閱 [Azure 資源管理員的負載平衡器支援](load-balancer-arm.md)) |
|SQL 稽核 |建議的相容性，進階偵測及調查的目的啟用稽核的 Azure SQL 伺服器和資料庫的存取。 |
|SQL 透明資料加密 |建議您加密靜止能夠針對您的 Azure SQL 資料庫、 相關聯的備份和交易記錄檔，因此即使在達到您的資料時，它將無法讀取。 |

10. 一旦您完成設定所有選項，請按一下 [ **儲存** ，認可這些變更。

> [AZURE.NOTE] 啟用資料收集功能會安裝監視代理程式。 如果您不想現在從這個位置開啟資料收集，可以稍後從健康情況和建議檢視開啟。 您也可以針對訂用帳戶或只針對選取的 VM 啟用這項功能。 請參閱 [Azure 安全性中心常見問題集](security-center-faq.md) 要深入了解支援的 Vm。 

## 後續步驟
在本文件中，您已了解如何在「Azure 資訊安全中心」設定安全性原則。 若要深入了解「Azure 資訊安全中心」，請參閱下列主題：

- [Azure 資訊安全中心的安全性健全狀況監視](security-center-monitoring.md) – 了解如何監視您的 Azure 資源的健全狀況
- [管理和 Azure 資訊安全中心的安全性警示回應](security-center-managing-and-responding-alerts.md) -了解如何管理和回應安全性警示
- [Azure 安全性中心常見問題集](security-center-faq.md) – 尋找使用服務的相關的常見問題集
- [Azure 安全性部落格](http://blogs.msdn.com/b/azuresecurity/) – 尋找關於 Azure 安全性與相容性部落格文章

