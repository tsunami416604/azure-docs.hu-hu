---
title: Az Azure-erőforrások - erőforrás naplózási privileged Identity Management |} Microsoft Docs
description: Ismerteti, hogyan láthatja az összes szerepkör tevékenység az egy adott erőforráshoz.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: b8fa7d5600c0de8a3319ea4de785281372959937
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-roles---audit"></a>Privileged Identity Management - szerepkör erőforrás - naplózás

Erőforrás naplózási lehetővé teszi az összes szerepkör tevékenység az erőforrás nézetét. Az előre definiált dátum vagy egyéni tartomány információkat szűrheti is.
![](media/azure-pim-resource-rbac/rbac-resource-audit.png)

Erőforrás naplózási is megtekintheti a felhasználói tevékenység részletei gyors hozzáférést biztosít. A "Ellenőrzési típusú" területen válassza a "Aktiválás". Kattintson a "(tevékenységek)" Azure-erőforrások, a felhasználó műveletek megjelenítéséhez.
![](media/azure-pim-resource-rbac/rbac-audit-activity.png)

![](media/azure-pim-resource-rbac/rbac-audit-activity-details.png)

# <a name="my-audit"></a>Saját naplózás

A naplózási lehetővé teszi a felhasználó személyes szerepkör tevékenységek nézetét. Az előre definiált dátum vagy egyéni tartomány információkat szűrheti is.
![](media/azure-pim-resource-rbac/my-audit-time.png)

## <a name="view-activation-and-azure-resource-activity"></a>Aktiválás és az Azure erőforrás-tevékenység megtekintése

Abban az esetben, ha egy adott felhasználó elvégez a különböző erőforrások mely műveletek van szüksége, áttekintheti a megadott aktiválási időszak (a jogosult felhasználók) társított Azure-erőforrás tevékenység. Indítsa el a tagok megtekintése vagy egy adott szerepkör tagjainak listája a felhasználó megadásával. Az eredmény a dátum a felhasználó Azure-erőforrások műveleteket, és a legutóbbi szerepkör aktiválások, hogy ugyanazon időszakra grafikus nézetet jelenít meg.

![](media/azure-pim-resource-rbac/rbac-user-details.png)

Egy adott szerepkör aktiválása kiválasztásával jelennek meg a szerepkör aktiválása részleteit és a megfelelő Azure-erőforrás tevékenység történt, hogy a felhasználó active.

![](media/azure-pim-resource-rbac/rbac-user-resource-activity.png)

