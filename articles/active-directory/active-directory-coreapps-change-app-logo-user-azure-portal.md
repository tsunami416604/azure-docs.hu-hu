---
title: "Módosítja a nevét, vagy egy vállalati alkalmazás Azure Active Directoryban embléma |} Microsoft Docs"
description: "Hogyan módosítja a nevét vagy az Azure Active Directoryban egyéni vállalati alkalmazások embléma"
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.assetid: d01303ce-e6cb-4f3b-a4d6-ec29dfd68146
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: curtand
ms.reviewer: asteen
ms.custom: it-pro
ms.openlocfilehash: fe03d2a8dcb39cb06679386959ac17354a543089
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="change-the-name-or-logo-of-an-enterprise-app-in-azure-active-directory"></a>Módosítja a nevét, vagy egy vállalati alkalmazás Azure Active Directoryban embléma
Nem módosítja a nevét, vagy egy egyéni vállalati alkalmazás Azure Active Directory (Azure AD) emblémának. Az ilyen jellegű változtatásokat a megfelelő engedélyekkel kell rendelkeznie, és be kell az egyéni alkalmazás hozta létre.

## <a name="how-do-i-change-an-enterprise-apps-name-or-logo"></a>Hogyan változtathatom meg egy vállalati alkalmazás nevét vagy embléma?
1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com) egy olyan fiókkal, amely a címtár globális rendszergazdája.
2. Válassza ki **minden szolgáltatás**, adja meg **Azure Active Directory** a szövegmezőbe, majd válassza ki azt a **Enter**.
3. Az a **Azure Active Directory - *directoryname***  (Ez azt jelenti, hogy az Azure AD ablaktáblán a kezelt könyvtár) ablaktáblán válassza ki **vállalati alkalmazások**.

    ![Vállalati alkalmazások megnyitásakor](./media/active-directory-coreapps-change-app-logo-azure-portal/open-enterprise-apps.png)
4. Az a **vállalati alkalmazások** ablaktáblán válassza előbb **összes alkalmazás**. Kezelheti az alkalmazások listájának megtekintéséhez.
5. Az a **vállalati alkalmazások – összes alkalmazás** ablaktáblában jelöljön ki egy alkalmazást.
6. Az a ***appname*** (Ez azt jelenti, hogy a ablaktáblán nevű, a kijelölt alkalmazást a címben) ablaktáblán válassza ki **tulajdonságok**.

    ![A Tulajdonságok parancs kiválasztása](./media/active-directory-coreapps-change-app-logo-azure-portal/select-app.png)
7. Az a ***appname*** **-tulajdonságok** ablaktáblán, keresse meg egy fájlt egy új embléma használják, vagy szerkesztheti az alkalmazás neve, vagy mindkettőt.

    ![Az alkalmazás embléma vagy nameproperties parancs módosítása](./media/active-directory-coreapps-change-app-logo-azure-portal/change-logo.png)
8. Válassza ki a **mentése** parancsot.

## <a name="next-steps"></a>További lépések
* [Összes saját csoportok](active-directory-groups-view-azure-portal.md)
* [Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](active-directory-coreapps-assign-user-azure-portal.md)
* [Egy felhasználó vagy csoport-hozzárendelés eltávolítása a vállalati alkalmazások](active-directory-coreapps-remove-assignment-azure-portal.md)
* [Tiltsa le a felhasználói bejelentkezéseket a vállalati alkalmazás](active-directory-coreapps-disable-app-azure-portal.md)
