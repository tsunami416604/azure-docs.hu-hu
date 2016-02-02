<properties 
   pageTitle="混合式網路功能 (Azure 架構模式)" 
   description="混合式網路功能模式是基礎結構區域的一部分，CPIF 架構文件中有相關的詳細說明。" 
   services="" 
   documentationCenter="" 
   authors="arynes" 
   manager="fredhar" 
   editor=""/>

<tags
   ms.service="multiple"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple" 
   ms.date="03/25/2015"
   ms.author="arynes"/>


# 混合式網路功能 (Azure 架構模式)



CPIF 描述組織、客戶及合作夥伴應如何使用 Azure、System Center 及 Windows Server 的混合式雲端平台和管理功能來設計和部署以雲端為目標的工作負載。

**混合式網路功能**模式是**基礎結構**區域的一部分，CPIF 架構文件中有相關的詳細說明。

## 混合式網路功能

混合式網路功能設計模式詳述供應網路功能所需的 Azure 功能和服務；網路功能可跨越地理界限提供可預期的效能和高可用性。 Microsoft Azure 文件站台提供 Microsoft Azure 區域和各區域可用之服務的完整清單。 本文件提供 Microsoft Azure 混合式環境之網路功能的概觀。 Microsoft Azure 虛擬網路功能可讓您在 Azure 中建立邏輯隔離的網路，並透過網際網路或使用私用網路連線將其連接到內部部署資料中心。 此外，您也可以使用 IPsec VPN 連線將個別的用戶端機器連接到隔離的 Azure 網路。

混合式網路功能架構模式包含下列焦點區域：

- 將內部部署網路連接到 Azure
- 跨越區域延伸 Azure 虛擬網路
- 在訂用帳戶之間延伸 Azure 虛擬網路
- 為開發人員提供遠端網路存取

## 架構模式概觀

因可建立的可行案例數目不足，導致混合式網路功能架構模式過於複雜。 本架構模式將著重在下列四個案例：

- 單一訂用帳戶和區域內，搭配多重躍點虛擬網路路由的站對站混合式網路功能
- 跨越訂用帳戶和區域，搭配多重躍點虛擬網路路由的站對站混合式網路功能
- 使用 MPLS 連線功能的 ExpressRoute 混合式網路功能
- 使用 IXP 連線功能的 ExpressRoute 混合式網路功能

## 其他資源



## 另請參閱















