---
title: Azure-erőforrások megtagadási hozzárendeléseinek listázása a Azure Portal
description: Megtudhatja, hogyan listázhatja azokat a felhasználókat, csoportokat, egyszerű szolgáltatásokat és felügyelt identitásokat, amelyek a Azure Portal használatával megtagadták bizonyos Azure-erőforrásokhoz való hozzáférést.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77137424"
---
# <a name="list-deny-assignments-for-azure-resources-using-the-azure-portal"></a>Azure-erőforrások megtagadási hozzárendeléseinek listázása a Azure Portal használatával

A [hozzárendelések megtagadása](deny-assignments.md) esetén a felhasználók bizonyos Azure-erőforrás-műveleteket hajtanak végre, még akkor is, ha egy szerepkör-hozzárendelés hozzáférést biztosít Ez a cikk bemutatja, hogyan listázhatja a megtagadási hozzárendeléseket a Azure Portal használatával.

> [!NOTE]
> Nem hozhat létre közvetlenül saját megtagadási hozzárendeléseket. További információ a megtagadási hozzárendelések létrehozásáról: a [hozzárendelések megtagadása](deny-assignments.md).

## <a name="prerequisites"></a>Előfeltételek

A megtagadási hozzárendeléssel kapcsolatos információk lekéréséhez a következőket kell tennie:

- `Microsoft.Authorization/denyAssignments/read`az [Azure-erőforrások legtöbb beépített szerepkörének](built-in-roles.md)részét képező engedély.

## <a name="list-deny-assignments"></a>Megtagadás-hozzárendelések felsorolása

Kövesse az alábbi lépéseket a megtagadási hozzárendelések listázásához az előfizetés vagy a felügyeleti csoport hatókörében.

1. A Azure Portal kattintson a **minden szolgáltatás** , majd a **felügyeleti csoportok** vagy **előfizetések**elemre.

1. Kattintson a listázni kívánt felügyeleti csoportra vagy előfizetésre.

1. Kattintson a **Hozzáférés-vezérlés (IAM)** elemre.

1. Kattintson a **megtagadási hozzárendelések** fülre (vagy kattintson a **Megtekintés gombra a** megtagadási hozzárendelések csempén).

    Ha ezen a hatókörön vannak megtagadási hozzárendelések, vagy a hatókör örökölt, a rendszer felsorolja őket.

    ![Hozzáférés-vezérlés – hozzárendelések tiltása lap](./media/deny-assignments-portal/access-control-deny-assignments.png)

1. További oszlopok megjelenítéséhez kattintson az **Oszlopok szerkesztése**elemre.

    ![Hozzárendelések megtagadása – oszlopok](./media/deny-assignments-portal/deny-assignments-columns.png)

    |  |  |
    | --- | --- |
    | **Név** | A megtagadási hozzárendelés neve. |
    | **Résztvevő típusa** | Felhasználó, csoport, rendszer által definiált csoport vagy egyszerű szolgáltatásnév. |
    | **Megtagadva**  | A megtagadási hozzárendelésben szereplő rendszerbiztonsági tag neve. |
    | **ID** | A megtagadási hozzárendelés egyedi azonosítója. |
    | **Kizárt rendszerbiztonsági tag** | Azt határozza meg, hogy vannak-e olyan rendszerbiztonsági tag, amely ki van zárva a megtagadási hozzárendelésből. |
    | **Nem vonatkozik a gyermekekre** | Azt határozza meg, hogy a megtagadási hozzárendelés örökölt-e az alhatókörek számára. |
    | **Védett rendszerek** | Azt jelzi, hogy a megtagadási hozzárendelést az Azure felügyeli-e. Jelenleg mindig igen. |
    | **Hatókör** | Felügyeleti csoport, előfizetés, erőforráscsoport vagy erőforrás. |

1. Vegyen fel egy pipát az engedélyezett elemek valamelyikére, majd kattintson az **OK** gombra a kijelölt oszlopok megjelenítéséhez.

## <a name="list-details-about-a-deny-assignment"></a>Megtagadási hozzárendelés részleteinek listázása

A megtagadási hozzárendelés további részleteinek listázásához kövesse az alábbi lépéseket.

1. Nyissa meg a **megtagadási hozzárendelések** panelt az előző szakaszban leírtak szerint.

1. Kattintson a hozzárendelés megtagadása névre a **felhasználók** panel megnyitásához.

    ![Hozzárendelés megtagadása – felhasználók](./media/deny-assignments-portal/deny-assignment-users.png)

    A **felhasználók** panel a következő két szakaszt tartalmazza.

    |  |  |
    | --- | --- |
    | **A megtagadási hozzárendelés a következőre vonatkozik**  | Rendszerbiztonsági tag, amelyekre a megtagadási hozzárendelés vonatkozik. |
    | **Hozzárendelés megtagadása kizárások** | A megtagadási hozzárendelésből kizárt rendszerbiztonsági tag. |

    A **rendszer által definiált rendszerbiztonsági tag** az összes felhasználót, csoportot, egyszerű szolgáltatást és felügyelt identitást jelképezi egy Azure ad-címtárban.

1. A megtagadott engedélyek listájának megtekintéséhez kattintson a **megtagadott engedélyek**elemre.

    ![Hozzárendelés-megtagadási engedélyek megtagadása](./media/deny-assignments-portal/deny-assignment-denied-permissions.png)

    | Művelettípus | Leírás |
    | --- | --- |
    | **Műveletek**  | A rendszer megtagadta a felügyeleti műveleteket. |
    | **NotActions** | A megtagadott felügyeleti műveletből kizárt felügyeleti műveletek. |
    | **DataActions**  | A rendszer megtagadta az adatműveleteket. |
    | **NotDataActions** | A megtagadott adatműveletből kizárt adatműveletek. |

    Az előző képernyőképen látható példához a következő engedélyek érvényesek:

    - Az adatsíkon lévő összes tárolási művelet a számítási műveletek kivételével megtagadva.

1. Egy megtagadási hozzárendelés tulajdonságainak megtekintéséhez kattintson a **Tulajdonságok**elemre.

    ![Hozzárendelés megtagadása – tulajdonságok](./media/deny-assignments-portal/deny-assignment-properties.png)

    A **Tulajdonságok** panelen megtekintheti a megtagadási hozzárendelés nevét, azonosítóját, leírását és hatókörét. A nem **vonatkozik a gyermekekre** kapcsoló azt jelzi, hogy a megtagadási hozzárendelés örökölt-e az alhatókörben. A **rendszer által védett** kapcsoló jelzi, hogy a megtagadási hozzárendelést az Azure felügyeli-e. Jelenleg ez minden esetben **Igen** .

## <a name="next-steps"></a>További lépések

* [Az Azure-erőforrások megtagadási hozzárendeléseinek megismerése](deny-assignments.md)
* [Azure-erőforrások megtagadási hozzárendeléseinek listázása Azure PowerShell használatával](deny-assignments-powershell.md)
