---
title: 'Azure ExpressRoute: Átjáró hozzáadása virtuális hálózathoz: PowerShell'
description: Ez a cikk segítséget nyújt a virtuális hálózat átjáró hozzáadása egy már létrehozott Erőforrás-kezelő virtuális hálózat ExpressRoute.This article helps you add VNet gateway to an already created Resource Manager VNet for ExpressRoute.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: article
ms.date: 02/21/2019
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: 6e6d79afa1126acb26cb8856c39ba486cce31a5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74037429"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell"></a>Az ExpressRoute virtuális hálózati átjáróinak konfigurálása a PowerShell-lel
> [!div class="op_single_selector"]
> * [Resource Manager – Azure Portal](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager – PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Klasszikus - PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Videó – Azure-portál](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

Ez a cikk segít egy virtuális hálózati (VNet) átjáró hozzáadásában, átméretezésében és eltávolításában egy már meglévő virtuális hálózathoz. Ennek a konfigurációnak a lépései azokra a virtuális hálózatokra vonatkoznak, amelyeket az ExpressRoute-konfiguráció Resource Manager központi telepítési modelljével hoztak létre. További információ: [Az ExpressRoute virtuális hálózati átjáróinak betekintése.](expressroute-about-virtual-network-gateways.md)

## <a name="before-beginning"></a>Mielőtt hozzálát

### <a name="working-with-powershell"></a>A PowerShell együttműködése

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [working with cloud shell](../../includes/expressroute-cloudshell-powershell-about.md)]

### <a name="configuration-reference-list"></a>Konfigurációs hivatkozási lista

[!INCLUDE [expressroute-gateway-rm-ps](../../includes/expressroute-gateway-rm-ps-include.md)]

## <a name="next-steps"></a>További lépések
A virtuális hálózat átjárólétrehozása után a virtuális hálózatot egy ExpressRoute-kapcsolathoz csatolhatja. Lásd: [Virtuális hálózat csatolása ExpressRoute-kapcsolattal](expressroute-howto-linkvnet-arm.md).
