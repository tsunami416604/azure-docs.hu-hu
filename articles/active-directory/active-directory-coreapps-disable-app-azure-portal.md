---
title: Tiltsa le a felhasználói bejelentkezések Azure Active Directory vállalati alkalmazás |} Microsoft Docs
description: Egy vállalati alkalmazás letiltása, hogy egy felhasználó sem lehet, hogy jelentkezzen be az Azure Active Directoryban
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: barbkess
ms.reviewer: asteen
ms.custom: it-pro
ms.openlocfilehash: be0c8ede4a99c532aecbfbb4ee0fc51657f96886
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="disable-user-sign-ins-for-an-enterprise-app-in-azure-active-directory"></a>Tiltsa le a felhasználói bejelentkezéseket a vállalati alkalmazás Azure Active Directoryban
Akkor is könnyen tiltsa le a vállalati alkalmazás, így a felhasználók nem lehetséges, hogy jelentkezzen be azt az Azure Active Directory (Azure AD). A vállalati alkalmazások kezelésére a megfelelő engedélyekkel kell rendelkeznie, és a címtár globális rendszergazdának kell lennie.

## <a name="how-do-i-disable-user-sign-ins"></a>Hogyan tiltsa le a felhasználói bejelentkezéseket?
1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com) egy olyan fiókkal, amely a címtár globális rendszergazdája.
2. Válassza ki **minden szolgáltatás**, adja meg **Azure Active Directory** a szövegmezőbe, majd válassza ki azt a **Enter**.
3. Az a **Azure Active Directory** -  ***directoryname*** (Ez azt jelenti, hogy az Azure AD ablaktáblán a kezelt könyvtár) ablaktáblán válassza ki **vállalati alkalmazások**.

    ![Vállalati alkalmazások megnyitásakor](./media/active-directory-coreapps-disable-app-azure-portal/open-enterprise-apps.png)
4. Az a **vállalati alkalmazások** ablaktáblán válassza előbb **összes alkalmazás**. Kezelheti az alkalmazások listájának megtekintéséhez.
5. Az a **vállalati alkalmazások – összes alkalmazás** ablaktáblában jelöljön ki egy alkalmazást.
6. Az a ***appname*** (Ez azt jelenti, hogy a ablaktáblán nevű, a kijelölt alkalmazást a címben) ablaktáblán válassza ki **tulajdonságok**.

    ![Az összes alkalmazások paranccsal](./media/active-directory-coreapps-disable-app-azure-portal/select-app.png)
7. Az a ***appname*** - **tulajdonságok** ablaktáblán válassza előbb **nem** a **engedélyezve van, hogy a felhasználók bejelentkezési?**.
8. Válassza ki a **mentése** parancsot.

## <a name="next-steps"></a>További lépések
* [Összes csoport megtekintéshez](active-directory-groups-view-azure-portal.md)
* [Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](active-directory-coreapps-assign-user-azure-portal.md)
* [Egy felhasználó vagy csoport-hozzárendelés eltávolítása a vállalati alkalmazások](active-directory-coreapps-remove-assignment-azure-portal.md)
* [Módosítja a nevét, vagy egy vállalati alkalmazás embléma](active-directory-coreapps-change-app-logo-user-azure-portal.md)
