---
title: "Egy felhasználó vagy csoport-hozzárendelés eltávolítása egy vállalati alkalmazás Azure Active Directoryban |} Microsoft Docs"
description: "A hozzáférés hozzárendelése egy felhasználóhoz vagy csoporthoz eltávolítása a vállalati alkalmazások az Azure Active Directoryban"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 7b2d365b-ae92-477f-9702-353cc6acc5ea
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: curtand
ms.reviewer: asteen
ms.custom: it-pro
ms.openlocfilehash: 99cbc54b4daa988dbf741275ce92d1c863af6720
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="remove-a-user-or-group-assignment-from-an-enterprise-app-in-azure-active-directory"></a>Egy felhasználó vagy csoport-hozzárendelés eltávolítása egy vállalati alkalmazás Azure Active Directoryban
Nem lett hozzárendelve hozzáférés a vállalati alkalmazások az Azure Active Directory (Azure AD) egyik felhasználó vagy csoport eltávolítása. A vállalati alkalmazások kezelésére a megfelelő engedélyekkel kell rendelkeznie, és a címtár globális rendszergazdának kell lennie.

## <a name="how-do-i-remove-a-user-or-group-assignment"></a>Hogyan el egy felhasználó vagy csoport-hozzárendelést?
1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com) egy olyan fiókkal, amely a címtár globális rendszergazdája.
2. Válassza ki **további szolgáltatások**, adja meg **Azure Active Directory** a szövegmezőbe, majd válassza ki azt a **Enter**.
3. Az a **Azure Active Directory - *directoryname***  (Ez azt jelenti, hogy az Azure AD panelen a kezelt könyvtár) panelen válassza ki **vállalati alkalmazások**.

    ![Vállalati alkalmazások megnyitásakor](./media/active-directory-coreapps-remove-assignment-user-azure-portal/open-enterprise-apps.png)
4. Az a **vállalati alkalmazások** panelen válassza **összes alkalmazás**. Kezelheti az alkalmazások listáját láthatja.
5. Az a **vállalati alkalmazások – összes alkalmazás** panelen, jelöljön ki egy alkalmazást.
6. Az a ***appname*** (Ez azt jelenti, hogy a panel nevű, a kijelölt alkalmazást a címben) panelen válassza ki **felhasználók és csoportok**.

    ![Felhasználók vagy csoportok kiválasztása](./media/active-directory-coreapps-remove-assignment-user-azure-portal/remove-app-users.png)
7. A a ***appname*** **-felhasználó & csoport-hozzárendelés** panelen válasszon egyet a további felhasználókat és csoportokat, és válassza ki a **eltávolítása** parancsot. Erősítse meg a döntést, a parancssorba.

    ![A Remove parancsot kiválasztása](./media/active-directory-coreapps-remove-assignment-user-azure-portal/remove-users.png)

## <a name="next-steps"></a>Következő lépések
* [Összes saját csoportok](active-directory-groups-view-azure-portal.md)
* [Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](active-directory-coreapps-assign-user-azure-portal.md)
* [Tiltsa le a felhasználói bejelentkezéseket a vállalati alkalmazás](active-directory-coreapps-disable-app-azure-portal.md)
* [Módosítja a nevét, vagy egy vállalati alkalmazás embléma](active-directory-coreapps-change-app-logo-user-azure-portal.md)
