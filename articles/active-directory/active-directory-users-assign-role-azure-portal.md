---
title: "Felhasználó hozzárendelése az Azure Active Directory rendszergazdai szerepkörök |} Microsoft Docs"
description: "Ismerteti, hogyan módosíthatja a felhasználói felügyeleti adatokat az Azure Active Directoryban"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: a1ca1a53-50d8-4bf0-ae8f-73fa1253e2d9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2017
ms.author: curtand
ms.reviewer: jeffsta
ms.openlocfilehash: 290f2e837cae9bdb95cf8acb486938632927ca8c
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2017
---
# <a name="assign-a-user-to-administrator-roles-in-azure-active-directory"></a>Felhasználó hozzárendelése az Azure Active Directory rendszergazdai szerepkörök
Ez a cikk azt ismerteti, hogyan egy rendszergazdai szerepkör hozzárendelése az Azure Active Directory (Azure AD) felhasználó. A szervezetben új felhasználók hozzáadásával kapcsolatos további információkért lásd: [új felhasználók hozzáadása az Azure Active Directory](active-directory-users-create-azure-portal.md). A hozzáadott felhasználók alapértelmezés szerint nem rendelkeznek rendszergazdai engedélyekkel, de bármikor hozzájuk rendelhet szerepköröket.

## <a name="assign-a-role-to-a-user"></a>A szerepkör hozzárendelése felhasználóhoz
1. Jelentkezzen be az [Azure AD felügyeleti központba](https://aad.portal.azure.com) egy olyan fiókkal, amely a címtár globális rendszergazdája.
2. Válassza ki **felhasználók és csoportok**.

   ![Nyitó felhasználók kezelése](./media/active-directory-users-assign-role-azure-portal/create-users-user-management.png)
3. Válassza ki **minden felhasználó**.

   ![Minden felhasználói csoport megnyitása](./media/active-directory-users-assign-role-azure-portal/create-users-open-users-blade.png)
1. Válasszon egy felhasználót a listáról.
2. Válassza ki a kijelölt felhasználó **Directory szerepkör** majd rendelje hozzá a felhasználót a szerepkörhöz a **Directory szerepkör** listája. A felhasználói és rendszergazdai szerepkörökről további információért lásd: [Rendszergazdai szerepkörök hozzárendelése az Azure AD-ben](active-directory-assign-admin-roles-azure-portal.md).

      ![A felhasználó hozzárendelése egy szerepkörhöz](./media/active-directory-users-assign-role-azure-portal/create-users-assign-role.png)
6. Kattintson a **Mentés** gombra.

## <a name="next-steps"></a>Következő lépések
* [Felhasználó hozzáadása](active-directory-users-create-azure-portal.md)
* [A felhasználó jelszavát az új Azure-portálon](active-directory-users-reset-password-azure-portal.md)
* [A felhasználó munkahelyi adatainak módosítása](active-directory-users-work-info-azure-portal.md)
* [Felhasználói profilok kezelése](active-directory-users-profile-azure-portal.md)
* [Felhasználó törlése az Azure AD-ben](active-directory-users-delete-user-azure-portal.md)
