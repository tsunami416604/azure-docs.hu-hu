---
title: A PIM - Azure Active Directory Azure AD-szerepkörökhöz tartozó naplózási előzmények megtekintése |} A Microsoft Docs
description: Ismerje meg az Azure AD-szerepkörökhöz tartozó naplózási előzmények megtekintése az Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 06/10/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8061cff8d39db66cb22a5650c7688657aa8b3554
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67053939"
---
# <a name="view-audit-history-for-azure-ad-roles-in-pim"></a>Az Azure AD-szerepköröket a PIM naplózási előzmények megtekintése

Az Azure Active Directory (Azure AD) Privileged Identity Management (PIM) naplózási előzmények segítségével tekintse meg a szerepkör-hozzárendelések és aktiválások az elmúlt 30 nap összes a kiemelt szerepkörökhöz tartozó. Ha meg szeretné tekinteni a teljes naplózási előzmények tevékenység a címtárban, beleértve a rendszergazda, a végfelhasználó és a szinkronizálási tevékenység, használhatja a [Azure Active Directory biztonsági és a tevékenység jelentések](../reports-monitoring/overview-reports.md).

## <a name="view-audit-history"></a>Naplózási előzmények megtekintése

Kövesse az alábbi lépéseket az Azure AD-szerepkörökhöz tartozó naplózási előzmények megtekintése.

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com/) egy felhasználóval, amely tagja a [kiemelt szerepkörű rendszergazda](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) szerepkör.

1. Nyissa meg **az Azure AD Privileged Identity Management**.

1. Kattintson a **Azure AD-szerepkörök**.

1. Kattintson a **címtárbeli szerepkörök naplózási előzményei**.

    Attól függően, a naplózási előzmények és az összes aktiválás maximális aktiválások száma / nap és átlagos aktiválások száma / nap egy oszlopdiagram jelenik meg.

    ![Címtárbeli szerepkörök naplózási előzményei](media/pim-how-to-use-audit-log/directory-roles-audit-history.png)

    A lap alján egy tábla minden egyes művelethez a rendelkezésre álló naplózási előzmények információ jelenik meg. Az oszlopok jelentése a következő:

    | Oszlop | Leírás |
    | --- | --- |
    | Time | Ha a művelet történt. |
    | Kérelmező | A szerepkör aktiválása vagy módosítsa a kért felhasználó. Ha az érték **Azure rendszer**, ellenőrizze az Azure naplózási előzmények további információt. |
    | Műveletek | A kérelmező által végzett műveleteket. Műveletek hozzárendelése, kijelölés megszüntetése, aktiválás, inaktiválja vagy AddedOutsidePIM is tartalmazhat. |
    | Tag | Az aktiválás, vagy egy szerepkörhöz rendelt felhasználó. |
    | Szerepkör | Szerepkör hozzárendelése vagy a felhasználó aktiválása. |
    | Érvelés | Az aktiválás során a OK mezőben megadott szöveg. |
    | lejárati | Amikor egy aktivált szerepkör érvényessége lejár. Csak jogosult szerepkör-hozzárendelések vonatkozik. |

1. A naplózási előzmények rendezéséhez kattintson a **idő**, **művelet**, és **szerepkör** gombokat.

## <a name="filter-audit-history"></a>Szűrő naplózási előzmények

1. A naplózási Előzmények lap tetején kattintson a **szűrő** gombra.

    A **diagram paramétereinek frissítése** ablaktáblán jelenik meg.

1. A **időtartomány**, jelöljön ki egy időtartományt.

1. A **szerepkörök**, adja hozzá a megtekinteni kívánt szerepkörök jelölőnégyzeteiből.

    ![Diagramterület paraméterek frissítése](media/pim-how-to-use-audit-log/update-chart-parameters.png)

1. Kattintson a **kész** a szűrt naplózási előzmények megtekintése.

## <a name="next-steps"></a>További lépések

- [A PIM az Azure-erőforrások szerepköreihez tartozó tevékenység és a naplózási előzmények megtekintése](azure-pim-resource-rbac.md)
