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
ms.date: 03/19/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 0f648405a3d71bf27c64dacbb3fd78f3e9801137
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80063020"
---
# <a name="list-deny-assignments-for-azure-resources-using-the-rest-api"></a>Azure-erőforrások megtagadási hozzárendeléseinek listázása a REST API használatával

A [hozzárendelések megtagadása](deny-assignments.md) esetén a felhasználók bizonyos Azure-erőforrás-műveleteket hajtanak végre, még akkor is, ha egy szerepkör-hozzárendelés hozzáférést biztosít Ez a cikk bemutatja, hogyan listázhatja a megtagadási hozzárendeléseket a REST API használatával.

> [!NOTE]
> Nem hozhat létre közvetlenül saját megtagadási hozzárendeléseket. További információ a megtagadási hozzárendelések létrehozásáról: a [hozzárendelések megtagadása](deny-assignments.md).

## <a name="prerequisites"></a>Előfeltételek

A megtagadási hozzárendeléssel kapcsolatos információk lekéréséhez a következőket kell tennie:

- `Microsoft.Authorization/denyAssignments/read`az [Azure-erőforrások legtöbb beépített szerepkörének](built-in-roles.md)részét képező engedély.

## <a name="list-a-single-deny-assignment"></a>Egyetlen megtagadási hozzárendelés listázása

1. Kezdje a következő kéréssel:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments/{deny-assignment-id}?api-version=2018-07-01-preview
    ```

1. Az URI-n belül cserélje le a *{scope}* értéket arra a hatókörre, amelyre vonatkozóan meg szeretné jeleníteni a megtagadási hozzárendeléseket.

    > [!div class="mx-tableFixed"]
    > | Hatókör | Típus |
    > | --- | --- |
    > | `subscriptions/{subscriptionId}` | Előfizetés |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Erőforráscsoport |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Erőforrás |

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

    > [!div class="mx-tableFixed"]
    > | Hatókör | Típus |
    > | --- | --- |
    > | `subscriptions/{subscriptionId}` | Előfizetés |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Erőforráscsoport |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Erőforrás |

1. Cserélje le a *{Filter}* helyére azt a feltételt, amelyet alkalmazni szeretne a megtagadási hozzárendelések listájának szűréséhez.

    > [!div class="mx-tableFixed"]
    > | Szűrés | Leírás |
    > | --- | --- |
    > | (nincs szűrő) | Felsorolja az összes megtagadási hozzárendelést a (z) felett, és a megadott hatókör alá esik. |
    > | `$filter=atScope()` | Felsorolja a csak a megadott hatókörhöz és a fentiekhez tartozó megtagadási hozzárendeléseket. A nem tartalmazza a megtagadási hozzárendeléseket alhatókörben. |
    > | `$filter=assignedTo('{objectId}')` | Felsorolja a megadott felhasználóhoz vagy egyszerű szolgáltatáshoz tartozó megtagadási hozzárendeléseket.<br/>Ha a felhasználó egy olyan csoport tagja, amely megtagadási hozzárendelést tartalmaz, a megtagadási hozzárendelés is megjelenik. Ez a szűrő a csoportok esetében tranzitív, ami azt jelenti, hogy ha a felhasználó egy csoport tagja, és a csoport egy másik, megtagadási hozzárendeléssel rendelkező csoport tagja, akkor a megtagadási hozzárendelés is fel van sorolva.<br/>Ez a szűrő csak egy felhasználóhoz vagy egy egyszerű szolgáltatáshoz tartozó objektumazonosítót fogad el. Nem lehet átadni egy objektum AZONOSÍTÓját egy csoport számára. |
    > | `$filter=atScope()+and+assignedTo('{objectId}')` | Felsorolja a megadott felhasználó vagy szolgáltatásnév, valamint a megadott hatókör megtagadásának hozzárendeléseit. |
    > | `$filter=denyAssignmentName+eq+'{deny-assignment-name}'` | Felsorolja a megadott nevű megtagadási hozzárendeléseket. |
    > | `$filter=principalId+eq+'{objectId}'` | Felsorolja a megadott felhasználóhoz, csoporthoz vagy egyszerű szolgáltatáshoz tartozó hozzárendelések megtagadását. |

## <a name="list-deny-assignments-at-the-root-scope-"></a>Megtagadási hozzárendelések listázása a gyökérszintű hatókörben (/)

1. Emelje meg a hozzáférést a [Azure Active Directory globális rendszergazdai jogosultságának jogosultságszint-emelése](elevate-access-global-admin.md)című témakörben leírtak szerint.

1. Használja az alábbi kérelmet:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter={filter}
    ```

1. Cserélje le a *{Filter}* helyére azt a feltételt, amelyet alkalmazni szeretne a megtagadási hozzárendelések listájának szűréséhez. Szűrő szükséges.

    > [!div class="mx-tableFixed"]
    > | Szűrés | Leírás |
    > | --- | --- |
    > | `$filter=atScope()` | Csak a gyökérszintű hatókörhöz tartozó megtagadási hozzárendelések listázása. A nem tartalmazza a megtagadási hozzárendeléseket alhatókörben. |
    > | `$filter=denyAssignmentName+eq+'{deny-assignment-name}'` | A megadott nevű megtagadási hozzárendelések listázása. |

1. Emelt szintű hozzáférés eltávolítása.

## <a name="next-steps"></a>További lépések

- [Az Azure-erőforrások megtagadási hozzárendeléseinek megismerése](deny-assignments.md)
- [Globális rendszergazda jogosultságszintjének emelése az Azure Active Directoryban](elevate-access-global-admin.md)
- [Azure REST API-referenciák](/rest/api/azure/)
