---
title: 'Adjon hozzá egy virtuális hálózati átjárót egy virtuális hálózathoz az expressroute-hoz: PowerShell: Azure |} A Microsoft Docs'
description: Ez a cikk segít egy már létrehozott Resource Manager virtuális hálózathoz, az ExpressRoute virtuális hálózati átjáró hozzáadása.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: article
ms.date: 04/17/2017
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: 8f8d021adae1cb25198c53e486fdb50e42ac211b
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53097913"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell"></a>Az ExpressRoute virtuális hálózati átjáróinak konfigurálása a PowerShell-lel
> [!div class="op_single_selector"]
> * [Resource Manager – Azure Portal](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager – PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Klasszikus – PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Videó – Azure portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

Ez a cikk segít hozzáadása, átméretezése és egy már meglévő virtuális hálózat (VNet) virtuális hálózati átjáró eltávolítása. Ez a konfiguráció lépései kifejezetten egy ExpressRoute-konfigurációban használt a Resource Manager üzemi modell használatával létrehozott virtuális hálózatok vonatkoznak. További információ a virtuális hálózati átjárók és az expressroute-hoz a gateway konfigurációs beállításairól: [kapcsolatos az ExpressRoute virtuális hálózati átjárók](expressroute-about-virtual-network-gateways.md). 


## <a name="before-beginning"></a>Mielőtt hozzálát
Győződjön meg arról, hogy telepítette a legújabb Azure PowerShell-parancsmagokat. Ha még nem telepítette a legújabb parancsmagok, meg kell tennie a konfigurációs lépések megkezdése előtt. További információk: [Az Azure PowerShell telepítése és konfigurálása](/powershell/azure/overview).

[!INCLUDE [expressroute-gateway-rm-ps](../../includes/expressroute-gateway-rm-ps-include.md)]

## <a name="next-steps"></a>További lépések
Miután létrehozta a virtuális hálózati átjáró, kapcsolat a virtuális hálózat csatlakoztatása egy ExpressRoute-kapcsolatcsoporthoz. Lásd: [virtuális hálózat összekapcsolása egy ExpressRoute-kapcsolatcsoport](expressroute-howto-linkvnet-arm.md).

