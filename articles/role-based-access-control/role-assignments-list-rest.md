---
title: Szerepkör-hozzárendelések listázása az Azure RBAC és a REST API használatával
description: Megtudhatja, hogyan állapíthatja meg, hogy a felhasználók, csoportok, egyszerű szolgáltatás- és felügyelt identitások milyen erőforrásokhoz férhetnek hozzá az Azure szerepköralapú hozzáférés-vezérlés (RBAC) és a REST API használatával.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062156"
---
# <a name="list-role-assignments-using-azure-rbac-and-the-rest-api"></a>Szerepkör-hozzárendelések listázása az Azure RBAC és a REST API használatával

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)]Ez a cikk ismerteti, hogyan listázhatoa a szerepkör-hozzárendeléseket a REST API használatával.

> [!NOTE]
> Ha a szervezet kiszervezte a felügyeleti funkciókat egy olyan szolgáltatóhoz, amely [az Azure delegált erőforrás-kezelését](../lighthouse/concepts/azure-delegated-resource-management.md)használja, az adott szolgáltató által engedélyezett szerepkör-hozzárendelések itt nem jelennek meg.

## <a name="list-role-assignments"></a>Szerepkör-hozzárendelések felsorolása

Az RBAC-ban a hozzáférés listázásához sorolja fel a szerepkör-hozzárendeléseket. A szerepkör-hozzárendelések listázásához használja a [Szerepkör-hozzárendelések - REST-API-k listázása.](/rest/api/authorization/roleassignments/list) Az eredmények finomításához adjon meg egy hatókört és egy választható szűrőt.

1. Kezdje a következő kéréssel:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter={filter}
    ```

1. Az URI-n belül cserélje le *a(z) {scope}* értéket arra a hatókörre, amelynek szerepkör-hozzárendeléseit fel szeretné sorolni.

    > [!div class="mx-tableFixed"]
    > | Hatókör | Típus |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Felügyeleti csoport |
    > | `subscriptions/{subscriptionId1}` | Előfizetés |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Erőforráscsoport |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Erőforrás |

    Az előző példában a microsoft.web egy olyan erőforrás-szolgáltató, amely egy App Service-példányra hivatkozik. Hasonlóképpen bármely más erőforrás-szolgáltatót is használhat, és megadhatja a hatókört. További információ: [Azure Resource providers and types](../azure-resource-manager/management/resource-providers-and-types.md) and supported Azure Resource Manager resource provider [operations.](resource-provider-operations.md)  
     
1. Cserélje le *a(z) {filter}* elemet arra a feltételre, amelyet a szerepkör-hozzárendelési lista szűrésére alkalmazni kíván.

    > [!div class="mx-tableFixed"]
    > | Szűrés | Leírás |
    > | --- | --- |
    > | `$filter=atScope()` | Csak a megadott hatókör szerepkör-hozzárendeléseit sorolja fel, az alhatókörökben lévő szerepkör-hozzárendeléseket nem beleértve. |
    > | `$filter=assignedTo('{objectId}')` | Egy adott felhasználó vagy egyszerű szolgáltatás szerepkör-hozzárendelései.<br/>Ha a felhasználó egy szerepkör-hozzárendeléssel rendelkező csoport tagja, akkor a szerepkör-hozzárendelés is megjelenik. Ez a szűrő tranzitív a csoportok számára, ami azt jelenti, hogy ha a felhasználó egy csoport tagja, és ez a csoport egy másik, szerepkör-hozzárendeléssel rendelkező csoport tagja, akkor a szerepkör-hozzárendelés is megjelenik.<br/>Ez a szűrő csak egy felhasználó vagy egy egyszerű szolgáltatás objektumazonosítóját fogadja el. Csoportazonosítója nem adható át. |
    > | `$filter=atScope()+and+assignedTo('{objectId}')` | A megadott felhasználó vagy egyszerű szolgáltatás szerepkör-hozzárendeléseit sorolja fel a megadott hatókörben. |
    > | `$filter=principalId+eq+'{objectId}'` | Egy adott felhasználó, csoport vagy egyszerű szolgáltatás szerepkör-hozzárendeléseit sorolja fel. |

## <a name="next-steps"></a>További lépések

- [Szerepkör-hozzárendelések hozzáadása vagy eltávolítása az Azure RBAC és a REST API használatával](role-assignments-rest.md)
- [Azure REST API-útmutató](/rest/api/azure/)
