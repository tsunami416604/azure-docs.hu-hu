---
title: Azure-erőforrás-szerepkörök hozzárendelése a pim-ben – Azure AD - vendégeihez | Microsoft dokumentumok
description: Ismerje meg, hogyan hívhat meg külső vendégfelhasználókat, és hogyan rendelhet hozzá Azure-erőforrásszerepköröket az Azure AD emelt szintű identitáskezelés (PIM) szolgáltatásában.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2efcf77d65fa2f9e203ed805cd7d78b9802ee3aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74021944"
---
# <a name="invite-guest-users-and-assign-azure-resource-roles-in-privileged-identity-management"></a>Vendégfelhasználók meghívása és Azure-erőforrás-szerepkörök hozzárendelése a Kiemelt identitáskezelés ben

Az Azure Active Directory (Azure AD) vendégfelhasználói az Azure AD-n belüli vállalkozások közötti (B2B) együttműködési funkciók részét képezik, így az Azure AD-ben vendégként kezelheti a külső vendégfelhasználókat és -szállítókat. Ha a B2B-együttműködést az Azure AD privilegizált identitáskezeléssel (PIM) kombinálja, kiterjesztheti a megfelelőségi és cégirányítási követelményeket a vendégekre. Használhatja például ezeket a kiemelt identitáskezelési funkciókat az Azure-identitáskezelési feladatokhoz a vendégekkel:

- Hozzáférés hozzárendelése adott Azure-erőforrásokhoz
- Just-in-time hozzáférés engedélyezése
- Hozzárendelés időtartamának és záró dátumának megadása
- Többtényezős hitelesítés megkövetelése aktív hozzárendeléskor vagy aktiváláskor
- Hozzáférés-felülvizsgálatok végrehajtása
- Riasztások és naplók hasznosítása

Ez a cikk ismerteti, hogyan hívhat meg egy vendéget a szervezetbe, és kezelheti az Azure-erőforrásokhoz való hozzáférésüket a kiemelt identitáskezelés használatával.

## <a name="when-would-you-invite-guests"></a>Mikor hívnál vendégeket?

Íme néhány példa arra, hogy mikor hívhat meg vendégeket a szervezetbe:

- Engedélyezze egy külső önálló vállalkozó, amely csak egy e-mail fiókkal rendelkezik, hogy hozzáférjen a projekt Azure-erőforrásaihoz.
- Engedélyezze egy olyan nagy szervezet külső partnerének, amely helyszíni Active Directory összevonási szolgáltatásokat használ a költségalkalmazás eléréséhez.
- Lehetővé teszi, hogy a szervezetben nem található támogatási mérnökök (például a Microsoft támogatási szolgálata) ideiglenesen hozzáférjenek az Azure-erőforráshoz a problémák elhárításához.

## <a name="how-does-collaboration-using-b2b-guests-work"></a>Hogyan működik az együttműködés a B2B vendégek használatával?

B2B együttműködés használataesetén vendégként meghívhat egy külső felhasználót a szervezetbe. A vendég a szervezet felhasználójaként kezelhető, de a vendéget az otthoni szervezetében kell hitelesíteni, nem pedig az Azure AD-szervezetben. Ez azt jelenti, hogy ha a vendég már nem fér hozzá a saját szervezetéhez, akkor elveszíti a hozzáférést a szervezethez. Ha például a vendég kilép a szervezetből, automatikusan elveszíti a hozzáférést az Azure AD-ben megosztott erőforrásokhoz anélkül, hogy bármit is kellene tennie. A B2B-együttműködésről a [Mi a vendégfelhasználói hozzáférés az Azure Active Directory B2B szolgáltatásában című témakörben.](../b2b/what-is-b2b.md)

![A vendégfelhasználó hitelesítését bemutató diagram a kezdőkönyvtárban](./media/pim-resource-roles-external-users/b2b-external-user.png)

## <a name="check-guest-collaboration-settings"></a>Vendégegyüttműködési beállítások ellenőrzése

Annak érdekében, hogy a vendégeket meghívhassa a szervezetbe, ellenőrizze a vendégegyüttműködési beállításokat.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).

1. Válassza az **Azure Active Directory** > **felhasználói beállításait.**

1. Válassza **a Külső együttműködési beállítások kezelése lehetőséget.**

    ![Külső együttműködési beállítások lap, amely az engedélyeket, a meghívást és az együttműködésre vonatkozó korlátozási beállításokat jeleníti meg](./media/pim-resource-roles-external-users/external-collaboration-settings.png)

1. Győződjön meg arról, hogy **a vendégmeghívó szerepkör adminisztrátorai és felhasználói meghívhatják a** kapcsolót, és a kapcsoló **ja.**

## <a name="invite-a-guest-and-assign-a-role"></a>Vendég meghívása és szerepkör hozzárendelése

Privilegizált identitáskezelés használatával meghívhat egy vendéget, és jogosulttá teheti őket egy Azure-erőforrás szerepkörre.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) egy olyan felhasználóval, aki a [kiemelt szerepkör-rendszergazdai](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) vagy [felhasználói rendszergazdai](../users-groups-roles/directory-assign-admin-roles.md#user-administrator) szerepkör tagja.

1. Nyissa meg **az Azure AD kiemelt identitáskezelés .**

1. Válassza az **Azure-erőforrások lehetőséget.**

1. Az **Erőforrás szűrősegítségével** szűrheti a felügyelt erőforrások listáját.

1. Válassza ki a kezelni kívánt erőforrást, például egy erőforrást, erőforráscsoportot, előfizetést vagy felügyeleti csoportot.

    A hatókört csak arra kell beállítania, amire a vendégnek szüksége van.

1. A Kezelés csoportban válassza a **Szerepkörök** lehetőséget az Azure-erőforrások szerepköreinek listájának megtekintéséhez.

    ![Az Azure-erőforrások szerepköreinek listája az aktív és jogosult felhasználók számát mutatja](./media/pim-resource-roles-external-users/resources-roles.png)

1. Válassza ki azt a minimális szerepkört, amire a felhasználónak szüksége lesz.

    ![A szerepkör aktuális tagjait felsoroló kijelölt szerepkörlap](./media/pim-resource-roles-external-users/selected-role.png)

1. A szerepkörlapon válassza a **Tag hozzáadása lehetőséget** az Új hozzárendelés ablaktábla megnyitásához.

1. Kattintson **a Tag vagy csoport kijelölése gombra.**

    ![Új hozzárendelés – Tagok vagy csoportok ablaktáblájának kijelölése felhasználókat és csoportokat tartalmazó panelen a Meghívás lehetőséggel együtt](./media/pim-resource-roles-external-users/select-member-group.png)

1. Vendég meghívásához kattintson a **Meghívás gombra.**

    ![Vendégoldal meghívása mezőkkel egy e-mail cím megadásához és személyes üzenet megadásához](./media/pim-resource-roles-external-users/invite-guest.png)

1. Miután kiválasztott egy vendéget, kattintson **a Meghívás gombra.**

    A vendéget kiválasztott tagként kell hozzáadni.

1. A **Tag vagy csoport kijelölése** ablaktáblán kattintson a **Kijelölés gombra.**

1. A **Tagsági beállítások** ablaktáblán válassza ki a hozzárendelés típusát és időtartamát.

    ![Új hozzárendelés – Tagsági beállítások lap a hozzárendelés típusának, kezdő dátumának és záró dátumának megadására.](./media/pim-resource-roles-external-users/membership-settings.png)

1. A hozzárendelés befejezéséhez válassza a **Kész,** majd a **Hozzáadás lehetőséget.**

    A vendégszerepkör-hozzárendelés megjelenik a szerepkörlistában.

    ![A vendéget jogosultként felsoroló szerepköroldal](./media/pim-resource-roles-external-users/role-assignment.png)

## <a name="activate-role-as-a-guest"></a>Szerepkör aktiválása vendégként

Ha Ön külső felhasználó, el kell fogadnia a meghívást, hogy az Azure AD-szervezet vendége legyen, és esetleg aktiválnia kell a szerepkör-hozzárendelést.

1. Nyissa meg az e-mailt a meghívóval. Az e-mail a következőhöz hasonlóan fog kinézni.

    ![E-mailes meghívó könyvtárnévvel, személyes üzenettel és első lépések hivatkozással](./media/pim-resource-roles-external-users/email-invite.png)

1. Válassza az **Első lépések** hivatkozást az e-mailben.

1. Az engedélyek áttekintése után kattintson az **Elfogadás gombra.**

    ![Engedélyek ellenőrzése lap egy böngészőben olyan engedélyekkel, amelyeket a szervezet át szeretne tekinteni](./media/pim-resource-roles-external-users/invite-accept.png)

1. Előfordulhat, hogy a rendszer megkéri, hogy fogadja el a használati feltételeket, és adja meg, hogy be van-e jelentkezve. Az Azure Portalon, ha *jogosult* egy szerepkörre, még nem lesz hozzáférése az erőforrásokhoz.

1. A szerepkör-hozzárendelés aktiválásához nyissa meg az e-mailt az aktiválási szerepkör-hivatkozással. Az e-mail a következőhöz hasonlóan fog kinézni.

    ![E-mail, amely jelzi, hogy jogosult egy szerepkörre egy Szerepkör aktiválása hivatkozással](./media/pim-resource-roles-external-users/email-role-assignment.png)

1. Válassza **a Szerepkör aktiválása** lehetőséget a jogosult szerepkörök megnyitásához a Kiemelt identitáskezelés ben.

    ![Saját szerepkörök lap a Kiemelt identitáskezelés listában, amely felsorolja a jogosult szerepköröket](./media/pim-resource-roles-external-users/my-roles-eligible.png)

1. A Művelet csoportban jelölje ki az **Aktiválás** hivatkozást.

    A szerepkör beállításaitól függően meg kell adnia néhány információt a szerepkör aktiválásához.

1. Miután megadta a szerepkör beállításait, kattintson az **Aktiválás gombra** a szerepkör aktiválásához.

    ![A laplista hatókörének és beállításainak aktiválása a kezdési idő, az időtartam és az ok megadásához](./media/pim-resource-roles-external-users/activate-role.png)

    Hacsak a rendszergazda nem szükséges a kérelem jóváhagyásához, akkor hozzáféréssel kell rendelkeznie a megadott erőforrásokhoz.

## <a name="view-activity-for-a-guest"></a>Vendég tevékenységének megtekintése

A naplók megtekintésével nyomon követheti, hogy mit csinálnak a vendégek.

1. Rendszergazdaként nyissa meg a Kiemelt identitáskezelés szolgáltatást, és válassza ki a megosztott erőforrást.

1. **Válassza az Erőforrás-naplózás** lehetőséget az adott erőforrás tevékenységének megtekintéséhez. Az alábbi példa egy erőforráscsoport tevékenységére mutat be.

    ![Azure-erőforrások – Az idő, a kérelmező és a művelet naplózási oldala](./media/pim-resource-roles-external-users/audit-resource.png)

1. A vendég tevékenységének megtekintéséhez válassza az **Azure Active Directory** > **felhasználói** > *vendégnevét.*

1. Válassza **a Naplók naplózása** lehetőséget a szervezet naplóinak megtekintéséhez. Szükség esetén szűrőket is megadhat.

    ![A címtár naplóinak felsorolási dátuma, célja, kezdeményezése és tevékenysége](./media/pim-resource-roles-external-users/audit-directory.png)

## <a name="next-steps"></a>További lépések

- [Azure AD-rendszergazdai szerepkörök hozzárendelése a kiemelt identitáskezelésben](pim-how-to-add-role-to-user.md)
- [Mi a vendégfelhasználói hozzáférés az Azure AD B2B együttműködésben?](../b2b/what-is-b2b.md)
