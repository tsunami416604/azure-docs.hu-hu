---
title: Vendégek meghívása és Azure-beli erőforrás-szerepkörök kiosztása a PIM-Azure Active Directoryban | Microsoft Docs
description: Ismerje meg, hogyan hívhat meg külső vendég felhasználókat, és hogyan rendelhet hozzá Azure-beli erőforrás-szerepköröket Azure AD Privileged Identity Management (PIM) szolgáltatásban.
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
ms.openlocfilehash: f3e01b58a2a2fc6f93ae5ccc15e200a0cea69a0c
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804216"
---
# <a name="invite-guest-users-and-assign-azure-resource-roles-in-pim"></a>Vendég felhasználók meghívása és Azure-erőforrás-szerepkörök kiosztása a PIM-ben

A Azure Active Directory (Azure AD) vállalatok közötti (B2B) funkciók az Azure AD-n belül olyan képességek, amelyek lehetővé teszik a szervezetek számára, hogy bármely fiók használatával működjenek együtt a külső vendég felhasználókkal (vendégekkel) és a szállítókkal. Ha Azure AD Privileged Identity Management (PIM) használatával kombinálja a B2B-t, továbbra is alkalmazhatja a megfelelőségi és irányítási követelményeket a vendégek számára. Használhatja például a következő PIM-funkciókat az Azure Identity-feladatokhoz a vendégekkel:

- Hozzáférés kiosztása adott Azure-erőforrásokhoz
- Igény szerinti hozzáférés engedélyezése
- Hozzárendelés időtartamának és befejezési dátumának megadása
- MFA megkövetelése aktív hozzárendelés vagy aktiválás esetén
- Hozzáférési felülvizsgálatok végrehajtása
- Riasztások és naplófájlok kihasználása

Ez a cikk bemutatja, hogyan hívhat meg egy vendéget a szervezet számára, és hogyan kezelheti az Azure-erőforrásokhoz való hozzáférését Privileged Identity Management használatával.

## <a name="when-would-you-invite-guests"></a>Mikor hívja meg a vendégeket?

Íme néhány példa arra, hogy mikor hívhatja meg a szervezet vendégeit:

- A projekthez tartozó Azure-erőforrásokhoz való hozzáféréshez csak egy e-mail-fiókkal rendelkező külső önálló szolgáltató engedélyezése.
- Külső partner engedélyezése egy olyan nagyvállalatnál, amely helyszíni Active Directory összevonási szolgáltatások (AD FS) használatával fér hozzá a költségelszámolás alkalmazásához.
- Lehetővé teszi, hogy a szervezeten kívüli támogatási mérnökök (például a Microsoft támogatási szolgálata) átmenetileg hozzáférjenek az Azure-erőforráshoz a hibák elhárítása érdekében.

## <a name="how-does-collaboration-using-b2b-guests-work"></a>Hogyan működik a B2B-vendégeket használó együttműködés?

Ha B2B-együttműködést használ, egy külső felhasználót is meghívhat vendégként a szervezet számára. A vendég úgy tűnik, hogy a szervezetében van, de a vendégnek nincs hitelesítő adata társítva. Ha egy vendég hitelesítése szükséges, akkor azokat hitelesíteni kell a saját szervezetében, és nem a cégen belül. Ez azt jelenti, hogy ha a vendég már nem fér hozzá a saját szervezetéhez, akkor is elveszti a hozzáférést a szervezethez. Ha például a vendég elhagyja a szervezetét, automatikusan elveszíti az Azure AD-vel megosztott összes erőforráshoz való hozzáférést anélkül, hogy bármit el kellene végeznie. További információ a B2B-ről: [Mi a vendég felhasználói hozzáférés Azure Active Directory B2B-ben?](../b2b/what-is-b2b.md).

![Azt bemutató ábra, hogy a vendég felhasználó hogyan jelenik meg a címtárban, de a saját címtárában van hitelesítve](./media/pim-resource-roles-external-users/b2b-external-user.png)

## <a name="check-guest-collaboration-settings"></a>Vendég együttműködési beállítások keresése

Győződjön meg arról, hogy meg tudja hívni a vendégeket a szervezetbe, ellenőrizze a vendég együttműködési beállításait.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).

1. Kattintson **Azure Active Directory** > **felhasználói beállítások**elemre.

1. Kattintson a **külső együttműködési beállítások kezelése**lehetőségre.

    ![Külső csoportmunka-beállítások lap, amely az engedélyek, a meghívás és az együttműködés korlátozására vonatkozó beállításokat jeleníti meg](./media/pim-resource-roles-external-users/external-collaboration-settings.png)

1. Győződjön meg arról, hogy a **vendég meghívó szerepkörben található rendszergazdák és felhasználók meghívhatják** a kapcsolót **Igen**értékre.

## <a name="invite-a-guest-and-assign-a-role"></a>Vendég meghívása és szerepkör kiosztása

Ha a PIM-t használja, meghívhat egy vendéget, és jogosult lehet egy Azure-beli erőforrás-szerepkörre, ugyanúgy, mint a tag felhasználó.

1. Jelentkezzen be [Azure Portal](https://portal.azure.com/) egy olyan felhasználóval, aki tagja a [Kiemelt szerepkörű rendszergazda](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) vagy a [felhasználói rendszergazda](../users-groups-roles/directory-assign-admin-roles.md#user-administrator) szerepkörnek.

1. Nyissa meg **Azure ad Privileged Identity Management**.

1. Kattintson az **Azure-erőforrások**elemre.

1. Az **Erőforrás-szűrő** használatával szűrheti a felügyelt erőforrások listáját.

1. Kattintson a kezelni kívánt erőforrásra, például egy erőforrásra, egy erőforráscsoport, egy előfizetés vagy egy felügyeleti csoportra.

    A hatókört csak a vendég igényeinek megfelelően állítsa be.

1. A kezelés alatt kattintson a **szerepkörök** elemre az Azure-erőforrások szerepköreinek megjelenítéséhez.

    ![Az aktív és jogosult felhasználók számát mutató Azure-erőforrások szerepköreinek listája](./media/pim-resource-roles-external-users/resources-roles.png)

1. Kattintson a felhasználó által igényelt minimális szerepkörre.

    ![A szerepkör aktuális tagjait felsoroló kiválasztott szerepkör lap](./media/pim-resource-roles-external-users/selected-role.png)

1. A szerepkör lapon kattintson a **tag hozzáadása** elemre az új hozzárendelés ablaktábla megnyitásához.

1. Kattintson **a tag vagy csoport kiválasztása**elemre.

    ![Új hozzárendelés – válasszon ki egy tag vagy csoport ablaktáblát, amely felsorolja a felhasználókat és a csoportokat, valamint egy Meghívási lehetőséget.](./media/pim-resource-roles-external-users/select-member-group.png)

1. Vendég meghívásához kattintson a **meghívás**elemre.

    ![Vendég oldal meghívása e-mail-cím megadásához és személyes üzenet megadásához](./media/pim-resource-roles-external-users/invite-guest.png)

1. A vendég kiválasztása után kattintson a **meghívás**elemre.

    A vendéget kijelölt tagként kell hozzáadni.

1. A **tag vagy csoport kiválasztása** ablaktáblán kattintson a **kiválasztás**elemre.

1. A **tagsági beállítások** ablaktáblán válassza ki a hozzárendelés típusát és időtartamát.

    ![Új hozzárendelés – tagsági beállítások lap a hozzárendelési típus, a kezdő dátum és a befejezési dátum megadására szolgáló beállításokkal](./media/pim-resource-roles-external-users/membership-settings.png)

1. A hozzárendelés befejezéséhez kattintson a **kész** , majd a **Hozzáadás**elemre.

    A vendég szerepkör-hozzárendelés megjelenik a szerepkör listájában.

    ![A szerepkör lap jogosultként tartalmazza a vendéget](./media/pim-resource-roles-external-users/role-assignment.png)

## <a name="activate-role-as-a-guest"></a>Szerepkör aktiválása vendégként

Külső felhasználóként először el kell fogadnia a meghívást az Azure AD-szervezet számára, és esetleg aktiválni kell a szerepkört.

1. Nyissa meg az e-mailt a meghívóval. Az e-mail a következőhöz hasonlóan fog kinézni.

    ![E-mail meghívása a címtár nevével, személyes üzenettel és az első lépések hivatkozással](./media/pim-resource-roles-external-users/email-invite.png)

1. Kattintson az **első lépések** hivatkozásra az e-mailben.

1. Az engedélyek áttekintése után kattintson az **elfogadás**gombra.

    ![Tekintse át az engedélyek lapot egy böngészőben azon engedélyek listájával, amelyeket a szervezet szeretne áttekinteni](./media/pim-resource-roles-external-users/invite-accept.png)

1. Előfordulhat, hogy a rendszer felszólítja a használati feltételek elfogadására, és megadja, hogy szeretne-e bejelentkezni.

    Megnyílik a Azure Portal. Ha csak egy szerepkörre jogosult, nem férhet hozzá az erőforrásokhoz.

1. A szerepkör aktiválásához nyissa meg az e-mailt az aktiválási szerepkör hivatkozásával. Az e-mail a következőhöz hasonlóan fog kinézni.

    ![A PIM-ből származó e-mail-üzenet, amely azt jelzi, hogy jogosult a szerepkör aktiválása szerepkörre hivatkozással.](./media/pim-resource-roles-external-users/email-role-assignment.png)

1. Kattintson a **szerepkör aktiválása** elemre, hogy megnyissa a jogosult szerepköröket a PIM-ben.

    ![A PIM saját szerepkörök lapja, amely felsorolja a jogosult szerepköröket](./media/pim-resource-roles-external-users/my-roles-eligible.png)

1. A művelet területen kattintson az **aktiválás** hivatkozásra.

    A szerepkör beállításaitól függően meg kell adnia néhány információt a szerepkör aktiválásához.

1. Miután megadta a szerepkör beállításait, kattintson az **aktiválás** gombra a szerepkör aktiválásához.

    ![Az oldal-lista hatókörének és beállításainak aktiválása a kezdési idő, az időtartam és az OK megadásához](./media/pim-resource-roles-external-users/activate-role.png)

    Ha a rendszergazda nem szükséges a kérelem jóváhagyásához, hozzáféréssel kell rendelkeznie a megadott erőforrásokhoz.

## <a name="view-activity-for-a-guest"></a>Vendég tevékenységének megtekintése

A felhasználókhoz hasonlóan a naplók is megtekinthetők, hogy nyomon kövessék, mit csinálnak a vendégek.

1. Rendszergazdaként nyissa meg a PIM-t, és válassza ki a megosztott erőforrást.

1. Az erőforrás tevékenységének megtekintéséhez kattintson az **erőforrás-naplózás** elemre. Az alábbi példa egy erőforráscsoport tevékenységére mutat példát.

    ![Azure-erőforrások – erőforrás-naplózási oldal, amely az idő, a kérelmező és a művelet listáját tartalmazza](./media/pim-resource-roles-external-users/audit-resource.png)

1. A vendég tevékenységének megtekintéséhez kattintson **Azure Active Directory** > **felhasználók** > vendég neve elemre.

1. Kattintson a **naplók** elemre a szervezet naplófájljainak megtekintéséhez. Szükség esetén szűrők megadására is lehetőség van.

    ![Címtár-naplózási naplók listázási dátuma, cél, kezdeményező és tevékenység](./media/pim-resource-roles-external-users/audit-directory.png)

## <a name="next-steps"></a>További lépések

- [Azure AD-rendszergazdai szerepkörök kiosztása a PIM-ben](pim-how-to-add-role-to-user.md)
- [Mi a vendég felhasználói hozzáférés a Azure Active Directory B2B-ben?](../b2b/what-is-b2b.md)
