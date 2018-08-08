---
title: Címtárbeli szerepkörök hozzárendelése a felhasználókhoz az Azure AD PIM segítségével |} A Microsoft Docs
description: Ismerje meg, hogyan címtárbeli szerepkörök hozzárendelése a felhasználókhoz az Azure Active Directory Privileged Identity Management és az Azure portal használatával.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: pim
ms.date: 07/23/2018
ms.author: rolyon
ms.openlocfilehash: 1aede38cabba7f9811f2b9320bc1e9a9da857f08
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2018
ms.locfileid: "39621813"
---
# <a name="assign-directory-roles-to-users-using-azure-ad-pim"></a>Címtárbeli szerepkörök hozzárendelése a felhasználókhoz az Azure AD PIM használatával

Az Azure Active Directory (Azure AD), a globális rendszergazdák kezdeményezhetik **állandó** directory szerepkör-hozzárendeléseket. A szerepkör-hozzárendelések hozható létre a [az Azure portal](../users-groups-roles/directory-assign-admin-roles.md) vagy [PowerShell-parancsok](/powershell/module/azuread#directory_roles).

Az Azure AD Privileged Identity Management (PIM) szolgáltatást is lehetővé teszi a kiemelt szerepkörű rendszergazdák állandó directory szerepkör-hozzárendeléseket. Ezenkívül a kiemelt szerepkörű rendszergazdák felhasználókat tehet **jogosult** -címtárbeli szerepkörökhöz tartozó. A jogosult rendszergazda aktiválhatja a szerepkört, amikor szükségük van rá, majd rájuk vonatkozó engedélyek lejárnak, amint kész is van. A PIM használata kezelheti szerepkörökkel kapcsolatos további információkért lásd: [címtárbeli szerepkörök Azure AD PIM használata kezelheti](pim-roles.md).

## <a name="make-a-user-eligible-for-a-role"></a>Győződjön meg arról, a felhasználó jogosult szerepkör

Kövesse az alábbi lépéseket, hogy egy felhasználó jogosult az Azure AD-címtár szerepkörhöz.

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com/) egy felhasználóval, amely tagja a [kiemelt szerepkörű rendszergazda](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) szerepkör.

    Hozzáférést biztosít egy másik felhasználó a PIM kezelése kapcsolatos információkért lásd: [hozzáférés biztosítása a PIM számára](pim-how-to-give-access-to-pim.md).

1. Nyissa meg **az Azure AD Privileged Identity Management**.

    Ha a PIM még még nem engedélyezte az Azure Portalon, lépjen a [Ismerkedés az Azure AD PIM](pim-getting-started.md).

1. Kattintson a **az Azure AD-címtárbeli szerepkörök**.

1. Kattintson a **szerepkör (előzetes verzió)** vagy **tagok**.

    ![Azure AD-címtárbeli szerepkörök](./media/pim-how-to-add-role-to-user/pim-directory-roles.png)

1. Kattintson a **tag hozzáadása** felügyelt tagok hozzáadása párbeszédpanel megnyitásához.

1. Kattintson a **Szerepkörválasztás**, kattintson a kezelése, és kattintson a kívánt szerepkört **kiválasztása**.

    ![Szerepkörválasztás](./media/pim-how-to-add-role-to-user/pim-select-a-role.png)

1. Kattintson a **tagok kiválasztása**, válassza ki a felhasználókat, rendelje hozzá a szerepkörhöz, és kattintson a kívánt **kiválasztása**.

    ![Szerepkörválasztás](./media/pim-how-to-add-role-to-user/pim-select-members.png)

1. Felügyelt tagok kattintson a Hozzáadás **OK** a felhasználó hozzáadása a szerepkörhöz.

     Ha a szerepkör van hozzárendelve, a kiválasztott felhasználó megjelenik a tagok listája, **jogosult** a szerepkörhöz.

    ![A szerepkör jogosult felhasználó](./media/pim-how-to-add-role-to-user/pim-directory-role-eligible.png)

1. Most, hogy a felhasználó nem jogosult a szerepkörhöz, tudassa vele, hogy azokat, az aktiválást található utasítások szerint [aktiválása vagy a szerepkör inaktiválása](pim-how-to-activate-role.md).

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

## <a name="next-steps"></a>További lépések
[!INCLUDE [active-directory-privileged-identity-management-toc](../../../includes/active-directory-privileged-identity-management-toc.md)]
