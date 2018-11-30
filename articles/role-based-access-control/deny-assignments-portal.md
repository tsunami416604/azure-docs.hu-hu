---
title: Nézet megtagadása hozzárendelések, az Azure portal használatával |} A Microsoft Docs
description: Ismerje meg, hogy a felhasználók, csoportok, a szolgáltatásnevek és felügyelt identitások, amelyek meghatározott műveleteket hajthat végre az Azure portal használatával adott hatókörben hozzáférése megtekintése.
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
ms.date: 11/30/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: f5870ddbbb8be0ebbeae7656485521a327b86d5b
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2018
ms.locfileid: "52642808"
---
# <a name="view-deny-assignments-using-the-azure-portal"></a>Nézet megtagadása hozzárendelések, az Azure portal használatával

[Hozzárendelések megtagadása](deny-assignments.md) meggátolja a felhasználókat adott műveletek végrehajtására, még akkor is, ha a szerepkör-hozzárendelés hozzáférést biztosít számukra. Annak ellenére, hogy nem hozható létre a saját megtagadása hozzárendelések, továbbra is szeretné megtekintheti hozzárendelések megtagadja, mert hatással lehet az általános engedélyeit. A megtagadás-hozzárendelésekkel kapcsolatos információk lekéréséhez a `Microsoft.Authorization/denyAssignments/read` engedéllyel kell rendelkeznie, amelyet a legtöbb [beépített szerepkör](built-in-roles.md) tartalmaz.

Ez a cikk bemutatja, hogyan megtekintése az Azure portal segítségével hozzárendelések elutasítása.

> [!NOTE]
> Jelenleg megtagadási hozzárendelések csak olvashatók, és csak állítható be az Azure-ban.

## <a name="view-deny-assignments"></a>Megtagadási hozzárendelések megtekintése

Kövesse az alábbi lépéseket megtekintéséhez az előfizetés vagy a felügyeleti csoport hatókörű hozzárendelések tiltása.

1. Az Azure Portalon kattintson a **minden szolgáltatás** , majd **felügyeleti csoportok** vagy **előfizetések**.

1. Kattintson a felügyeleti csoport vagy a megtekinteni kívánt előfizetést.

1. Kattintson a **hozzáférés-vezérlés (IAM)**.

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

## <a name="view-details-about-a-deny-assignment"></a>Egy megtagadási hozzárendelés részleteinek megtekintése

Kövesse az alábbi lépéseket egy megtagadási hozzárendeléssel kapcsolatos további részletek megtekintéséhez.

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

* [Megismerheti hozzárendelés elutasítása](deny-assignments.md)
* [Listában elutasítása hozzárendelések rbac-RÓL és a REST API használatával](deny-assignments-rest.md)
