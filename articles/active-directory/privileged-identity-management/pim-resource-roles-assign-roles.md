---
title: Azure-beli erőforrás-szerepkörök kiosztása a PIM-Azure Active Directoryban | Microsoft Docs
description: Ismerje meg, hogyan rendelhet hozzá Azure-beli erőforrás-szerepköröket Azure AD Privileged Identity Management (PIM) szolgáltatáshoz.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e2850f17c96ef031f9e1b8e11558ab369e4175b0
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804285"
---
# <a name="assign-azure-resource-roles-in-pim"></a>Azure-beli erőforrás-szerepkörök kiosztása a PIM-ben

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) felügyelheti a beépített Azure-erőforrás-szerepköröket, valamint az egyéni szerepköröket, beleértve a (de nem kizárólag):

- Tulajdonos
- Felhasználói hozzáférés adminisztrátora
- Közreműködő
- Biztonsági rendszergazda
- Security Manager és egyéb

> [!NOTE]
> A tulajdonosi vagy felhasználói hozzáférés-rendszergazdai szerepkörökhöz hozzárendelt felhasználók vagy csoportok tagjai, valamint az előfizetés-kezelést az Azure AD-ben engedélyező globális rendszergazdák az erőforrás-rendszergazdák. Ezek a rendszergazdák szerepköröket rendelhetnek hozzá, konfigurálhatják a szerepkör beállításait, és áttekinthetik a hozzáférést az Azure-erőforrások PIM használatával. Ez a fiók nem rendelkezik jogosultsággal a PIM-erőforrások kezeléséhez, ha a felhasználónak nincs erőforrás-rendszergazdai szerepköre. Megtekintheti az [Azure-erőforrások beépített szerepköreinek](../../role-based-access-control/built-in-roles.md)listáját.

## <a name="assign-a-role"></a>Szerepkör kiosztása

Kövesse az alábbi lépéseket, hogy a felhasználók jogosultak legyenek az Azure-erőforrások szerepkörre.

1. Jelentkezzen be [Azure Portalba](https://portal.azure.com/) egy olyan felhasználóval, aki tagja a [Kiemelt szerepkörű rendszergazda](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) szerepkörnek.

    További információ a PIM felügyeletének másik rendszergazdai hozzáférésének megadásáról: [hozzáférés biztosítása más rendszergazdák számára a PIM kezeléséhez](pim-how-to-give-access-to-pim.md).

1. Nyissa meg **Azure ad Privileged Identity Management**.

    Ha még nem indította el a PIM-t a Azure Portalban, ugorjon a [PIM használatának megkezdése](pim-getting-started.md)lehetőségre.

1. Kattintson az **Azure-erőforrások**elemre.

1. Az **Erőforrás-szűrő** használatával szűrheti a felügyelt erőforrások listáját.

    ![A felügyelni kívánt Azure-erőforrások listája](./media/pim-resource-roles-assign-roles/resources-list.png)

1. Kattintson a kezelni kívánt erőforrásra, például egy előfizetésre vagy egy felügyeleti csoportra.

1. A kezelés alatt kattintson a **szerepkörök** elemre az Azure-erőforrások szerepköreinek megjelenítéséhez.

    ![Azure-erőforrások szerepkörei](./media/pim-resource-roles-assign-roles/resources-roles.png)

1. Kattintson a **tag hozzáadása** elemre az új hozzárendelés ablaktábla megnyitásához.

1. A szerepkör kiválasztása ablaktábla megnyitásához kattintson **a szerepkör kiválasztása** elemre.

    ![Új hozzárendelés ablaktábla](./media/pim-resource-roles-assign-roles/resources-select-role.png)

1. Kattintson a hozzárendelni kívánt szerepkörre, majd a **kiválasztás**elemre.

    Megnyílik a tag vagy csoport kiválasztása panel.

1. Kattintson a szerepkörhöz hozzárendelni kívánt tagra vagy csoportra, majd a **kiválasztás**elemre.

    ![Tag vagy csoport ablaktábla kiválasztása](./media/pim-resource-roles-assign-roles/resources-select-member-or-group.png)

    Megnyílik a tagsági beállítások panel.

1. A **hozzárendelés típusa** listában válassza a **jogosult** vagy az **aktív**lehetőséget.

    ![Tagságok beállításai panel](./media/pim-resource-roles-assign-roles/resources-membership-settings-type.png)

    A PIM for Azure-erőforrások két különböző hozzárendelési típust biztosítanak:

    - A **jogosult** hozzárendelésekhez a szerepkör tagjának kell lennie a szerepkör használatára vonatkozó művelet végrehajtásához. A műveletek tartalmazhatják a többtényezős hitelesítés (MFA) ellenőrzését, üzleti indoklást biztosítanak, vagy a kijelölt jóváhagyók jóváhagyását kérik.

    - Az **aktív** hozzárendelésekhez nem szükséges, hogy a tag bármilyen műveletet hajtson végre a szerepkör használatához. Az aktívként hozzárendelt tagok rendelkeznek a szerepkörhöz tartozó jogosultságokkal.

1. Ha a hozzárendelésnek állandónak kell lennie (tartósan jogosult vagy véglegesen kiosztott), jelölje be a **véglegesen** jelölőnégyzetet.

    A szerepkör beállításaitól függően előfordulhat, hogy a jelölőnégyzet nem jelenik meg, vagy lehet, hogy nem módosítható.

1. Egy adott hozzárendelés időtartamának megadásához törölje a jelölőnégyzet jelölését, és módosítsa a kezdő és/vagy befejező dátum és idő mezőket.

    ![Tagsági beállítások – dátum és idő](./media/pim-resource-roles-assign-roles/resources-membership-settings-date.png)

1. Ha végzett, kattintson a **Kész** gombra.

    ![Új hozzárendelés – Hozzáadás](./media/pim-resource-roles-assign-roles/resources-new-assignment-add.png)

1. Az új szerepkör-hozzárendelés létrehozásához kattintson a **Hozzáadás**gombra. Megjelenik az állapot értesítése.

    ![Új hozzárendelés – értesítés](./media/pim-resource-roles-assign-roles/resources-new-assignment-notification.png)

## <a name="update-or-remove-an-existing-role-assignment"></a>Meglévő szerepkör-hozzárendelés frissítése vagy eltávolítása

A meglévő szerepkör-hozzárendelések frissítéséhez vagy eltávolításához kövesse az alábbi lépéseket.

1. Nyissa meg **Azure ad Privileged Identity Management**.

1. Kattintson az **Azure-erőforrások**elemre.

1. Kattintson a kezelni kívánt erőforrásra, például egy előfizetésre vagy egy felügyeleti csoportra.

1. A kezelés alatt kattintson a **szerepkörök** elemre az Azure-erőforrások szerepköreinek megjelenítéséhez.

    ![Azure-erőforrás szerepkörei – szerepkör kiválasztása](./media/pim-resource-roles-assign-roles/resources-update-select-role.png)

1. Kattintson a frissíteni vagy eltávolítani kívánt szerepkörre.

1. Keresse meg a szerepkör-hozzárendelést a **jogosult szerepkörök** vagy az **aktív szerepkörök** lapon.

    ![Szerepkör-hozzárendelés frissítése vagy eltávolítása](./media/pim-resource-roles-assign-roles/resources-update-remove.png)

1. A szerepkör-hozzárendelés frissítéséhez vagy eltávolításához kattintson a **frissítés** vagy az **Eltávolítás** elemre.

    A szerepkör-hozzárendelés kiterjesztésével kapcsolatos információkért lásd: [Azure-beli erőforrás-szerepkörök kiterjesztése vagy megújítása a PIM-ben](pim-resource-roles-renew-extend.md).

## <a name="next-steps"></a>További lépések

- [Azure-erőforrás szerepköreinek kiterjesztése vagy megújítása a PIM-ben](pim-resource-roles-renew-extend.md)
- [Az Azure erőforrás-szerepkör beállításainak konfigurálása a PIM-ben](pim-resource-roles-configure-role-settings.md)
- [Azure AD-szerepkörök kiosztása a PIM-ben](pim-how-to-add-role-to-user.md)
