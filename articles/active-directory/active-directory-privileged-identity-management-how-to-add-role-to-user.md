---
title: Hogyan lehet hozzáadni vagy eltávolítani egy felhasználói szerepkört |} Microsoft Docs
description: Útmutató a szerepkörök hozzáadása a kiemelt jogosultságú identitások az Azure Active Directory Privileged Identity Management alkalmazással.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.component: users-groups-roles
ms.date: 01/03/2018
ms.author: curtand
ms.openlocfilehash: 6024016d7c59551e5678069cb6a6e1e7ab45ad6e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="azure-ad-privileged-identity-management-how-to-add-or-remove-a-user-role"></a>Azure AD Privileged Identity Management: Felhasználói szerepkör hozzáadása vagy eltávolítása
Az Azure Active Directory (AD), egy globális rendszergazda (vagy vállalati rendszergazda) frissítheti felhasználók amelyek **véglegesen** rendelt szerepkörök az Azure ad-ben. Ez történik, például a PowerShell-parancsmagokkal `Add-MsolRoleMember` és `Remove-MsolRoleMember`. Vagy az Azure-portálon az használhatják a [rendszergazdai szerepkörök hozzárendelése az Azure Active Directory](active-directory-assign-admin-roles.md).

Az Azure AD Privileged Identity Management alkalmazás lehetővé teszi, hogy a kiemelt szerepkörű rendszergazda állandó szerepkör-hozzárendelések, valamint hogy. Emellett a kiemelt szerepkörű rendszergazda felhasználók tehet **jogosult** szabása rendszergazdai szerepkörökhöz. Jogosult rendszergazda is aktiválja a szerepkört, amikor szükség, majd rájuk vonatkozó engedélyek lejárnak, ha kész van.

## <a name="manage-roles-with-pim-in-the-azure-portal"></a>Az Azure-portálon a PIM szerepkörök kezelése
A szervezet rendelhet felhasználók különböző rendszergazdai szerepkörök az Azure AD, az Office 365 és más Microsoft szolgáltatásokat és alkalmazásokat.  További részleteket a következő szerepkörök található [szerepkörök az Azure AD PIM](active-directory-privileged-identity-management-roles.md).

A felhasználó hozzáadása vagy eltávolítása a Privileged Identity Management használatával szerepkör, a PIM irányítópult elindítani. Válassza a **rendszergazdai szerepkörök a felhasználók** gombra, vagy válasszon egy adott szerepkör (például a globális rendszergazda) a szerepkörök táblából.

> [!NOTE]
> Amennyiben a PIM még nem engedélyezte az Azure portálon, [Ismerkedés az Azure AD PIM](active-directory-privileged-identity-management-getting-started.md) részleteiről.

Ha azt szeretné, a másik felhasználó hozzáférésének PIM magát, a szerepkörök, amelyek a PIM a felhasználónak rendelkeznie kell részelemcímkék ismertetését további [a PIM hozzáférésének hogyan](active-directory-privileged-identity-management-how-to-give-access-to-pim.md).

## <a name="add-a-user-to-a-role"></a>Adja hozzá a felhasználó egy szerepkörhöz
1. Az a [Azure-portálon](https://portal.azure.com/), jelölje be a **Azure AD Privileged Identity Management** csempére az irányítópulton.
2. Válassza ki **kiemelt szerepköröket kezelése**.
3. Az a **szerepkör összegzés** table, válassza ki a kezelni kívánt szerepkört.
4. A szerepkör paneljén válassza **Hozzáadás**.
5. Kattintson a **válasszon ki egy felhasználót** és keresse meg a felhasználót a **válasszon ki egy felhasználót** panelen.  
6. A keresési eredmények listájáról válassza ki a felhasználót, és kattintson a **végzett**.
7. Kattintson a **OK** a mentéshez. A kiválasztott felhasználó megjelenik a listában a megfelelő a szerepkörhöz.

> [!NOTE]
> Új felhasználói szerepkör a szerepkör alapértelmezés szerint csak jogosultak. Ha azt szeretné, hogy a szerepkör véglegessé, kattintson a felhasználónévre a listában. A felhasználói adatok egy új panelen megjelennek. Válassza ki **ellenőrizze perm** a felhasználói adatokat menüben.  
> Ha a felhasználó nem tudja regisztrálni Azure multi-factor Authentication (MFA), vagy egy Microsoft-fiókot használ (általában @outlook.com), meg kell győződnie állandó a szerepkört. Regisztrálnia az MFA szolgáltatásra az aktiválás során meg kell adnia a megfelelő rendszergazdák.

Most, hogy a felhasználó nem jogosult a szerepkörhöz, tájékoztathatja a felhasználókat, hogy akkor is aktiválhatja az utasításainak megfelelően [aktiválása vagy deaktiválása](active-directory-privileged-identity-management-how-to-activate-role.md).

## <a name="remove-a-user-from-a-role"></a>Felhasználó eltávolítása egy szerepkör
Távolítsa el a felhasználók a megfelelő szerepkör-hozzárendelések, de győződjön meg arról, hogy mindig van legalább egy felhasználót, aki egy állandó globális rendszergazdai.

Kövesse az alábbi lépéseket egy adott felhasználó eltávolítása egy szerepkört:

1. Válasszon ki egy szerepkört az Azure AD PIM irányítópult vagy kattintson a a szerepkört a szerepkör listában keresse meg a **rendszergazdai szerepkörök a felhasználók** gombra.
2. Kattintson a felhasználót a felhasználói listáról.
3. Kattintson a **eltávolítása**. Üzenet kéri megerősítését.
4. Kattintson a **Igen** eltávolítja a szerepkört a felhasználó.

Ha nem biztos abban, hogy mely felhasználók továbbra is szükséges a szerepkör-hozzárendeléseket, akkor is [a szerepkör egy hozzáférés-ellenőrzés indítása](active-directory-privileged-identity-management-how-to-start-security-review.md).

## <a name="next-steps"></a>További lépések
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

