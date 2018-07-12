---
title: Hogyan lehet egy felhasználói szerepkör hozzáadása vagy eltávolítása |} A Microsoft Docs
description: Megtudhatja, hogyan adhat hozzá a szerepkört az Azure Active Directory Privileged Identity Management alkalmazással emelt jogosultsági szintű identitásait.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: protection
ms.date: 01/03/2018
ms.author: rolyon
ms.openlocfilehash: eac0869c0f4a7dd780d6988ff9bc4362458a7e3d
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38590505"
---
# <a name="azure-ad-privileged-identity-management-how-to-add-or-remove-a-user-role"></a>Azure AD Privileged Identity Management: Felhasználói szerepkör hozzáadása vagy eltávolítása
Az Azure Active Directory (AD), a globális rendszergazda (vagy a vállalati rendszergazda) frissítheti, amelyek felhasználók **véglegesen** szerepkörökhöz rendelt Azure AD-ben. Ez történik például a PowerShell-parancsmagokkal `Add-MsolRoleMember` és `Remove-MsolRoleMember`. Vagy használhatják az Azure Portalon leírtak szerint [rendszergazdai szerepkörök hozzárendelése az Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md).

Az Azure AD Privileged Identity Management alkalmazás lehetővé teszi, hogy a végleges szerepkör-hozzárendeléseket, valamint a kiemelt szerepkörű rendszergazdák. Ezenkívül a kiemelt szerepkörű rendszergazdák felhasználókat tehet **jogosult** rendszergazdai szerepkörökhöz. Jogosult rendszergazda aktiválhatja a szerepkört, amikor szükségük van rá, majd rájuk vonatkozó engedélyek lejárnak, amint kész is van.

## <a name="manage-roles-with-pim-in-the-azure-portal"></a>Kezelheti a szerepköröket a PIM az Azure Portalon
A szervezeten belül felhasználók hozzárendelheti a különböző rendszergazdai szerepkörökkel az Azure AD-ben az Office 365 és más Microsoft szolgáltatásokat és alkalmazásokat.  Az elérhető szerepkörök további részleteket tekinthet meg [szerepkörök az Azure AD PIM](pim-roles.md).

Adjon hozzá vagy távolíthat el felhasználót egy szerepkörre a Privileged Identity Management használatával a, a PIM irányítópult megjelenítéséhez. Ezután kattintson a **rendszergazdák** gombra, vagy egy adott szerepkörrel (például a globális rendszergazdai) kiválasztása a szerepkörök táblából.

> [!NOTE]
> Ha a PIM még még nem engedélyezte az Azure Portalon, lépjen a [Ismerkedés az Azure AD PIM](pim-getting-started.md) részleteiről.

Ha szeretne egy másik felhasználói hozzáférésének PIM magát, a szerepköröket a PIM a felhasználónak szüksége van, amely olyan további [hozzáférés biztosítása a PIM számára](pim-how-to-give-access-to-pim.md).

## <a name="add-a-user-to-a-role"></a>A felhasználó hozzáadása szerepkörhöz
1. Az a [az Azure portal](https://portal.azure.com/), jelölje be a **Azure AD Privileged Identity Management** csempét az irányítópulton.
2. Válassza ki **kiemelt szerepkörök kezelése**.
3. Az a **szerepkör összefoglalása** táblában, válassza ki a kezelni kívánt szerepkört.
4. A szerepkör panelen válassza ki a **Hozzáadás**.
5. Kattintson a **válassza ki a felhasználók** , és keresse meg a felhasználó a **válassza ki a felhasználók** panelen.  
6. A keresési eredmények listájáról válassza ki a felhasználót, és kattintson a **kész**.
7. Kattintson a **OK** a mentéshez. A kiválasztott felhasználó megjelenik a listában a jogosultságot a szerepkörre.

> [!NOTE]
> Új felhasználói szerepkör a szerepkör alapértelmezés szerint csak jogosultak. Ha azt szeretné, hogy a szerepkör állandó, kattintson a felhasználónévre a listában. A felhasználói adatok egy új panel jelenik meg. Válassza ki **márka perm** a felhasználói adatokat menüben.  
> Ha a felhasználó nem tudja regisztrálni az Azure multi-factor Authentication (MFA), vagy a Microsoft-fiókkal (általában @outlook.com), végre kell hajtania az összes szerepkörökhöz állandó. Jogosult rendszergazdák rendszer megkéri, hogy a multi-factor Authentication az aktiválás során regisztrálja.

Most, hogy a felhasználó nem jogosult a szerepkörhöz, tudassa vele, hogy azokat, az aktiválást található utasítások szerint [aktiválása vagy a szerepkör inaktiválása](pim-how-to-activate-role.md).

## <a name="remove-a-user-from-a-role"></a>Felhasználó eltávolítása a szerepkörből
Jogosult szerepkör-hozzárendelések távolítsa el a felhasználókat, de győződjön meg arról, hogy mindig van legalább egy felhasználót, aki egy állandó globális rendszergazda.

Kövesse az alábbi lépéseket egy adott felhasználó eltávolítása a szerepkörből:

1. Keresse meg a szerepkört a szerepkör-lista egy szerepkörhöz az Azure AD PIM irányítópult kiválasztásával, vagy kattintson a a **rendszergazdák** gombra.
2. Kattintson a felhasználó a felhasználói listájában.
3. Kattintson a **eltávolítása**. Egy üzenet rákérdez, hogy erősítse meg.
4. Kattintson a **Igen** eltávolítja a szerepkört a felhasználó elől.

Ha nem biztos abban, hogy mely felhasználók továbbra is szükséges a szerepkör-hozzárendeléseket, akkor is [a szerepkörhöz tartozó hozzáférési felülvizsgálat indítása](pim-how-to-start-security-review.md).

## <a name="next-steps"></a>További lépések
[!INCLUDE [active-directory-privileged-identity-management-toc](../../../includes/active-directory-privileged-identity-management-toc.md)]

