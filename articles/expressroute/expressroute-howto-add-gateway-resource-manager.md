---
title: 'Adjon hozzá egy virtuális hálózati átjárót egy virtuális hálózathoz az expressroute-hoz: PowerShell: Azure |} A Microsoft Docs'
description: Ez a cikk végigvezeti egy már létrehozott Resource Manager virtuális hálózathoz, az ExpressRoute virtuális hálózati átjáró hozzáadása.
documentationcenter: na
services: expressroute
author: charwen
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: 63e0bd60-abad-4963-8e27-3aa973e0d968
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/17/2017
ms.author: charwen
ms.openlocfilehash: 32e49a11b02afedf69e5aa61ca2f626ffe5a125e
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51239576"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell"></a>Az ExpressRoute virtuális hálózati átjáróinak konfigurálása a PowerShell-lel
> [!div class="op_single_selector"]
> * [Resource Manager – Azure Portal](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager – PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Klasszikus – PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Videó – Azure portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

Ez a cikk végigvezeti a lépéseken hozzáadásához, méretezze át, és távolítsa el a virtuális hálózati (VNet) átjáró egy már meglévő virtuális hálózat címtere. Ez a konfiguráció lépései kifejezetten egy ExpressRoute-konfigurációban használt a Resource Manager üzemi modell használatával létrehozott virtuális hálózatok vonatkoznak. További információ a virtuális hálózati átjárók és az expressroute-hoz a gateway konfigurációs beállításairól: [kapcsolatos az ExpressRoute virtuális hálózati átjárók](expressroute-about-virtual-network-gateways.md). 


## <a name="before-beginning"></a>Mielőtt hozzálát
Győződjön meg arról, hogy telepítette a legújabb Azure PowerShell-parancsmagokat. Ha még nem telepítette a legújabb parancsmagok, meg kell tennie a konfigurációs lépések megkezdése előtt. További információk: [Az Azure PowerShell telepítése és konfigurálása](/powershell/azure/overview).

[!INCLUDE [expressroute-gateway-rm-ps](../../includes/expressroute-gateway-rm-ps-include.md)]

## <a name="next-steps"></a>További lépések
Miután létrehozta a virtuális hálózati átjáró, kapcsolat a virtuális hálózat csatlakoztatása egy ExpressRoute-kapcsolatcsoporthoz. Lásd: [virtuális hálózat összekapcsolása egy ExpressRoute-kapcsolatcsoport](expressroute-howto-linkvnet-arm.md).

