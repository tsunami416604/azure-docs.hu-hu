---
title: Az Azure-erőforrásokhoz való hozzáférés kezelése a RBAC és a Azure Portal használatával | Microsoft Docs
description: Ismerje meg, hogyan kezelheti az Azure-erőforrásokhoz való hozzáférést a felhasználók, csoportok, egyszerű szolgáltatások és felügyelt identitások számára a szerepköralapú hozzáférés-vezérlés (RBAC) és a Azure Portal használatával. Ez tartalmazza a hozzáférés felsorolásának, a hozzáférés adásának és a hozzáférés eltávolításának módját.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8078f366-a2c4-4fbb-a44b-fc39fd89df81
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/24/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 1e9a53c41535c17de2d56227012160c7f6eb25c6
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71337620"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-the-azure-portal"></a>Az Azure-erőforrásokhoz való hozzáférés kezelése a RBAC és a Azure Portal használatával

A [szerepköralapú hozzáférés-vezérlés (RBAC)](overview.md) az Azure-erőforrásokhoz való hozzáférés kezelésének módja. Ez a cikk azt ismerteti, hogyan kezelheti a hozzáférést a Azure Portal használatával. Ha a Azure Active Directoryhoz való hozzáférést szeretné kezelni, tekintse meg [a rendszergazdai szerepkörök megtekintése és társítása a Azure Active Directory-ben](../active-directory/users-groups-roles/directory-manage-roles-portal.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

A szerepkör-hozzárendelések hozzáadásához és eltávolításához a következőket kell tennie:

- `Microsoft.Authorization/roleAssignments/write` és `Microsoft.Authorization/roleAssignments/delete` engedélyek, például a [felhasználói hozzáférés rendszergazdája](built-in-roles.md#user-access-administrator) vagy a [tulajdonos](built-in-roles.md#owner)

## <a name="overview-of-access-control-iam"></a>A hozzáférés-vezérlés (IAM) áttekintése

A **hozzáférés-vezérlés (iam)** az Azure-erőforrásokhoz való hozzáférés kezeléséhez használt panel. Az identitás-és hozzáférés-kezelés néven is ismert, és a Azure Portal több helyén is megjelenik. Az alábbi példa az előfizetés hozzáférés-vezérlés (IAM) paneljét mutatja be.

![Egy előfizetés hozzáférés-vezérlés (IAM) panelje](./media/role-assignments-portal/access-control-numbers.png)

A következő táblázat ismerteti az egyes elemek használatát:

| # | Elem | Mit használ a |
| --- | --- | --- |
| 1 | Az erőforrás, ahol a hozzáférés-vezérlés (IAM) meg van nyitva | Hatókör azonosítása (ebben a példában az előfizetés) |
| 2 | **Hozzáadás** gomb | Szerepkör-hozzárendelések hozzáadása |
| 3 | **Hozzáférés-ellenőrzési** lap | Egyetlen felhasználó szerepkör-hozzárendeléseinek megtekintése |
| 4 | **Szerepkör-hozzárendelések** lap | A szerepkör-hozzárendelések megtekintése az aktuális hatókörben |
| 5 | **Szerepkörök** lap | Az összes szerepkör és engedély megtekintése |

Ahhoz, hogy a leghatékonyabb legyen a hozzáférés-vezérlés (IAM) paneljén, segít abban, hogy a következő három kérdésre válaszoljon a hozzáférés kezelése közben:

1. **Kinek van hozzáférése?**

    A felhasználóra, csoportra, egyszerű szolgáltatásnévre vagy felügyelt identitásra hivatkozik. Ezt *rendszerbiztonsági tag*is nevezik.

1. **Milyen engedélyekre van szükségük?**

    Az engedélyek szerepkörökbe vannak csoportosítva. A számos beépített szerepkör listájából választhat.

1. **Hová kell férnek hozzá?**

    Hol hivatkozik arra az erőforrás-készletre, amelyre a hozzáférés vonatkozik. Ahol lehet felügyeleti csoport, előfizetés, erőforráscsoport vagy egyetlen erőforrás, például egy Storage-fiók. Ezt a *hatókört*nevezzük.

## <a name="open-access-control-iam"></a>Hozzáférés-vezérlés (IAM) megnyitása

Először meg kell határoznia a hozzáférés-vezérlés (IAM) panel megnyitásának helyét. Ez attól függ, hogy milyen erőforrásokhoz szeretné kezelni a hozzáférést. Szeretné kezelni a hozzáférést minden egyes felügyeleti csoportban, minden előfizetésben, minden erőforráscsoport vagy egyetlen erőforrás esetében?

1. A Azure Portal kattintson a **minden szolgáltatás** elemre, majd válassza ki a hatókört. Kiválaszthatja például a **felügyeleti csoportokat**, **előfizetéseket**, **erőforráscsoportokat**vagy egy erőforrást.

1. Kattintson az adott erőforrásra.

1. Kattintson a **Hozzáférés-vezérlés (IAM)** elemre.

    Az alábbi példa az előfizetés hozzáférés-vezérlés (IAM) paneljét mutatja be. Ha itt bármilyen hozzáférés-vezérlési változást hajt végre, azok a teljes előfizetésre érvényesek lesznek.

    ![Egy előfizetés hozzáférés-vezérlés (IAM) panelje](./media/role-assignments-portal/access-control-subscription.png)

## <a name="view-roles-and-permissions"></a>Szerepkörök és engedélyek megtekintése

A szerepkör-definíció a szerepkör-hozzárendeléshez használható engedélyek gyűjteménye. Az Azure több mint 70 [beépített szerepkörrel rendelkezik az Azure-erőforrásokhoz](built-in-roles.md). Az alábbi lépéseket követve megtekintheti az elérhető szerepköröket és engedélyeket.

1. Nyissa meg a **hozzáférés-vezérlést (iam)** bármely hatókörben.

1. Kattintson a **szerepkörök** fülre az összes beépített és egyéni szerepkör listájának megtekintéséhez.

   Az aktuális hatókörben az egyes szerepkörökhöz hozzárendelt felhasználók és csoportok számát láthatja.

   ![Szerepkörök listája](./media/role-assignments-portal/roles-list.png)

1. Kattintson egy egyéni szerepkörre, és tekintse meg a szerepkörhöz rendelt engedélyeket, és tekintse meg a szerepkör engedélyeit is.

   ![Szerepkörök hozzárendelései](./media/role-assignments-portal/role-assignments.png)

## <a name="view-role-assignments"></a>Szerepkör-hozzárendelések megtekintése

A hozzáférés kezelésekor tudnia kell, hogy ki férhet hozzá, milyen engedélyekkel és milyen hatókörrel rendelkezik. Egy felhasználó, csoport, szolgáltatásnév vagy felügyelt identitás hozzáférésének listázásához megtekintheti a szerepkör-hozzárendeléseit.

### <a name="view-role-assignments-for-a-single-user"></a>Egyetlen felhasználó szerepkör-hozzárendeléseinek megtekintése

Az alábbi lépéseket követve megtekintheti egy adott hatókörhöz tartozó egyetlen felhasználó, csoport, szolgáltatásnév vagy felügyelt identitás hozzáférését.

1. Nyissa meg a **hozzáférés-vezérlést (iam)** egy hatókörön, például a felügyeleti csoport, az előfizetés, az erőforráscsoport vagy az erőforrás területen, ahol a hozzáférést szeretné megtekinteni.

1. Kattintson a **hozzáférés-ellenőrzési** lapra.

    ![Hozzáférés-vezérlés – hozzáférés ellenőrzése lap](./media/role-assignments-portal/access-control-check-access.png)

1. A **Keresés** listában válassza ki, hogy milyen rendszerbiztonsági tag számára szeretné megtekinteni a hozzáférést.

1. A keresőmezőbe írja be a karakterláncot a megjelenítendő nevek, e-mail-címek vagy objektumazonosítók kereséséhez.

    ![Hozzáférés-kiválasztási lista kijelölése](./media/role-assignments-portal/check-access-select.png)

1. Kattintson a rendszerbiztonsági tag elemre a **hozzárendelések** panel megnyitásához.

    ![hozzárendelések ablaktábla](./media/role-assignments-portal/check-access-assignments.png)

    Ezen az ablaktáblán megtekintheti a kijelölt rendszerbiztonsági tag és a hatókörhöz rendelt szerepköröket. Ha ezen a hatókörön vannak megtagadási hozzárendelések, vagy a hatókör örökölt, a rendszer felsorolja őket.

### <a name="view-all-role-assignments-at-a-scope"></a>Hatókör összes szerepkör-hozzárendelésének megtekintése

1. Nyissa meg a **hozzáférés-vezérlést (iam)** egy hatókörön, például a felügyeleti csoport, az előfizetés, az erőforráscsoport vagy az erőforrás területen, ahol a hozzáférést szeretné megtekinteni.

1. A **szerepkör-hozzárendelések** lapra kattintva megtekintheti az összes szerepkör-hozzárendelést ezen a hatókörön.

   ![Hozzáférés-vezérlés – szerepkör-hozzárendelések lap](./media/role-assignments-portal/access-control-role-assignments.png)

   A szerepkör-hozzárendelések lapon megtekintheti, hogy ki fér hozzá a hatókörhöz. Látható, hogy egyes szerepkörök hatóköre **erre az erőforrásra** érvényes, míg mások más hatókörökből **(öröklődnek)** . A hozzáférés vagy kifejezetten ehhez az erőforráshoz van hozzárendelve, vagy egy hozzárendelésből a szülő hatókörbe van származtatva.

## <a name="add-a-role-assignment"></a>Szerepkör-hozzárendelés hozzáadása

A RBAC-ben a hozzáférés biztosításához hozzá kell rendelnie egy szerepkört egy felhasználóhoz, egy csoporthoz, egy egyszerű szolgáltatáshoz vagy egy felügyelt identitáshoz. A következő lépésekkel engedélyezheti a hozzáférést a különböző hatókörökhöz.

### <a name="assign-a-role-at-a-scope"></a>Szerepkör társítása hatókörhöz

1. Nyissa meg a **hozzáférés-vezérlést (iam)** egy olyan hatókörön (például felügyeleti csoport, előfizetés, erőforráscsoport vagy erőforrás), amelyhez hozzáférést szeretne biztosítani.

1. A **szerepkör-hozzárendelések** lapra kattintva megtekintheti az összes szerepkör-hozzárendelést ezen a hatókörön.

1. Kattintson a **hozzáadás** > **szerepkör-hozzárendelés hozzáadása** elemre a szerepkör-hozzárendelés hozzáadása ablaktábla megnyitásához.

   Ha nem rendelkezik jogosultsággal a szerepkörök hozzárendeléséhez, a szerepkör-hozzárendelés hozzáadása lehetőség le lesz tiltva.

   ![Menü hozzáadása](./media/role-assignments-portal/add-menu.png)

   ![Szerepkör-hozzárendelési ablaktábla hozzáadása](./media/role-assignments-portal/add-role-assignment.png)

1. A **Szerepkör** legördülő listájában válasszon ki egy szerepkört, például a **Virtuális gépek közreműködője** szerepkört.

1. A **Select (kiválasztás** ) listán válasszon ki egy felhasználót, csoportot, szolgáltatásnevet vagy felügyelt identitást. Ha a listában nem látja a rendszerbiztonsági tagot, írhat a **Kiválasztás** mezőbe megjelenítendő nevek, e-mail-címek és objektumazonosítók a címtárban történő kereséséhez.

1. Kattintson a **Mentés** gombra a szerepkör hozzárendeléséhez.

   Néhány pillanat elteltével a rendszerbiztonsági tag a kiválasztott hatókörhöz rendeli a szerepkört.

### <a name="assign-a-user-as-an-administrator-of-a-subscription"></a>Felhasználó hozzáadása egy előfizetés adminisztrátoraként

Ahhoz, hogy egy felhasználó egy Azure-előfizetés rendszergazdája legyen, rendelje hozzá őket a [tulajdonosi](built-in-roles.md#owner) szerepkörhöz az előfizetés hatókörében. A Tulajdonos szerepkör teljes hozzáférést biztosít a felhasználónak az előfizetés összes erőforrásához, beleértve a hozzáférés mások számára való delegálásának jogát is. Ez ugyanúgy történik, mint bármely más szerepkör-hozzárendelés esetében.

1. A Azure Portal kattintson a **minden szolgáltatás** , majd az **előfizetések**elemre.

1. Kattintson arra az előfizetésre, amelynél a hozzáférést biztosítani kívánja.

1. Kattintson a **Hozzáférés-vezérlés (IAM)** elemre.

1. Kattintson a **Szerepkör-hozzárendelések** lapra az előfizetés szerepkör-hozzárendeléseinek megtekintéséhez.

1. Kattintson a **hozzáadás** > **szerepkör-hozzárendelés hozzáadása** elemre a szerepkör-hozzárendelés hozzáadása ablaktábla megnyitásához.

   Ha nem rendelkezik jogosultsággal a szerepkörök hozzárendeléséhez, a szerepkör-hozzárendelés hozzáadása lehetőség le lesz tiltva.

   ![Menü hozzáadása](./media/role-assignments-portal/add-menu.png)

   ![Szerepkör-hozzárendelési ablaktábla hozzáadása](./media/role-assignments-portal/add-role-assignment.png)

1. A **Szerepkör** legördülő listában válassza a **Tulajdonos** szerepkört.

1. A **kiválasztási** listában válasszon ki egy felhasználót. Ha a listában nem látja a felhasználót, beírhatja a nevét a **kiválasztási** mezőbe, hogy rákeressen a megjelenítendő neveket és e-mail-címeket tartalmazó címtárban.

1. Kattintson a **Mentés** gombra a szerepkör hozzárendeléséhez.

   Néhány pillanat múlva a rendszer hozzárendeli a felhasználót a Tulajdonos szerepkörhöz az előfizetés hatókörében.

## <a name="remove-role-assignments"></a>Szerepkör-hozzárendelések eltávolítása

Az RBAC-ben hozzáférés eltávolításához egy szerepkör-hozzárendelést kell eltávolítania. A hozzáférés eltávolításához kövesse az alábbi lépéseket.

1. Nyissa meg a **hozzáférés-vezérlést (iam)** egy hatókörön, például a felügyeleti csoport, az előfizetés, az erőforráscsoport vagy az erőforrás területen, ahol el szeretné távolítani a hozzáférést.

1. Kattintson a **Szerepkör-hozzárendelések** lapra az előfizetés szerepkör-hozzárendeléseinek megtekintéséhez.

1. A szerepkör-hozzárendelések listájában jelölje be az eltávolítani kívánt szerepkör-hozzárendeléssel rendelkező rendszerbiztonsági tag melletti jelölőnégyzetet.

   ![Szerepkör-hozzárendelés eltávolítási üzenete](./media/role-assignments-portal/remove-role-assignment-select.png)

1. Kattintson az **Eltávolítás**gombra.

   ![Szerepkör-hozzárendelés eltávolítási üzenete](./media/role-assignments-portal/remove-role-assignment.png)

1. A megjelenő szerepkör-hozzárendelés eltávolítása üzenetben kattintson az **Igen**gombra.

    Az örökölt szerepkör-hozzárendeléseket nem lehet eltávolítani. Ha örökölt szerepkör-hozzárendelést kell eltávolítania, azt abban a hatókörben kell megtennie, ahol a szerepkör-hozzárendelés létrejött. A **hatókör** oszlopban, a **(örökölt)** elem mellett található egy hivatkozás, amely arra a hatókörre mutat, ahol ez a szerepkör hozzá van rendelve. Lépjen az ott szereplő hatókörhöz a szerepkör-hozzárendelés eltávolításához.

   ![Szerepkör-hozzárendelés eltávolítási üzenete](./media/role-assignments-portal/remove-role-assignment-inherited.png)

## <a name="next-steps"></a>Következő lépések

* [Oktatóanyag: felhasználói hozzáférés biztosítása az Azure-erőforrásokhoz a RBAC és a Azure Portal használatával](quickstart-assign-role-user-portal.md)
* [Oktatóanyag: felhasználói hozzáférés biztosítása az Azure-erőforrásokhoz a RBAC és a Azure PowerShell használatával](tutorial-role-assignments-user-powershell.md)
* [Azure-erőforrások RBAC kapcsolatos hibák](troubleshooting.md)
* [Erőforrások rendszerezése az Azure-beli felügyeleti csoportokkal](../governance/management-groups/overview.md)
