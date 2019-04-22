---
title: Azure-erőforrás szerepkörök hozzárendelése az Azure Active Directory - a PIM |} A Microsoft Docs
description: Ismerje meg, hogyan lehet Azure-erőforrás szerepkörök hozzárendelése az Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 610aeec9e4c40d0aad0c28f02697e2cf01edbe4a
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59493991"
---
# <a name="assign-azure-resource-roles-in-pim"></a>Rendelje hozzá a PIM az Azure-erőforrásszerepkörök

Az Azure Active Directory (Azure AD) Privileged Identity Management (PIM) képes kezelni, a beépített Azure-erőforrások szerepköreihez, valamint a egyéni szerepköröket, beleértve többek között a (,):

- Tulajdonos
- Felhasználói hozzáférés rendszergazdája
- Közreműködő
- Biztonsági rendszergazda
- Biztonságkezelő és egyéb

> [!NOTE]
> Felhasználók vagy a tulajdonos vagy a felhasználói hozzáférés rendszergazdája szerepkör, és előfizetés-kezelés engedélyezése az Azure AD globális rendszergazdák csoport tagjainak olyan erőforrás-rendszergazdák. Ezek a rendszergazdák előfordulhat, hogy szerepköröket, szerepkör-beállítások konfigurálása és a PIM használata az Azure-erőforrások hozzáférésének. A lista megtekintése [beépített szerepkörök az Azure-erőforrások](../../role-based-access-control/built-in-roles.md).

## <a name="assign-a-role"></a>Szerepkör hozzárendelése

Kövesse az alábbi lépéseket, hogy egy felhasználó jogosult az Azure-erőforrás szerepet.

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com/) egy felhasználóval, amely tagja a [kiemelt szerepkörű rendszergazda](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) szerepkör.

    Egy másik rendszergazdai hozzáférést a PIM kezelése kapcsolatos információkért lásd: [hozzáférés biztosítása más rendszergazdák számára, miközben a PIM kezelése](pim-how-to-give-access-to-pim.md).

1. Nyissa meg **az Azure AD Privileged Identity Management**.

    Ha a PIM még nem indult el az Azure Portalon még, ugorjon [használatához a PIM](pim-getting-started.md).

1. Kattintson a **Azure-erőforrások**.

1. Használja a **Erőforrásszűrő** a felügyelt erőforrások szűréséhez.

    ![Azure-erőforrások kezelése listája](./media/pim-resource-roles-assign-roles/resources-list.png)

1. Kattintson az erőforrás szeretne felügyelni, mint például egy előfizetést vagy felügyeleti csoportot.

1. Kattintson a kezelés, **szerepkörök** szerepkörök az Azure-erőforrások listájának megtekintéséhez.

    ![Azure-erőforrások szerepkörök](./media/pim-resource-roles-assign-roles/resources-roles.png)

1. Kattintson a **tag hozzáadása** az új hozzárendelés panel megnyitásához.

1. Kattintson a **Szerepkörválasztás** válassza a szerepkör ablaktábla megnyitásához.

    ![Új hozzárendelés ablaktábla](./media/pim-resource-roles-assign-roles/resources-select-role.png)

1. Válasszon egy szerepkört szeretne hozzárendelni, és kattintson a **kiválasztása**.

    Válassza a tag vagy a csoport ablaktábla megnyitása.

1. Kattintson a tag vagy csoportot, amelyhez hozzá szeretné rendelni a szerepkört, és kattintson a **kiválasztása**.

    ![Válassza ki egy tagot vagy csoport ablaktábla](./media/pim-resource-roles-assign-roles/resources-select-member-or-group.png)

    A tagsági beállítások panel nyílik meg.

1. Az a **hozzárendelés-típus** listáról válassza ki **jogosult** vagy **aktív**.

    ![Tagsági beállítások panel](./media/pim-resource-roles-assign-roles/resources-membership-settings-type.png)

    A PIM használata Azure-erőforrások két különböző típusok biztosítja:

    - **Jogosult** hozzárendelések szükséges a szerepkör tagjai egy műveletet a szerepkör használatához. Műveletek tartalmazhat, egy üzleti indoklásának megadása, vagy jóváhagyási kérése a kijelölt jóváhagyókat többtényezős hitelesítés (MFA)-ellenőrzés elvégzése.

    - **Aktív** hozzárendelések nem igénylik a tagot a szerepkör használatához bármely művelet elvégzésére. A tagok hozzárendelve aktívként mindig a szerepkörhöz rendelt jogosultság.

1. Ha a hozzárendelés állandó kell lennie (állandó jogosultság vagy állandó jelleggel hozzárendelt), válassza ki a **véglegesen** jelölőnégyzetet.

    Attól függően, a beállításokat a jelölőnégyzet nem jelenik meg, vagy lehet, hogy unmodifiable.

1. Adjon meg egy adott hozzárendelés időtartama, törölje a jelet a jelölőnégyzetből, és a kezdési és/vagy a záró dátum és idő mezők módosításához.

    ![Tagsági beállítások – dátum és idő](./media/pim-resource-roles-assign-roles/resources-membership-settings-date.png)

1. Ha végzett, kattintson a **Kész** gombra.

    ![-Új-hozzárendelés hozzáadása](./media/pim-resource-roles-assign-roles/resources-new-assignment-add.png)

1. Az új szerepkör-hozzárendelés létrehozásához kattintson a **Hozzáadás**. Megjelenik egy értesítés állapotát.

    ![Új hozzárendelés – értesítés](./media/pim-resource-roles-assign-roles/resources-new-assignment-notification.png)

## <a name="update-or-remove-an-existing-role-assignment"></a>Frissítse vagy egy meglévő szerepkör-hozzárendelés eltávolítása

Kövesse az alábbi lépéseket, frissítéséhez vagy egy meglévő szerepkör-hozzárendelés eltávolításához.

1. Nyissa meg **az Azure AD Privileged Identity Management**.

1. Kattintson a **Azure-erőforrások**.

1. Kattintson az erőforrás szeretne felügyelni, mint például egy előfizetést vagy felügyeleti csoportot.

1. Kattintson a kezelés, **szerepkörök** szerepkörök az Azure-erőforrások listájának megtekintéséhez.

    ![Azure-erőforrások szerepköreihez – szerepkör kiválasztása](./media/pim-resource-roles-assign-roles/resources-update-select-role.png)

1. Kattintson a frissíteni vagy eltávolítani kívánt szerepkört.

1. A szerepkör-hozzárendelés keresése a **jogosult szerepkörök** vagy **aktív szerepkörök** lapokon.

    ![Frissítés vagy a szerepkör-hozzárendelés eltávolítása](./media/pim-resource-roles-assign-roles/resources-update-remove.png)

1. Kattintson a **frissítése** vagy **eltávolítása** frissítéséhez vagy a szerepkör-hozzárendelés eltávolításához.

    Egy szerepkör-hozzárendelés kiterjesztésére vonatkozó további információkért lásd: [bővítése vagy újítanak meg az Azure-erőforrásszerepkörök a PIM](pim-resource-roles-renew-extend.md).

## <a name="next-steps"></a>További lépések

- [A PIM az Azure-erőforrás szerepkörök meghosszabbítása vagy megújítása](pim-resource-roles-renew-extend.md)
- [A PIM Azure szerepkör-beállítások konfigurálása](pim-resource-roles-configure-role-settings.md)
- [Az Azure AD PIM-szerepkörök hozzárendelése](pim-how-to-add-role-to-user.md)
