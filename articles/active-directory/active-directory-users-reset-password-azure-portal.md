---
title: "Jelszó alaphelyzetbe állítása, az Azure Active Directoryban |} Microsoft Docs"
description: "Ismerteti az Azure Active Directoryban a felhasználó jelszavának visszaállítása"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: fad5624b-2f13-4abc-b3d4-b347903a8f16
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8e7c38c7f0d40a310dd0b6bd0e866d2d55115550
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="reset-the-password-for-a-user-in-azure-active-directory"></a>Az Azure Active Directoryban a felhasználó jelszavának visszaállítása
> [!div class="op_single_selector"]
> * [Azure Portal](active-directory-users-reset-password-azure-portal.md)
> * [klasszikus Azure portál](active-directory-create-users-reset-password.md)
>
>

## <a name="how-to-reset-the-password-for-a-user"></a>A felhasználó jelszavának alaphelyzetbe állítása
1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com) egy olyan fiókkal, amely a címtár globális rendszergazdája.
2. Válassza ki **további szolgáltatások**, adja meg **felhasználók és csoportok** a szövegmezőbe, majd válassza ki azt a **Enter**.

   ![Nyitó felhasználók kezelése](./media/active-directory-users-reset-password-azure-portal/create-users-user-management.png)
3. Az a **felhasználók és csoportok** panelen válassza **felhasználók**.

   ![A felhasználók panel megnyitása](./media/active-directory-users-reset-password-azure-portal/create-users-open-users-blade.png)
4. A **Felhasználók és csoportok – Felhasználók** panelen válasszon ki egy felhasználót a listából.
5. A kiválasztott felhasználó paneljén válassza az **Áttekintés** elemet, majd a parancssávon válassza ki a **Jelszó alaphelyzetbe állítása** parancsot.

    ![A jelszó alaphelyzetbe állítása paranccsal](./media/active-directory-users-reset-password-azure-portal/create-users-reset-password-command.png)
6. Az a **jelszó-átállítási** panelen válassza **jelszó-átállítási**.

## <a name="next-steps"></a>Következő lépések
* [Felhasználó hozzáadása](active-directory-users-create-azure-portal.md)
* [Felhasználó hozzárendelése egy szerepkörhöz az Azure AD-ben](active-directory-users-assign-role-azure-portal.md)
* [A felhasználó munkahelyi adatainak módosítása](active-directory-users-work-info-azure-portal.md)
* [Felhasználói profilok kezelése](active-directory-users-profile-azure-portal.md)
* [Felhasználó törlése az Azure AD-ben](active-directory-users-delete-user-azure-portal.md)
