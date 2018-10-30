---
title: Szerepköralapú hozzáférés-vezérlés (RBAC) az Azure Site Recovery-hozzáférés kezelése |} A Microsoft Docs
description: Ez a cikk ismerteti a szerepköralapú hozzáférés-bontrol (RBAC) az Azure Site Recovery-hozzáférés kezelése a alkalmazni.
ms.service: site-recovery
ms.date: 10/16/2018
author: mayurigupta13
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: 19d41d8c9b1a54cefc09190531b064d640048385
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2018
ms.locfileid: "50212046"
---
# <a name="manage-site-recovery-access-with-role-based-access-control-rbac"></a>A szerepköralapú hozzáférés-vezérléssel (RBAC) a Site Recovery-hozzáférés kezelése

Az Azure Szerepköralapú hozzáférés-vezérlés (RBAC) részletes hozzáférés-vezérlést biztosít az Azure-hoz. Az RBAC használatával, feladatkörök elkülönítse a csapaton belül, és csak különleges hozzáférési engedélyeket a felhasználóknak adott feladatok elvégzéséhez szükség szerint.

Az Azure Site Recovery a Site Recovery felügyeleti műveleteinek szabályozásához 3 beépített szerepkört biztosít. További információ az [Azure beépített RBAC-szerepköreivel](../role-based-access-control/built-in-roles.md) kapcsolatban.

* [Site Recovery-közreműködő](../role-based-access-control/built-in-roles.md#site-recovery-contributor) – Ez a szerepkör minden olyan engedéllyel rendelkezik, amelyek az Azure Site Recovery-műveletek Recovery Services-tárolókban történő kezeléséhez szükségesek. Az ezzel a szerepkörrel rendelkező felhasználók nem hozhatnak létre, illetve nem törölhetnek Recovery Services-tárolókat, és nem adhatnak hozzáférési jogokat más felhasználóknak. Ez a szerepkör az vész-helyreállítási rendszergazdák engedélyezhetik és felügyelhetik a esettől a vész-helyreállítási alkalmazások vagy az egész vállalatra, akik számára a leginkább megfelelő.
* [Site Recovery-operátor](../role-based-access-control/built-in-roles.md#site-recovery-operator) – Ez a szerepkör a feladatátvétel és feladat-visszavétel végrehajtásához és felügyeletéhez szükséges engedélyekkel rendelkezik. Ezzel a szerepkörrel rendelkező felhasználók nem engedélyezze vagy tiltsa le a replikációt, hozzon létre vagy törölhetnek tárolókat, regisztrálhatnak új infrastruktúrát vagy adhatnak hozzáférési jogokat más felhasználóknak. Ez a szerepkör egy vész-helyreállítási operátorok számára is a feladatátvételi virtuális gépek számára a leginkább megfelelő, vagy alkalmazások tulajdonosaitól alkalmazástulajdonosok és a egy szimulált vagy tényleges katasztrófa utáni helyzetben, például a Vészhelyreállítás a rendszergazdák részletes. POST feloldás a vész Helyreállítási operátor újra védetté és feladat-visszavételt a virtuális gépeket.
* [Site Recovery-olvasó](../role-based-access-control/built-in-roles.md#site-recovery-reader) – Ez a szerepkör a Site Recovery összes felügyeleti műveletének a megtekintésére rendelkezik engedélyekkel. Ez a szerepkör az olyan informatikai felügyeleti vezető, akik protection aktuális állapotának figyelése és emelje a támogatási jegyeket, szükség esetén – egygépes.

Ha még több vezérlő saját szerepköröket definiál keres, tekintse meg, hogyan [hozhat létre egyéni szerepkörök](../role-based-access-control/custom-roles.md) az Azure-ban.

## <a name="permissions-required-to-enable-replication-for-new-virtual-machines"></a>Új virtuális gépek replikációjának engedélyezéséhez szükséges engedélyek
Az Azure-bA az Azure Site Recovery segítségével új virtuális gép van replikálva, amikor a társított felhasználói hozzáférési szintek érvényesíti annak érdekében, hogy a felhasználó rendelkezik-e a szükséges engedélyekkel a Site Recovery a megadott Azure-erőforrások használatára.

Egy új virtuális gép replikálásának engedélyezéséhez a felhasználónak kell rendelkeznie:
* A kiválasztott erőforráscsoportban egy virtuális gép létrehozásához szükséges engedéllyel
* A kiválasztott virtuális hálózat egy virtuális gép létrehozásához szükséges engedéllyel
* A kiválasztott tárfiók írási engedéllyel

A felhasználó a következő engedélyeket kell egy új virtuális gép replikálása befejeződik.

> [!IMPORTANT]
>Győződjön meg arról, hogy megfelelő engedélyekkel az üzemi modell / adják (Resource Manager / Classic) erőforrás üzembe helyezéshez használt.

| **Erőforrás típusa** | **Üzemi modell** | **Engedély** |
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
|  |  | Microsoft.Network/virtualNetworks/subnets/read |
|  |  | Microsoft.Network/virtualNetworks/subnets/join/action |
|  | Klasszikus | Microsoft.ClassicNetwork/virtualNetworks/read |
|  |  | Microsoft.ClassicNetwork/virtualNetworks/join/action |
| Storage | Resource Manager | Microsoft.Storage/storageAccounts/read |
|  |  | Microsoft.Storage/storageAccounts/listkeys/action |
|  | Klasszikus | Microsoft.ClassicStorage/storageAccounts/read |
|  |  | Microsoft.ClassicStorage/storageAccounts/listKeys/action |
| Erőforráscsoport | Resource Manager | Microsoft.Resources/deployments/* |
|  |  | Microsoft.Resources/subscriptions/resourceGroups/read |

Fontolja meg a "Virtuális gépek Közreműködője" és "Klasszikus virtuális gépek Közreműködője" [beépített szerepkörök](../role-based-access-control/built-in-roles.md) a Resource Manager és klasszikus üzembe helyezési modellek jelölik.

## <a name="next-steps"></a>További lépések
* [Szerepköralapú hozzáférés-vezérlés](../role-based-access-control/role-assignments-portal.md): Ismerkedés az RBAC az Azure Portalon.
* Ismerje meg, hogyan való hozzáférés kezelése:
  * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
  * [Azure CLI](../role-based-access-control/role-assignments-cli.md)
  * [REST API](../role-based-access-control/role-assignments-rest.md)
* [Szerepköralapú hozzáférés-vezérlés hibáinak elhárítása](../role-based-access-control/troubleshooting.md): gyakori hibák rögzítésére vonatkozó javaslatokat kérhet.
