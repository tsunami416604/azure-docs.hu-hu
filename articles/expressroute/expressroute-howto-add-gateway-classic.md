---
title: 'Azure ExpressRoute: Átjáró hozzáadása virtuális hálózathoz: klasszikus'
description: VNet-átjáró konfigurálása egy klasszikus üzembe helyezési modell virtuális hálózat powershell egy ExpressRoute-konfiguráció használatával.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: article
ms.date: 12/06/2019
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: d7927af5b831dbe3294e1abc3a878e323bf1867e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74928049"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell-classic"></a>Virtuális hálózati átjáró konfigurálása az ExpressRoute számára a PowerShell használatával (klasszikus)
> [!div class="op_single_selector"]
> * [Resource Manager – PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Klasszikus - PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Videó – Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

Ez a cikk végigvezeti a virtuális hálózati (VNet) átjáró hozzáadásának, átméretezésének és eltávolításának lépésein egy már meglévő virtuális hálózathoz. Ennek a konfigurációnak a lépései kifejezetten a **klasszikus központi telepítési modell** használatával létrehozott és ExpressRoute-konfigurációban használt virtuális hálózatokra vonatkoznak. 

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Tudnivalók az Azure üzembehelyezési modellekről**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-beginning"></a>Mielőtt hozzálát
Ellenőrizze, hogy telepítette-e az ehhez a konfigurációhoz szükséges Azure PowerShell-parancsmagokat.

[!INCLUDE [classic powershell install instructions](../../includes/expressroute-poweshell-classic-install-include.md)]

[!INCLUDE [expressroute-gateway-classic-ps](../../includes/expressroute-gateway-classic-ps-include.md)]

## <a name="next-steps"></a>További lépések
A virtuális hálózat átjárólétrehozása után a virtuális hálózatot egy ExpressRoute-kapcsolathoz csatolhatja. Lásd: [Virtuális hálózat csatolása ExpressRoute-kapcsolattal](expressroute-howto-linkvnet-classic.md).

