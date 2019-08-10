---
title: Egyéni Azure AD-szerepkör kiosztása Privileged Identity Managementban (PIM) | Microsoft Docs
description: Egyéni Azure AD-szerepkör hozzárendelése hozzárendelési Privileged Identity Managementhoz (PIM)
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/06/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7a3f6eb815677133f3d7fe6ce07d6abf23db1f04
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/10/2019
ms.locfileid: "68947337"
---
# <a name="assign-an-azure-ad-custom-role-in-privileged-identity-management"></a>Egyéni Azure AD-szerepkör kiosztása Privileged Identity Management

Ebből a cikkből megtudhatja, hogyan használhatja a Privileged Identity Management (PIM) alkalmazást az alkalmazások kezeléséhez létrehozott egyéni szerepkörökhöz az Azure Active Directory (Azure AD) felügyeleti felhasználói felületén.

- További információ az Azure AD-ben az alkalmazások felügyeletének delegálására szolgáló egyéni szerepkörök létrehozásáról: [Azure Active Directory (előzetes verzió) – egyéni rendszergazdai szerepkörök](../users-groups-roles/roles-custom-overview.md).
- Ha még nem használta Privileged Identity Management, további információt a [PIM használatának első](pim-getting-started.md)lépéseiben olvashat.
- További információ arról, hogyan lehet egy másik rendszergazdai hozzáférést biztosítani a Privileged Identity Management kezeléséhez: [hozzáférés biztosítása más rendszergazdák számára a PIM kezeléséhez](pim-how-to-give-access-to-pim.md).

> [!NOTE]
> Az Azure AD egyéni szerepkörei nincsenek integrálva az előzetes verzióban a beépített címtárbeli szerepkörökbe. Miután a képesség általánosan elérhetővé válik, a szerepkör-kezelés a beépített szerepkörök felületén történik.

## <a name="assign-a-role"></a>Szerepkör kiosztása

A Privileged Identity Management a Azure Active Directory (Azure AD) alkalmazás-kezelésben létrehozható egyéni szerepköröket kezelheti.  A következő lépésekkel jogosult hozzárendelést végezhet egy egyéni címtárbeli szerepkörhöz.

1. Jelentkezzen be [Privileged Identity Managementra](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart) a Azure Portal egy olyan felhasználói fiókkal, amely hozzá van rendelve a Kiemelt szerepkörű rendszergazda szerepkörhöz.
1. Válassza az **Egyéni Azure ad-szerepkörök (előzetes verzió)** lehetőséget.

    ![A jogosult szerepkör-hozzárendelések megtekintéséhez válassza az Azure AD egyéni szerepkörök előzetes verzióját.](./media/azure-ad-custom-roles-assign/view-custom.png)

1. Válassza a **szerepkörök** lehetőséget az Azure ad-alkalmazások egyéni szerepköreinek megjelenítéséhez.

    ![Szerepkörök kiválasztása: a jogosult szerepkör-hozzárendelések listája](./media/azure-ad-custom-roles-assign/view-roles.png)

1. Válassza a **tag hozzáadása** elemet a hozzárendelés lap megnyitásához.
1. Ha korlátozni szeretné a szerepkör-hozzárendelés hatókörét egyetlen alkalmazásra, válassza a **hatókör** lehetőséget az alkalmazás hatókörének megadásához.

    ![jogosult szerepkör-hozzárendelések hatókörének korlátozása az Azure AD-ben](./media/azure-ad-custom-roles-assign/set-scope.png)

1. Válassza a **szerepkör kiválasztása** lehetőséget a **szerepkör kiválasztása** lista megnyitásához.

    ![Válassza ki a felhasználóhoz rendelendő jogosult szerepkört](./media/azure-ad-custom-roles-assign/select-role.png)

1. Válassza ki a hozzárendelni kívánt szerepkört, majd kattintson a **kiválasztás**gombra. Megnyílik a **tag kiválasztása** lista.

    ![Válassza ki azt a felhasználót, akihez a szerepkört hozzárendeli](./media/azure-ad-custom-roles-assign/select-member.png)

1. Válassza ki a szerepkörhöz hozzárendelni kívánt felhasználót, majd kattintson a **kiválasztás**gombra. Megnyílik a **tagsági beállítások** listája.

    ![A szerepkör-hozzárendelés típusának beállítása a jogosult vagy az aktív értékre](./media/azure-ad-custom-roles-assign/membership-settings.png)

1. A **tagsági beállítások** lapon válassza a **jogosult** vagy az **aktív**lehetőséget:

    - A **jogosult** hozzárendelésekhez szükség van a szerepkörhöz hozzárendelt felhasználóra, mielőtt használni tudná a szerepkört. A műveletek lehetnek például a multi-Factor Authentication-ellenőrzések átadása, az üzleti indoklás vagy a kijelölt jóváhagyók jóváhagyásának kérése.
    - Az **aktív** hozzárendelésekhez nincs szükség a hozzárendelt felhasználóra a szerepkör használatára vonatkozó művelet végrehajtásához. Az aktív felhasználók mindig rendelkeznek a szerepkörhöz hozzárendelt jogosultságokkal.

1. Ha az **állandó** jelölőnégyzet be van kapcsolva és elérhető (a szerepkör beállításaitól függően), megadhatja, hogy a hozzárendelés állandó-e. Jelölje be a jelölőnégyzetet, hogy a hozzárendelés tartósan jogosult vagy véglegesen hozzá legyen rendelve. Törölje a jelölőnégyzet jelölését a hozzárendelés időtartamának megadásához.
1. Az új szerepkör-hozzárendelés létrehozásához kattintson a **Mentés** , majd a **Hozzáadás**elemre. Megjelenik egy értesítés a hozzárendelési folyamat állapotáról.

A szerepkör-hozzárendelés ellenőrzéséhez nyisson meg egy nyitott szerepkört, válassza a **hozzárendelések** > hozzárendelés lehetőséget, és ellenőrizze, hogy a szerepkör-hozzárendelés megfelelően van-e azonosítva a jogosult vagy az aktív módon.

 ![Ellenőrizze, hogy a szerepkör-hozzárendelés jogosultként vagy aktívként jelenik-e meg](./media/azure-ad-custom-roles-assign/verify-assignments.png)

## <a name="next-steps"></a>További lépések

- [Egyéni Azure AD-szerepkör aktiválása](azure-ad-custom-roles-assign.md)
- [Egyéni Azure AD-szerepkör-hozzárendelés eltávolítása vagy frissítése](azure-ad-custom-roles-update-remove.md)
- [Egyéni Azure AD-szerepkör-hozzárendelés konfigurálása](azure-ad-custom-roles-configure.md)
- [Szerepkör-definíciók az Azure AD-ben](../users-groups-roles/directory-assign-admin-roles.md)
