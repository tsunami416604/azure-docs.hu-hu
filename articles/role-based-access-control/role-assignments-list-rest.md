---
title: Szerepkör-hozzárendelések listázása az Azure RBAC és a REST API használatával
description: Megtudhatja, hogyan határozhatja meg, hogy a felhasználók, csoportok, egyszerű szolgáltatások és felügyelt identitások milyen erőforrásokhoz férnek hozzá az Azure szerepköralapú hozzáférés-vezérlés (RBAC) és a REST API használatához.
services: active-directory
documentationcenter: na
author: rolyon
manager: mtillman
editor: ''
ms.assetid: 1f90228a-7aac-4ea7-ad82-b57d222ab128
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: a494e7fd4c9fb79faa6a1d8cb2c3c871796ccdc5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80062156"
---
# <a name="list-role-assignments-using-azure-rbac-and-the-rest-api"></a>Szerepkör-hozzárendelések listázása az Azure RBAC és a REST API használatával

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)]Ez a cikk a szerepkör-hozzárendelések REST API használatával történő listázását ismerteti.

> [!NOTE]
> Ha a szervezete egy Azure-beli [delegált erőforrás-kezelést](../lighthouse/concepts/azure-delegated-resource-management.md)használó szolgáltatónál kiszervezett felügyeleti funkciókat használ, az adott szolgáltató által meghatalmazott szerepkör-hozzárendelések nem jelennek meg.

## <a name="list-role-assignments"></a>Szerepkör-hozzárendelések felsorolása

A RBAC-ben a hozzáférés listázásához listázhatja a szerepkör-hozzárendeléseket. A szerepkör-hozzárendelések listázásához használja a [szerepkör-hozzárendelések-lista](/rest/api/authorization/roleassignments/list) REST API-k egyikét. Az eredmények pontosításához meg kell adnia egy hatókört és egy opcionális szűrőt.

1. Kezdje a következő kéréssel:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter={filter}
    ```

1. Az URI-n belül cserélje le a *{scope}* elemet arra a hatókörre, amelyre a szerepkör-hozzárendeléseket szeretné listázni.

    > [!div class="mx-tableFixed"]
    > | Hatókör | Típus |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Felügyeleti csoport |
    > | `subscriptions/{subscriptionId1}` | Előfizetés |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Erőforráscsoport |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Erőforrás |

    Az előző példában a Microsoft. web egy olyan erőforrás-szolgáltató, amely egy App Service példányra hivatkozik. Hasonlóképpen használhatja bármely más erőforrás-szolgáltatót, és megadhatja a hatókört. További információ: [Azure erőforrás-szolgáltatók és típusok](../azure-resource-manager/management/resource-providers-and-types.md) és támogatott [Azure Resource Manager erőforrás-szolgáltatói műveletek](resource-provider-operations.md).  
     
1. Cserélje le a *{Filter}* helyére azt a feltételt, amelyet alkalmazni szeretne a szerepkör-hozzárendelési lista szűréséhez.

    > [!div class="mx-tableFixed"]
    > | Szűrés | Leírás |
    > | --- | --- |
    > | `$filter=atScope()` | Csak a megadott hatókörhöz tartozó szerepkör-hozzárendelések felsorolása, nem tartalmazza a szerepkör-hozzárendeléseket az alhatókörben. |
    > | `$filter=assignedTo('{objectId}')` | Egy adott felhasználó vagy szolgáltatásnév szerepkör-hozzárendeléseinek felsorolása.<br/>Ha a felhasználó egy szerepkör-hozzárendeléssel rendelkező csoport tagja, akkor a szerepkör-hozzárendelés is megjelenik. Ez a szűrő a csoportok esetében tranzitív, ami azt jelenti, hogy ha a felhasználó egy csoport tagja, és a csoport egy másik, szerepkör-hozzárendelést tartalmazó csoport tagja, akkor a szerepkör-hozzárendelés is megjelenik.<br/>Ez a szűrő csak egy felhasználóhoz vagy egy egyszerű szolgáltatáshoz tartozó objektumazonosítót fogad el. Nem lehet átadni egy objektum AZONOSÍTÓját egy csoport számára. |
    > | `$filter=atScope()+and+assignedTo('{objectId}')` | Felsorolja a megadott felhasználóhoz vagy szolgáltatáshoz tartozó szerepkör-hozzárendeléseket, valamint a megadott hatókört. |
    > | `$filter=principalId+eq+'{objectId}'` | Egy adott felhasználó, csoport vagy egyszerű szolgáltatásnév szerepkör-hozzárendeléseinek felsorolása. |

## <a name="next-steps"></a>További lépések

- [Szerepkör-hozzárendelések hozzáadása vagy eltávolítása az Azure RBAC és a REST API használatával](role-assignments-rest.md)
- [Azure REST API-referenciák](/rest/api/azure/)
