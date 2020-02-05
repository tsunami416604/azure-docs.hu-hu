---
title: Azure AD-szerepkörök naplózási jelentésének megtekintése a PIM-ben – Azure AD | Microsoft Docs
description: Megtudhatja, hogyan tekintheti meg Azure AD Privileged Identity Management (PIM) Azure AD-szerepkörök naplózási előzményeit.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 11/13/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d46036efa04b4e0225cad6e8a70cd31ad3c10bd
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024177"
---
# <a name="view-audit-history-for-azure-ad-roles-in-pim"></a>Azure AD-szerepkörök naplózási előzményeinek megtekintése a PIM-ben

A Privileged Identity Management (PIM) naplózási előzményeivel megtekintheti az elmúlt 30 napban lévő összes szerepkör-hozzárendelést és aktiválást az összes Kiemelt szerepkör esetében. Ha szeretné megtekinteni a Azure Active Directory (Azure AD) szervezet tevékenységének teljes naplózási előzményeit, beleértve a rendszergazdai, a végfelhasználói és a szinkronizálási tevékenységet, használhatja a [Azure Active Directory biztonsági és tevékenységi jelentéseket](../reports-monitoring/overview-reports.md).

## <a name="determine-your-version-of-pim"></a>A PIM verziójának meghatározása

A 2019-es verziótól kezdődően a Privileged Identity Management Azure AD-szerepkörök részét egy új verzióra frissíti a rendszer, amely megfelel az Azure-erőforrás-szerepkörökkel kapcsolatos élményeknek. Ez további funkciókat hoz létre, valamint [a meglévő API módosításait](azure-ad-roles-features.md#api-changes)is. Az új verzió bevezetését követően a cikkben ismertetett eljárások a jelenleg használt Privileged Identity Management verziójától függenek. Az ebben a szakaszban ismertetett lépéseket követve meghatározhatja, hogy a Privileged Identity Management melyik verzióját kell megadnia. A Privileged Identity Management-verziójának megismerése után kiválaszthatja a jelen cikkben szereplő, az adott verziónak megfelelő eljárásokat.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/) egy olyan felhasználóval, aki tagja a [Kiemelt szerepkörű rendszergazda](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) szerepkörnek.
1. Nyissa meg **Azure ad Privileged Identity Management**. Ha az Áttekintés oldal tetején található egy szalagcím, kövesse a jelen cikk **új verzió** lapján található utasításokat. Ellenkező esetben kövesse az **előző verzió** lapon megjelenő utasításokat.

    ![Azure AD-szerepkörök új verziója](./media/pim-how-to-add-role-to-user/pim-new-version.png)

# <a name="new-versiontabnew"></a>[Új verzió](#tab/new)

Kövesse az alábbi lépéseket az Azure AD-szerepkörök naplózási előzményeinek megtekintéséhez.

## <a name="view-resource-audit-history"></a>Erőforrás-naplózási előzmények megtekintése

Az erőforrás-naplózás az Azure AD-szerepkörökhöz társított összes tevékenység megtekintését teszi lehetővé.

1. Nyissa meg **Azure ad Privileged Identity Management**.

1. Válassza az **Azure ad-szerepkörök**elemet.

1. Válassza az **erőforrás-naplózás**lehetőséget.

1. Az előzmények szűrése előre megadott dátummal vagy egyéni tartománnyal.

    ![Erőforrás-naplózási lista szűrőkkel](media/azure-pim-resource-rbac/rbac-resource-audit.png)

## <a name="view-my-audit"></a>Saját naplózás megtekintése

A saját audit lehetővé teszi a személyes szerepkör-tevékenységek megtekintését.

1. Nyissa meg **Azure ad Privileged Identity Management**.

1. Válassza az **Azure ad-szerepkörök**elemet.

1. Válassza ki azt az erőforrást, amelyre vonatkozóan meg szeretné tekinteni a naplózási előzményeket.

1. Válassza **a saját naplózás**lehetőséget.

1. Az előzmények szűrése előre megadott dátummal vagy egyéni tartománnyal.

    ![Az aktuális felhasználó naplózási listája](media/azure-pim-resource-rbac/my-audit-time.png)

# <a name="previous-versiontabprevious"></a>[Előző verzió](#tab/previous)

## <a name="view-audit-history"></a>Naplózási előzmények megtekintése

Kövesse az alábbi lépéseket az Azure AD-szerepkörök naplózási előzményeinek megtekintéséhez.

1. Jelentkezzen be [Azure Portalba](https://portal.azure.com/) egy olyan felhasználóval, aki tagja a [Kiemelt szerepkörű rendszergazda](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) szerepkörnek.

1. Nyissa meg **Azure ad Privileged Identity Management**.

1. Válassza az **Azure ad-szerepkörök**elemet.

1. Válassza ki a **címtárbeli szerepkörök naplózási előzményeit**.

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
    | Lejárati | Ha egy aktivált szerepkör lejár. Csak a jogosult szerepkör-hozzárendelésekre vonatkozik. |

1. A naplózási előzmények rendezéséhez kattintson az **idő**, a **művelet**és a **szerepkör** gombokra.

## <a name="filter-audit-history"></a>Naplózási előzmények szűrése

1. A naplózási Előzmények lap tetején kattintson a **szűrő** gombra.

    Megjelenik a **frissítési diagram paramétereinek** panelje.

1. Az **időtartomány**területen válassza ki az időtartományt.

1. A **szerepkörök**területen jelölje be a jelölőnégyzeteket a megtekinteni kívánt szerepkörök jelzéséhez.

    ![Diagram paramétereinek frissítése panel](media/pim-how-to-use-audit-log/update-chart-parameters.png)

1. A szűrt naplózási előzmények megtekintéséhez válassza a **kész** lehetőséget.

---

## <a name="next-steps"></a>Következő lépések

- [Az Azure Resource szerepköreinek tevékenység-és naplózási előzményeinek megtekintése Privileged Identity Management](azure-pim-resource-rbac.md)
