---
title: Az Azure-erőforrások hozzárendelésének listázása az Azure Portalon
description: Megtudhatja, hogyan listázhat a felhasználók, csoportok, egyszerű szolgáltatásés felügyelt identitások, amelyek megtagadták a hozzáférést az adott Azure-erőforrás-műveletek adott hatókörök az Azure Portalon.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8078f366-a2c4-4fbb-a44b-fc39fd89df81
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/10/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 4db76e5c6191457346ca1f95678cf73843334d3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77137424"
---
# <a name="list-deny-assignments-for-azure-resources-using-the-azure-portal"></a>Az Azure-erőforrások letagadni idulásának listája az Azure Portal használatával

[A hozzárendelések letiltása letiltja](deny-assignments.md) a felhasználókat bizonyos Azure-erőforrás-műveletek végrehajtásában, még akkor is, ha egy szerepkör-hozzárendelés hozzáférést biztosít számukra. Ez a cikk ismerteti, hogyan listázhatja a megtagadási hozzárendelések az Azure Portalhasználatával.

> [!NOTE]
> Nem hozhat létre közvetlenül saját megtagadási hozzárendeléseket. A megtagadási hozzárendelések létrehozásáról a [Hozzárendelések megtagadása](deny-assignments.md)című témakörben talál további információt.

## <a name="prerequisites"></a>Előfeltételek

A megtagadási feladattal kapcsolatos információk hozásához a következőkre van szüksége:

- `Microsoft.Authorization/denyAssignments/read`az [Azure-erőforrások](built-in-roles.md)legtöbb beépített szerepkörében megtalálható.

## <a name="list-deny-assignments"></a>Megtagadás-hozzárendelések felsorolása

Az alábbi lépésekkel listázhatja a megtagadási hozzárendeléseket az előfizetési vagy felügyeleti csoport hatókörén.

1. Az Azure Portalon kattintson a **Minden szolgáltatás** elemre, majd a **Felügyeleti csoportok** vagy **előfizetések**elemre.

1. Kattintson a felsorolni kívánt felügyeleti csoportra vagy előfizetésre.

1. Kattintson a **Hozzáférés-vezérlés (IAM)** elemre.

1. Kattintson a **Hozzárendelések megtagadása** fülre (vagy kattintson a **Nézet** gombra a Megtagadási hozzárendelések megtekintése csempén).

    Ha van olyan megtagadási hozzárendelések ebben a hatókörben, vagy örökölt, hogy a hatókör, akkor a lista.

    ![Hozzáférés-vezérlés – Hozzárendelések megtagadása lap](./media/deny-assignments-portal/access-control-deny-assignments.png)

1. További oszlopok megjelenítéséhez kattintson **az Oszlopok szerkesztése gombra.**

    ![Hozzárendelések megtagadása - oszlopok](./media/deny-assignments-portal/deny-assignments-columns.png)

    |  |  |
    | --- | --- |
    | **Név** | A megtagadási hozzárendelés neve. |
    | **Fő típus** | Felhasználó, csoport, rendszer által definiált csoport vagy egyszerű szolgáltatás. |
    | **Megtagadva**  | A megtagadási hozzárendelésben szereplő rendszerbiztonsági tag neve. |
    | **Id** | A megtagadási hozzárendelés egyedi azonosítója |
    | **Kizárt megbízók** | Azt jelzi, hogy vannak-e olyan rendszerbiztonsági tagok, amelyek ki vannak zárva a megtagadási hozzárendelésből. |
    | **Nem vonatkozik a gyermekekre** | Azt jelzi, hogy a megtagadási hozzárendelés alható-e. |
    | **Rendszer védett** | Azt jelzi, hogy a megtagadási hozzárendelést az Azure kezeli-e. Jelenleg mindig igen. |
    | **Hatókör** | Felügyeleti csoport, előfizetés, erőforráscsoport vagy erőforrás. |

1. Jelölje be az engedélyezett elemekbármelyikét, majd kattintson az **OK** gombra a kijelölt oszlopok megjelenítéséhez.

## <a name="list-details-about-a-deny-assignment"></a>Megtagadási hozzárendelés részleteinek listázása

A megtagadási hozzárendeléssel kapcsolatos további részletek listázásához kövesse az alábbi lépéseket.

1. Nyissa meg a **Megtagadási hozzárendelések** ablaktáblát az előző szakaszban leírtak szerint.

1. Kattintson a megtagadási hozzárendelés nevére a **Felhasználók** panel megnyitásához.

    ![Hozzárendelés megtagadása - felhasználók](./media/deny-assignments-portal/deny-assignment-users.png)

    A **Felhasználók** panel a következő két szakaszt tartalmazza.

    |  |  |
    | --- | --- |
    | **A Megtagadás hozzárendelés a**  | A megtagadási hozzárendelésáltal alkalmazott rendszerbiztonsági tagok. |
    | **Hozzárendelés megtagadása kizárás** | A megtagadási hozzárendelésből kizárt rendszerbiztonsági tagok. |

    **A rendszer által definiált egyszerű** képviseli az összes felhasználó, csoport, szolgáltatásnévés felügyelt identitások egy Azure AD-címtárban.

1. A visszautasított engedélyek listájának megtekintéséhez kattintson a **Megtagadott engedélyek gombra.**

    ![Megtagadási hozzárendelés – Elutasított engedélyek](./media/deny-assignments-portal/deny-assignment-denied-permissions.png)

    | Művelettípus | Leírás |
    | --- | --- |
    | **Műveletek**  | Megtagadták a kezelési műveleteket. |
    | **NotActions** | A visszautasított felügyeleti műveletből kizárt felügyeleti műveletek. |
    | **DataActions (Adatműveletek)**  | Adatműveletek megtagadva. |
    | **NotDataActions** | A megtagadott adatműveletből kizárt adatműveletek. |

    Az előző képernyőképen látható példában a következők a hatályos engedélyek:

    - Az adatsíkon lévő összes tárolási művelet megtagadva, kivéve a számítási műveleteket.

1. A megtagadási hozzárendelés tulajdonságainak megtekintéséhez kattintson a **Tulajdonságok gombra.**

    ![Hozzárendelés megtagadása - Tulajdonságok](./media/deny-assignments-portal/deny-assignment-properties.png)

    A **Tulajdonságok** panelen láthatja a megtagadási hozzárendelés nevét, azonosítót, leírást és hatókört. A **Nem vonatkozik a gyermekek** kapcsoló jelzi, hogy a megtagadási hozzárendelés öröklődik-e a segédcsövek. A **rendszervédett** kapcsoló azt jelzi, hogy ezt a megtagadási hozzárendelést az Azure kezeli-e. Jelenleg ez minden esetben **Igen.**

## <a name="next-steps"></a>További lépések

* [Az Azure-erőforrások megtagadási hozzárendeléseinek ismertetése](deny-assignments.md)
* [Az Azure-erőforrások letagadni idulásának listája az Azure PowerShell használatával](deny-assignments-powershell.md)
