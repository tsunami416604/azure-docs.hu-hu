---
title: Azure-erőforrásszerepkörök hozzárendelése a kiemelt identitáskezelésben – Azure Active Directory | Microsoft dokumentumok
description: Ismerje meg, hogyan rendelhet azure-erőforrás-szerepköröket az Azure AD kiemelt identitáskezelés (PIM) között.
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
ms.date: 10/23/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 34051a31c6ccf69356f330d7c5ecb009f760857a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266558"
---
# <a name="assign-azure-resource-roles-in-privileged-identity-management"></a>Azure-erőforrás-szerepkörök hozzárendelése a kiemelt identitáskezelésben

Az Azure Active Directory (Azure AD) kiemelt identitáskezelése (PIM) kezelheti a beépített Azure-erőforrás-szerepköröket, valamint az egyéni szerepköröket, beleértve (de nem kizárólagosan):

- Tulajdonos
- Felhasználói hozzáférés rendszergazdája
- Közreműködő
- Biztonsági rendszergazda
- Biztonsági vezető

> [!NOTE]
> A tulajdonosi vagy felhasználói hozzáférés-rendszergazdai előfizetési szerepkörökhöz rendelt felhasználók vagy csoporttagok, valamint az Azure AD-ben előfizetés-kezelést engedélyező Azure AD-rendszergazdák alapértelmezés szerint erőforrás-rendszergazdai engedélyekkel rendelkeznek. Ezek a rendszergazdák szerepköröket rendelhetnek hozzá, konfigurálhatnak szerepkör-beállításokat, és áttekinthetik a hozzáférést az Azure-erőforrások kiemelt identitáskezelés használatával. A felhasználó nem kezelheti az erőforrások kiemelt identitáskezelését erőforrás-rendszergazdai engedélyek nélkül. Tekintse meg az [Azure-erőforrások beépített szerepköreinek](../../role-based-access-control/built-in-roles.md)listáját.

## <a name="assign-a-role"></a>Szerepkör hozzárendelése

Kövesse az alábbi lépéseket, hogy a felhasználó jogosult egy Azure-erőforrás szerepkör.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) egy olyan felhasználóval, aki a [kiemelt szerepkör-rendszergazdai](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) szerepkör tagja.

    Arról, hogy miként adhat hozzáférést egy másik rendszergazdai hozzáférésnek a Kiemelt identitáskezelés kezeléséhez, olvassa el [a Hozzáférés megadása más rendszergazdáknak a Kiemelt identitáskezelés kezeléséhez című témakört.](pim-how-to-give-access-to-pim.md)

1. Nyissa meg **az Azure AD kiemelt identitáskezelés .**

1. Válassza az **Azure-erőforrások lehetőséget.**

1. Az **Erőforrás szűrősegítségével** szűrheti a felügyelt erőforrások listáját.

    ![A kezelned vehett Azure-erőforrások listája](./media/pim-resource-roles-assign-roles/resources-list.png)

1. Válassza ki a kezelni kívánt erőforrást, például egy előfizetést vagy felügyeleti csoportot.

1. A Kezelés csoportban válassza a **Szerepkörök** lehetőséget az Azure-erőforrások szerepköreinek listájának megtekintéséhez.

    ![Azure-erőforrások szerepkörei](./media/pim-resource-roles-assign-roles/resources-roles.png)

1. Válassza **a Tag hozzáadása** lehetőséget az Új hozzárendelés ablaktábla megnyitásához.

1. Válassza **a Szerepkör kiválasztása** lehetőséget a Szerepkör kiválasztása ablaktábla megnyitásához.

    ![Új hozzárendelés ablaktábla](./media/pim-resource-roles-assign-roles/resources-select-role.png)

1. Jelölje ki a hozzárendelni kívánt szerepkört, majd kattintson **a Kijelölés gombra.**

    Megnyílik a Tag vagy csoport kijelölése ablaktábla.

1. Jelölje ki a szerepkörhöz rendelni kívánt tagot vagy csoportot, majd kattintson a **Kijelölés gombra.**

    ![Tag vagy csoportablak tábla kijelölése](./media/pim-resource-roles-assign-roles/resources-select-member-or-group.png)

    Megnyílik a Tagsági beállítások ablaktábla.

1. A **Hozzárendeléstípus** listában válassza a **Jogosult** vagy **az Aktív**lehetőséget.

    ![Tagsági beállítások ablaktábla](./media/pim-resource-roles-assign-roles/resources-membership-settings-type.png)

    Az Azure-erőforrások kiemelt identitáskezelése két különböző hozzárendeléstípust biztosít:

    - **A jogosult** hozzárendelések megkövetelik a szerepkör tagjától, hogy a szerepkör használatához műveletet hajtson végre. A műveletek közé tartozhat a többtényezős hitelesítés (MFA) ellenőrzése, az üzleti indoklás megadása vagy a kijelölt jóváhagyók jóváhagyásának kérése.

    - **Az aktív** hozzárendelések nem igénylik a tagtól a szerepkör használatához szükséges műveleteket. Az aktívként hozzárendelt tagok mindig a szerepkörhöz rendelt jogosultságokkal rendelkeznek.

1. Ha a hozzárendelésnek állandónak (véglegesen jogosultnak vagy véglegesen hozzárendeltnek) kell lennie, jelölje be a **Végleges jelölőnégyzetet.**

    A szerepkör-beállításoktól függően előfordulhat, hogy a jelölőnégyzet nem jelenik meg, vagy nem módosítható.

1. Adott hozzárendelési időtartam megadásához törölje a jelet a jelölőnégyzetből, és módosítsa a kezdési és/vagy záró dátum- és időmezőket.

    ![Tagsági beállítások - dátum és idő](./media/pim-resource-roles-assign-roles/resources-membership-settings-date.png)

1. Ha végzett, válassza a **Kész gombot.**

    ![Új hozzárendelés - Hozzáadás](./media/pim-resource-roles-assign-roles/resources-new-assignment-add.png)

1. Az új szerepkör-hozzárendelés létrehozásához válassza a **Hozzáadás**lehetőséget. Megjelenik az állapotról szóló értesítés.

    ![Új hozzárendelés - Értesítés](./media/pim-resource-roles-assign-roles/resources-new-assignment-notification.png)

## <a name="update-or-remove-an-existing-role-assignment"></a>Meglévő szerepkör-hozzárendelés frissítése vagy eltávolítása

Meglévő szerepkör-hozzárendelés frissítéséhez vagy eltávolításához kövesse az alábbi lépéseket.

1. Nyissa meg **az Azure AD kiemelt identitáskezelés .**

1. Válassza az **Azure-erőforrások lehetőséget.**

1. Válassza ki a kezelni kívánt erőforrást, például egy előfizetést vagy felügyeleti csoportot.

1. A Kezelés csoportban válassza a **Szerepkörök** lehetőséget az Azure-erőforrások szerepköreinek listájának megtekintéséhez.

    ![Azure-erőforrás-szerepkörök – Szerepkör kiválasztása](./media/pim-resource-roles-assign-roles/resources-update-select-role.png)

1. Jelölje ki a frissíteni vagy eltávolítani kívánt szerepkört.

1. Keresse meg a szerepkör-hozzárendelést a **Jogosult szerepkörök** vagy **az Aktív szerepkörök** lapon.

    ![Szerepkör-hozzárendelés frissítése vagy eltávolítása](./media/pim-resource-roles-assign-roles/resources-update-remove.png)

1. A **szerepkör-hozzárendelés** frissítéséhez vagy eltávolításához válassza a Frissítés vagy az **Eltávolítás** lehetőséget.

    A szerepkör-hozzárendelés ek bővítéséről az [Azure-erőforrásszerepkörök kiterjesztése vagy megújítása a Kiemelt identitáskezelés ben](pim-resource-roles-renew-extend.md)című témakörben talál.

## <a name="next-steps"></a>További lépések

- [Azure-erőforrás-szerepkörök bővítése vagy megújítása a kiemelt identitáskezelésben](pim-resource-roles-renew-extend.md)
- [Az Azure-erőforrásszerepkör-beállítások konfigurálása a Kiemelt identitáskezelés ben](pim-resource-roles-configure-role-settings.md)
- [Azure AD-szerepkörök hozzárendelése a kiemelt identitáskezelésben](pim-how-to-add-role-to-user.md)
