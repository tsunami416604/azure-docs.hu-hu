---
title: Klasszikus az Azure Resource Manager áttelepítéstechnikai mélymerülés
description: Technikai részletes merülés a platform által támogatott erőforrások áttelepítése a klasszikus üzembe helyezési modell az Azure Resource Manager.
author: tanmaygore
manager: vashan
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: ebd67bdf34bce1d90057ca402b4e3be243b7ec6c
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81866204"
---
# <a name="technical-deep-dive-on-platform-supported-migration-from-classic-to-azure-resource-manager"></a>Részletes műszaki útmutató a klasszikusból az Azure Resource Manager-alapú üzemi modellbe történő, platform által támogatott migrálásról

> [!IMPORTANT]
> Ma az IaaS virtuális gépek mintegy 90%-a használja az [Azure Resource Managert.](https://azure.microsoft.com/features/resource-manager/) 2020. február 28-tól a klasszikus virtuális gépek elavultak, és 2023. március 1-jén teljesen nyugdíjba kerülnek. [Tudjon meg többet]( https://aka.ms/classicvmretirement) erről az eprecációról [és arról, hogy ez milyen hatással van Önre.](https://docs.microsoft.com/azure/virtual-machines/classic-vm-deprecation#how-does-this-affect-me)

Vessünk egy mély merülés az Azure klasszikus üzembe helyezési modell ről az Azure Resource Manager üzembe helyezési modell áttelepítése. Az erőforrásokat erőforrás- és szolgáltatásszinten vizsgáljuk, hogy könnyebben megértheti, hogy az Azure platform hogyan telepíti át az erőforrásokat a két üzembe helyezési modell között. További információért olvassa el a szolgáltatásbejelentésről szóló cikket: [Az IaaS-erőforrások platform által támogatott áttelepítése klasszikusról Az Azure Resource Manager szolgáltatásra.](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[!INCLUDE [virtual-machines-common-migration-deep-dive](../../../includes/virtual-machines-common-classic-resource-manager-migration-deep-dive.md)]

## <a name="next-steps"></a>További lépések

* [Az IaaS-erőforrások platform által támogatott áttelepítésének áttekintése klasszikusról Azure Resource Managerre](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Az IaaS-erőforrások klasszikusból Azure Resource Manager-alapú környezetbe való áttelepítésének megtervezése](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Az IaaS-erőforrások klasszikusról az Azure Resource Managerbe való áttelepítése a PowerShell használatával](migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [A CLI használatával áttelepítheti az IaaS-erőforrásokat klasszikusról Azure Resource Managerre](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [VPN-átjáró klasszikus az Erőforrás-kezelő áttelepítéséhez](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-classic-resource-manager-migration)
* [ExpressRoute-áramkörök és a kapcsolódó virtuális hálózatok áttelepítése a klasszikusról az Erőforrás-kezelő telepítési modelljére](https://docs.microsoft.com/azure/expressroute/expressroute-migration-classic-resource-manager)
* [Közösségi eszközök az IaaS-erőforrások klasszikusról Azure Resource Managerre való áttelepítéséhez](migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [A leggyakoribb áttelepítési hibák áttekintése](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Tekintse át az IaaS-erőforrások klasszikusról Azure Resource Managerbe való áttelepítésével kapcsolatos leggyakoribb kérdéseket](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
