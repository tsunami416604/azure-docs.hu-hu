---
title: Azure AD-szerepkörök hozzárendelése a PIM szolgáltatásban – Azure Active Directory | Microsoft dokumentumok
description: Megtudhatja, hogyan rendelhet Azure AD-szerepköröket az Azure AD kiemelt identitáskezelés (PIM) között.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 02/07/2020
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5048cefaae10cd55091dd72f0b73a3cf9d731a35
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253272"
---
# <a name="assign-azure-ad-roles-in-privileged-identity-management"></a>Azure AD-szerepkörök hozzárendelése a kiemelt identitáskezelésben

Az Azure Active Directory (Azure AD) segítségével egy globális rendszergazda **állandó** Azure AD-rendszergazdai szerepkör-hozzárendeléseket végezhet. Ezek a szerepkör-hozzárendelések az [Azure Portalon](../users-groups-roles/directory-assign-admin-roles.md) vagy a [PowerShell-parancsok](/powershell/module/azuread#directory_roles)használatával hozhatók létre.

Az Azure AD emelt szintű identitáskezelési (PIM) szolgáltatás is lehetővé teszi a kiemelt szerepkör-rendszergazdák számára, hogy állandó rendszergazdai szerepkör-hozzárendelések. Emellett a kiemelt szerepkör-rendszergazdák is, hogy a felhasználók **jogosultak** az Azure AD-rendszergazdai szerepkörök. A jogosult rendszergazda aktiválhatja a szerepkört, amikor szüksége van rá, majd az engedélyeik lejárnak, ha elkészült.

## <a name="determine-your-version-of-pim"></a>A PIM verziójának meghatározása

2019 novemberétől kezdődően a kiemelt identitáskezelés Azure AD-szerepkörök része frissül egy új verzióra, amely megfelel az Azure-erőforrás-szerepkörök élményének. Ez további funkciókat, valamint [a meglévő API-t is módosítja.](azure-ad-roles-features.md#api-changes) Az új verzió bevezetés alatt, hogy mely eljárásokat követi ebben a cikkben attól függ, hogy a kiemelt identitáskezelés jelenleg rendelkezik-e. Ebben a szakaszban ismertetett lépéseket követve határozza meg, hogy a Kiemelt identitáskezelés melyik verzióját használja. Miután ismeri a kiemelt identitáskezelés verzióját, kiválaszthatja a cikkben az adott verziónak megfelelő eljárásokat.

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com/) egy olyan felhasználóval, aki a [kiemelt szerepkör-rendszergazdai](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) szerepkörben van.
1. Nyissa meg **az Azure AD kiemelt identitáskezelés .** Ha az áttekintő oldal tetején szalaghirdetés található, kövesse a cikk **Új verzió** lapján található utasításokat. Ellenkező esetben kövesse az **Előző verzió** lap utasításait.

  [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

# <a name="new-version"></a>[Új verzió](#tab/new)

## <a name="assign-a-role"></a>Szerepkör hozzárendelése

Kövesse az alábbi lépéseket, hogy a felhasználó jogosult egy Azure AD-rendszergazdai szerepkör.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) egy olyan felhasználóval, aki a [kiemelt szerepkör-rendszergazdai](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) szerepkör tagja.

    Arról, hogy miként adhat hozzáférést egy másik rendszergazdai hozzáférésnek a Kiemelt identitáskezelés kezeléséhez, olvassa el [a Hozzáférés megadása más rendszergazdáknak a Kiemelt identitáskezelés kezeléséhez című témakört.](pim-how-to-give-access-to-pim.md)

1. Nyissa meg **az Azure AD kiemelt identitáskezelés .**

1. Válassza az **Azure AD-szerepkörök lehetőséget.**

1. Válassza ki a **szerepkörök** az Azure AD-engedélyek szerepkörök listájának megtekintéséhez.

    ![Azure AD-szerepkörök](./media/pim-how-to-add-role-to-user/roles-list.png)

1. Válassza **a Tag hozzáadása** lehetőséget az Új **hozzárendelés** lap megnyitásához.

1. Válassza **a Szerepkör kiválasztása** lehetőséget a Szerepkör kiválasztása lap megnyitásához.

    ![Új hozzárendelés ablaktábla](./media/pim-how-to-add-role-to-user/select-role.png)

1. Jelölje ki a hozzárendelni kívánt szerepkört, majd kattintson **a Kijelölés gombra.**

1. Jelölje ki azt a tagot, akihez hozzá szeretne rendelni a szerepkörhöz, majd válassza a **Kijelölés**lehetőséget.

1. A **Tagsági beállítások** ablaktábla **Hozzárendeléstípus** listájában válassza a **Jogosult** vagy **az Aktív**lehetőséget.

    - **A jogosult** hozzárendelések megkövetelik a szerepkör tagjától, hogy a szerepkör használatához műveletet hajtson végre. A műveletek közé tartozhat a többtényezős hitelesítés (MFA) ellenőrzése, az üzleti indoklás megadása vagy a kijelölt jóváhagyók jóváhagyásának kérése.

    - **Az aktív** hozzárendelések nem igénylik a tagtól a szerepkör használatához szükséges műveleteket. Az aktívként hozzárendelt tagok mindig a szerepkörhöz rendelt jogosultságokkal rendelkeznek.

1. Ha a hozzárendelésnek állandónak (véglegesen jogosultnak vagy véglegesen hozzárendeltnek) kell lennie, jelölje be a **Végleges jelölőnégyzetet.**

    A szerepkör-beállításoktól függően előfordulhat, hogy a jelölőnégyzet nem jelenik meg, vagy nem módosítható.

1. Adott hozzárendelési időtartam megadásához törölje a jelet a jelölőnégyzetből, és módosítsa a kezdési és/vagy záró dátum- és időmezőket. Ha végzett, válassza a **Kész gombot.**

    ![Tagsági beállítások - dátum és idő](./media/pim-how-to-add-role-to-user/start-and-end-dates.png)

1. Az új szerepkör-hozzárendelés létrehozásához válassza a **Hozzáadás**lehetőséget. Megjelenik az állapotról szóló értesítés.

    ![Új hozzárendelés - Értesítés](./media/pim-how-to-add-role-to-user/assignment-notification.png)

## <a name="update-or-remove-an-existing-role-assignment"></a>Meglévő szerepkör-hozzárendelés frissítése vagy eltávolítása

Meglévő szerepkör-hozzárendelés frissítéséhez vagy eltávolításához kövesse az alábbi lépéseket.

1. Nyissa meg **az Azure AD kiemelt identitáskezelés .**

1. Válassza az **Azure AD-szerepkörök lehetőséget.**

1. Válassza ki a **szerepkörök** az Azure AD szerepkörök listájának megtekintéséhez.

1. Jelölje ki a frissíteni vagy eltávolítani kívánt szerepkört.

1. Keresse meg a szerepkör-hozzárendelést a **Jogosult szerepkörök** vagy **az Aktív szerepkörök** lapon.

    ![Szerepkör-hozzárendelés frissítése vagy eltávolítása](./media/pim-how-to-add-role-to-user/remove-update-assignments.png)

1. A **szerepkör-hozzárendelés** frissítéséhez vagy eltávolításához válassza a Frissítés vagy az **Eltávolítás** lehetőséget.

# <a name="previous-version"></a>[Előző verzió](#tab/previous)

## <a name="make-a-user-eligible-for-a-role"></a>A felhasználó szerepkörre való jogosultságának letehetése

Kövesse az alábbi lépéseket, hogy a felhasználó jogosult egy Azure AD-rendszergazdai szerepkör.

1. Válassza a **Szerepkörök** vagy **tagok lehetőséget.**

    ![Azure AD-szerepkörök](./media/pim-how-to-add-role-to-user/pim-directory-roles.png)

1. Válassza **a Tag hozzáadása lehetőséget** a Felügyelt tagok hozzáadása **lehetőséget.**

1. Válassza **a Szerepkör kiválasztása**lehetőséget, jelölje ki a kezelni kívánt szerepkört, majd válassza a **Kijelölés**lehetőséget.

    ![Szerepkör kiválasztása](./media/pim-how-to-add-role-to-user/pim-select-a-role.png)

1. Válassza **a Tagok kiválasztása**lehetőséget, jelölje ki a szerepkörhöz rendelni kívánt felhasználókat, majd válassza a **Kijelölés**lehetőséget.

    ![Szerepkör kiválasztása](./media/pim-how-to-add-role-to-user/pim-select-members.png)

1. A **Felügyelt tagok hozzáadása**csoportban válassza az OK **gombot,** ha hozzá szeretné adni a felhasználót a szerepkörhöz.

1. A szerepkörök listájában jelölje ki az imént kiosztott szerepkört a tagok listájának megtekintéséhez.

     A szerepkör hozzárendelésekénél a kiválasztott felhasználó a tagok listájában **jogosultként** jelenik meg a szerepkörre.

    ![Szerepkörre jogosult felhasználó](./media/pim-how-to-add-role-to-user/pim-directory-role-eligible.png)

1. Most, hogy a felhasználó jogosult a szerepkörre, tudassa velük, hogy aktiválhatják az [Azure AD-szerepkörök aktiválása](pim-how-to-activate-role.md)a kiemelt identitáskezelés ben című útmutató utasításainak megfelelően.

    A jogosult rendszergazdáknak regisztrálniuk kell az Azure többtényezős hitelesítésre (MFA) az aktiválás során. Ha egy felhasználó nem tud regisztrálni az MFA-ra, vagy Microsoft-fiókot (például @outlook.com) használ, állandóvá kell tennie őket minden szerepkörében.

## <a name="make-a-role-assignment-permanent"></a>Szerepkör-hozzárendelés állandóvá tétel

Alapértelmezés szerint az új felhasználók csak egy Azure AD-rendszergazdai *szerepkörre jogosultak.* Kövesse az alábbi lépéseket, ha állandóvá szeretné tenni a szerepkör-hozzárendelést.

1. Nyissa meg **az Azure AD kiemelt identitáskezelés .**

1. Válassza az **Azure AD-szerepkörök lehetőséget.**

1. Válassza ki a **Members** (Tagok) elemet.

    ![A tagok listája](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Válassza ki azt **a Jogosult** szerepkört, amelyet állandóvá szeretne tenni.

1. Válassza az **Egyebek,** majd **a Perm átalakítása**lehetőséget.

    ![Szerepkör-hozzárendelés állandóvá tétel](./media/pim-how-to-add-role-to-user/pim-make-perm.png)

    A szerepkör most **antól állandóként**van felsorolva.

    ![Állandó változással rendelkező tagok listája](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members-permanent.png)

## <a name="remove-a-user-from-a-role"></a>Felhasználó eltávolítása egy szerepkörből

Eltávolíthatja a felhasználókat a szerepkör-hozzárendelésekből, de győződjön meg arról, hogy mindig van legalább egy felhasználó, aki állandó globális rendszergazda. Ha nem biztos abban, hogy mely felhasználóknak van még szükségük [szerepkör-hozzárendelésükre, elindíthatja a szerepkör hozzáférés-felülvizsgálatát.](pim-how-to-start-security-review.md)

Az alábbi lépésekkel távolítson el egy adott felhasználót egy Azure AD-rendszergazdai szerepkörből.

1. Nyissa meg **az Azure AD kiemelt identitáskezelés .**

1. Válassza az **Azure AD-szerepkörök lehetőséget.**

1. Válassza ki a **Members** (Tagok) elemet.

    ![A tagok listája](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Jelölje ki az eltávolítani kívánt szerepkör-hozzárendelést.

1. Válassza az **Egyebek,** majd **az Eltávolítás**lehetőséget.

    ![Szerepkör eltávolítása](./media/pim-how-to-add-role-to-user/pim-remove-role.png)

1. A megerősítést kérő üzenetben válassza az **Igen**lehetőséget.

    ![Szerepkör eltávolítása](./media/pim-how-to-add-role-to-user/pim-remove-role-confirm.png)

    A szerepkör-hozzárendelés törlődik.

## <a name="authorization-error-when-assigning-roles"></a>Engedélyezési hiba szerepkörök hozzárendelésekén

Ha a közelmúltban engedélyezte a kiemelt identitáskezelést egy előfizetéshez, és engedélyezési hibát kap, amikor megpróbálja egy felhasználót jogosultá tenni egy Azure AD-rendszergazdai szerepkörre, annak az lehet, hogy az MS-PIM egyszerű szolgáltatása még nem rendelkezik a megfelelő engedélyekkel. Az MS-PIM egyszerű szolgáltatásának [felhasználói hozzáférés-rendszergazdai](../../role-based-access-control/built-in-roles.md#user-access-administrator) szerepkörrel kell rendelkeznie ahhoz, hogy szerepköröket rendeljen másokhoz. Ahelyett, hogy megvárna, amíg az MS-PIM hozzárendeli a Felhasználói hozzáférés rendszergazdai szerepkörét, manuálisan is hozzárendelheti.

Az alábbi lépésekkel rendelje hozzá a Felhasználói hozzáférés-rendszergazda szerepkört az MS-PIM egyszerű szolgáltatáshoz egy előfizetéshez.

1. Jelentkezzen be az Azure Portalon globális rendszergazdaként.

1. Válassza **az Összes szolgáltatás,** majd **az Előfizetések lehetőséget.**

1. Válassza ki az előfizetését.

1. Válassza a **Hozzáférés-vezérlés (IAM)** elemet.

1. Válassza **a szerepkör-hozzárendelések** lehetőséget az előfizetéshatókörszerepkör-hozzárendelések aktuális listájának megtekintéséhez.

   ![Hozzáférés-vezérlési (IAM) panel előfizetéshez](./media/pim-how-to-add-role-to-user/ms-pim-access-control.png)

1. Ellenőrizze, hogy az **MS-PIM** egyszerű szolgáltatás a **Felhasználói hozzáférés-rendszergazda** szerepkörhöz van-e rendelve.

1. Ha nem, válassza a **Szerepkör-hozzárendelés hozzáadása** lehetőséget a **Szerepkör-hozzárendelés hozzáadása** ablaktábla megnyitásához.

1. A **Szerepkör** legördülő listában válassza a **Felhasználói hozzáférés rendszergazdája** szerepkört.

1. A **Kijelölés** listában keresse meg és jelölje ki az **MS-PIM** egyszerű szolgáltatást.

   ![Szerepkör-hozzárendelés hozzáadása ablaktábla – Engedélyek hozzáadása az MS-PIM egyszerű szolgáltatásához](./media/pim-how-to-add-role-to-user/ms-pim-add-permissions.png)

1. A szerepkör hozzárendeléséhez válassza a **Mentés** gombot.

   Néhány pillanat múlva az MS-PIM egyszerű szolgáltatás a felhasználói hozzáférés-rendszergazda szerepkört rendeli az előfizetéshatókörben.

   ![Hozzáférés-vezérlési lap az MS-PIM egyszerű szolgáltatásának Felhasználói hozzáférés-rendszergazdai szerepkör-hozzárendelésével](./media/pim-how-to-add-role-to-user/ms-pim-user-access-administrator.png)

 ---

## <a name="next-steps"></a>További lépések

- [Az Azure AD-rendszergazdai szerepkör beállításainak konfigurálása a Kiemelt identitáskezelés ben](pim-how-to-change-default-settings.md)
- [Azure-erőforrás-szerepkörök hozzárendelése a kiemelt identitáskezelésben](pim-resource-roles-assign-roles.md)