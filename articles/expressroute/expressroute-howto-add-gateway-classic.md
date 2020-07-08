---
title: 'Azure ExpressRoute: átjáró hozzáadása VNet: klasszikus'
description: VNet-átjáró konfigurálása klasszikus üzemi modell VNet a PowerShell használatával egy ExpressRoute-konfigurációhoz.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: how-to
ms.date: 12/06/2019
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: bcffd60596f8c2286b91eaf13be3e09bb8bec94f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84736407"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell-classic"></a>ExpressRoute virtuális hálózati átjáró konfigurálása a PowerShell (klasszikus) használatával
> [!div class="op_single_selector"]
> * [Resource Manager – PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Klasszikus – PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Videó – Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

Ebből a cikkből megtudhatja, hogyan adhat hozzá, méretezheti át és távolíthat el egy virtuális hálózati (VNet-) átjárót egy már meglévő VNet. A konfiguráció lépései kifejezetten a **klasszikus üzemi modellel** létrehozott és a ExpressRoute-konfigurációban használt virtuális hálózatok vonatkoznak. 

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Tudnivalók az Azure üzembehelyezési modellekről**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-beginning"></a>Mielőtt hozzálát
Ellenőrizze, hogy telepítette-e az ehhez a konfigurációhoz szükséges Azure PowerShell-parancsmagokat.

[!INCLUDE [classic powershell install instructions](../../includes/expressroute-poweshell-classic-install-include.md)]

[!INCLUDE [expressroute-gateway-classic-ps](../../includes/expressroute-gateway-classic-ps-include.md)]

## <a name="next-steps"></a>További lépések
Miután létrehozta a VNet-átjárót, összekapcsolhatja a VNet egy ExpressRoute-áramkörrel. Lásd: [Virtual Network csatolása ExpressRoute-áramkörhöz](expressroute-howto-linkvnet-classic.md).

