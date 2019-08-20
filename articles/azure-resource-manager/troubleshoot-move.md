---
title: Hibák elhárítása az Azure-erőforrások új előfizetésre vagy erőforráscsoporthoz való áthelyezésekor
description: Azure Resource Manager segítségével az erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 08/19/2019
ms.author: tomfitz
ms.openlocfilehash: 445ee2784a74a366089a49a0e2f2f17d51ef93bf
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624300"
---
# <a name="troubleshoot-moving-azure-resources-to-new-resource-group-or-subscription"></a>Azure-erőforrások új erőforráscsoporthoz vagy előfizetésbe való áthelyezésének hibája

Ez a cikk javaslatokat nyújt az erőforrások áthelyezésekor felmerülő problémák megoldásához.

## <a name="upgrade-a-subscription"></a>Előfizetés frissítése

Ha valóban frissíteni szeretné az Azure-előfizetését (például az ingyenesről az utólagos elszámolású váltásra), akkor konvertálnia kell az előfizetést.

* Ingyenes próbaverzió frissítése, lásd: [az ingyenes próba- vagy a Microsoft Imagine Azure-előfizetés frissítése használatalapú fizetésre](../billing/billing-upgrade-azure-subscription.md).
* Ha módosítani szeretné egy használatalapú fizetéses fiókra, lásd: [az Azure használatalapú fizetéses előfizetésre váltani egy másik ajánlatra](../billing/billing-how-to-switch-azure-offer.md).

Ha az előfizetést, nem konvertálhatóak [hozzon létre egy Azure-támogatáskérést](../azure-supportability/how-to-create-azure-support-request.md). Válassza ki **előfizetés-kezelési** issue type számára.

## <a name="service-limitations"></a>Szolgáltatási korlátozások

Egyes szolgáltatások az erőforrások áthelyezésekor további szempontokat igényelnek. Ha a következő szolgáltatásokat helyezi át, ellenőrizze az útmutatást és a korlátozásokat.

* [App Services](./move-limitations/app-service-move-limitations.md)
* [Azure DevOps-szolgáltatások](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
* [Klasszikus üzemi modell](./move-limitations/classic-model-move-limitations.md)
* [Hálózat](./move-limitations/networking-move-limitations.md)
* [Recovery Services](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
* [Virtuális gépek](./move-limitations/virtual-machines-move-limitations.md)

## <a name="large-requests"></a>Nagyméretű kérelmek

Ha lehetséges, nagy break helyezi át a külön áthelyezési műveleteket. A Resource Manager azonnal hibát jelez, ha több mint 800 erőforrás van egyetlen műveletben. Azonban legalább 800 erőforrások áthelyezése is meghiúsulhat időtúllépés által.

## <a name="resource-not-in-succeeded-state"></a>Az erőforrás nem sikeres állapotban van

Ha olyan hibaüzenetet kap, amely azt jelzi, hogy egy erőforrás nem helyezhető át, mert nem sikeres állapotban van, előfordulhat, hogy egy függő erőforrás, amely blokkolja az áthelyezést. Tekintse meg [a függő erőforrások állapotát](./move-limitations/networking-move-limitations.md#state-of-dependent-resources).

## <a name="next-steps"></a>További lépések

Az erőforrások áthelyezésére szolgáló parancsokért lásd: [erőforrások áthelyezése új erőforráscsoporthoz vagy](resource-group-move-resources.md)előfizetésbe.
