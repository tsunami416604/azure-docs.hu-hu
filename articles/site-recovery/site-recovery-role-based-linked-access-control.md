---
title: Az Azure szerepköralapú hozzáférés-vezérlésének kezelése Azure Site Recovery
description: Ez a cikk azt ismerteti, hogyan alkalmazhat szerepköralapú hozzáférés-vezérlést (RBAC) Azure Site Recovery hozzáférésének kezeléséhez.
ms.service: site-recovery
ms.date: 04/08/2019
author: mayurigupta13
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: ce389f9281b02662f87353f00c9bca92cdf86937
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083761"
---
# <a name="manage-site-recovery-access-with-role-based-access-control-rbac"></a>Site Recovery hozzáférés kezelése szerepköralapú hozzáférés-vezérléssel (RBAC)

Az Azure szerepköralapú hozzáférés-vezérlés (RBAC) lehetővé teszi a részletes hozzáférés-kezelést az Azure-hoz. A RBAC segítségével elkülönítheti a munkatársain belüli feladatokat, és adott feladatok elvégzéséhez szükség esetén csak konkrét hozzáférési engedélyeket biztosíthat a felhasználóknak.

A Azure Site Recovery 3 beépített szerepkört biztosít Site Recovery felügyeleti műveletek vezérléséhez. További információ az [Azure beépített RBAC-szerepköreivel](../role-based-access-control/built-in-roles.md) kapcsolatban.

* [Site Recovery-közreműködő](../role-based-access-control/built-in-roles.md#site-recovery-contributor) – Ez a szerepkör minden olyan engedéllyel rendelkezik, amelyek az Azure Site Recovery-műveletek Recovery Services-tárolókban történő kezeléséhez szükségesek. Az ezzel a szerepkörrel rendelkező felhasználók nem hozhatnak létre, illetve nem törölhetnek Recovery Services-tárolókat, és nem adhatnak hozzáférési jogokat más felhasználóknak. Ez a szerepkör olyan vész-helyreállítási rendszergazdák számára ajánlott, akik az adott esetnek megfelelően engedélyezhetik és kezelhetik az alkalmazások vagy a teljes szervezetek vész-helyreállítási lehetőségeit.
* [Site Recovery-operátor](../role-based-access-control/built-in-roles.md#site-recovery-operator) – Ez a szerepkör a feladatátvétel és feladat-visszavétel végrehajtásához és felügyeletéhez szükséges engedélyekkel rendelkezik. Az ezzel a szerepkörrel rendelkező felhasználó nem tudja engedélyezni vagy letiltani a replikálást, a tárolók létrehozását és törlését, az új infrastruktúra regisztrálását vagy a hozzáférési jogosultságok más felhasználóknak való hozzárendelését Ez a szerepkör az olyan vész-helyreállítási operátorok számára legmegfelelőbb, akik feladatátvételt végezhetnek a virtuális gépeken és az alkalmazásokon, amikor az alkalmazások tulajdonosai és a rendszergazdák valós vagy szimulált vészhelyzetekben, például DR-részletezésben vannak megadva. A katasztrófa feloldása után a DR operátor újra védetté teheti és visszaállíthatja a virtuális gépeket.
* [Site Recovery-olvasó](../role-based-access-control/built-in-roles.md#site-recovery-reader) – Ez a szerepkör a Site Recovery összes felügyeleti műveletének a megtekintésére rendelkezik engedélyekkel. Ez a szerepkör az IT-figyelő számára legmegfelelőbb, aki nyomon követheti a védelem aktuális állapotát, és szükség esetén támogatási jegyeket is felvehet.

Ha a saját szerepköröket is meg szeretné határozni még több szabályozáshoz, olvassa el az [Egyéni szerepkörök létrehozása](../role-based-access-control/custom-roles.md) az Azure-ban című témakört.

## <a name="permissions-required-to-enable-replication-for-new-virtual-machines"></a>Az új virtuális gépek replikálásának engedélyezéséhez szükséges engedélyek
Amikor új virtuális gépet replikál az Azure-ba Azure Site Recovery használatával, a rendszer ellenőrzi, hogy a felhasználó rendelkezik-e a szükséges engedélyekkel a Site Recovery számára biztosított Azure-erőforrások használatához.

Egy új virtuális gép replikálásának engedélyezéséhez a felhasználónak a következőket kell tennie:
* Engedély virtuális gép létrehozásához a kiválasztott erőforráscsoporthoz
* Engedély virtuális gép létrehozásához a kiválasztott virtuális hálózaton
* Engedély a kiválasztott Storage-fiókba való íráshoz

Egy új virtuális gép replikálásának befejezéséhez a felhasználónak a következő engedélyekkel kell rendelkeznie.

> [!IMPORTANT]
>Győződjön meg arról, hogy a megfelelő engedélyek hozzá lettek adva az erőforrás-telepítéshez használt üzembe helyezési modellben (Resource Manager/klasszikus).

> [!NOTE]
> Ha engedélyezi a replikációt egy Azure-beli virtuális gép számára, és engedélyezni szeretné a Site Recovery számára a frissítések kezelését, akkor a replikáció engedélyezésekor érdemes lehet új Automation-fiókot is létrehoznia, ebben az esetben Önnek is rendelkeznie kell egy Automation-fiók létrehozásához előfizetést is.

| **Erőforrás típusa** | **Üzembe helyezési modell** | **Engedéllyel** |
| --- | --- | --- |
| Számítás | Resource Manager | Microsoft.Compute/availabilitySets/read |
|  |  | Microsoft.Compute/virtualMachines/read |
|  |  | Microsoft.Compute/virtualMachines/write |
|  |  | Microsoft.Compute/virtualMachines/delete |
|  | Klasszikus | Microsoft.ClassicCompute/domainNames/read |
|  |  | Microsoft.ClassicCompute/domainNames/write |
|  |  | Microsoft.ClassicCompute/domainNames/delete |
|  |  | Microsoft.ClassicCompute/virtualMachines/read |
|  |  | Microsoft.ClassicCompute/virtualMachines/write |
|  |  | Microsoft.ClassicCompute/virtualMachines/delete |
| Network (Hálózat) | Resource Manager | Microsoft.Network/networkInterfaces/read |
|  |  | Microsoft.Network/networkInterfaces/write |
|  |  | Microsoft.Network/networkInterfaces/delete |
|  |  | Microsoft.Network/networkInterfaces/join/action |
|  |  | Microsoft.Network/virtualNetworks/read |
|  |  | Microsoft.Network/virtualNetworks/subnets/read |
|  |  | Microsoft.Network/virtualNetworks/subnets/join/action |
|  | Klasszikus | Microsoft.ClassicNetwork/virtualNetworks/read |
|  |  | Microsoft.ClassicNetwork/virtualNetworks/join/action |
| Tárolás | Resource Manager | Microsoft.Storage/storageAccounts/read |
|  |  | Microsoft.Storage/storageAccounts/listkeys/action |
|  | Klasszikus | Microsoft.ClassicStorage/storageAccounts/read |
|  |  | Microsoft.ClassicStorage/storageAccounts/listKeys/action |
| Erőforráscsoport | Resource Manager | Microsoft.Resources/deployments/* |
|  |  | Microsoft.Resources/subscriptions/resourceGroups/read |

Érdemes lehet a "Virtual Machine közreműködő" és a "klasszikus virtuálisgép-közreműködő" [beépített szerepköröket](../role-based-access-control/built-in-roles.md) használni a Resource Manager és a klasszikus üzemi modellekhez.

## <a name="next-steps"></a>Következő lépések
* [Szerepköralapú Access Control](../role-based-access-control/role-assignments-portal.md): első lépések a RBAC a Azure Portal.
* Ismerje meg, hogyan kezelheti a hozzáférést a következővel:
  * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
  * [Azure CLI](../role-based-access-control/role-assignments-cli.md)
  * [REST API](../role-based-access-control/role-assignments-rest.md)
* [Szerepköralapú Access Control hibaelhárítás](../role-based-access-control/troubleshooting.md): javaslatok a gyakori problémák elhárítására.
