---
title: Azure-erőforrások áthelyezése új előfizetést vagy az erőforrás-csoport során előforduló hibák elhárítása
description: Azure Resource Manager segítségével az erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: tomfitz
ms.openlocfilehash: e23d7c571a010e5bfb42e5f15368e0194272ed53
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723466"
---
# <a name="troubleshoot-moving-azure-resources-to-new-resource-group-or-subscription"></a>Hibaelhárítás Azure-erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe

Ez a cikk az erőforrások áthelyezésekor problémák megoldásához javaslatokat nyújt.

## <a name="upgrade-a-subscription"></a>Egy előfizetés frissítése

Ha valójában szeretné frissíteni (például a használatalapú fizetéses előfizetésre vált az ingyenes) Azure-előfizetése, az előfizetés konvertálnia kell.

* Ingyenes próbaverzió frissítése, lásd: [az ingyenes próba- vagy a Microsoft Imagine Azure-előfizetés frissítése használatalapú fizetésre](../billing/billing-upgrade-azure-subscription.md).
* Ha módosítani szeretné egy használatalapú fizetéses fiókra, lásd: [az Azure használatalapú fizetéses előfizetésre váltani egy másik ajánlatra](../billing/billing-how-to-switch-azure-offer.md).

Ha az előfizetést, nem konvertálhatóak [hozzon létre egy Azure-támogatáskérést](../azure-supportability/how-to-create-azure-support-request.md). Válassza ki **előfizetés-kezelési** issue type számára.

## <a name="service-limitations"></a>Szolgáltatási korlátozások

Egyes szolgáltatások megkövetelik a további szempontok erőforrások áthelyezésekor. Ha a következő szolgáltatásokat telepít át, győződjön meg arról, irányelvek és korlátozásairól ellenőrizheti.

* [App Services](./move-limitations/app-service-move-limitations.md)
* [Azure DevOps Services](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
* [Klasszikus üzemi modell](./move-limitations/classic-model-move-limitations.md)
* [Recovery Services](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
* [Virtuális gépek](./move-limitations/virtual-machines-move-limitations.md)
* [Virtuális hálózatok](./move-limitations/virtual-network-move-limitations.md)

## <a name="large-requests"></a>Nagyméretű kérelmek

Ha lehetséges, nagy break helyezi át a külön áthelyezési műveleteket. Erőforrás-kezelő azonnal hibát ad vissza, ha rendelkezésre áll a több mint 800 erőforrás egyetlen művelettel. Azonban legalább 800 erőforrások áthelyezése is meghiúsulhat időtúllépés által.

## <a name="next-steps"></a>További lépések

Erőforrások áthelyezése parancsokért lásd: [erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](resource-group-move-resources.md).
