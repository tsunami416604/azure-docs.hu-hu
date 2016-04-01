<properties
   pageTitle="在 Azure 入口網站中建立 VM 的 FQDN | Microsoft Azure"
   description="了解如何在 Azure 入口網站中為基於資源管理員的虛擬機器建立完整格式的網域名稱或 FQDN。"
   services="virtual-machines"
   documentationCenter=""
   authors="dsk-2015"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/21/2015"
   ms.author="dkshir"/>

# 在 Azure 入口網站中建立完整格式的網域名稱

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] 傳統部署模型。


當您建立虛擬機器中 [Azure 入口網站](https://portal.azure.com) 使用 **資源管理員** 部署模型，入口網站建立虛擬機器的公用 IP 資源。 您可以使用此 IP 位址從遠端存取虛擬機器。 不過，在入口網站不會建立 [完整的網域名稱](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) 或 FQDN，預設情況下。 因為使用 FQDN 而非 IP 位址較容易記得，這篇文章會示範加入一個 FQDN 至您的虛擬機器。

本文假設您已登入您的訂閱，在網站中，並使用可用的映像，以建立虛擬機器 **資源管理員**。 一旦您的虛擬機器開始執行，請依照下列步驟。

1.  檢視入口網站上的虛擬機器設定，並按一下 [公用 IP 位址]。

    ![找出 IP 資源](media/virtual-machines-create-fqdn-on-portal/locatePublicIP.PNG)

2.  **中斷關聯** 從虛擬機器的公用 IP。 請注意，它還未顯示網域名稱。 按一下之後 **是** ] 按鈕，可能需要幾秒鐘的時間才會完成 dissociation。

    ![將 IP 資源解除關聯](media/virtual-machines-create-fqdn-on-portal/dissociateIP.PNG)

    完成下列步驟之後，我們會將這個公用 IP 與虛擬機器結合。 如果公用 IP 是 _動態公用 IP_, ，則會遺失的 IPV4 位址和 FQDN 設定後，就會指派一個新。

3.  一次 **Dissociate** 按鈕呈現灰色，按一下 [ **所有設定** 區段的公用 IP，以及開啟 **組態** ] 索引標籤。 輸入所需的 DNS 名稱標籤。 **儲存** 這項設定。

    ![輸入 DNS 名稱標籤](media/virtual-machines-create-fqdn-on-portal/dnsNameLabel.PNG)

4.  返回入口網站中按一下虛擬機器分頁 **所有設定** 虛擬機器。 開啟 **網路介面** 索引標籤，然後按一下與此虛擬機器相關聯的網路介面資源。 這會開啟 **網路介面** 入口網站中的分頁。

    ![開啟網路介面](media/virtual-machines-create-fqdn-on-portal/openNetworkInterface.PNG)

5.  請注意， **公用 IP 位址** 欄位是空白的網路介面。 按一下 [ **所有設定** 區段，此網路介面，以及開啟 **IP 位址** ] 索引標籤。 在 **IP 位址** 刀鋒視窗中，按一下 [ **啟用** 的 **公用 IP 位址** 欄位。 選取 **IP 位址設定所需設定** 索引標籤並選取您先前已取消關聯的預設 IP。 按一下 [ **儲存**。 可能需要幾分鐘的時間才能加回 IP 資源。

    ![設定 IP 資源](media/virtual-machines-create-fqdn-on-portal/configureIP.PNG)

6.  關閉所有其他的刀鋒視窗，並返回 **虛擬機器** 刀鋒視窗。 在設定中按一下 [公用 IP] 資源。 請注意公用 IP 刀鋒視窗現在會顯示所需的 FQDN，因為 **DNS 名稱**。

    ![FQDN 已建立](media/virtual-machines-create-fqdn-on-portal/fqdnCreated.PNG)

    您可以立即使用這個 DNS 名稱從遠端連線到虛擬機器。 例如，連接到具有完整網域名稱 `testdnslabel.eastus.cloudapp.azure.com` 和使用者名稱 `adminuser` 的 Linux 虛擬機器時使用 `SSH adminuser@testdnslabel.eastus.cloudapp.azure.com`。


