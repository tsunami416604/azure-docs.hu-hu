---
title: Felhasználó hozzárendelése rendszergazdai szerepkörökhöz az Azure Active Directory-ban | Microsoft Docs
description: Felhasználói rendszergazda-információk módosítása az Azure Active Directoryban
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: quickstart
ms.date: 06/25/2018
ms.author: lizross
ms.reviewer: jeffsta
ms.openlocfilehash: 7ed564d5954841f96109568b33183908d25bb8be
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36939548"
---
# <a name="assign-a-user-to-administrator-roles-in-azure-active-directory"></a>Felhasználó hozzárendelése rendszergazdai szerepkörökhöz az Azure Active Directory-ban
Ez a cikk azt ismerteti, hogyan rendelhető hozzá egy rendszergazdai szerepkör egy felhasználóhoz az Azure Active Directoryban (Azure AD). További információ új vállalati felhasználók hozzáadásáról: [Új felhasználók felvétele az Azure Active Directoryba](../add-users-azure-active-directory.md). A hozzáadott felhasználók alapértelmezés szerint nem rendelkeznek rendszergazdai engedélyekkel, de bármikor hozzájuk rendelhet szerepköröket.

## <a name="assign-a-role-to-a-user"></a>Szerepkör hozzárendelése felhasználóhoz
1. Jelentkezzen be az [Azure portálra](https://portal.azure.com) egy olyan fiókkal, amely a címtár globális rendszergazdája vagy kiemelt szerepkörű rendszergazdája.

2. Válassza az **Azure Active Directory** lehetőséget, a **Felhasználók** elemet, majd válasszon ki egy felhasználót.

    ![Felhasználókezelés megnyitása](./media/active-directory-users-assign-role-azure-portal/create-users-user-management.png)

3. A kijelölt felhasználónál válassza a **Címtárbeli szerepkör**, majd a **Szerepkör hozzáadása** elemet, és válassza ki a megfelelő rendszergazdai szerepköröket (például **Feltételes hozzáférésű rendszergazda**) a **Címtárbeli szerepkörök** listából. A rendszergazdai szerepkörökről további információt a [Rendszergazdai szerepkörök hozzárendelése az Azure AD-ben](../active-directory-assign-admin-roles-azure-portal.md) című témakörben talál. 

    ![Felhasználó hozzárendelése egy szerepkörhöz](./media/active-directory-users-assign-role-azure-portal/create-users-assign-role.png)

1. A mentéshez válassza a **Kiválasztás** lehetőséget.

## <a name="next-steps"></a>További lépések
* [Rövid útmutató: Felhasználók hozzáadása vagy törlése az Azure Active Directoryban](add-users-azure-active-directory.md)
* [Felhasználói profilok kezelése](active-directory-users-profile-azure-portal.md)
* [Vendégfelhasználók hozzáadása másik címtárból](../b2b/what-is-b2b.md) 
* [Felhasználó hozzárendelése egy szerepkörhöz az Azure AD-ben](active-directory-users-assign-role-azure-portal.md)
* [Törölt felhasználó visszaállítása](active-directory-users-restore.md)
