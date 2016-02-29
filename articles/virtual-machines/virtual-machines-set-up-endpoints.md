<properties
    pageTitle="在 Azure 中的虛擬機器上設定端點"
    description="了解如何設定 Azure 傳統入口網站中的端點，以允許與 Azure 中的虛擬機器進行通訊。"
    services="virtual-machines"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/28/2015"
    ms.author="cynthn"/>

#如何設定虛擬機器的端點

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] 資源管理員模型。

您在 Azure 中建立的所有虛擬機器都可以自動使用私人網路通道，與相同雲端服務或虛擬網路中的其他虛擬機器進行通訊。 不過，網際網路或其他虛擬網路上的電腦需要端點，才能將傳入網路流量導向至虛擬機器。

當您在 Azure 傳統入口網站建立虛擬機器時，也會自動建立遠端桌面、Windows PowerShell 遠端處理和安全殼層 (SSH) 的端點。 建立虛擬機器或日後有需要時，您可以設定其他端點。

[AZURE.INCLUDE [service-management-pointer-to-resource-manager](../../includes/service-management-pointer-to-resource-manager.md)]

- [關於網路安全性群組](virtual-networks-nsg.md)

請注意，網路安全性群組會控制虛擬機器的存取權，但是不提供連接埠轉送功能。 若要執行連接埠轉送，請參閱下列文章：

- [開始使用 Azure 資源管理員設定網際網路面向的負載平衡器](../load-balancer/load-balancer-arm-powershell.md)

每個端點都有一個公用連接埠和一個私人連接埠：

- Azure 負載平衡器使用公用連接埠接聽從網際網路到虛擬機器的連入流量。
- 虛擬機器使用私人連接埠接聽目的地通常為虛擬機器上執行的應用程式或服務的連入流量。

當您使用 Azure 傳統入口網站建立端點時，會提供 IP 通訊協定的預設值以及已知網路通訊協定的 TCP 或 UDP 連接埠。 針對自訂端點，您必須指定正確的 IP 通訊協定 (TCP 或 UDP) 以及公用和私人連接埠。 若要將連入流量隨機分散到多部虛擬機器，您必須建立負載平衡的集合，其中包含多個端點。

建立端點之後，您可以使用存取控制清單 (ACL) 定義規則，根據來源 IP 位址允許或拒絕端點公用連接埠的連入流量。 不過，如果虛擬機器位於 Azure 虛擬網路，請改用網路安全性群組。 如需詳細資訊，請參閱 [關於網路安全性群組](virtual-networks-nsg.md)。

> [AZURE.NOTE]: Azure 虛擬機器防火牆設定會自動完成連接埠關聯與遠端桌面和安全殼層 (SSH)，且大部分情況下，Windows PowerShell 遠端功能。 至於其他所有端點的指定連接埠，不會自動設定虛擬機器的防火牆。 您建立虛擬機器的端點時，需要確定虛擬機器的防火牆也允許端點組態相對應通訊協定和私人連接埠的流量。

##建立端點

1.  如果您未曾執行過這項操作，請登入 Azure 傳統入口網站。
2.  按一下 [ **虛擬機器**, ，然後按一下您想要設定虛擬機器的名稱。
3.  按一下 [ **端點**。  **端點** 頁面會列出虛擬機器目前的所有端點。

    ![端點](./media/virtual-machines-set-up-endpoints/endpointswindows.png)

4.  在工作列上，按一下 [ **新增**。
5.  在 **將端點加入至虛擬機器** 頁面上，選擇端點的型別。

    - 如果您要建立新的端點不包含在負載平衡的集合，或在新的負載平衡集合中的第一個端點，選擇 **新增獨立端點**, ，然後按一下 [向左箭號。
    - 否則，請選擇 **將端點加入至現有的負載平衡集**, ，選取負載平衡集名稱，然後按一下向左箭號。 在 **指定端點的詳細資料** 頁面上，輸入端點的名稱，然後按一下核取記號，以建立端點。

6.  在 **指定端點的詳細資料** 頁面上，輸入中的端點名稱 **名稱**。 您也可以選擇網路通訊協定名稱，從清單中，將會填入初始值 **通訊協定**, ，**公用連接埠**, ，和 **私用連接埠**。
7.  對於自訂端點，在 **通訊協定**, ，選擇 [ **TCP** 或 **UDP**。
8.  對於自訂連接埠，在 **公用連接埠**, ，輸入網際網路連入流量的連接埠號碼。 在 **私用連接埠**, ，輸入虛擬機器所接聽的連接埠號碼。 這些連接埠號碼可以不同。 請確定已經設定虛擬機器的防火牆允許通訊協定 (在步驟 7 中) 和私人連接埠對應的流量。
9.  如果此端點將成為負載平衡的集合中的第一個，請按一下 [ **建立負載平衡集**, ，然後按一下向右箭號。 在 **設定負載平衡集** 頁面上，指定負載平衡集名稱、 探查通訊協定和連接埠，以及探查間隔和傳送的探查數。 Azure 負載平衡器會將探查傳送到負載平衡集合中的虛擬機器來監視其可用性。 Azure 負載平衡器不會將流量轉送到未回應探查的虛擬機器。 按一下向右箭頭。
10. 按一下核取記號以建立端點。

新的端點將會列在 **端點** 頁面。

![端點建立成功](./media/virtual-machines-set-up-endpoints/endpointwindowsnew.png)

若要使用 Azure PowerShell cmdlet 來設定此項目，請參閱 [Add-azureendpoint](https://msdn.microsoft.com/library/azure/dn495300.aspx)。

##在端點上管理 ACL

為了定義可以傳送流量的電腦集合，端點上的 ACL 能夠根據來源 IP 位址限制流量。 請依照這些步驟，在端點上新增、修改或移除 ACL。

> [AZURE.NOTE] 如果端點是負載平衡集的一部分，您對端點上的 ACL 任何變更都會套用至集合中的所有端點。

如果虛擬機器位於 Azure 虛擬網路，建議使用網路安全性群組而非 ACL。 如需詳細資訊，請參閱 [關於網路安全性群組](virtual-networks-nsg.md)。

1.  如果您未曾執行過這項操作，請登入 Azure 傳統入口網站。
2.  按一下 [ **虛擬機器**, ，然後按一下您想要設定虛擬機器的名稱。
3.  按一下 [ **端點**。 從清單中選取適當的端點。

    ![ACL 清單](./media/virtual-machines-set-up-endpoints/EndpointsShowsDefaultEndpointsForVM.png)

5.  在工作列上，按一下 [ **管理 ACL** 開啟 **指定 ACL 詳細資料** 對話方塊。

    ![指定 ACL 詳細資料](./media/virtual-machines-set-up-endpoints/EndpointACLdetails.png)

6.  使用清單中的資料列來新增、刪除或編輯 ACL 的規則並變更奇順序。  **遠端子網路** 值是 Azure 負載平衡器用來允許或拒絕根據來源 IP 位址的流量網際網路的連入流量的 IP 位址範圍。 請務必指定 CIDR 格式 (也就是位址前置詞格式) 的 IP 位址範圍。 例如，131.107.0.0/16。

您可以使用規則僅允許網際網路上的電腦對應的特定電腦流量，或拒絕特定已知位址範圍的流量。

規則的評估順序是從第一個規則開始，一直到最後一個規則為止。 這表示規則應會以最寬鬆到最嚴格的順序來排列。 如需範例和詳細資訊，請參閱 [什麼是網路存取控制清單?](../virtual-network/virtual-networks-acl/)。

若要使用 Azure PowerShell cmdlet 來設定此項目，請參閱 [管理存取控制端點的清單 (Acl)，使用 PowerShell](../virtual-network/virtual-networks-acl-powershell.md)。

## 其他資源

[Azure 基礎結構服務的負載平衡](virtual-machines-load-balance.md)

