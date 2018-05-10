---
title: Felhasználó hozzárendelése az Azure Active Directory rendszergazdai szerepkörök |} Microsoft Docs
description: Ismerteti, hogyan módosíthatja a felhasználói felügyeleti adatokat az Azure Active Directoryban
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 01/08/2018
ms.author: curtand
ms.reviewer: jeffsta
ms.openlocfilehash: 61662acb10a6f2085d297eae473e1330c619d48d
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="assign-a-user-to-administrator-roles-in-azure-active-directory"></a>Felhasználó hozzárendelése az Azure Active Directory rendszergazdai szerepkörök
Ez a cikk azt ismerteti, hogyan egy rendszergazdai szerepkör hozzárendelése az Azure Active Directory (Azure AD) felhasználó. A szervezetben új felhasználók hozzáadásával kapcsolatos további információkért lásd: [új felhasználók hozzáadása az Azure Active Directory](active-directory-users-create-azure-portal.md). A hozzáadott felhasználók alapértelmezés szerint nem rendelkeznek rendszergazdai engedélyekkel, de bármikor hozzájuk rendelhet szerepköröket.

## <a name="assign-a-role-to-a-user"></a>A szerepkör hozzárendelése felhasználóhoz
1. Jelentkezzen be az [Azure AD felügyeleti központba](https://aad.portal.azure.com) egy olyan fiókkal, amely a címtár globális rendszergazdája.
2. Válassza ki **felhasználók és csoportok**.

   ![Nyitó felhasználók kezelése](./media/active-directory-users-assign-role-azure-portal/create-users-user-management.png)
3. Válassza ki **minden felhasználó**.
  
  ![Minden felhasználói csoport megnyitása](./media/active-directory-users-assign-role-azure-portal/create-users-open-users-blade.png)
4. Válasszon egy felhasználót a listáról.
5. Válassza ki a kijelölt felhasználó **Directory szerepkör** majd rendelje hozzá a felhasználót a szerepkörhöz a **Directory szerepkör** listája. A felhasználói és rendszergazdai szerepkörökről további információért lásd: [Rendszergazdai szerepkörök hozzárendelése az Azure AD-ben](active-directory-assign-admin-roles-azure-portal.md).

      ![A felhasználó hozzárendelése egy szerepkörhöz](./media/active-directory-users-assign-role-azure-portal/create-users-assign-role.png)
6. Kattintson a **Mentés** gombra.

## <a name="next-steps"></a>További lépések
* [Gyors üzembe helyezés: Hozzáadása vagy törlése a felhasználók az Azure Active Directoryban](add-users-azure-active-directory.md)
* [Felhasználói profilok kezelése](active-directory-users-profile-azure-portal.md)
* [Egy másik címtárból adja hozzá a vendégfelhasználók számára](active-directory-b2b-what-is-azure-ad-b2b.md) 
* [Felhasználó hozzárendelése egy szerepkörhöz az Azure AD-ben](active-directory-users-assign-role-azure-portal.md)
* [Törölt felhasználó visszaállítása](active-directory-users-restore.md)
