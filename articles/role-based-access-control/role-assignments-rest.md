---
title: Az RBAC és a REST API - Azure-hozzáférés kezelése |} Microsoft Docs
description: Megtudhatja, hogyan kezelheti a hozzáférést a felhasználók, csoportok és alkalmazások, szerepkörön alapuló hozzáférés-vezérlést (RBAC) és a REST API használatával. Ez magában foglalja a listázási hozzáférés, engedélyezheti a hozzáférést, és megszünteti a hozzáférést.
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
ms.topic: article
ms.date: 06/20/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: cfcb87fdff8105b25d4f7e63b775aaf9243d2a90
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/22/2018
ms.locfileid: "36317007"
---
# <a name="manage-access-using-rbac-and-the-rest-api"></a>Az RBAC és a REST API-hozzáférés kezelése

[Szerepköralapú hozzáférés-vezérlést (RBAC)](overview.md) kezelése az Azure-ban az erőforrásokhoz való hozzáférés módja. Ez a cikk ismerteti, hogyan kezelheti a hozzáférést a felhasználók, csoportok és alkalmazások RBAC és a REST API használatával.

## <a name="list-access"></a>A hozzáférési lista

Az RBAC lista eléréséhez felsorolja a szerepkör-hozzárendeléseket. A szerepkör-hozzárendelések listában egyikét használja a [szerepkör-hozzárendelések - lista](/rest/api/authorization/roleassignments/list) REST API-k. Az eredmények szűkítéséhez, meg kell adnia a hatókör és választható szűrőt. Az API-t hívja, hozzáféréssel kell rendelkeznie a `Microsoft.Authorization/roleAssignments/read` művelet a megadott hatókörben. Több [beépített szerepkörök](built-in-roles.md) férhetnek hozzá ezt a műveletet.

1. Indítsa el a következő kért:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter={filter}
    ```

1. Az URI belül cserélje le a *{hatókör}* legyen a szerepkör-hozzárendelések elemet a hatókörben.

    | Hatókör | Típus |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Előfizetés |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Erőforráscsoport |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Erőforrás |

1. Cserélje le *{szűrő}* azzal a feltétellel, amely a szerepkör-hozzárendelés csoportlista szűréséhez alkalmazni kívánja.

    | Szűrés | Leírás |
    | --- | --- |
    | `$filter=atScope()` | Szerepkör-hozzárendelések csak a megadott hatókör, a nem az a szerepkör-hozzárendelések subscopes, beleértve a listában. |
    | `$filter=principalId%20eq%20'{objectId}'` | Szerepkör-hozzárendeléseit egy adott felhasználó, csoport vagy szolgáltatás egyszerű listában. |
    | `$filter=assignedTo('{objectId}')` | Szerepkör-hozzárendelések listáját egy adott felhasználó, például a csoportok öröklődik. |

## <a name="grant-access"></a>Hozzáférés biztosítása

Az RBAC hozzáférést, létrehoz egy szerepkör-hozzárendelés. Szerepkör-hozzárendelés létrehozásához használja a [- szerepkör-hozzárendelések létrehozása](/rest/api/authorization/roleassignments/create) REST-API, és adja meg a rendszerbiztonsági tag, a szerepkör-definíció és a hatókör. Ez az API hívása, hozzáféréssel kell rendelkeznie a `Microsoft.Authorization/roleAssignments/write` műveletet. A beépített szerepkörök, csak [tulajdonos](built-in-roles.md#owner) és [felhasználói hozzáférés adminisztrátora](built-in-roles.md#user-access-administrator) férhetnek hozzá ezt a műveletet.

1. Használja a [szerepkör-definíciók - lista](/rest/api/authorization/roledefinitions/list) REST API-t, vagy tekintse át [beépített szerepkörök](built-in-roles.md) azonosító lekérése a hozzárendelni kívánt szerepkör-definíció.

1. A GUID eszköz használatával fog történni a szerepkör-hozzárendelés azonosító egyedi azonosító létrehozása. Az azonosító formátuma alább látható: `00000000-0000-0000-0000-000000000000`

1. Indítsa el a következő kérés és fő:

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
    
1. Az URI belül cserélje le a *{hatókör}* a szerepkör-hozzárendelés hatókörét.

    | Hatókör | Típus |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Előfizetés |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Erőforráscsoport |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Erőforrás |

1. Cserélje le *{roleAssignmentName}* azon szerepkör-hozzárendelés GUID azonosítóval.

1. A kérelem törzsében belül cserélje le a *{subscriptionId}* az előfizetés-azonosítóval.

1. Cserélje le *{roledefinitionid-értékkel}* a szerepkör-definíció azonosítójú.

1. Cserélje le *{principalId}* a felhasználó, csoport vagy egyszerű szolgáltatásnév a szerepkörhöz rendelt objektum azonosítójával.

## <a name="remove-access"></a>Hozzáférés eltávolítása

Szerepalapú elérését, le kell tiltani a szerepkör-hozzárendelés. Szerepkör-hozzárendelés eltávolításához használja a [- szerepkör-hozzárendelések törlése](/rest/api/authorization/roleassignments/delete) REST API-t. Ez az API hívása, hozzáféréssel kell rendelkeznie a `Microsoft.Authorization/roleAssignments/delete` műveletet. A beépített szerepkörök, csak [tulajdonos](built-in-roles.md#owner) és [felhasználói hozzáférés adminisztrátora](built-in-roles.md#user-access-administrator) férhetnek hozzá ezt a műveletet.

1. A szerepkör-hozzárendelés azonosítója (GUID) beolvasása. Ez az azonosító először létre kell hoznia a szerepkör-hozzárendelés vagy beszerezheti a szerepkör-hozzárendelések listáját adja vissza.

1. Indítsa el a következő kért:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentName}?api-version=2015-07-01
    ```

1. Az URI belül cserélje le a *{hatókör}* a szerepkör-hozzárendelés eltávolításához a hatókörben.

    | Hatókör | Típus |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Előfizetés |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Erőforráscsoport |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Erőforrás |

1. Cserélje le *{roleAssignmentName}* azon szerepkör-hozzárendelés GUID azonosítóval.

## <a name="next-steps"></a>További lépések

- [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure Manager REST API-val](../azure-resource-manager/resource-group-template-deploy-rest.md)
- [Az Azure REST API-referencia](/rest/api/azure/)
- [A REST API használatával egyéni szerepkörök létrehozása](custom-roles-rest.md)