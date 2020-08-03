---
title: Jogosult tulajdonosok és tagok kiosztása Kiemelt jogosultságú hozzáférési csoportokhoz – Azure Active Directory
description: Megtudhatja, hogyan rendelhet hozzá jogosult tulajdonosokat vagy szerepkörhöz hozzárendelhető csoport tagjait Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 07/27/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e1dcc98366e37455f462fe1a0740aa161201912f
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2020
ms.locfileid: "87506224"
---
# <a name="assign-eligibility-for-a-privileged-access-group-preview-in-privileged-identity-management"></a>Jogosultsági szintű hozzáférési csoport (előzetes verzió) kiosztása Privileged Identity Management

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) segítségével kezelheti az Azure AD-ben az emelt szintű hozzáférési csoportok hozzárendeléseinek jogosultságát és aktiválását. Jogosultságot rendelhet a csoport tagjaihoz vagy tulajdonosainak.

>[!NOTE]
>Minden olyan felhasználónak, aki jogosultsági szintű hozzáférési csoport tagságához vagy tulajdonosához jogosult, rendelkeznie kell egy prémium szintű Azure AD P2 licenccel. További információ: [a Privileged Identity Management használatára vonatkozó licencfeltételek](subscription-requirements.md).

## <a name="assign-an-owner-or-member-of-a-group"></a>Tulajdonos vagy csoport tagjainak kiosztása

Kövesse az alábbi lépéseket, hogy a felhasználók jogosultak legyenek egy emelt szintű hozzáférési csoport tagjának vagy tulajdonosának a használatára.

1. Jelentkezzen be [Privileged Identity Managementra](https://portal.azure.com/) a Azure Portal a [Kiemelt szerepkörű rendszergazda](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) szerepkörrel rendelkező jogosultságokkal.

    További információ a Privileged Identity Management kezeléséhez szükséges további rendszergazdai hozzáférésről: [hozzáférés biztosítása más rendszergazdák számára a Privileged Identity Management kezeléséhez](pim-how-to-give-access-to-pim.md).

1. Nyissa meg **Azure ad Privileged Identity Management**.

1. Válassza a **privilegizált hozzáférés (előzetes verzió)** lehetőséget.

1. Megkeresheti a csoport nevét, és a **csoport típusát** használva szűrheti a listát, és kiválaszthatja a kezelni kívánt csoportot.

    ![A felügyelni kívánt privilegizált hozzáférési csoportok listája](./media/groups-assign-member-owner/privileged-access-list.png)

1. A **kezelés**területen válassza a **hozzárendelések**lehetőséget.

1. Válassza a **hozzárendelések hozzáadása**lehetőséget.

    ![Új hozzárendelés ablaktábla](./media/groups-assign-member-owner/groups-add-assignment.png)

1. Válassza ki azokat a tagokat vagy tulajdonosokat, akik jogosultak legyenek az emelt szintű hozzáférési csoportra.

    ![Tag vagy csoport ablaktábla kiválasztása](./media/groups-assign-member-owner/add-assignments.png)

1. Válassza a **tovább** lehetőséget a tagság vagy a tulajdonos időtartamának megadásához.

    ![Tag vagy csoport ablaktábla kiválasztása](./media/groups-assign-member-owner/assignment-duration.png)

1. A **hozzárendelés típusa** listában válassza a **jogosult** vagy az **aktív**lehetőséget. A Kiemelt jogosultságú hozzáférési csoportok két különböző hozzárendelési típust biztosítanak:

    - A **jogosult** hozzárendelésekhez a szerepkör tagjának kell lennie a szerepkör használatára vonatkozó művelet végrehajtásához. A műveletek tartalmazhatják a többtényezős hitelesítés (MFA) ellenőrzését, üzleti indoklást biztosítanak, vagy a kijelölt jóváhagyók jóváhagyását kérik.

    - Az **aktív** hozzárendelésekhez nem szükséges, hogy a tag bármilyen műveletet hajtson végre a szerepkör használatához. Az aktívként hozzárendelt tagok rendelkeznek a szerepkörhöz tartozó jogosultságokkal.

1. Ha a hozzárendelésnek állandónak kell lennie (tartósan jogosult vagy véglegesen kiosztott), jelölje be a **véglegesen** jelölőnégyzetet. A szervezet beállításaitól függően előfordulhat, hogy a jelölőnégyzet nem jelenik meg, vagy előfordulhat, hogy nem szerkeszthető.

1. Ha elkészült, válassza a **hozzárendelés**lehetőséget.

1. Az új szerepkör-hozzárendelés létrehozásához válassza a **Hozzáadás**lehetőséget. Megjelenik az állapot értesítése.

    ![Új hozzárendelés – értesítés](./media/groups-assign-member-owner/groups-assignment-notification.png)

## <a name="update-or-remove-an-existing-role-assignment"></a>Meglévő szerepkör-hozzárendelés frissítése vagy eltávolítása

A meglévő szerepkör-hozzárendelések frissítéséhez vagy eltávolításához kövesse az alábbi lépéseket.

1. Nyissa meg **Azure ad Privileged Identity Management**.

1. Válassza a **privilegizált hozzáférés (előzetes verzió)** lehetőséget.

1. Megkeresheti a csoport nevét, és a **csoport típusát** használva szűrheti a listát, és kiválaszthatja a kezelni kívánt csoportot.

    ![A felügyelni kívánt privilegizált hozzáférési csoportok listája](./media/groups-assign-member-owner/privileged-access-list.png)

1. A **kezelés**területen válassza a **hozzárendelések**lehetőséget.

1. Válassza ki a frissíteni vagy eltávolítani kívánt szerepkört.

1. Keresse meg a szerepkör-hozzárendelést a **jogosult szerepkörök** vagy az **aktív szerepkörök** lapon.

    ![Szerepkör-hozzárendelés frissítése vagy eltávolítása](./media/groups-assign-member-owner/groups-add-assignment.png)

1. A szerepkör-hozzárendelés frissítéséhez vagy eltávolításához válassza a **frissítés** vagy az **Eltávolítás** lehetőséget.

    További információ a szerepkör-hozzárendelés kibővítéséről: [Azure-beli erőforrás-szerepkörök kiterjesztése vagy megújítása Privileged Identity Managementban](pim-resource-roles-renew-extend.md).

## <a name="next-steps"></a>További lépések

- [Azure-beli erőforrás-szerepkörök kiterjesztése vagy megújítása Privileged Identity Management](pim-resource-roles-renew-extend.md)
- [Az Azure erőforrás-szerepkör beállításainak konfigurálása Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
- [Azure AD-szerepkörök kiosztása Privileged Identity Management](pim-how-to-add-role-to-user.md)
