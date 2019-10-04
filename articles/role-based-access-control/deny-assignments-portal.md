---
title: Listában elutasítása hozzárendelések Azure-erőforrások az Azure portal használatával |} A Microsoft Docs
description: Ismerje meg, hogyan kell felsorolni a felhasználók, csoportok, a szolgáltatásnevek és felügyelt identitások, amelyek az adott Azure-erőforrás-műveleteket hajthat végre, az Azure portal használatával adott hatókörök hozzáférése.
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
ms.openlocfilehash: 21ffb1a2539a2e724a91dd3b2818270a5e573ef8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67127485"
---
# <a name="list-deny-assignments-for-azure-resources-using-the-azure-portal"></a>Listában elutasítása hozzárendelések Azure-erőforrások az Azure portal használatával

[Hozzárendelések megtagadása](deny-assignments.md) meggátolja a felhasználókat adott Azure-erőforrás műveleteket végrehajtani, akkor is, ha a szerepkör-hozzárendelés hozzáférést biztosít számukra. Ez a cikk azt ismerteti, hogyan kell felsorolni az Azure portal használatával hozzárendelések elutasítása.

> [!NOTE]
> Nem közvetlenül hozhat létre saját hozzárendelések elutasítása. Információ a nem engedélyezi a hozzárendelések jönnek létre, lásd: [hozzárendelések megtagadása](deny-assignments.md).

## <a name="prerequisites"></a>Előfeltételek

Egy megtagadási hozzárendelés adatainak beolvasása, kell rendelkeznie:

- `Microsoft.Authorization/denyAssignments/read` engedéllyel, amely tartalmazza a legtöbb [beépített szerepkörök az Azure-erőforrások](built-in-roles.md).

## <a name="list-deny-assignments"></a>Listában elutasítása hozzárendelések

Kövesse az alábbi lépéseket listában elutasítása-hozzárendelést az előfizetés vagy a felügyeleti csoport hatóköre.

1. Az Azure Portalon kattintson a **minden szolgáltatás** , majd **felügyeleti csoportok** vagy **előfizetések**.

1. Kattintson a felügyeleti csoport vagy a megjeleníteni kívánt előfizetést.

1. Kattintson a **hozzáférés-vezérlés (IAM)** .

1. Kattintson a **hozzárendelések megtagadása** lap (vagy kattintson a **nézet** gombot a nézetben megtagadása hozzárendelések csempére).

    Ha bármelyik megtagadása a hatókör-hozzárendelést, vagy az örökölt ebben a hatókörben, azok megjelennek.

    ![Hozzáférés-vezérlés – hozzárendeléseket lapon megtagadása](./media/deny-assignments-portal/access-control-deny-assignments.png)

1. További oszlopok megjelenítéséhez kattintson **oszlopok szerkesztése**.

    ![Elutasítás-hozzárendelések – oszlopok](./media/deny-assignments-portal/deny-assignments-columns.png)

    |  |  |
    | --- | --- |
    | **Name (Név)** | A megtagadási hozzárendelés neve. |
    | **Résztvevő típusa** | Felhasználó, csoport, rendszer által meghatározott csoporthoz vagy egyszerű szolgáltatást. |
    | **Megtagadva**  | A rendszerbiztonsági tagot, amely megtalálható a Megtagadás hozzárendelés neve. |
    | **Azonosító** | A megtagadási hozzárendelés egyedi azonosítója. |
    | **Kizárt rendszerbiztonsági tagok** | Hogy vannak-e a Megtagadás hozzárendelés kizárt rendszerbiztonsági. |
    | **Gyermekek nem vonatkozik** | A megtagadási hozzárendelés e subscopes az örökölt. |
    | **A rendszer által védett** | A megtagadási hozzárendelés e Azure felügyeli. Jelenleg minden esetben igen. |
    | **Hatókör** | A felügyeleti csoport, előfizetés, erőforráscsoport vagy erőforrás. |

1. Az engedélyezett elemek hozzáadása egy pipa, és kattintson a **OK** a kijelölt oszlopok megjelenítéséhez.

## <a name="list-details-about-a-deny-assignment"></a>Lista részleteit egy megtagadási-hozzárendelést

Kövesse az alábbi lépéseket egy megtagadási hozzárendeléssel kapcsolatos további részletek.

1. Nyissa meg a **hozzárendelések megtagadása** ablaktáblán az előző szakaszban leírtak szerint.

1. A megtagadási hozzárendelés nevére kattintva megnyílik a **felhasználók** panelen.

    ![Hozzárendelés - felhasználók elutasítása](./media/deny-assignments-portal/deny-assignment-users.png)

    A **felhasználók** panelen a következő két szakasz tartalmazza.

    |  |  |
    | --- | --- |
    | **Érvényes hozzárendelés elutasítása**  | Rendszerbiztonsági tagok, amelyek a megtagadási hozzárendelés vonatkozik. |
    | **Nem tartalmazza a hozzárendelés elutasítása** | Rendszerbiztonsági tagok, amelyek ki lettek zárva a Megtagadás hozzárendelést. |

    **A rendszer által definiált egyszerű** felhasználók, csoportok, a szolgáltatásnevek és az Azure AD-címtár felügyelt identitások jelöli.

1. Az engedélyeket, a rendszer megtagadta a listájának megtekintéséhez kattintson **megtagadva engedélyek**.

    ![Hozzárendelés - engedélyek megtagadva megtagadása](./media/deny-assignments-portal/deny-assignment-denied-permissions.png)

    | Művelettípus | Leírás |
    | --- | --- |
    | **Műveletek**  | Elutasított felügyeleti műveleteket. |
    | **notActions** | Felügyeleti műveletek zárva felügyeleti művelet megtagadva. |
    | **DataActions**  | Adatműveletek megtagadva. |
    | **NotDataActions** | Kizárt Adatműveletek adat művelet megtagadva. |

    Ha az előző képernyőképen látható például a következők a hatályos engedélyek:

    - Az adatsík műveleteket a rendszer megtagadta a az alábbiakat kivéve minden tároló számítási műveletek.

1. Egy megtagadási hozzárendelés tulajdonságainak megtekintéséhez kattintson **tulajdonságok**.

    ![Hozzárendelés - tulajdonságok megtagadása](./media/deny-assignments-portal/deny-assignment-properties.png)

    Az a **tulajdonságok** panelen láthatja a Megtagadás hozzárendelés neve, Azonosítóját, leírás és hatókör. A **nem vonatkozik a gyermekek** kapcsoló azt jelzi-e a Megtagadás hozzárendelés subscopes az örökölt. A **rendszer védett** kapcsoló azt jelzi, hogy ez a megtagadási hozzárendelése az Azure által felügyelt. Jelenleg ez az **Igen** minden esetben.

## <a name="next-steps"></a>További lépések

* [Megismerheti az Azure-erőforrások hozzárendelések megtagadása](deny-assignments.md)
* [Listában elutasítása hozzárendelések Azure-erőforrások Azure PowerShell-lel](deny-assignments-powershell.md)
