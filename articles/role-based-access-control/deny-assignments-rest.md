---
title: Azure-erőforrások megtagadási hozzárendeléseinek listázása a REST API
description: Útmutató a felhasználók, csoportok és alkalmazások megtagadási hozzárendeléseinek listázásához szerepköralapú hozzáférés-vezérléssel (RBAC) az Azure-erőforrások és a REST API számára.
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
ms.date: 06/10/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 9e6214b3cb2cdca2d80ebae43771b206e3396d8b
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2020
ms.locfileid: "77137321"
---
# <a name="list-deny-assignments-for-azure-resources-using-the-rest-api"></a>Azure-erőforrások megtagadási hozzárendeléseinek listázása a REST API használatával

A [hozzárendelések megtagadása](deny-assignments.md) esetén a felhasználók bizonyos Azure-erőforrás-műveleteket hajtanak végre, még akkor is, ha egy szerepkör-hozzárendelés hozzáférést biztosít Ez a cikk bemutatja, hogyan listázhatja a megtagadási hozzárendeléseket a REST API használatával.

> [!NOTE]
> Nem hozhat létre közvetlenül saját megtagadási hozzárendeléseket. További információ a megtagadási hozzárendelések létrehozásáról: a [hozzárendelések megtagadása](deny-assignments.md).

## <a name="prerequisites"></a>Előfeltételek

A megtagadási hozzárendeléssel kapcsolatos információk lekéréséhez a következőket kell tennie:

- `Microsoft.Authorization/denyAssignments/read` engedély, amely az [Azure-erőforrások legtöbb beépített szerepkörében](built-in-roles.md)szerepel.

## <a name="list-a-single-deny-assignment"></a>Egyetlen megtagadási hozzárendelés listázása

1. Kezdje a következő kéréssel:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments/{deny-assignment-id}?api-version=2018-07-01-preview
    ```

1. Az URI-n belül cserélje le a *{scope}* értéket arra a hatókörre, amelyre vonatkozóan meg szeretné jeleníteni a megtagadási hozzárendeléseket.

    | Hatókör | Típus |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Előfizetést |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Erőforráscsoport |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Erőforrás |

1. Cserélje le a *{deny-hozzárendelés-ID}* helyet a lekérdezni kívánt megtagadási hozzárendelés-azonosítóra.

## <a name="list-multiple-deny-assignments"></a>Több megtagadási hozzárendelés listázása

1. Kezdje a következő kérelmek egyikével:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview
    ```

    Választható paraméterekkel:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter={filter}
    ```

1. Az URI-n belül cserélje le a *{scope}* értéket arra a hatókörre, amelyre vonatkozóan meg szeretné jeleníteni a megtagadási hozzárendeléseket.

    | Hatókör | Típus |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Előfizetést |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Erőforráscsoport |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Erőforrás |

1. Cserélje le a *{Filter}* helyére azt a feltételt, amelyet alkalmazni szeretne a megtagadási hozzárendelések listájának szűréséhez.

    | Szűrés | Leírás |
    | --- | --- |
    | (nincs szűrő) | A megadott hatókör alá tartozó összes megtagadási hozzárendelés listázása. |
    | `$filter=atScope()` | Csak a megadott hatókörhöz és a fentiekhez tartozó megtagadási hozzárendelések listázása. A nem tartalmazza a megtagadási hozzárendeléseket alhatókörben. |
    | `$filter=denyAssignmentName%20eq%20'{deny-assignment-name}'` | A megadott nevű megtagadási hozzárendelések listázása. |

## <a name="list-deny-assignments-at-the-root-scope-"></a>Megtagadási hozzárendelések listázása a gyökérszintű hatókörben (/)

1. Emelje meg a hozzáférést a [Azure Active Directory globális rendszergazdai jogosultságának jogosultságszint-emelése](elevate-access-global-admin.md)című témakörben leírtak szerint.

1. Használja a következő kérelmet:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter={filter}
    ```

1. Cserélje le a *{Filter}* helyére azt a feltételt, amelyet alkalmazni szeretne a megtagadási hozzárendelések listájának szűréséhez. Szűrő szükséges.

    | Szűrés | Leírás |
    | --- | --- |
    | `$filter=atScope()` | Csak a gyökérszintű hatókörhöz tartozó megtagadási hozzárendelések listázása. A nem tartalmazza a megtagadási hozzárendeléseket alhatókörben. |
    | `$filter=denyAssignmentName%20eq%20'{deny-assignment-name}'` | A megadott nevű megtagadási hozzárendelések listázása. |

1. Emelt szintű hozzáférés eltávolítása.

## <a name="next-steps"></a>Következő lépések

- [Az Azure-erőforrások megtagadási hozzárendeléseinek megismerése](deny-assignments.md)
- [Globális rendszergazda jogosultságszintjének emelése az Azure Active Directoryban](elevate-access-global-admin.md)
- [Azure REST API-referencia](/rest/api/azure/)
