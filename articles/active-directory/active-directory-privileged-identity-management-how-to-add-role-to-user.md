---
title: "Hogyan lehet hozzáadni vagy eltávolítani egy felhasználói szerepkört |} Microsoft Docs"
description: "Útmutató a szerepkörök hozzáadása a kiemelt jogosultságú identitások az Azure Active Directory Privileged Identity Management alkalmazással."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: 6a47ced8-cf34-4ce8-bea2-e4fc548cfe22
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/06/2017
ms.author: billmath
ms.custom: pim;oldportal;it-pro;
ms.openlocfilehash: 5a2150b67fb4926ac9bb04c74a281ba78692c5f4
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="azure-ad-privileged-identity-management-how-to-add-or-remove-a-user-role"></a>Azure AD Privileged Identity Management: Felhasználói szerepkör hozzáadása vagy eltávolítása
Az Azure Active Directory (AD), egy globális rendszergazda (vagy vállalati rendszergazda) frissítheti felhasználók amelyek **véglegesen** rendelt szerepkörök az Azure ad-ben. Ez történik, például a PowerShell-parancsmagokkal `Add-MsolRoleMember` és `Remove-MsolRoleMember`. Vagy a klasszikus Azure portálra az használhatják a [rendszergazdai szerepkörök hozzárendelése az Azure Active Directory](active-directory-assign-admin-roles-azure-portal.md).

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

## <a name="next-steps"></a>Következő lépések
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

