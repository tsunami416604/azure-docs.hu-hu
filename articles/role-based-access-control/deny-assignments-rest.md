---
title: Listában elutasítása hozzárendelések Azure-erőforrások használatával a REST API – Azure |} A Microsoft Docs
description: Megtudhatja, hogyan listában elutasítása a felhasználók, csoportok és alkalmazások, a szerepköralapú hozzáférés-vezérlés (RBAC) használatával az Azure-erőforrások és a REST API-hozzárendeléseit.
services: active-directory
documentationcenter: na
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 29b8e0953109238b724cc8df9f456706f71a041e
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2019
ms.locfileid: "56341623"
---
# <a name="list-deny-assignments-for-azure-resources-using-the-rest-api"></a>Listában elutasítása a REST API használatával az Azure erőforrás-hozzárendelések

Jelenleg a megtagadás-hozzárendelések **csak olvashatók**, és csak az Azure állíthatja be azokat. Nem hozhat létre saját megtagadási hozzárendeléseket, azonban listázhatja a megtagadás-hozzárendeléseket, mert ezek hatással lehetnek a hatályos engedélyekre. Ez a cikk bemutatja, hogyan, hogy a listában elutasítása hozzárendelések rbac-RÓL és a REST API használatával.

## <a name="list-a-single-deny-assignment"></a>Egy listában elutasítása hozzárendelés

1. Indítsa el a következő kérelmet:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments/{deny-assignment-id}?api-version=2018-07-01-preview
    ```

1. Cserélje le az URI-belül *{hatókör}* a hatókörben, amelynek meg szeretné a Megtagadás hozzárendelések listázása.

    | Hatókör | Typo |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Előfizetés |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Erőforráscsoport |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Erőforrás |

1. Cserélje le *{Megtagadás-hozzárendelés-azonosító}* szeretné beolvasni, a Megtagadás hozzárendelés azonosítóval.

## <a name="list-multiple-deny-assignments"></a>Több listában elutasítása hozzárendelések

1. A kezdéshez válasszon egyet a következő kérelmet:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview
    ```

    A választható paraméterek:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter={filter}
    ```

1. Cserélje le az URI-belül *{hatókör}* a hatókörben, amelynek meg szeretné a Megtagadás hozzárendelések listázása.

    | Hatókör | Typo |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Előfizetés |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Erőforráscsoport |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Erőforrás |

1. Cserélje le *{szűrő}* azzal a feltétellel, hogy a Megtagadás hozzárendelés szűréséhez a alkalmazni szeretné.

    | Szűrés | Leírás |
    | --- | --- |
    | (nincs szűrő) | Összes megtagadása hozzárendelések,:, kisebb és nagyobb a megadott hatókörben. |
    | `$filter=atScope()` | Listában elutasítása hozzárendelések csak a megadott hatókörhöz vagy újabb. Nem tartalmazza a Megtagadás subscopes-hozzárendelést. |
    | `$filter=denyAssignmentName%20eq%20'{deny-assignment-name}'` | Listában elutasítása hozzárendelések a megadott néven. |

## <a name="list-deny-assignments-at-the-root-scope-"></a>Listában elutasítása hozzárendelések a gyökérszintű hatókörben (/)

1. A hozzáférési szintjének emelése, leírtak szerint [az Azure Active Directoryban egy globális rendszergazda hozzáférési szintjének emelése](elevate-access-global-admin.md).

1. Használja a következő kérelmet:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter={filter}
    ```

1. Cserélje le *{szűrő}* azzal a feltétellel, hogy a Megtagadás hozzárendelés szűréséhez a alkalmazni szeretné. Szűrő megadása kötelező.

    | Szűrés | Leírás |
    | --- | --- |
    | `$filter=atScope()` | Listában elutasítása hozzárendelések csak a legfelső szintű hatókörhöz. Nem tartalmazza a Megtagadás subscopes-hozzárendelést. |
    | `$filter=denyAssignmentName%20eq%20'{deny-assignment-name}'` | Listában elutasítása hozzárendelések a megadott néven. |

1. Távolítsa el az emelt szintű hozzáférés.

## <a name="next-steps"></a>További lépések

- [Megismerheti az Azure-erőforrások hozzárendelések megtagadása](deny-assignments.md)
- [Globális rendszergazda jogosultságszintjének emelése az Azure Active Directoryban](elevate-access-global-admin.md)
- [Azure REST API-referencia](/rest/api/azure/)
