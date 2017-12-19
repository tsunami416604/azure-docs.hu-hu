---
title: "Vegyen fel egy virtuális hálózat virtuális hálózati átjáró ExpressRoute: PowerShell: Azure |} Microsoft Docs"
description: "Ez a cikk bemutatja, hogyan ExpressRoute egy már létrehozott erőforrás-kezelő virtuális hálózatot a virtuális hálózat átjárót ad hozzá."
documentationcenter: na
services: expressroute
author: charwen
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 63e0bd60-abad-4963-8e27-3aa973e0d968
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/17/2017
ms.author: charwen
ms.openlocfilehash: 3aeddd03e0be548933775164ae790ba208fc13ae
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2017
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell"></a>Az ExpressRoute virtuális hálózati átjáróinak konfigurálása a PowerShell-lel
> [!div class="op_single_selector"]
> * [Resource Manager – Azure Portal](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager – PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Klasszikus - PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Video - Azure-portálon](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

Ez a cikk végigvezeti a lépéseken hozzáadásához átméretezése, és távolítsa el a virtuális hálózathoz (VNet) átjáró egy már meglévő vnet. Ehhez a konfigurációhoz lépésekre, kifejezetten a Vnetek létrehozott erőforrás-kezelő telepítési modellel, amely egy ExpressRoute-konfigurációt fogja használni. További információ a virtuális hálózati átjárók és az átjáró konfigurációs beállításainak ExpressRoute: [kapcsolatos az ExpressRoute virtuális hálózati átjárók](expressroute-about-virtual-network-gateways.md). 


## <a name="before-beginning"></a>Mielőtt hozzálát
Győződjön meg arról, hogy telepítette-e a legújabb Azure PowerShell-parancsmagokat. Ha még nem telepítette a legújabb parancsmagok, ehhez a konfigurációs lépések megkezdése előtt szüksége. További információk: [Az Azure PowerShell telepítése és konfigurálása](/powershell/azure/overview).

[!INCLUDE [expressroute-gateway-rm-ps](../../includes/expressroute-gateway-rm-ps-include.md)]

## <a name="next-steps"></a>Következő lépések
Miután létrehozta a virtuális hálózat átjáró, a virtuális hálózat hozzákapcsolhatja egy ExpressRoute-kapcsolatcsoportot. Lásd: [virtuális hálózat csatolása ExpressRoute-kapcsolatcsoportot](expressroute-howto-linkvnet-arm.md).

