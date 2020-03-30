---
title: Azure szerepköralapú hozzáférés-vezérlés kezelése az Azure Site Recovery szolgáltatásban
description: Ez a cikk bemutatja, hogyan alkalmazhat szerepköralapú hozzáférés-vezérlés (RBAC) az Azure Site Recovery-hozzáférés kezeléséhez.
ms.service: site-recovery
ms.date: 04/08/2019
author: mayurigupta13
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: ce389f9281b02662f87353f00c9bca92cdf86937
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257575"
---
# <a name="manage-site-recovery-access-with-role-based-access-control-rbac"></a>Site Recovery-hozzáférés kezelése szerepköralapú hozzáférés-vezérléssel (RBAC)

Az Azure szerepköralapú hozzáférés-vezérlés (RBAC) lehetővé teszi a részletes hozzáférés-kezelés az Azure-ban. Az RBAC használatával elkülönítheti a felelősségek a csapaton belül, és csak adott hozzáférési engedélyeket adhat a felhasználóknak, ha szükséges az adott feladatok végrehajtásához.

Az Azure Site Recovery 3 beépített szerepkört biztosít a Site Recovery felügyeleti műveletek vezérléséhez. További információ az [Azure beépített RBAC-szerepköreivel](../role-based-access-control/built-in-roles.md) kapcsolatban.

* [Site Recovery-közreműködő](../role-based-access-control/built-in-roles.md#site-recovery-contributor) – Ez a szerepkör minden olyan engedéllyel rendelkezik, amelyek az Azure Site Recovery-műveletek Recovery Services-tárolókban történő kezeléséhez szükségesek. Az ezzel a szerepkörrel rendelkező felhasználók nem hozhatnak létre, illetve nem törölhetnek Recovery Services-tárolókat, és nem adhatnak hozzáférési jogokat más felhasználóknak. Ez a szerepkör a legalkalmasabb a vész-helyreállítási rendszergazdák számára, akik engedélyezhetik és kezelhetik a vészhelyreállítást az alkalmazások vagy a teljes szervezetek számára, az esettől függetlenül.
* [Site Recovery-operátor](../role-based-access-control/built-in-roles.md#site-recovery-operator) – Ez a szerepkör a feladatátvétel és feladat-visszavétel végrehajtásához és felügyeletéhez szükséges engedélyekkel rendelkezik. Az ilyen szerepkörrel rendelkező felhasználók nem engedélyezhetik vagy tilthatják le a replikációt, nem hozhatnak létre vagy törölhetnek tárolókat, nem regisztrálhatnak új infrastruktúrát, és nem rendelhetnek hozzáférési jogokat más felhasználókhoz. Ez a szerepkör a legalkalmasabb egy vész-helyreállítási operátor, aki feladatátvételi virtuális gépek vagy alkalmazások, ha az alkalmazás tulajdonosok és a rendszergazdák utasítására egy tényleges vagy szimulált katasztrófa helyzetben, például egy VÉSZ-fúró. A katasztrófa utáni, a VÉSZ-üzemeltető újra védheti és feladat-visszavétela a virtuális gépek.
* [Site Recovery-olvasó](../role-based-access-control/built-in-roles.md#site-recovery-reader) – Ez a szerepkör a Site Recovery összes felügyeleti műveletének a megtekintésére rendelkezik engedélyekkel. Ez a szerepkör a legalkalmasabb egy informatikai figyelési végrehajtó, aki figyelheti a jelenlegi védelmi állapot, és szükség esetén támogatási jegyek emelése.

Ha saját szerepköröket szeretne definiálni még több vezérléshez, olvassa el, hogyan [hozhat létre egyéni szerepköröket](../role-based-access-control/custom-roles.md) az Azure-ban.

## <a name="permissions-required-to-enable-replication-for-new-virtual-machines"></a>Az új virtuális gépek replikációjának engedélyezéséhez szükséges engedélyek
Amikor egy új virtuális gép replikálódik az Azure-ba az Azure Site Recovery használatával, a társított felhasználó hozzáférési szintjei érvényesítve győződjön meg arról, hogy a felhasználó rendelkezik a szükséges engedélyekkel a Site Recovery számára biztosított Azure-erőforrások használatához.

Új virtuális gép replikációjának engedélyezéséhez a felhasználónak rendelkeznie kell a következőkkel:
* A kijelölt erőforráscsoportban lévő virtuális gép létrehozásának engedélyezése
* A kijelölt virtuális hálózatban lévő virtuális gép létrehozására való engedély
* Írási engedély a kijelölt Tárfiókba

A felhasználónak a következő engedélyekre van szüksége az új virtuális gép replikációjának befejezéséhez.

> [!IMPORTANT]
>Győződjön meg arról, hogy a megfelelő engedélyek et az erőforrás-telepítéshez használt központi telepítési modell (Resource Manager/ Classic) adja hozzá.

> [!NOTE]
> Ha engedélyezi az Azure virtuális gép replikációját, és engedélyezni szeretné a Site Recovery számára a frissítések kezelését, akkor a replikáció engedélyezése mellett új Automation-fiókot is létrehozhat, amely esetben engedélyt kell kérnie egy automatizálási fiók létrehozásához ugyanabban a előfizetést, mint a boltozatot is.

| **Erőforrás típusa** | **Telepítési modell** | **Engedély** |
| --- | --- | --- |
| Compute | Resource Manager | Microsoft.Compute/availabilitySets/read |
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
|  |  | Microsoft.Network/virtualNetworks/alhálózatok/olvasás |
|  |  | Microsoft.Network/virtualNetworks/alhálózatok/join/action |
|  | Klasszikus | Microsoft.ClassicNetwork/virtualNetworks/read |
|  |  | Microsoft.ClassicNetwork/virtualNetworks/join/action |
| Storage | Resource Manager | Microsoft.Storage/storageAccounts/read |
|  |  | Microsoft.Storage/storageAccounts/listkeys/action |
|  | Klasszikus | Microsoft.ClassicStorage/storageAccounts/read |
|  |  | Microsoft.ClassicStorage/storageAccounts/listKeys/action |
| Erőforráscsoport | Resource Manager | Microsoft.Resources/deployments/* |
|  |  | Microsoft.Resources/subscriptions/resourceGroups/read |

Fontolja meg a "Virtual Machine Contributor" és a "Classic Virtual Machine Contributor" beépített szerepkörök használata az [Erőforrás-kezelő](../role-based-access-control/built-in-roles.md) és a Klasszikus üzembe helyezési modellek, illetve.

## <a name="next-steps"></a>További lépések
* [Szerepköralapú hozzáférés-vezérlés:](../role-based-access-control/role-assignments-portal.md)Az RBAC első lépései az Azure Portalon.
* További információ a hozzáférés kezeléséhez:
  * [Powershell](../role-based-access-control/role-assignments-powershell.md)
  * [Azure CLI](../role-based-access-control/role-assignments-cli.md)
  * [REST API](../role-based-access-control/role-assignments-rest.md)
* [Szerepköralapú hozzáférés-vezérlés hibaelhárítása](../role-based-access-control/troubleshooting.md): Javaslatok a gyakori problémák elhárítására.
