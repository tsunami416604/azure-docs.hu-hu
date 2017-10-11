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
ms.date: 06/27/2017
ms.author: curtand
ms.reviewer: jeffsta
ms.openlocfilehash: bfadf133154488f9827cfbeaa98ddb0eb84b52f6
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="assign-a-user-to-administrator-roles-in-azure-active-directory"></a>Felhasználó hozzárendelése az Azure Active Directory rendszergazdai szerepkörök
Ez a cikk azt ismerteti, hogyan egy rendszergazdai szerepkör hozzárendelése az Azure Active Directory (Azure AD) felhasználó. A szervezetben új felhasználók hozzáadásával kapcsolatos további információkért lásd: [új felhasználók hozzáadása az Azure Active Directory](active-directory-users-create-azure-portal.md). A hozzáadott felhasználók alapértelmezés szerint nem rendelkeznek rendszergazdai engedélyekkel, de bármikor hozzájuk rendelhet szerepköröket.

## <a name="assign-a-role-to-a-user"></a>A szerepkör hozzárendelése felhasználóhoz
1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com) egy olyan fiókkal, amely a címtár globális rendszergazdája.
2. Válassza ki **további szolgáltatások**, adja meg **felhasználók és csoportok** a szövegmezőbe, majd válassza ki azt a **Enter**.

   ![Nyitó felhasználók kezelése](./media/active-directory-users-assign-role-azure-portal/create-users-user-management.png)
3. Az a **felhasználók és csoportok** panelen válassza **minden felhasználó**.

   ![Az összes felhasználók panel megnyitása](./media/active-directory-users-assign-role-azure-portal/create-users-open-users-blade.png)
4. Az a **felhasználók és csoportok - minden felhasználó** panelen válasszon ki egy felhasználót a listáról.
5. A kiválasztott felhasználó paneljén válassza **Directory szerepkör**, majd rendelje hozzá a felhasználót a szerepkörhöz a **Directory szerepkör** listája. A felhasználói és rendszergazdai szerepkörökről további információért lásd: [Rendszergazdai szerepkörök hozzárendelése az Azure AD-ben](active-directory-assign-admin-roles.md).

      ![A felhasználó hozzárendelése egy szerepkörhöz](./media/active-directory-users-assign-role-azure-portal/create-users-assign-role.png)
6. Kattintson a **Mentés** gombra.

## <a name="next-steps"></a>Következő lépések
* [Felhasználó hozzáadása](active-directory-users-create-azure-portal.md)
* [A felhasználó jelszavát az új Azure-portálon](active-directory-users-reset-password-azure-portal.md)
* [A felhasználó munkahelyi adatainak módosítása](active-directory-users-work-info-azure-portal.md)
* [Felhasználói profilok kezelése](active-directory-users-profile-azure-portal.md)
* [Felhasználó törlése az Azure AD-ben](active-directory-users-delete-user-azure-portal.md)
