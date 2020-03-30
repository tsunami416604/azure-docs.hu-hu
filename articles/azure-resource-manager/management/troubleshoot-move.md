---
title: Áthelyezési hibák elhárítása
description: Az Azure Resource Manager használatával erőforrásokat helyezhet át egy új erőforráscsoportba vagy előfizetésbe.
ms.topic: conceptual
ms.date: 08/27/2019
ms.openlocfilehash: 5a65f7daa0f5e3b1c8c6ddfdbecc0ff7d53e5afd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75891272"
---
# <a name="troubleshoot-moving-azure-resources-to-new-resource-group-or-subscription"></a>Azure-erőforrások új erőforráscsoportba vagy előfizetésbe való áthelyezésének hibakeresése

Ez a cikk javaslatokat tartalmaz az erőforrások áthelyezésekének során felmerülő problémák megoldásához.

## <a name="upgrade-a-subscription"></a>Előfizetés frissítése

Ha valóban szeretné frissíteni az Azure-előfizetést (például az ingyenesről a felosztó-kiosztóra való áttérésre), konvertálnia kell az előfizetését.

* Az ingyenes próbaverzió frissítéséhez olvassa el [Az ingyenes próbaverzió vagy a Microsoft Imagine Azure-előfizetés frissítése szolgáltatásra szolgáltatásra.](../../billing/billing-upgrade-azure-subscription.md)
* A felosztó-kiosztó fiók módosításáról az Azure Azure-as-you-go előfizetés módosítása másik ajánlatra.change a pay-as-go account, to [Change your-go-as account, to Change your-go-as account, see Change your Azure Pay-As-Go subscription to a different offer](../../billing/billing-how-to-switch-azure-offer.md).

Ha nem tudja konvertálni az előfizetést, [hozzon létre egy Azure-támogatási kérelmet.](../../azure-portal/supportability/how-to-create-azure-support-request.md) Válassza **az Előfizetéskezelés lehetőséget** a probléma típusához.

## <a name="service-limitations"></a>A szolgáltatás korlátai

Egyes szolgáltatások további szempontokat igényelnek az erőforrások áthelyezésekekekekekén. Ha a következő szolgáltatásokat helyezi át, ellenőrizze az útmutatást és a korlátozásokat.

* [App Services](./move-limitations/app-service-move-limitations.md)
* [Azure DevOps Services](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
* [Klasszikus üzembe helyezési modell](./move-limitations/classic-model-move-limitations.md)
* [Hálózat](./move-limitations/networking-move-limitations.md)
* [Recovery Services](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
* [Virtuális gépek](./move-limitations/virtual-machines-move-limitations.md)

## <a name="large-requests"></a>Nagy kérések

Ha lehetséges, bontsa nagy mozog külön mozog műveleteket. Az Erőforrás-kezelő azonnal hibát ad vissza, ha egy műveletben több mint 800 erőforrás található. Azonban a mozgó kevesebb, mint 800 források is sikertelen időtúllépés.

## <a name="resource-not-in-succeeded-state"></a>Az erőforrás nem sikerült állapotban van

Ha olyan hibaüzenetet kap, amely azt jelzi, hogy egy erőforrás nem helyezhető át, mert nem sikerült állapotban van, akkor valójában egy függő erőforrás lehet, amely blokkolja az áthelyezést. A hibakód általában **MoveCannotProceedWithResourcesNotInSucceededState**.

Ha a forrás vagy a célerőforrás-csoport virtuális hálózatot tartalmaz, a virtuális hálózat összes függő erőforrásának állapota az áthelyezés során ellenőrzésre kerül. Az ellenőrzés a virtuális hálózattól közvetlenül és közvetve is tartalmazza ezeket az erőforrásokat. Ha ezen erőforrások bármelyike hibás állapotban van, az áthelyezés le van tiltva. Ha például egy virtuális hálózatot használó virtuális gép meghibásodott, az áthelyezés le van tiltva. Az áthelyezés akkor is le van tiltva, ha a virtuális gép nem az áthelyezett erőforrások egyike, és nem tartozik az áthelyezéshez szükséges erőforráscsoportok egyikébe.

Amikor ezt a hibaüzenetet kapja, két lehetősége van. Helyezze át az erőforrásokat egy olyan erőforráscsoportba, amely nem rendelkezik virtuális hálózattal, vagy [lépjen kapcsolatba az ügyfélszolgálattal.](../../azure-portal/supportability/how-to-create-azure-support-request.md)

## <a name="next-steps"></a>További lépések

Az erőforrások áthelyezésére vonatkozó parancsokról az [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe (Erőforrások áthelyezése)](move-resource-group-and-subscription.md)témakörben található.
