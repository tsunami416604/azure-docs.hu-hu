---
title: Azure AD egyéni szerepkör hozzárendelése – Kiemelt identitáskezelés (PIM)
description: Azure AD egyéni szerepkör hozzárendelése emelt szintű identitáskezeléshez (PIM)
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/06/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0303d37ef5bbbf266feb5456b0bc224ce272ee13
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77499249"
---
# <a name="assign-an-azure-ad-custom-role-in-privileged-identity-management"></a>Azure AD egyéni szerepkör hozzárendelése a kiemelt identitáskezelésben

Ez a cikk bemutatja, hogyan használhatja a kiemelt identitáskezelés (PIM) just-in-time és időhöz kötött hozzárendelés egyéni szerepkörök höz létrehozott alkalmazások az Azure Active Directory (Azure AD) felügyeleti élményt hozhat létre.

- Az Azure AD-ben az alkalmazáskezelés delegálására szolgáló egyéni szerepkörök létrehozásáról az [Egyéni rendszergazdai szerepkörök az Azure Active Directoryban (előzetes verzió)](../users-groups-roles/roles-custom-overview.md)című témakörben talál további információt.
- Ha még nem használta a Kiemelt identitáskezelés szolgáltatást, további információt a [Kiemelt identitáskezelés használatának megkezdése](pim-getting-started.md)című részen talál.
- Arról, hogy miként adhat hozzáférést egy másik rendszergazdai hozzáférésnek a Kiemelt identitáskezelés kezeléséhez, olvassa el [a Hozzáférés megadása más rendszergazdáknak a Kiemelt identitáskezelés kezeléséhez című témakört.](pim-how-to-give-access-to-pim.md)

> [!NOTE]
> Az Azure AD egyéni szerepkörök nincsenek integrálva a beépített címtárszerepkörök előzetes verzió során. Ha a képesség általánosan elérhetővé válik, a szerepkör-kezelés a beépített szerepkörök ben kerül sor. Ha a következő szalagcím jelenik meg, ezeket a szerepköröket [a beépített szerepkörök ben](pim-how-to-activate-role.md) kell kezelni, és ez a cikk nem vonatkozik:
>
> [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

## <a name="assign-a-role"></a>Szerepkör hozzárendelése

A kiemelt identitáskezelés kezelheti az Azure Active Directory (Azure AD) alkalmazáskezelésben létrehozható egyéni szerepköröket.  A következő lépések letehetik a jogosult hozzárendelést egy egyéni címtárszerepkörhöz.

1. Jelentkezzen be a [kiemelt identitáskezelés](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart) az Azure Portalon egy felhasználói fiókkal, amely hozzá van rendelve a kiemelt szerepkör-rendszergazdai szerepkörhöz.
1. Válassza az **Azure AD egyéni szerepkörök (előzetes verzió)** lehetőséget.

    ![Válassza ki az Azure AD egyéni szerepkörök előnézetét a jogosult szerepkör-hozzárendelések megtekintéséhez](./media/azure-ad-custom-roles-assign/view-custom.png)

1. **Válassza ki a szerepkörök** az Azure AD-alkalmazások egyéni szerepköreinek listájának megtekintéséhez.

    ![Válassza ki a Szerepkörök lehetőséget, tekintse meg a jogosult szerepkör-hozzárendelések listáját](./media/azure-ad-custom-roles-assign/view-roles.png)

1. A hozzárendeléslap megnyitásához válassza a **Tag hozzáadása** lehetőséget.
1. Ha a szerepkör-hozzárendelés hatókörét egyetlen alkalmazásra szeretné korlátozni, válassza a **Hatókör** lehetőséget az alkalmazáshatókör megadásához.

    ![a jogosult szerepkör-hozzárendelések hatókörének korlátozása az Azure AD-ben](./media/azure-ad-custom-roles-assign/set-scope.png)

1. Válassza **a Szerepkör kiválasztása** a Szerepkör **kiválasztása** lista megnyitásához lehetőséget.

    ![válassza ki a felhasználóhoz hozzárendelni kívánt jogosult szerepkört](./media/azure-ad-custom-roles-assign/select-role.png)

1. Jelölje ki a hozzárendelni kívánt szerepkört, majd kattintson **a Kijelölés gombra.** Megnyílik **a Tagkijelöléslistája** lista.

    ![válassza ki azt a felhasználót, akinek a szerepkört hozzárendeli](./media/azure-ad-custom-roles-assign/select-member.png)

1. Jelölje ki azt a felhasználót, amelyet hozzá szeretne rendelni a szerepkörhöz, majd kattintson a **Kijelölés gombra.** Megnyílik **a Tagsági beállítások** lista.

    ![A szerepkör-hozzárendelés típusának beállítása jogosultra vagy aktívra](./media/azure-ad-custom-roles-assign/membership-settings.png)

1. A **Tagsági beállítások** lapon válassza a **Jogosult** vagy **az Aktív**lehetőséget:

    - **A jogosult** hozzárendelések megkövetelik, hogy a szerepkörhöz rendelt felhasználó hajtson végre egy műveletet, mielőtt használhatnák a szerepkört. A műveletek közé tartozhat a többtényezős hitelesítési ellenőrzés sikeres teljesítése, az üzleti indoklás megadása, vagy a kijelölt jóváhagyók jóváhagyásának kérése.
    - **Az aktív** hozzárendelések nem igénylik a hozzárendelt felhasználótól a szerepkör használatához bármilyen műveletet. Az aktív felhasználók mindig rendelkeznek a szerepkörhöz rendelt jogosultságokkal.

1. Ha az **Állandó** jelölőnégyzet jelen van és elérhető (a szerepkör beállításaitól függően), megadhatja, hogy a hozzárendelés állandó-e. Jelölje be azt a jelölőnégyzetet, ha azt szeretné, hogy a hozzárendelés véglegesen jogosult vagy véglegesen hozzálegyen rendelve. A hozzárendelés időtartamának megadásához törölje a jelet a jelölőnégyzetből.
1. Az új szerepkör-hozzárendelés létrehozásához kattintson a **Mentés,** majd a **Hozzáadás gombra.** Megjelenik egy értesítés a hozzárendelési folyamat állapotáról.

A szerepkör-hozzárendelés ellenőrzéséhez nyitott szerepkörben válassza **a Hozzárendelések** > **hozzárendelése** lehetőséget, és ellenőrizze, hogy a szerepkör-hozzárendelés megfelelően jogosultként vagy aktívként van-e azonosítva.

 ![Annak ellenőrzése, hogy a szerepkör-hozzárendelés jogosultként vagy aktívként látható-e.](./media/azure-ad-custom-roles-assign/verify-assignments.png)

## <a name="next-steps"></a>További lépések

- [Egyéni Azure AD-szerepkör aktiválása](azure-ad-custom-roles-assign.md)
- [Azure AD egyéni szerepkör-hozzárendelés eltávolítása vagy frissítése](azure-ad-custom-roles-update-remove.md)
- [Azure AD egyéni szerepkör-hozzárendelés konfigurálása](azure-ad-custom-roles-configure.md)
- [Szerepkör-definíciók az Azure AD-ben](../users-groups-roles/directory-assign-admin-roles.md)
