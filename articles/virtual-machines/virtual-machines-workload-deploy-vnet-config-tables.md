<properties
    pageTitle="使用組態資料表的虛擬網路 | Microsoft Azure"
    description="了解如何利用使用預先決定設定的組態資料表設定，來設定跨單位 Azure 虛擬網路。"
    documentationCenter=""
    services="virtual-machines"
    authors="JoeDavies-MSFT"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="Windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/20/2015"
    ms.author="josephd"/>

# 使用設定表格建立跨單位虛擬網路

[AZURE.INCLUDE [learn-about-deployment-models-classic-include](../../includes/learn-about-deployment-models-classic-include.md)] 資源管理員部署模型。

本主題將使用先前在下列設定表格組合中指定的設定，帶領您逐步建立跨單位虛擬網路：

- 表格 V：跨單位虛擬網路設定
- 表格 S：虛擬網路中的子網路
- 表格 D：內部部署 DNS 伺服器
- 表格 L：適用於區域網路的位址首碼

這些表格通常是在下列主題中進行填寫：說明在 Azure 中設定 IT 工作負載的方法，並包含跨單位虛擬網路。 請參閱 [第 1 階段 ︰ 設定 Azure](virtual-machines-workload-intranet-sharepoint-phase1.md) 範例。

下列程序會參考這些表格中的資訊，引導您進行完整的虛擬網路設定程序。 如果您未在另一個主題中，這些資料表中已經指定的設定，但您仍想要設定跨單位虛擬網路，請參閱 [設定 Azure 虛擬網路的跨單位站台對站台連線](../vpn-gateway/vpn-gateway-site-to-site-create.md)。

> [AZURE.NOTE] 此程序會帶您逐步完成建立虛擬網路使用站台對站 VPN 連線。 站台對站連線使用 Azure ExpressRoute 的相關資訊，請參閱 [ExpressRoute 技術概觀](../expressroute/expressroute-introduction.md)。

## 使用您的設定表格設定建立新的跨單位 Azure 虛擬網路

1. 登入 [Azure 傳統入口網站]。
2. 按一下工作列中 **新增 > 網路服務 > 虛擬網路 > 自訂建立**。
3. 在 **虛擬網路詳細資料** 頁面 ︰
    - 在 **名稱**, ，輸入表 V 中的項目 1 的名稱。
    - 在 **位置**, ，從表 V 中的項目 2 中選取區域。
4. 按一下 [下一步] 箭頭以繼續。
5. 在 **DNS 伺服器和 VPN 連線能力** 頁面 ︰
    - 在 **DNS 伺服器**, ，針對表格 D 中的每個項目設定易記名稱和您的內部 DNS 伺服器的 IP 位址。
    - 在 **站台對站連線能力**, ，請選取 **設定站台對站 VPN**。
    - 如果您已經設定區域網路且想来使用它，選取其名稱中的 **區域網路**。 如果您想要建立新的區域網路，請選取 **指定新的區域網路** 中 **區域網路**。
    - 如果您沒有訂用帳戶已經設定區域網路，您將不會看到 **區域網路** 欄位。
6. 按一下 [下一步] 箭頭以繼續。
7. 在 **站台對站連線能力** 頁面 (如果您選取 **指定新的區域網路** 在步驟 5 中):
    - 在 **名稱**, ，輸入表 V （區域網路名稱） 中的項目 3 的名稱。
    - 在 **VPN 裝置 IP 位址**, ，輸入表 V 中的項目 4 的位址。
    - 在 **位址空間**, ，針對表格 L 中的每個項目輸入前置詞依據組織網路的 IP 位址空間 (在 **起始 IP**) 和首碼長度 (在 **CIDR （位址計數）**)。
8. 按一下 [下一步] 箭頭以繼續。
9. 在 **虛擬網路位址空間** 頁面 ︰
    - 在     **位址空間**, ，輸入表 V 中的項目 5 的虛擬網路的私人 IP 位址空間方面的前置詞 (在 **起始 IP**) 和首碼長度 (在 **CIDR （位址計數）**)。
    - 在 **子網路**, ，針對表格 s 中的每個項目
        - 輸入的子網路名稱 **子網路** 資料行中，視需要覆寫預設名稱。
        - 輸入子網路，根據前置詞的私人 IP 位址空間 (在 **起始 IP**) 和首碼長度 (在 **CIDR （位址計數）**)。
    - 按一下 [ **新增閘道子網路**。
10. 按一下勾號以完成設定。

## 其他資源

[虛擬網路概觀](../virtual-network/virtual-networks-overview.md)

[虛擬網路設定工作](../documentation/services/virtual-machines/)

[設定 Azure 虛擬網路的跨單位站對站連線](../vpn-gateway/vpn-gateway-site-to-site-create.md)


