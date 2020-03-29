---
title: Az Azure-erőforrások megtagadási hozzárendeléseinek listája a REST API-val
description: Megtudhatja, hogyan listázhatja a felhasználók, csoportok és alkalmazások megtagadási hozzárendeléseit az Azure-erőforrások és a REST API szerepköralapú hozzáférés-vezérlés (RBAC) használatával.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063020"
---
# <a name="list-deny-assignments-for-azure-resources-using-the-rest-api"></a>Az Azure-erőforrások megtagadási hozzárendeléseinek listája a REST API használatával

[A hozzárendelések letiltása letiltja](deny-assignments.md) a felhasználókat bizonyos Azure-erőforrás-műveletek végrehajtásában, még akkor is, ha egy szerepkör-hozzárendelés hozzáférést biztosít számukra. Ez a cikk ismerteti, hogyan listázhatja a megtagadási hozzárendelések a REST API használatával.

> [!NOTE]
> Nem hozhat létre közvetlenül saját megtagadási hozzárendeléseket. A megtagadási hozzárendelések létrehozásáról a [Hozzárendelések megtagadása](deny-assignments.md)című témakörben talál további információt.

## <a name="prerequisites"></a>Előfeltételek

A megtagadási feladattal kapcsolatos információk hozásához a következőkre van szüksége:

- `Microsoft.Authorization/denyAssignments/read`az [Azure-erőforrások](built-in-roles.md)legtöbb beépített szerepkörében megtalálható.

## <a name="list-a-single-deny-assignment"></a>Egyetlen megtagadási hozzárendelés felsorolása

1. Kezdje a következő kéréssel:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments/{deny-assignment-id}?api-version=2018-07-01-preview
    ```

1. Az URI-n belül cserélje le *a(z) {scope}* értéket arra a hatókörre, amelynek megtagadási hozzárendeléseit fel szeretné sorolni.

    > [!div class="mx-tableFixed"]
    > | Hatókör | Típus |
    > | --- | --- |
    > | `subscriptions/{subscriptionId}` | Előfizetés |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Erőforráscsoport |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Erőforrás |

1. Cserélje le *a(z) {deny-assignment-id} azonosítót* a beolvasni kívánt megtagadási hozzárendelés-azonosítóra.

## <a name="list-multiple-deny-assignments"></a>Több megtagadási hozzárendelés ek listázása

1. Kezdje az alábbi kérések egyikével:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview
    ```

    Választható paraméterekkel:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter={filter}
    ```

1. Az URI-n belül cserélje le *a(z) {scope}* értéket arra a hatókörre, amelynek megtagadási hozzárendeléseit fel szeretné sorolni.

    > [!div class="mx-tableFixed"]
    > | Hatókör | Típus |
    > | --- | --- |
    > | `subscriptions/{subscriptionId}` | Előfizetés |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Erőforráscsoport |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Erőforrás |

1. Cserélje le *a(z) {filter}* elemet arra a feltételre, amelyet a megtagadási hozzárendelés-lista szűréséhez alkalmazni kíván.

    > [!div class="mx-tableFixed"]
    > | Szűrés | Leírás |
    > | --- | --- |
    > | (nincs szűrő) | Felsorolja az összes megtagadási hozzárendelést a megadott hatókörben, a fenti és alatt. |
    > | `$filter=atScope()` | A lista csak a megadott hatókörhöz és a fenti műveletekhez tartozó hozzárendeléseket sorolja fel. Nem tartalmazza az alhatókörök ben lévő megtagadási hozzárendeléseket. |
    > | `$filter=assignedTo('{objectId}')` | A megadott felhasználó vagy egyszerű szolgáltatás hozzárendeléseit sorolja fel.<br/>Ha a felhasználó egy megtagadási hozzárendeléssel rendelkező csoport tagja, akkor a megtagadási hozzárendelés is megjelenik a listában. Ez a szűrő tranzitív a csoportok számára, ami azt jelenti, hogy ha a felhasználó egy csoport tagja, és ez a csoport egy másik csoport tagja, amely nek megtagadási hozzárendelése van, akkor a megtagadási hozzárendelés is szerepel a listában.<br/>Ez a szűrő csak egy felhasználó vagy egy egyszerű szolgáltatás objektumazonosítóját fogadja el. Csoportazonosítója nem adható át. |
    > | `$filter=atScope()+and+assignedTo('{objectId}')` | A megadott felhasználó vagy egyszerű szolgáltatás hozzárendelései és a megadott hatókör letagadási hozzárendelései. |
    > | `$filter=denyAssignmentName+eq+'{deny-assignment-name}'` | A megadott nevű hozzárendelések listája. |
    > | `$filter=principalId+eq+'{objectId}'` | A megadott felhasználó, csoport vagy szolgáltatásnév megtagadási hozzárendeléseit sorolja fel. |

## <a name="list-deny-assignments-at-the-root-scope-"></a>Megtagadási hozzárendelések listázása a gyökérhatókörben (/)

1. Az Azure Active Directory [globális rendszergazdájának elérése elévülése](elevate-access-global-admin.md)című dokumentumban leírtak szerint magasabb rakhatja a hozzáférést.

1. Használja az alábbi kérelmet:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter={filter}
    ```

1. Cserélje le *a(z) {filter}* elemet arra a feltételre, amelyet a megtagadási hozzárendelés-lista szűréséhez alkalmazni kíván. Szűrő szükséges.

    > [!div class="mx-tableFixed"]
    > | Szűrés | Leírás |
    > | --- | --- |
    > | `$filter=atScope()` | Csak a gyökérhatókör megtagadási hozzárendeléseinek listázása. Nem tartalmazza az alhatókörök ben lévő megtagadási hozzárendeléseket. |
    > | `$filter=denyAssignmentName+eq+'{deny-assignment-name}'` | A megadott nevű megtagadási hozzárendelések listája. |

1. Távolítsa el az emelt szintű hozzáférést.

## <a name="next-steps"></a>További lépések

- [Az Azure-erőforrások megtagadási hozzárendeléseinek ismertetése](deny-assignments.md)
- [Globális rendszergazda jogosultságszintjének emelése az Azure Active Directoryban](elevate-access-global-admin.md)
- [Azure REST API-útmutató](/rest/api/azure/)
