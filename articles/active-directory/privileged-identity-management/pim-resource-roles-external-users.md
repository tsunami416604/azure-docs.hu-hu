---
title: Vendégek meghívása, és rendelje hozzá a PIM - Azure Active Directory Azure-erőforrások szerepköreihez |} A Microsoft Docs
description: Ismerje meg, hogyan külső vendégfelhasználók meghívása és az Azure-erőforrás szerepkörök hozzárendelése az Azure AD Privileged Identity Management (PIM).
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
ms.date: 03/13/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 68c5e5e2ed0d3ec767a239439476a98bac73bcb4
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2019
ms.locfileid: "58576879"
---
# <a name="invite-guest-users-and-assign-azure-resource-roles-in-pim"></a>Vendégfelhasználók meghívása és az Azure-erőforrás szerepköröket a PIM

Az Azure Active Directory (Azure AD)--vállalatközi (B2B) képességeket, amely lehetővé teszi a szervezetek működhet együtt a külső vendégfelhasználóknak (vendég) és a szállítók minden olyan fiók használatával az Azure AD-n belül. Ha kombinálja az Azure AD Privileged Identity Management (PIM) B2B, folytathatja a megfelelőségi és szabályozási követelmények vonatkoznak a vendégek. Például használhatja ezeket a PIM-funkciókat az Azure-identitás feladatokat a vendégek:

- Adott Azure-erőforrásokhoz való hozzáférés hozzárendelése
- Igény szerinti elérésének engedélyezése
- Adja meg a hozzárendelés időtartam és a záró dátum
- Többtényezős hitelesítés megkövetelése aktív hozzárendeléskor vagy az aktiválás
- Hajtsa végre a hozzáférési felülvizsgálatok
- Riasztások, és a naplók

Ez a cikk ismerteti a szervezet számára Vendég meghívása és a Privileged Identity Management használata Azure-erőforrásokhoz való hozzáférés kezelése.

## <a name="when-would-you-invite-guests"></a>Ha szeretné meghívni vendégek?

Ha a szervezet számára, előfordulhat, hogy meghívhatnak vendégeket az alábbiakban néhány példaforgatókönyvek:

- Lehetővé teszi egy külső önálló szállító, amely csak a projekt az Azure-erőforrások eléréséhez e-mail fiókja van.
- Egy külső partner egy nagy szervezet, amely a helyszíni Active Directory összevonási szolgáltatások használja a költségek alkalmazáshoz való hozzáférés engedélyezése.
- Lehetővé teszi a támogatási szakértők nem a szervezetben (például a Microsoft támogatási) kapcsolatos problémák elhárítása az Azure-erőforrás átmenetileg el.

## <a name="how-does-collaboration-using-b2b-guests-work"></a>Hogyan történik az együttműködés B2B használatával vendégek munkahelyi?

B2B-együttműködés használata esetén meghívhatja egy külső felhasználót vendégként a szervezet számára. A Vendég úgy tűnik, hogy a szervezetben, de a vendég nem rendelkezik a hozzá társított hitelesítő adatokat. Amikor egy Vendég hitelesítését, hitelesíteniük kell a szervezet és a szervezet nem az. Ez azt jelenti, hogy ha a Vendég már nem rendelkezik hozzáféréssel a saját szervezet, azokat is elveszíti a hozzáférést a szervezet számára. Például ha a Vendég a szervezetből kilépő, azok automatikusan férhet hozzá, akkor megosztott erőforrások velük az Azure AD-ban anélkül, hogy bármi egyebet kellene. B2B kapcsolatos további információkért lásd: [Mi az az Azure Active Directory B2B vendégfelhasználói hozzáférés?](../b2b/what-is-b2b.md).

![B2B és Vendég](./media/pim-resource-roles-external-users/b2b-external-user.png)

## <a name="check-guest-collaboration-settings"></a>Vendég együttműködési beállítások ellenőrzése

Ahhoz, hogy is meghívhatnak vendégeket a szervezetbe, ellenőrizni kell a Vendég együttműködési beállítások.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).

1. Kattintson a **Azure Active Directory** > **felhasználói beállítások**.

1. Kattintson a **külső együttműködési beállítások kezelése**.

    ![Külső együttműködési beállítások](./media/pim-resource-roles-external-users/external-collaboration-settings.png)

1. Győződjön meg arról, hogy a **rendszergazdák és a vendégmeghívó szerepkörű felhasználók küldhetnek meghívót** kapcsoló értéke **Igen**.

## <a name="invite-a-guest-and-assign-a-role"></a>Vendég meghívása és a egy szerepkör hozzárendelése

A PIM használata esetén Vendég meghívása, és győződjön meg az Azure-erőforrás szerepkörhöz hasonlóan a tag felhasználó jogosult.

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com/) egy felhasználóval, amely tagja a [kiemelt szerepkörű rendszergazda](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) vagy [felhasználói rendszergazdája](../users-groups-roles/directory-assign-admin-roles.md#user-administrator) szerepkör.

1. Nyissa meg **az Azure AD Privileged Identity Management**.

1. Kattintson a **Azure-erőforrások**.

1. Használja a **Erőforrásszűrő** a felügyelt erőforrások szűréséhez.

1. Kattintson az erőforrás szeretne felügyelni, mint például egy erőforrást, erőforráscsoport, előfizetés vagy a felügyeleti csoport.

    A hatókör csak szüksége van a Vendég milyen kell beállítania.

1. Kattintson a kezelés, **szerepkörök** szerepkörök az Azure-erőforrások listájának megtekintéséhez.

    ![Azure-erőforrások szerepkörök](./media/pim-resource-roles-external-users/resources-roles.png)

1. Kattintson a minimális szerepkör, amely a felhasználónak lesz szüksége.

    ![A kijelölt szerepkör](./media/pim-resource-roles-external-users/selected-role.png)

1. A szerepkör lapján kattintson a **tag hozzáadása** az új hozzárendelés panel megnyitásához.

1. Kattintson a **tag vagy csoport kijelölése**.

    ![Tag vagy csoport kijelölése](./media/pim-resource-roles-external-users/select-member-group.png)

1. Vendég meghívása, kattintson a **meghívása**.

    ![Vendég meghívása](./media/pim-resource-roles-external-users/invite-guest.png)

1. Miután kiválasztotta a Vendég, kattintson a **meghívása**.

    A Vendég kiválasztott tagként hozzá kell adni.

1. Az a **tag vagy csoport kijelölése** ablaktáblán kattintson a **kiválasztása**.

1. Az a **tagsági beállítások** ablaktáblán válassza ki a hozzárendelés típusa és időtartama.

    ![Tagsági beállítások](./media/pim-resource-roles-external-users/membership-settings.png)

1. A hozzárendelés befejezéséhez kattintson a **kész** , majd **Hozzáadás**.

    A Vendég szerepkör-hozzárendelés megjelenik a szerepkör-listában.

    ![Vendég szerepkör-hozzárendelés](./media/pim-resource-roles-external-users/role-assignment.png)

## <a name="activate-role-as-a-guest"></a>Vendégként a szerepkör aktiválása

Külső felhasználóként akkor először fogadja az Azure AD-szervezet, valószínűleg a a szerepkör aktiválásához.

1. Nyissa meg az e-mailt a meghívást. Az e-mailt az alábbihoz hasonlóan fog kinézni.

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

## <a name="view-activity-for-a-guest"></a>A Vendég tevékenységek megtekintése

Csakúgy, mint a tag felhasználó megtekintheti a vendégek mire nyomon követheti, hogy a naplók.

1. Rendszergazdaként nyissa meg a PIM, és válassza ki az erőforrást, amely meg van osztva.

1. Kattintson a **erőforrás naplózása** az a tevékenység az adott erőforráshoz. Az alábbiakban látható egy példa a tevékenység egy erőforráscsoporthoz.

    ![Erőforrás naplózása](./media/pim-resource-roles-external-users/audit-resource.png)

1. A Vendég a tevékenység megtekintéséhez kattintson **Azure Active Directory** > **felhasználók** > Vendég neve.

1. Kattintson a **Auditnaplók** a szervezet számára a naplók megtekintéséhez. Ha szükséges, a szűrők is megadhat.

    ![szervezet naplózása](./media/pim-resource-roles-external-users/audit-directory.png)

## <a name="next-steps"></a>További lépések

- [A PIM az Azure AD-rendszergazdai szerepkörök hozzárendelése](pim-how-to-add-role-to-user.md)
- [Mi az az Azure Active Directory B2B vendégfelhasználói hozzáférés?](../b2b/what-is-b2b.md)
