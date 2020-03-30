---
title: Szerepkör-hozzárendelések hozzáadása vagy eltávolítása külső felhasználók számára az RBAC-kal és az Azure Portalon
description: Ismerje meg, hogyan adhat hozzáférést az Azure-erőforrásokhoz a szervezeten kívüli felhasználók számára az Azure szerepköralapú hozzáférés-vezérlés (RBAC) használatával.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: skwan
ms.custom: it-pro
ms.openlocfilehash: 1c440b85f792ac5bb1336f4d20f930aafc38ad7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245641"
---
# <a name="add-or-remove-role-assignments-for-external-guest-users-using-azure-rbac-and-the-azure-portal"></a>Szerepkör-hozzárendelések hozzáadása vagy eltávolítása külső vendégfelhasználók számára az Azure RBAC és az Azure Portal használatával

[Az Azure szerepköralapú hozzáférés-vezérlés (RBAC)](overview.md) lehetővé teszi a nagyobb szervezetek és a külső közreműködőkkel, szállítókkal vagy szabadúszókkal dolgozó kis- és középvállalkozások jobb biztonsági kezelését, amelyeknek szükségük van a környezet adott erőforrásaihoz való hozzáférésre, de nem feltétlenül a teljes infrastruktúrához vagy a számlázással kapcsolatos hatókörökhöz. Az [Azure Active Directory B2B](../active-directory/b2b/what-is-b2b.md) funkcióival együttműködhet a külső vendégfelhasználókkal, és az RBAC segítségével csak azokat az engedélyeket adhatja meg, amelyekre a vendégfelhasználóknak szükségük van a környezetben.

## <a name="prerequisites"></a>Előfeltételek

Szerepkör-hozzárendelések hozzáadásához vagy eltávolításához a következőkre van szükség:

- `Microsoft.Authorization/roleAssignments/write`és `Microsoft.Authorization/roleAssignments/delete` engedélyek, például [a Felhasználói hozzáférés rendszergazdája](built-in-roles.md#user-access-administrator) vagy [a tulajdonos](built-in-roles.md#owner)

## <a name="when-would-you-invite-guest-users"></a>Mikor hívna meg vendégfelhasználókat?

Íme néhány példa arra, amikor vendégfelhasználókat hívhat meg a szervezetbe, és engedélyeket adhat:

- Engedélyezze egy külső önálló vállalkozó, amely csak egy e-mail fiókkal rendelkezik, hogy hozzáférjen a projekt Azure-erőforrásaihoz.
- Lehetővé teszi egy külső partner számára bizonyos erőforrások vagy egy teljes előfizetés kezelését.
- Lehetővé teszi, hogy a szervezetben nem található támogatási mérnökök (például a Microsoft támogatási szolgálata) ideiglenesen hozzáférjenek az Azure-erőforráshoz a problémák elhárításához.

## <a name="permission-differences-between-member-users-and-guest-users"></a>A tag- és a vendégfelhasználók közötti engedélykülönbségek

A címtár natív tagjai (tagfelhasználók) eltérő engedélyekkel rendelkeznek, mint a más címtárból b2B együttműködési vendégként meghívott felhasználók (vendégfelhasználók). A tagok például szinte az összes címtáradatot olvashatják, míg a vendégfelhasználók korlátozott könyvtárengedélyekkel rendelkeznek. A tag- és vendégfelhasználókról a Következő témakörben olvashat: [Mik az alapértelmezett felhasználói engedélyek az Azure Active Directoryban?](../active-directory/fundamentals/users-default-permissions.md)

## <a name="add-a-guest-user-to-your-directory"></a>Vendégfelhasználó hozzáadása a címtárhoz

Az alábbi lépésekkel hozzáadhat egy vendégfelhasználót a címtárhoz az Azure Active Directory-lapon.

1. Győződjön meg arról, hogy a szervezet külső együttműködési beállításai úgy vannak konfigurálva, hogy meghívhatja a vendégeket. További információ: [B2B külső együttműködés engedélyezése és annak kezelése, hogy ki hívhat meg vendégeket.](../active-directory/b2b/delegate-invitations.md)

1. Az Azure Portalon kattintson az **Azure Active Directory** > **felhasználói** > **új vendégfelhasználó**elemre.

    ![Új vendégfelhasználói funkció az Azure Portalon](./media/role-assignments-external-users/invite-guest-user.png)

1. Új vendégfelhasználó hozzáadásához kövesse a lépéseket. További információ: [Add Azure Active Directory B2B együttműködési felhasználók az Azure Portalon.](../active-directory/b2b/add-users-administrator.md#add-guest-users-to-the-directory)

Miután hozzáadott egy vendégfelhasználót a címtárhoz, közvetlen hivatkozást küldhet a vendégfelhasználónak egy megosztott alkalmazásra, vagy a vendégfelhasználó a beváltási URL-re kattintva a meghívó e-mailben.

![Vendégfelhasználó e-mail meghívása](./media/role-assignments-external-users/invite-email.png)

Ahhoz, hogy a vendégfelhasználó hozzáférhessen a címtárhoz, be kell fejeznie a meghívási folyamatot.

![Vendégfelhasználó ellenőrző engedélyei](./media/role-assignments-external-users/invite-review-permissions.png)

A meghívási folyamatról az [Azure Active Directory B2B együttműködési meghívásbeváltáscímű](../active-directory/b2b/redemption-experience.md)témakörben talál további információt.

## <a name="add-a-role-assignment-for-a-guest-user"></a>Szerepkör-hozzárendelés hozzáadása vendégfelhasználóhoz

Az RBAC-ban a hozzáférés engedélyezéséhez hozzá kell rendelnie egy szerepkört. Egy vendégfelhasználó szerepkör-hozzárendelésének hozzáadásához [ugyanazokat](role-assignments-portal.md#add-a-role-assignment) a lépéseket kell végrehajtania, mint egy tagfelhasználó, csoport, egyszerű szolgáltatás vagy felügyelt identitás esetén. Kövesse az alábbi lépéseket, adjon hozzá egy szerepkör-hozzárendelést egy vendégfelhasználóhoz különböző hatókörökön.

1. Az Azure Portalon kattintson a **Minden szolgáltatás** lehetőségre.

1.  Válassza ki azt az erőforráskészletet, amelyre a hozzáférés vonatkozik, más néven a hatókört. Kiválaszthatja például a **Felügyeleti csoportok**, **Az Előfizetések**, **az Erőforráscsoportok**vagy egy erőforrás lehetőséget.

1. Kattintson az adott erőforrásra.

1. Kattintson a **Hozzáférés-vezérlés (IAM)** elemre.

    Az alábbi képernyőképen egy erőforráscsoport hozzáférés-vezérlési (IAM) panelje látható. Ha itt bármilyen hozzáférés-vezérlési módosítást hajt végre, azok csak az erőforráscsoportra vonatkoznak.

    ![Hozzáférés-vezérlési (IAM) panel erőforráscsoporthoz](./media/role-assignments-external-users/access-control-resource-group.png)

1. Kattintson a **Szerepkör-hozzárendelések** fülre a hatókör összes szerepkör-hozzárendelésének megtekintéséhez.

1. A Szerepkör-hozzárendelés **hozzáadása** > ablaktábla megnyitásához kattintson a**Szerepkör-hozzárendelés hozzáadása** gombra.

    Ha nincs engedélye a szerepkörök hozzárendeléséhez, a Szerepkör-hozzárendelés hozzáadása beállítás le lesz tiltva.

    ![Hozzáadás menü](./media/role-assignments-external-users/add-menu.png)

1. A **Szerepkör** legördülő listájában válasszon ki egy szerepkört, például a **Virtuális gépek közreműködője** szerepkört.

1. A **Kijelölés** listában jelölje ki a vendégfelhasználót. Ha a listában nem látja a felhasználót, a **Kijelölés** mezőbe írhatja be a megjelenítendő nevek, e-mail címek és objektumazonosítók kereséséhez a könyvtárban.

   ![Szerepkör-hozzárendelés hozzáadása ablaktábla](./media/role-assignments-external-users/add-role-assignment.png)

1. A **Mentés gombra** kattintva rendelje hozzá a szerepkört a kijelölt hatókörhöz.

    ![Szerepkör-hozzárendelés a virtuálisgép közreműködőjéhez](./media/role-assignments-external-users/access-control-role-assignments.png)

## <a name="add-a-role-assignment-for-a-guest-user-not-yet-in-your-directory"></a>Szerepkör-hozzárendelés hozzáadása olyan vendégfelhasználóhoz, aki még nem volt a címtárban

Egy vendégfelhasználó szerepkör-hozzárendelésének hozzáadásához [ugyanazokat](role-assignments-portal.md#add-a-role-assignment) a lépéseket kell végrehajtania, mint egy tagfelhasználó, csoport, egyszerű szolgáltatás vagy felügyelt identitás esetén.

Ha a vendégfelhasználó még nincs a címtárban, a felhasználót közvetlenül a Szerepkör-hozzárendelés hozzáadása ablaktáblából hívhatja meg.

1. Az Azure Portalon kattintson a **Minden szolgáltatás** lehetőségre.

1.  Válassza ki azt az erőforráskészletet, amelyre a hozzáférés vonatkozik, más néven a hatókört. Kiválaszthatja például a **Felügyeleti csoportok**, **Az Előfizetések**, **az Erőforráscsoportok**vagy egy erőforrás lehetőséget.

1. Kattintson az adott erőforrásra.

1. Kattintson a **Hozzáférés-vezérlés (IAM)** elemre.

1. Kattintson a **Szerepkör-hozzárendelések** fülre a hatókör összes szerepkör-hozzárendelésének megtekintéséhez.

1. A Szerepkör-hozzárendelés **hozzáadása** > ablaktábla megnyitásához kattintson a**Szerepkör-hozzárendelés hozzáadása** gombra.

    ![Hozzáadás menü](./media/role-assignments-external-users/add-menu.png)

1. A **Szerepkör** legördülő listájában válasszon ki egy szerepkört, például a **Virtuális gépek közreműködője** szerepkört.

1. A **Kijelölés** listában írja be a meghívni kívánt személy e-mail címét, és jelölje ki az illetőt.

   ![Vendégfelhasználó meghívása a Szerepkör-hozzárendelés hozzáadása ablaktáblán](./media/role-assignments-external-users/add-role-assignment-new-guest.png)

1. A **Mentés gombra** kattintva vegye fel a vendégfelhasználót a könyvtárba, rendelje hozzá a szerepkört, és küldjön meghívót.

    Néhány pillanat múlva értesítést kap a szerepkör-hozzárendelésről és a meghívóval kapcsolatos információkról.

    ![Szerepkör-hozzárendelés és meghívott felhasználói értesítés](./media/role-assignments-external-users/invited-user-notification.png)

1. A vendégfelhasználó manuális meghívásához kattintson a jobb gombbal, és másolja a meghívó hivatkozást az értesítésbe. Ne kattintson a meghívó hivatkozásra, mert elindítja a meghívási folyamatot.

    A meghívó hivatkozás a következő formátumú lesz:

    `https://invitations.microsoft.com/redeem/...`

1. Küldje el a meghívóhivatkozást a vendégfelhasználónak a meghívási folyamat befejezéséhez.

    A meghívási folyamatról az [Azure Active Directory B2B együttműködési meghívásbeváltáscímű](../active-directory/b2b/redemption-experience.md)témakörben talál további információt.

## <a name="remove-a-guest-user-from-your-directory"></a>Vendégfelhasználó eltávolítása a címtárból

Mielőtt eltávolítana egy vendégfelhasználót egy címtárból, először távolítsa el az adott vendégfelhasználó szerepkör-hozzárendeléseit. A vendégfelhasználó könyvtárból való eltávolításához kövesse az alábbi lépéseket.

1. Nyissa meg **a hozzáférés-vezérlést (IAM)** egy hatókörön, például felügyeleti csoporton, előfizetésen, erőforráscsoporton vagy erőforráson, ahol a vendégfelhasználó szerepkör-hozzárendeléssel rendelkezik.

1. Kattintson a **Szerepkör-hozzárendelések** fülre az összes szerepkör-hozzárendelés megtekintéséhez.

1. A szerepkör-hozzárendelések listájában jelölje be a vendégfelhasználót az eltávolítani kívánt szerepkör-hozzárendeléssel.

   ![Szerepkör-hozzárendelés eltávolítása](./media/role-assignments-external-users/remove-role-assignment-select.png)

1. Kattintson az **Eltávolítás** lehetőségre.

   ![Szerepkör-hozzárendelés eltávolítási üzenete](./media/role-assignments-external-users/remove-role-assignment.png)

1. A megjelenő szerepkör-hozzárendelési üzenet eltávolítása korválassza az **Igen**gombot.

1. A bal oldali navigációs sávon kattintson az **Azure Active Directory** > **felhasználói**elemre.

1. Kattintson az eltávolítani kívánt vendégfelhasználóra.

1. Kattintson a **Törlés** gombra.

   ![Vendégfelhasználó törlése](./media/role-assignments-external-users/delete-guest-user.png)

1. A megjelenő törlési üzenetben kattintson az **Igen**gombra.

## <a name="troubleshoot"></a>Hibaelhárítás

### <a name="guest-user-cannot-browse-the-directory"></a>A vendégfelhasználó nem tud böngészni a könyvtárban

A vendégfelhasználók korlátozott címtárengedélyekkel rendelkeznek. A vendégfelhasználók például nem tudnak böngészni a címtárban, és nem kereshetnek csoportokat vagy alkalmazásokat. További információ: [Mik az alapértelmezett felhasználói engedélyek az Azure Active Directoryban?](../active-directory/fundamentals/users-default-permissions.md).

![A vendégfelhasználó nem tud tallózni a címtárban lévő felhasználók között](./media/role-assignments-external-users/directory-no-users.png)

Ha egy vendégfelhasználónak további jogosultságokra van szüksége a címtárban, címtárszerepkört rendelhet a vendégfelhasználóhoz. Ha szeretné, hogy egy vendégfelhasználó teljes olvasási hozzáféréssel rendelkezik a címtárhoz, hozzáadhatja a vendégfelhasználót az Azure AD [címtárolvasók](../active-directory/users-groups-roles/directory-assign-admin-roles.md) szerepköréhez. További információ: [Engedélyek megadása az Azure Active Directory-bérlőben lévő partnerszervezetektől származó felhasználóknak.](../active-directory/b2b/add-guest-to-role.md)

![Címtárolvasók szerepkör hozzárendelése](./media/role-assignments-external-users/directory-roles.png)

### <a name="guest-user-cannot-browse-users-groups-or-service-principals-to-assign-roles"></a>A vendégfelhasználó nem tud tallózni a felhasználók, csoportok vagy egyszerű szolgáltatástagok között a szerepkörök hozzárendeléséhez

A vendégfelhasználók korlátozott címtárengedélyekkel rendelkeznek. Még ha egy vendégfelhasználó [egy hatókör tulajdonosa](built-in-roles.md#owner) is, ha megpróbál nak hozzáadni egy szerepkör-hozzárendelést, hogy valaki másnak hozzáférést biztosítson, nem tallózhat a felhasználók, csoportok vagy egyszerű szolgáltatástagok listájában.

![A vendégfelhasználó nem tud tallózni a rendszerbiztonsági tagok között a szerepkörök hozzárendeléséhez](./media/role-assignments-external-users/directory-no-browse.png)

Ha a vendégfelhasználó tudja valakinek a pontos bejelentkezési nevét a címtárban, hozzáférést adhat. Ha szeretné, hogy egy vendégfelhasználó teljes olvasási hozzáféréssel rendelkezik a címtárhoz, hozzáadhatja a vendégfelhasználót az Azure AD [címtárolvasók](../active-directory/users-groups-roles/directory-assign-admin-roles.md) szerepköréhez. További információ: [Engedélyek megadása az Azure Active Directory-bérlőben lévő partnerszervezetektől származó felhasználóknak.](../active-directory/b2b/add-guest-to-role.md)

### <a name="guest-user-cannot-register-applications-or-create-service-principals"></a>A vendégfelhasználó nem regisztrálhat alkalmazásokat, és nem hozhat létre egyszerű szolgáltatást

A vendégfelhasználók korlátozott címtárengedélyekkel rendelkeznek. Ha egy vendégfelhasználónak képesnek kell lennie az alkalmazások regisztrálására vagy egyszerű szolgáltatáscsoportok létrehozására, hozzáadhatja a vendégfelhasználót az [Alkalmazásfejlesztői](../active-directory/users-groups-roles/directory-assign-admin-roles.md) szerepkörhöz az Azure AD-ben. További információ: [Engedélyek megadása az Azure Active Directory-bérlőben lévő partnerszervezetektől származó felhasználóknak.](../active-directory/b2b/add-guest-to-role.md)

![A vendégfelhasználó nem tud alkalmazásokat regisztrálni](./media/role-assignments-external-users/directory-access-denied.png)

### <a name="guest-user-does-not-see-the-new-directory"></a>A vendégfelhasználó nem látja az új könyvtárat

Ha egy vendégfelhasználó hozzáférést kapott egy címtárhoz, de nem látja az új könyvtárat az Azure Portalon, amikor megpróbál váltani a **Címtár + előfizetés** ablaktáblán, győződjön meg arról, hogy a vendégfelhasználó befejezte a meghívási folyamatot. A meghívási folyamatról az [Azure Active Directory B2B együttműködési meghívásbeváltáscímű](../active-directory/b2b/redemption-experience.md)témakörben talál további információt.

### <a name="guest-user-does-not-see-resources"></a>A vendégfelhasználó nem látja az erőforrásokat

Ha egy vendégfelhasználó hozzáférést kapott egy címtárhoz, de nem látja az azure-portálon az erőforrásokat, amelyekhez hozzáférést kaptak, győződjön meg arról, hogy a vendégfelhasználó a megfelelő könyvtárat választotta. Előfordulhat, hogy egy vendégfelhasználó több könyvtárhoz is hozzáférhet. A könyvtárak közötti váltáshoz kattintson a bal felső sarokban a **Könyvtár + előfizetés**elemre, majd a megfelelő könyvtárra.

![Könyvtárak + Előfizetések ablaktábla az Azure Portalon](./media/role-assignments-external-users/directory-subscription.png)

## <a name="next-steps"></a>További lépések

- [Azure Active Directory B2B együttműködési felhasználók hozzáadása az Azure Portalon](../active-directory/b2b/add-users-administrator.md)
- [Az Azure Active Directory B2B együttműködési felhasználóinak tulajdonságai](../active-directory/b2b/user-properties.md)
- [A B2B együttműködési meghívó e-mail elemei – Azure Active Directory](../active-directory/b2b/invitation-email-elements.md)
- [Vendégfelhasználó hozzáadása társadminisztrátorként](classic-administrators.md#add-a-guest-user-as-a-co-administrator)