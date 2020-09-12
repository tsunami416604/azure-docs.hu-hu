---
title: 'Azure ExpressRoute: átjáró hozzáadása VNet: PowerShell'
description: Ez a cikk segítséget nyújt a VNet-átjáró hozzáadásához egy már létrehozott Resource Manager-VNet a ExpressRoute.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 02/21/2019
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: cfdab553ba7f6506f66e892da3f1e8ce01c6d8bb
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/02/2020
ms.locfileid: "89396386"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell"></a>Az ExpressRoute virtuális hálózati átjáróinak konfigurálása a PowerShell-lel
> [!div class="op_single_selector"]
> * [Resource Manager – Azure Portal](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager – PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Klasszikus – PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Videó – Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

Ez a cikk segítséget nyújt egy virtuális hálózati (VNet-) átjáró hozzáadásához, átméretezéséhez és eltávolításához egy meglévő VNet. A konfiguráció lépései a ExpressRoute-konfiguráció Resource Manager-alapú üzemi modelljével létrehozott virtuális hálózatok vonatkoznak. További információ: [a ExpressRoute virtuális hálózati átjáróinak ismertetése](expressroute-about-virtual-network-gateways.md).

## <a name="before-beginning"></a>Mielőtt hozzálát

### <a name="working-with-powershell"></a>A PowerShell használata

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [working with cloud shell](../../includes/expressroute-cloudshell-powershell-about.md)]

### <a name="configuration-reference-list"></a>Konfigurációs hivatkozások listája

[!INCLUDE [expressroute-gateway-rm-ps](../../includes/expressroute-gateway-rm-ps-include.md)]

## <a name="next-steps"></a>Következő lépések
Miután létrehozta a VNet-átjárót, összekapcsolhatja a VNet egy ExpressRoute-áramkörrel. Lásd: [Virtual Network csatolása ExpressRoute-áramkörhöz](expressroute-howto-linkvnet-arm.md).
