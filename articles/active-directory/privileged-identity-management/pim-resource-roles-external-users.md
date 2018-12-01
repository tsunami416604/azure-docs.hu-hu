---
title: Külső felhasználó meghívása és az Azure-erőforrás szerepköröket a PIM |} A Microsoft Docs
description: Ismerje meg, hogy külső felhasználó meghívása és az Azure-erőforrás szerepkörök hozzárendelése az Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: pim
ms.date: 11/29/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 561fccd3e0baee481937954cc65601576a4fd60f
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2018
ms.locfileid: "52730182"
---
# <a name="invite-external-users-and-assign-azure-resource-roles-in-pim"></a>Külső felhasználó meghívása, és rendelje hozzá a PIM az Azure-erőforrásszerepkörök

Az Azure Active Directory (Azure AD)--vállalatközi (B2B) olyan képességeket, amellyel a cégek működhet együtt a külső felhasználók és a szállítók minden olyan fiók használatával az Azure AD-ben. Ha kombinálja az Azure AD Privileged Identity Management (PIM) B2B, folytathatja a külső felhasználók számára a megfelelőségi és szabályozási követelmények vonatkoznak. Például használhatja ezeket a PIM-funkciókat az Azure-erőforrások külső felhasználókkal:

- Adott Azure-erőforrásokhoz való hozzáférés hozzárendelése
- Igény szerinti elérésének engedélyezése
- Adja meg a hozzárendelés időtartam és a záró dátum
- Többtényezős hitelesítés megkövetelése aktív hozzárendeléskor vagy az aktiválás
- Hajtsa végre a hozzáférési felülvizsgálatok
- Riasztások, és a naplók

Ez a cikk ismerteti a címtár egy külső felhasználó meghívása, és azok a PIM használata az Azure-erőforrásokhoz való hozzáférésének kezelése.

## <a name="when-would-you-invite-external-users"></a>Ha szeretné meghívni a külső felhasználók?

A címtárhoz előfordulhat, hogy külső felhasználó meghívása esetén az alábbiakban néhány példaforgatókönyvek:

- Lehetővé teszi egy külső önálló szállító, amely csak a projekt az Azure-erőforrások eléréséhez e-mail fiókja van.
- Egy külső partner egy nagy szervezet, amely a helyszíni Active Directory összevonási szolgáltatások használja a költségek alkalmazáshoz való hozzáférés engedélyezése.
- Lehetővé teszi a támogatási szakértők nem a szervezetben (például a Microsoft támogatási) kapcsolatos problémák elhárítása az Azure-erőforrás átmenetileg el.

## <a name="how-does-external-collaboration-using-b2b-work"></a>Hogyan történik a külső együttműködés B2B munkahelyi?

B2B használatakor meghívhat egy külső felhasználót a címtárhoz. A külső felhasználót úgy tűnik, hogy a címtárban, de a felhasználó nem rendelkezik a hozzá társított hitelesítő adatokat. Amikor egy külső felhasználó hitelesítését, hitelesíteniük kell a saját címtárukkal és nem a címtárban. Ez azt jelenti, hogy a külső felhasználó már nem rendelkezik hozzáféréssel a kezdőkönyvtárba kerül, ha azok automatikusan megszakadna a címtárban. Például ha a külső felhasználót a szervezetből kilépő, azok automatikusan férhet hozzá, akkor megosztott erőforrások velük a címtár anélkül, hogy bármi egyebet kellene. B2B kapcsolatos további információkért lásd: [Mi az az Azure Active Directory B2B vendégfelhasználói hozzáférés?](../b2b/what-is-b2b.md).

![B2B- és külső felhasználó](./media/pim-resource-roles-external-users/b2b-external-user.png)

## <a name="check-external-collaboration-settings"></a>Külső együttműködési beállítások ellenőrzése

Ahhoz, hogy a külső felhasználók meghívhatja a könyvtárba, ellenőrizze a külső együttműködési beállítások.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).

1. Kattintson a **Azure Active Directory** > **felhasználói beállítások**.

1. Kattintson a **külső együttműködési beállítások kezelése**.

    ![Külső együttműködési beállítások](./media/pim-resource-roles-external-users/external-collaboration-settings.png)

1. Győződjön meg arról, hogy a **rendszergazdák és a vendégmeghívó szerepkörű felhasználók küldhetnek meghívót** kapcsoló értéke **Igen**.

## <a name="invite-an-external-user-and-assign-a-role"></a>Egy külső felhasználó meghívása és a egy szerepkör hozzárendelése

A PIM használata esetén egy külső felhasználó meghívása, és győződjön meg az Azure-erőforrás szerepkörhöz hasonlóan a tag felhasználó jogosult.

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com/) egy felhasználóval, amely tagja a [kiemelt szerepkörű rendszergazda](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) vagy [felhasználóifiók-adminisztrátor](../users-groups-roles/directory-assign-admin-roles.md#user-account-administrator) szerepkör.

1. Nyissa meg **az Azure AD Privileged Identity Management**.

1. Kattintson a **Azure-erőforrások**.

1. Használja a **Erőforrásszűrő** a felügyelt erőforrások szűréséhez.

1. Kattintson az erőforrás szeretne felügyelni, mint például egy erőforrást, erőforráscsoport, előfizetés vagy a felügyeleti csoport.

    A hatókör csak szüksége van a külső felhasználót kell beállítania.

1. Kattintson a kezelés, **szerepkörök** szerepkörök az Azure-erőforrások listájának megtekintéséhez.

    ![Azure-erőforrások szerepkörök](./media/pim-resource-roles-external-users/resources-roles.png)

1. Kattintson a minimális szerepkör, amely a felhasználónak lesz szüksége.

    ![A kijelölt szerepkör](./media/pim-resource-roles-external-users/selected-role.png)

1. A szerepkör lapján kattintson a **tag hozzáadása** az új hozzárendelés panel megnyitásához.

1. Kattintson a **tag vagy csoport kijelölése**.

    ![Tag vagy csoport kijelölése](./media/pim-resource-roles-external-users/select-member-group.png)

1. Egy külső felhasználó meghívása, kattintson a **meghívása**.

    ![Vendég meghívása](./media/pim-resource-roles-external-users/invite-guest.png)

1. Külső felhasználó megadása után kattintson a **meghívása**.

    A külső felhasználó kiválasztott tagként hozzá kell adni.

1. Egy tag vagy a csoport panelen válassza az kattintson **kiválasztása**.

1. A tagsági beállítások panelén válassza a hozzárendelés típusa és időtartama.

    ![Tagsági beállítások](./media/pim-resource-roles-external-users/membership-settings.png)

1. A hozzárendelés befejezéséhez kattintson a **kész** , majd **Hozzáadás**.

    A külső felhasználó szerepkör-hozzárendelés megjelenik a szerepkör-listában.

    ![Külső felhasználó szerepkör-hozzárendelés](./media/pim-resource-roles-external-users/role-assignment.png)

## <a name="activate-role-as-an-external-user"></a>Külső felhasználóként a szerepkör aktiválása

Külső felhasználóként akkor először fogadja el a meghívást, az Azure AD-címtárhoz, valószínűleg a a szerepkör aktiválásához.

1. Nyissa meg az e-mailt a címtárbeli meghívás. Az e-mailt az alábbihoz hasonlóan fog kinézni.

    ![E-mailes meghívó](./media/pim-resource-roles-external-users/email-invite.png)

1. Kattintson a **Ismerkedés** az e-mailben lévő hivatkozásra.

1. Az engedélyek áttekintése után kattintson **elfogadás**.

    ![Engedélyek felülvizsgálata](./media/pim-resource-roles-external-users/invite-accept.png)

1. Előfordulhat, hogy megkérdezi, hogy fogadja el a használati feltételeket, és adja meg, hogy szeretné-e bejelentkezve marad.

    Megnyílik az Azure Portalon. Ha Ön csak jogosult szerepkör, akkor nem kell erőforrásokhoz való hozzáférés.

1. A szerepkör aktiválásához nyissa meg az e-mailt a hivatkozás a szerepkör aktiválása. Az e-mailt az alábbihoz hasonlóan fog kinézni.

    ![E-mailes meghívó](./media/pim-resource-roles-external-users/email-role-assignment.png)

1. Kattintson a **szerepkör aktiválása** a jogosult szerepkörök az PIM-ben lehetőségre.

    ![Saját szerepkörök - jogosult](./media/pim-resource-roles-external-users/my-roles-eligible.png)

1. Művelet alatt kattintson a **aktiválás** hivatkozásra.

    A szerepkör-beállítások attól függően kell adjon meg néhány információt a szerepkör aktiválását.

1. Miután megadta a beállításokat a szerepkörhöz, kattintson a **aktiválás** a szerepkör aktiválását.

    ![Szerepkör aktiválása](./media/pim-resource-roles-external-users/activate-role.png)

    A rendszergazdának kell jóváhagynia a kérést, hacsak kell erőforrásokhoz való hozzáférést.

## <a name="view-activity-for-an-external-user"></a>A külső felhasználók tevékenységek megtekintése

Csakúgy, mint a tag felhasználó megtekintheti a külső felhasználók mire nyomon követheti, hogy a naplók.

1. Rendszergazdaként nyissa meg a PIM, és válassza ki az erőforrást, amely meg van osztva.

1. Kattintson a **erőforrás naplózása** az a tevékenység az adott erőforráshoz. Az alábbiakban látható egy példa a tevékenység egy erőforráscsoporthoz.

    ![Erőforrás naplózása](./media/pim-resource-roles-external-users/audit-resource.png)

1. A külső felhasználó a tevékenység megtekintéséhez kattintson **Azure Active Directory** > **felhasználók** > külső felhasználó.

1. Kattintson a **Auditnaplók** könyvtár számára a naplók megtekintéséhez. Ha szükséges, a szűrők is megadhat.

    ![Címtárnaplózás](./media/pim-resource-roles-external-users/audit-directory.png)

## <a name="next-steps"></a>További lépések

- [A PIM az Azure AD-címtárbeli szerepkörök hozzárendelése](pim-how-to-add-role-to-user.md)
- [Mi az az Azure Active Directory B2B vendégfelhasználói hozzáférés?](../b2b/what-is-b2b.md)
