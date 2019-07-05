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
ms.date: 04/09/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 07476c9f5db64a5d107a493022fa3548fe0dae4c
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476346"
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

![Vendégfelhasználó hogyan jelenik meg a címtárban, de hitelesítése a saját címtárukkal bemutató ábra](./media/pim-resource-roles-external-users/b2b-external-user.png)

## <a name="check-guest-collaboration-settings"></a>Vendég együttműködési beállítások ellenőrzése

Ahhoz, hogy is meghívhatnak vendégeket a szervezetbe, ellenőrizni kell a Vendég együttműködési beállítások.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).

1. Kattintson a **Azure Active Directory** > **felhasználói beállítások**.

1. Kattintson a **külső együttműködési beállítások kezelése**.

    ![Külső együttműködési beállítások lapon engedélyt, a meghívás és együttműködési eszközkorlátozásokra vonatkozó beállítások megjelenítése](./media/pim-resource-roles-external-users/external-collaboration-settings.png)

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

    ![Azure-erőforrások szerepkörök listája, amelyek aktív és a jogosult felhasználók számát megjelenítő](./media/pim-resource-roles-external-users/resources-roles.png)

1. Kattintson a minimális szerepkör, amely a felhasználónak lesz szüksége.

    ![A szerepkör tagjait felsoroló szerepkör lapon kiválasztva](./media/pim-resource-roles-external-users/selected-role.png)

1. A szerepkör lapján kattintson a **tag hozzáadása** az új hozzárendelés panel megnyitásához.

1. Kattintson a **tag vagy csoport kijelölése**.

    ![Új hozzárendelés – jelöljön ki egy tagot, vagy a felhasználók és csoportok, amely a meghívás lehetőséget felsoroló ablaktáblán csoport](./media/pim-resource-roles-external-users/select-member-group.png)

1. Vendég meghívása, kattintson a **meghívása**.

    ![Mezőbe írjon be egy e-mail címet, és adja meg a személyes üzenetet tartalmazó oldalt Vendég meghívása](./media/pim-resource-roles-external-users/invite-guest.png)

1. Miután kiválasztotta a Vendég, kattintson a **meghívása**.

    A Vendég kiválasztott tagként hozzá kell adni.

1. Az a **tag vagy csoport kijelölése** ablaktáblán kattintson a **kiválasztása**.

1. Az a **tagsági beállítások** ablaktáblán válassza ki a hozzárendelés típusa és időtartama.

    ![Új hozzárendelés - tagság beállítások lapon adja meg a hozzárendelés típusa, kezdő dátum és záró dátuma beállításokkal](./media/pim-resource-roles-external-users/membership-settings.png)

1. A hozzárendelés befejezéséhez kattintson a **kész** , majd **Hozzáadás**.

    A Vendég szerepkör-hozzárendelés megjelenik a szerepkör-listában.

    ![A Vendég jogosult, listázás szerepkör lap](./media/pim-resource-roles-external-users/role-assignment.png)

## <a name="activate-role-as-a-guest"></a>Vendégként a szerepkör aktiválása

Külső felhasználóként akkor először fogadja az Azure AD-szervezet, valószínűleg a a szerepkör aktiválásához.

1. Nyissa meg az e-mailt a meghívást. Az e-mailt az alábbihoz hasonlóan fog kinézni.

    ![Könyvtár neve, a személyes üzenet és a egy első lépések hivatkozásra a meghívó e-mailt](./media/pim-resource-roles-external-users/email-invite.png)

1. Kattintson a **Ismerkedés** az e-mailben lévő hivatkozásra.

1. Az engedélyek áttekintése után kattintson **elfogadás**.

    ![Tekintse át a böngészőben az engedélyeket, amelyek a szervezet céljai között, hogy tekintse át az listáját – engedélyek lap](./media/pim-resource-roles-external-users/invite-accept.png)

1. Előfordulhat, hogy megkérdezi, hogy fogadja el a használati feltételeket, és adja meg, hogy szeretné-e bejelentkezve marad.

    Megnyílik az Azure Portalon. Ha Ön csak jogosult szerepkör, akkor nem kell erőforrásokhoz való hozzáférés.

1. A szerepkör aktiválásához nyissa meg az e-mailt a hivatkozás a szerepkör aktiválása. Az e-mailt az alábbihoz hasonlóan fog kinézni.

    ![A PIM-aktiválás szerepkör hivatkozásra a szerepkör jogosult jelző e-mailt](./media/pim-resource-roles-external-users/email-role-assignment.png)

1. Kattintson a **szerepkör aktiválása** a jogosult szerepkörök az PIM-ben lehetőségre.

    ![Saját szerepkörök lap az jogosult szerepköröket a PIM-ben](./media/pim-resource-roles-external-users/my-roles-eligible.png)

1. Művelet alatt kattintson a **aktiválás** hivatkozásra.

    A szerepkör-beállítások attól függően kell adjon meg néhány információt a szerepkör aktiválását.

1. Miután megadta a beállításokat a szerepkörhöz, kattintson a **aktiválás** a szerepkör aktiválását.

    ![Oldal listaelem hatókör, valamint a beállításokat, adja meg a kezdő időpontja, időtartama és OK aktiválása](./media/pim-resource-roles-external-users/activate-role.png)

    A rendszergazdának kell jóváhagynia a kérést, hacsak kell erőforrásokhoz való hozzáférést.

## <a name="view-activity-for-a-guest"></a>A Vendég tevékenységek megtekintése

Csakúgy, mint a tag felhasználó megtekintheti a vendégek mire nyomon követheti, hogy a naplók.

1. Rendszergazdaként nyissa meg a PIM, és válassza ki az erőforrást, amely meg van osztva.

1. Kattintson a **erőforrás naplózása** az a tevékenység az adott erőforráshoz. Az alábbiakban látható egy példa a tevékenység egy erőforráscsoporthoz.

    ![Azure-erőforrások – erőforrás-naplózási oldalon az idő, illetve a kérelmező és művelet listázása](./media/pim-resource-roles-external-users/audit-resource.png)

1. A Vendég a tevékenység megtekintéséhez kattintson **Azure Active Directory** > **felhasználók** > Vendég neve.

1. Kattintson a **Auditnaplók** a szervezet számára a naplók megtekintéséhez. Ha szükséges, a szűrők is megadhat.

    ![Directory naplók listaelem dátuma, a cél, kezdeményezte, és a tevékenység](./media/pim-resource-roles-external-users/audit-directory.png)

## <a name="next-steps"></a>További lépések

- [A PIM az Azure AD-rendszergazdai szerepkörök hozzárendelése](pim-how-to-add-role-to-user.md)
- [Mi az az Azure Active Directory B2B vendégfelhasználói hozzáférés?](../b2b/what-is-b2b.md)
