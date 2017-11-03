---
title: "Egy felhasználó vagy csoport hozzárendelése az Azure Active Directory vállalati alkalmazások |} Microsoft Docs"
description: "Egy vállalati alkalmazás hozzárendelése egy felhasználóhoz vagy csoporthoz, az Azure Active Directory kiválasztása"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 5817ad48-d916-492b-a8d0-2ade8c50a224
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: curtand
ms.reviewer: asteen
ms.openlocfilehash: 8e61044f261033a473241e2de152026bf49c4c70
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="assign-a-user-or-group-to-an-enterprise-app-in-azure-active-directory"></a>Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás Azure Active Directoryban
Akkor is könnyen egy felhasználó vagy csoport hozzárendelése az Azure Active Directory (Azure AD) a vállalati alkalmazások. A vállalati alkalmazások kezelésére a megfelelő engedélyekkel kell rendelkeznie, és a címtár globális rendszergazdának kell lennie.

## <a name="how-do-i-assign-user-access-to-an-enterprise-app"></a>Hogyan oszthatok ki a felhasználói hozzáférés vállalati alkalmazások számára?
1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com) egy olyan fiókkal, amely a címtár globális rendszergazdája.
2. Válassza ki **további szolgáltatások**, a mezőben adja meg Azure Active Directoryban, és válassza **Enter**.
3. Az a **Azure Active Directory - *directoryname***  (Ez azt jelenti, hogy az Azure AD panelen a kezelt könyvtár) panelen válassza ki **vállalati alkalmazások**.

    ![Vállalati alkalmazások megnyitásakor](./media/active-directory-coreapps-assign-user-azure-portal/open-enterprise-apps.png)
4. Az a **vállalati alkalmazások** panelen válassza **összes alkalmazás**. Kezelheti az alkalmazások listáját láthatja.
5. Az a **vállalati alkalmazások – összes alkalmazás** panelen, jelöljön ki egy alkalmazást.
6. Az a ***appname*** (Ez azt jelenti, hogy a panel nevű, a kijelölt alkalmazást a címben) panelen válassza ki **felhasználók és csoportok**.

    ![Az összes alkalmazások paranccsal](./media/active-directory-coreapps-assign-user-azure-portal/select-app-users.png)
7. Az a ***appname*** **-felhasználó & csoport-hozzárendelés** panelen válassza a **Hozzáadás** parancsot.
8. Az a **hozzáadása hozzárendelés** panelen válassza **felhasználók és csoportok**.

    ![Az alkalmazás egy felhasználó vagy csoport hozzárendelése](./media/active-directory-coreapps-assign-user-azure-portal/assign-users.png)
9. Az a **felhasználók és csoportok** panelen válasszon ki egy vagy több felhasználót vagy csoportot a listából, és válassza ki a **válasszon** gomb a panel alján.
10. Az a **hozzáadása hozzárendelés** panelen válassza **szerepkör**. Ezt követően a **Szerepkörválasztás** panelen válassza ki a megfelelő szerepkört a kiválasztott felhasználókra vagy csoportokra alkalmazza, és válassza ki a **OK** gomb a panel alján.
11. Az a **hozzáadása hozzárendelés** panelen válassza a **hozzárendelése** gomb a panel alján. A kijelölt felhasználók vagy csoportok a vállalati alkalmazás a kiválasztott szerepkör által meghatározott engedélyekkel rendelkezik.

## <a name="next-steps"></a>Következő lépések
* [Összes saját csoportok](active-directory-groups-view-azure-portal.md)
* [Egy felhasználó vagy csoport-hozzárendelés eltávolítása a vállalati alkalmazások](active-directory-coreapps-remove-assignment-azure-portal.md)
* [Tiltsa le a felhasználói bejelentkezéseket a vállalati alkalmazás](active-directory-coreapps-disable-app-azure-portal.md)
* [Módosítja a nevét, vagy egy vállalati alkalmazás embléma](active-directory-coreapps-change-app-logo-user-azure-portal.md)
