---
title: Rbac-RÓL és a REST API – Azure-hozzáférés kezelése |} A Microsoft Docs
description: Ismerje meg, hogyan kezelheti a felhasználók, csoportok és alkalmazások, a szerepköralapú hozzáférés-vezérlés (RBAC) és a REST API használatával hozzáférését. Ez magában foglalja a listázási hozzáférés, hozzáférést biztosít, és távolítsa el a hozzáférést.
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
ms.date: 06/20/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 859a410a4ff9204e8e52fbd2cc3b38823f4bb830
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37435218"
---
# <a name="manage-access-using-rbac-and-the-rest-api"></a>Rbac-RÓL és a REST API-hozzáférés kezelése

A [szerepköralapú hozzáférés-vezérlés (RBAC)](overview.md) az erőforrásokhoz való hozzáférés kezelésének a módja az Azure-ban. Ez a cikk bemutatja, hogyan kezelheti a felhasználók, csoportok és alkalmazások rbac-RÓL és a REST API használatával hozzáférését.

## <a name="list-access"></a>Hozzáférések felsorolása

Az RBAC lista hozzáférés listázása a szerepkör-hozzárendeléseket. Szerepkör-hozzárendelések felsorolásához használja következők egyikét a [szerepkör-hozzárendelések – lista](/rest/api/authorization/roleassignments/list) REST API-k. Az eredmények pontosításához használjon, megadhatja a hatókör és a egy nem kötelező szűrő. Az API meghívása, hozzáféréssel kell rendelkeznie a `Microsoft.Authorization/roleAssignments/read` művelet a megadott hatókörben. Több [beépített szerepkörök](built-in-roles.md) , amelyekhez hozzáférést ezt a műveletet.

1. Indítsa el a következő kérelmet:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter={filter}
    ```

1. Cserélje le az URI-belül *{hatókör}* a hatókörben, amelynek meg szeretné a szerepkör-hozzárendelések lista.

    | Hatókör | Típus |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Előfizetés |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Erőforráscsoport |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Erőforrás |

1. Cserélje le *{szűrő}* azzal a feltétellel, hogy a szerepkör-hozzárendelés listájának szűrése a alkalmazni szeretné.

    | Szűrés | Leírás |
    | --- | --- |
    | `$filter=atScope()` | A listában csak a megadott hatókörön, a nem többek között a szerepkör-hozzárendelést subscopes szerepkör-hozzárendeléseit. |
    | `$filter=principalId%20eq%20'{objectId}'` | Listázza egy adott felhasználó, csoport vagy egyszerű szolgáltatás szerepkör-hozzárendeléseit. |
    | `$filter=assignedTo('{objectId}')` | Listázza egy adott felhasználó, köztük azokról, örökölt szerepkör-hozzárendeléseit. |

## <a name="grant-access"></a>Hozzáférés biztosítása

Az RBAC-ben a hozzáférés biztosítása egy szerepkör-hozzárendelés létrehozásával történik. Szerepkör-hozzárendelés létrehozásához használja a [szerepkör-hozzárendelések – hozzon létre](/rest/api/authorization/roleassignments/create) – REST API-t, és adja meg a rendszerbiztonsági tag, a szerepkör-definíció és a hatókör. Az API meghívásához, hozzáféréssel kell rendelkeznie a `Microsoft.Authorization/roleAssignments/write` műveletet. A beépített szerepkörök, csak [tulajdonosa](built-in-roles.md#owner) és [felhasználói hozzáférés rendszergazdája](built-in-roles.md#user-access-administrator) , amelyekhez hozzáférést ezt a műveletet.

1. Használja a [szerepkör-definíciók - lista](/rest/api/authorization/roledefinitions/list) vagy a REST API-t [beépített szerepkörök](built-in-roles.md) beolvasni a hozzárendelni kívánt szerepkör-definíció azonosítóját.

1. Egy GUID eszköz segítségével hozza létre a szerepkör-hozzárendelési azonosító esetében használt egyedi azonosító. Az azonosító formátuma: `00000000-0000-0000-0000-000000000000`

1. Indítsa el a következő kérés és a szervezet:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentName}?api-version=2015-07-01
    ```

    ```json
    {
      "properties": {
        "roleDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}",
        "principalId": "{principalId}"
      }
    }
    ```
    
1. Cserélje le az URI-t, belül *{hatókör}* a hatókörben, a szerepkör-hozzárendelésre vonatkozó.

    | Hatókör | Típus |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Előfizetés |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Erőforráscsoport |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Erőforrás |

1. Cserélje le *{roleAssignmentName}* a szerepkör-hozzárendelés GUID azonosítóját.

1. Cserélje le a kérelem törzsében lévő *{subscriptionId}* az előfizetés-azonosítóval.

1. Cserélje le *{roleDefinitionId}* a szerepkör-definíció azonosítóval.

1. Cserélje le *{principalId}* az egyszerű szolgáltatás a szerepkörhöz rendelt, felhasználó vagy csoport objektumazonosítóját.

## <a name="remove-access"></a>Hozzáférés eltávolítása

Az RBAC-ben hozzáférés eltávolításához egy szerepkör-hozzárendelést kell eltávolítania. Szerepkör-hozzárendelés eltávolításához használja a [szerepkör-hozzárendelések – Törlés](/rest/api/authorization/roleassignments/delete) REST API-t. Az API meghívásához, hozzáféréssel kell rendelkeznie a `Microsoft.Authorization/roleAssignments/delete` műveletet. A beépített szerepkörök, csak [tulajdonosa](built-in-roles.md#owner) és [felhasználói hozzáférés rendszergazdája](built-in-roles.md#user-access-administrator) , amelyekhez hozzáférést ezt a műveletet.

1. A szerepkör-hozzárendelés azonosítója (GUID) beolvasása. Ez az azonosító adja vissza, amikor először hoz létre a szerepkör-hozzárendelést vagy a szerepkör-hozzárendeléseket listázásával beszerezheti azt.

1. Indítsa el a következő kérelmet:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentName}?api-version=2015-07-01
    ```

1. Belül az URI-t, cserélje le a *{hatókör}* a hatókörben, a szerepkör-hozzárendelés eltávolításához.

    | Hatókör | Típus |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Előfizetés |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Erőforráscsoport |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Erőforrás |

1. Cserélje le *{roleAssignmentName}* a szerepkör-hozzárendelés GUID azonosítóját.

## <a name="next-steps"></a>További lépések

- [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure Manager REST API-val](../azure-resource-manager/resource-group-template-deploy-rest.md)
- [Az Azure REST API-referencia](/rest/api/azure/)
- [A REST API-val egyéni szerepkörök létrehozása](custom-roles-rest.md)