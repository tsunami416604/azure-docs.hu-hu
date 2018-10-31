---
title: A PIM az Azure AD-címtárbeli szerepkörök hozzárendelése |} A Microsoft Docs
description: Ismerje meg az Azure AD Privileged Identity Management (PIM) az Azure AD-címtárbeli szerepkörök hozzárendelése.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: pim
ms.date: 10/30/2018
ms.author: rolyon
ms.openlocfilehash: 5f0b5d1695603a7cd2a3c7ac1dbc484e44257d88
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50249611"
---
# <a name="assign-azure-ad-directory-roles-in-pim"></a>A PIM az Azure AD-címtárbeli szerepkörök hozzárendelése

Az Azure Active Directory (Azure AD), a globális rendszergazdák kezdeményezhetik **állandó** directory szerepkör-hozzárendeléseket. A szerepkör-hozzárendelések hozható létre a [az Azure portal](../users-groups-roles/directory-assign-admin-roles.md) vagy [PowerShell-parancsok](/powershell/module/azuread#directory_roles).

Az Azure AD Privileged Identity Management (PIM) szolgáltatást is lehetővé teszi a kiemelt szerepkörű rendszergazdák állandó directory szerepkör-hozzárendeléseket. Ezenkívül a kiemelt szerepkörű rendszergazdák felhasználókat tehet **jogosult** -címtárbeli szerepkörökhöz tartozó. A jogosult rendszergazda aktiválhatja a szerepkört, amikor szükségük van rá, majd rájuk vonatkozó engedélyek lejárnak, amint kész is van. A PIM használata kezelheti szerepkörökkel kapcsolatos további információkért lásd: [PIM segítségével kezelheti az Azure AD-címtárbeli szerepkörök](pim-roles.md).

## <a name="make-a-user-eligible-for-a-role"></a>Győződjön meg arról, a felhasználó jogosult szerepkör

Kövesse az alábbi lépéseket, hogy egy felhasználó jogosult az Azure AD-címtár szerepkörhöz.

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com/) egy felhasználóval, amely tagja a [kiemelt szerepkörű rendszergazda](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) szerepkör.

    Egy másik rendszergazdai hozzáférést a PIM kezelése kapcsolatos információkért lásd: [hozzáférés biztosítása más rendszergazdák számára, miközben a PIM kezelése](pim-how-to-give-access-to-pim.md).

1. Nyissa meg **az Azure AD Privileged Identity Management**.

    Ha a PIM még nem indult el az Azure Portalon még, ugorjon [használatához a PIM](pim-getting-started.md).

1. Kattintson a **az Azure AD-címtárbeli szerepkörök**.

1. Kattintson a **szerepkörök** vagy **tagok**.

    ![Azure AD-címtárbeli szerepkörök](./media/pim-how-to-add-role-to-user/pim-directory-roles.png)

1. Kattintson a **tag hozzáadása** felügyelt tagok hozzáadása párbeszédpanel megnyitásához.

1. Kattintson a **Szerepkörválasztás**, kattintson a kezelése, és kattintson a kívánt szerepkört **kiválasztása**.

    ![Szerepkörválasztás](./media/pim-how-to-add-role-to-user/pim-select-a-role.png)

1. Kattintson a **tagok kiválasztása**, válassza ki a felhasználókat, rendelje hozzá a szerepkörhöz, és kattintson a kívánt **kiválasztása**.

    ![Szerepkörválasztás](./media/pim-how-to-add-role-to-user/pim-select-members.png)

1. Felügyelt tagok kattintson a Hozzáadás **OK** a felhasználó hozzáadása a szerepkörhöz.

1. A szerepkörök listájában kattintson a szerepkörrel, csak a tagok listájának megtekintéséhez.

     Ha a szerepkör van hozzárendelve, a kiválasztott felhasználó megjelenik a tagok listája, **jogosult** a szerepkörhöz.

    ![A szerepkör jogosult felhasználó](./media/pim-how-to-add-role-to-user/pim-directory-role-eligible.png)

1. Most, hogy a felhasználó nem jogosult a szerepkörhöz, tudassa vele, hogy azokat, az aktiválást található utasítások szerint [PIM a saját Azure AD-címtárbeli szerepkörök aktiválása](pim-how-to-activate-role.md).

    Jogosult rendszergazdák rendszer megkéri, hogy regisztrálja az Azure multi-factor Authentication (MFA) az aktiválás során. Ha a felhasználó MFA-kiszolgáló nem tudja regisztrálni, vagy egy Microsoft-fiók használata (általában @outlook.com), végre kell hajtania az összes szerepkörökhöz állandó.

## <a name="make-a-role-assignment-permanent"></a>Állandóvá tétel szerepkör-hozzárendelés

Alapértelmezés szerint az új felhasználók jogosultak csak olyan címtárbeli szerepkörrel. Ha azt szeretné, hogy a szerepkör-hozzárendelés véglegessé, kövesse az alábbi lépéseket.

1. Nyissa meg **az Azure AD Privileged Identity Management**.

1. Kattintson a **az Azure AD-címtárbeli szerepkörök**.

1. Kattintson a **tagok**.

    ![A tagok listája](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Kattintson egy **jogosult** állandóvá kívánt szerepkört.

1. Kattintson a **további** majd **márka perm**.

    ![Állandóvá tétel szerepkör-hozzárendelés](./media/pim-how-to-add-role-to-user/pim-make-perm.png)

    A szerepkör most már szerepel az **állandó**.

    ![Az állandó módosítása tagok listája](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members-permanent.png)

## <a name="remove-a-user-from-a-role"></a>Felhasználó eltávolítása a szerepkörből

Felhasználók eltávolítása a szerepkör-hozzárendeléseket, de győződjön meg arról, hogy mindig van legalább egy felhasználót, aki egy állandó globális rendszergazda. Ha nem biztos abban, hogy mely felhasználók továbbra is szükséges a szerepkör-hozzárendeléseket, [a szerepkörhöz tartozó hozzáférési felülvizsgálat indítása](pim-how-to-start-security-review.md).

Kövesse az alábbi lépéseket egy adott felhasználó eltávolítása a címtárbeli szerepkört.

1. Nyissa meg **az Azure AD Privileged Identity Management**.

1. Kattintson a **az Azure AD-címtárbeli szerepkörök**.

1. Kattintson a **tagok**.

    ![A tagok listája](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Kattintson az eltávolítani kívánt szerepkör-hozzárendelés.

1. Kattintson a **további** majd **eltávolítása**.

    ![A szerepkör eltávolítása](./media/pim-how-to-add-role-to-user/pim-remove-role.png)

1. Az üzenet, amely arra kéri, hogy erősítse meg, kattintson a **Igen**.

    ![A szerepkör eltávolítása](./media/pim-how-to-add-role-to-user/pim-remove-role-confirm.png)

    A szerepkör-hozzárendelés eltávolítása.

## <a name="authorization-error-when-assigning-roles"></a>Engedélyezési hiba a szerepkörök hozzárendelése

Ha nemrég engedélyezte a PIM-előfizetéssel, és meg, hogy olyan címtárbeli szerepkörrel jogosult felhasználó hitelesítési hibaüzenetet kap, lehet, mert az MS-PIM szolgáltatás típusú rendszerbiztonsági taghoz még nem rendelkezik megfelelő engedélyekkel. Az MS-PIM szolgáltatás típusú rendszerbiztonsági taghoz kell rendelkeznie a [felhasználói hozzáférés rendszergazdája](../../role-based-access-control/built-in-roles.md#user-access-administrator) szerepkör más szerepkörök hozzárendelése. Várnia, amíg az MS-PIM hozzá van rendelve a felhasználói hozzáférés rendszergazdájának szerepköre, rendelheti hozzá kézzel.

Kövesse az alábbi lépéseket a felhasználói hozzáférés rendszergazdája szerepkör hozzárendelése az előfizetéshez tartozó egyszerű MS-PIM szolgáltatás.

1. Jelentkezzen be az Azure Portalra globális rendszergazdaként.

1. Válasszon **minden szolgáltatás** , majd **előfizetések**.

1. Válassza ki az előfizetését.

1. Válassza a **Hozzáférés-vezérlés (IAM)** lehetőséget az előfizetési hatókörben található szerepkör-hozzárendelések aktuális listájának megtekintéséhez.

   ![Egy előfizetés hozzáférés-vezérlés (IAM) paneljén](./media/pim-how-to-add-role-to-user/ms-pim-access-control.png)

1. Ellenőrizze, hogy a **MS-PIM** egyszerű szolgáltatás a **felhasználói hozzáférés rendszergazdája** szerepkör.

1. Ha nem, válassza a **Hozzáadás** megnyitásához a **engedélyek hozzáadása** ablaktáblán.

1. Az a **szerepkör** legördülő listában válassza a **felhasználói hozzáférés rendszergazdája** szerepkör.

1. Az a **kiválasztása** listában keresse meg és válassza ki a **MS-PIM** egyszerű szolgáltatást.

   ![A PIM-MS-engedélyek hozzáadása](./media/pim-how-to-add-role-to-user/ms-pim-add-permissions.png)

1. Válasszon **mentése** a szerepkör hozzárendelése.

   Néhány pillanat múlva a MS-PIM egyszerű szolgáltatást a felhasználói hozzáférés rendszergazdájának szerepköre az előfizetések szintjén van hozzárendelve.

   ![Felhasználói hozzáférés rendszergazdájának szerepköre a MS-PIM szolgáltatásra](./media/pim-how-to-add-role-to-user/ms-pim-user-access-administrator.png)


## <a name="next-steps"></a>További lépések

- [A PIM az Azure AD directory szerepkör-beállítások konfigurálása](pim-how-to-change-default-settings.md)
- [Rendelje hozzá a PIM az Azure-erőforrásszerepkörök](pim-resource-roles-assign-roles.md)
