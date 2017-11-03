---
title: "Tiltsa le a felhasználói bejelentkezések Azure Active Directory vállalati alkalmazás |} Microsoft Docs"
description: "Egy vállalati alkalmazás letiltása, hogy egy felhasználó sem lehet, hogy jelentkezzen be az Azure Active Directoryban"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: a27562f9-18dc-42e8-9fee-5419566f8fd7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: curtand
ms.reviewer: asteen
ms.custom: it-pro
ms.openlocfilehash: 4f587eb96aaa41ba2034eb9eaeb904f72c501abe
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="disable-user-sign-ins-for-an-enterprise-app-in-azure-active-directory"></a>Tiltsa le a felhasználói bejelentkezéseket a vállalati alkalmazás Azure Active Directoryban
Akkor is könnyen tiltsa le a vállalati alkalmazás, így a felhasználók nem lehetséges, hogy jelentkezzen be azt az Azure Active Directory (Azure AD). A vállalati alkalmazások kezelésére a megfelelő engedélyekkel kell rendelkeznie, és a címtár globális rendszergazdának kell lennie.

## <a name="how-do-i-disable-user-sign-ins"></a>Hogyan tiltsa le a felhasználói bejelentkezéseket?
1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com) egy olyan fiókkal, amely a címtár globális rendszergazdája.
2. Válassza ki **további szolgáltatások**, adja meg **Azure Active Directory** a szövegmezőbe, majd válassza ki azt a **Enter**.
3. Az a **Azure Active Directory** -  ***directoryname*** (Ez azt jelenti, hogy az Azure AD panelen a kezelt könyvtár) panelen válassza ki **vállalati alkalmazások**.

    ![Vállalati alkalmazások megnyitásakor](./media/active-directory-coreapps-disable-app-azure-portal/open-enterprise-apps.png)
4. Az a **vállalati alkalmazások** panelen válassza **összes alkalmazás**. Kezelheti az alkalmazások listájának megtekintéséhez.
5. Az a **vállalati alkalmazások – összes alkalmazás** panelen, jelöljön ki egy alkalmazást.
6. Az a ***appname*** (Ez azt jelenti, hogy a panel nevű, a kijelölt alkalmazást a címben) panelen válassza ki **tulajdonságok**.

    ![Az összes alkalmazások paranccsal](./media/active-directory-coreapps-disable-app-azure-portal/select-app.png)
7. Az a ***appname*** - **tulajdonságok** panelen válassza **nem** a **engedélyezve van, hogy a felhasználók bejelentkezési?**.
8. Válassza ki a **mentése** parancsot.

## <a name="next-steps"></a>Következő lépések
* [Összes csoport megtekintéshez](active-directory-groups-view-azure-portal.md)
* [Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](active-directory-coreapps-assign-user-azure-portal.md)
* [Egy felhasználó vagy csoport-hozzárendelés eltávolítása a vállalati alkalmazások](active-directory-coreapps-remove-assignment-azure-portal.md)
* [Módosítja a nevét, vagy egy vállalati alkalmazás embléma](active-directory-coreapps-change-app-logo-user-azure-portal.md)
