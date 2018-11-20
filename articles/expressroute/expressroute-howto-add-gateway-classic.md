---
title: 'PowerShell-lel ExpressRoute virtuális hálózati átjáró konfigurálása: klasszikus: Azure |} A Microsoft Docs'
description: Klasszikus üzemi modellben a virtuális hálózati átjáró konfigurálása virtuális hálózathoz a PowerShell használatával egy ExpressRoute-konfiguráció modell.
documentationcenter: na
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: article
ms.date: 11/05/2018
ms.author: charwen
ms.openlocfilehash: 366aed206e8b1fb72547a64a957d83eec78c1358
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2018
ms.locfileid: "52161981"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell-classic"></a>PowerShell (klasszikus) használatával ExpressRoute virtuális hálózati átjáró konfigurálása
> [!div class="op_single_selector"]
> * [Resource Manager – PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Klasszikus – PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Videó – Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

Ez a cikk végigvezeti a lépéseket adja hozzá, átméretezése és egy már meglévő virtuális hálózat (VNet) virtuális hálózati átjáró eltávolítása. Ez a konfiguráció lépései a kifejezetten a használatával létrehozott virtuális hálózatok vannak a **klasszikus üzemi modell** és a egy ExpressRoute-konfigurációban használni kívánt. 

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Tudnivalók az Azure üzembe helyezési modelljeiről**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-beginning"></a>Mielőtt hozzálát
Győződjön meg arról, hogy telepítette-e ehhez a konfigurációhoz szükséges az Azure PowerShell-parancsmagok (1.0.2-es vagy újabb). Ha még nem telepítette a parancsmagok, szüksége lesz erre a konfigurációs lépések megkezdése előtt. Azure PowerShell telepítésével kapcsolatos további információkért lásd: [telepítése és konfigurálása az Azure PowerShell-lel](/powershell/azure/overview).

[!INCLUDE [expressroute-gateway-classic-ps](../../includes/expressroute-gateway-classic-ps-include.md)]

## <a name="next-steps"></a>További lépések
Miután létrehozta a virtuális hálózati átjáró, kapcsolat a virtuális hálózat csatlakoztatása egy ExpressRoute-kapcsolatcsoporthoz. Lásd: [virtuális hálózat összekapcsolása egy ExpressRoute-kapcsolatcsoport](expressroute-howto-linkvnet-classic.md).

