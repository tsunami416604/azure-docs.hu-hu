---
title: Azure AD-szerepkörök naplózási előzményeinek megtekintése a PIM-Azure Active Directoryban | Microsoft Docs
description: Megtudhatja, hogyan tekintheti meg Azure AD Privileged Identity Management (PIM) Azure AD-szerepkörök naplózási előzményeit.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 10/22/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9aa1c5121c723148884584ca3f00522c1c459859
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72808999"
---
# <a name="view-audit-history-for-azure-ad-roles-in-pim"></a>Azure AD-szerepkörök naplózási előzményeinek megtekintése a PIM-ben

A Privileged Identity Management (PIM) naplózási előzményeivel megtekintheti az elmúlt 30 napban lévő összes szerepkör-hozzárendelést és aktiválást az összes Kiemelt szerepkör esetében. Ha szeretné megtekinteni a Azure Active Directory (Azure AD) szervezet tevékenységének teljes naplózási előzményeit, beleértve a rendszergazdai, a végfelhasználói és a szinkronizálási tevékenységet, használhatja a [Azure Active Directory biztonsági és tevékenységi jelentéseket](../reports-monitoring/overview-reports.md).

## <a name="view-audit-history"></a>Naplózási előzmények megtekintése

Kövesse az alábbi lépéseket az Azure AD-szerepkörök naplózási előzményeinek megtekintéséhez.

1. Jelentkezzen be [Azure Portalba](https://portal.azure.com/) egy olyan felhasználóval, aki tagja a [Kiemelt szerepkörű rendszergazda](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) szerepkörnek.

1. Nyissa meg **Azure ad Privileged Identity Management**.

1. Kattintson az **Azure ad-szerepkörök**elemre.

1. Kattintson a **címtár-szerepkörök naplózási előzményei**elemre.

    A naplózási előzményektől függően egy oszlopdiagram jelenik meg az összes aktiválással, a napi maximális aktiválással és napi átlagos aktiválással együtt.

    ![Címtárbeli szerepkörök naplózási előzményei](media/pim-how-to-use-audit-log/directory-roles-audit-history.png)

    A lap alján egy tábla jelenik meg, amely a rendelkezésre álló naplózási előzményekben szereplő egyes műveletekkel kapcsolatos információkat tartalmazza. Az oszlopok a következő jelentésekkel rendelkeznek:

    | Column | Leírás |
    | --- | --- |
    | Idő | Ha a művelet bekövetkezett. |
    | Kérelmező | A szerepkör aktiválását vagy módosítását kérő felhasználó. Ha az érték az **Azure System**, további információért tekintse meg az Azure naplózási előzményeit. |
    | Műveletek | A kérelmező által végrehajtott műveletek. A műveletek lehetnek például a hozzárendelés, a hozzárendelés megszüntetése, az aktiválás, az inaktiválás vagy a AddedOutsidePIM. |
    | Tag | Az a felhasználó, aki aktivál vagy hozzárendel egy szerepkörhöz. |
    | Szerepkör | A felhasználó által hozzárendelt vagy aktivált szerepkör. |
    | Érvelés | Az aktiválás során az OK mezőbe beírt szöveg |
    | lejárati | Ha egy aktivált szerepkör lejár. Csak a jogosult szerepkör-hozzárendelésekre vonatkozik. |

1. A naplózási előzmények rendezéséhez kattintson az **idő**, a **művelet**és a **szerepkör** gombokra.

## <a name="filter-audit-history"></a>Naplózási előzmények szűrése

1. A naplózási Előzmények lap tetején kattintson a **szűrő** gombra.

    Megjelenik a **frissítési diagram paramétereinek** panelje.

1. Az **időtartomány**területen válassza ki az időtartományt.

1. A **szerepkörök**területen jelölje be a jelölőnégyzeteket a megtekinteni kívánt szerepkörök jelzéséhez.

    ![Diagram paramétereinek frissítése panel](media/pim-how-to-use-audit-log/update-chart-parameters.png)

1. A szűrt naplózási előzmények megtekintéséhez kattintson a **kész** gombra.

## <a name="next-steps"></a>Következő lépések

- [Az Azure Resource szerepköreinek tevékenység-és naplózási előzményeinek megtekintése Privileged Identity Management](azure-pim-resource-rbac.md)
