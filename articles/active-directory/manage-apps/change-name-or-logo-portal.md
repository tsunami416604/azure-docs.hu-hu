---
title: Módosítja a nevét, vagy egy vállalati alkalmazás Azure Active Directoryban embléma |} Microsoft Docs
description: Hogyan módosítja a nevét vagy az Azure Active Directoryban egyéni vállalati alkalmazások embléma
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
ms.openlocfilehash: ad424d6ca8ea8c35aa502a3d1bd98940591c38e8
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35303597"
---
# <a name="change-the-name-or-logo-of-an-enterprise-app-in-azure-active-directory"></a>Módosítja a nevét, vagy egy vállalati alkalmazás Azure Active Directoryban embléma
Nem módosítja a nevét, vagy egy egyéni vállalati alkalmazás Azure Active Directory (Azure AD) emblémának. Az ilyen jellegű változtatásokat a megfelelő engedélyekkel kell rendelkeznie, és be kell az egyéni alkalmazás hozta létre.

## <a name="how-do-i-change-an-enterprise-apps-name-or-logo"></a>Hogyan változtathatom meg egy vállalati alkalmazás nevét vagy embléma?
1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com) egy olyan fiókkal, amely a címtár globális rendszergazdája.
2. Válassza ki **minden szolgáltatás**, adja meg **Azure Active Directory** a szövegmezőbe, majd válassza ki azt a **Enter**.
3. Az a **Azure Active Directory - *directoryname***  (Ez azt jelenti, hogy az Azure AD ablaktáblán a kezelt könyvtár) ablaktáblán válassza ki **vállalati alkalmazások**.

    ![Vállalati alkalmazások megnyitásakor](./media/change-name-or-logo-portal/open-enterprise-apps.png)
4. Az a **vállalati alkalmazások** ablaktáblán válassza előbb **összes alkalmazás**. Kezelheti az alkalmazások listájának megtekintéséhez.
5. Az a **vállalati alkalmazások – összes alkalmazás** ablaktáblában jelöljön ki egy alkalmazást.
6. Az a ***appname*** (Ez azt jelenti, hogy a ablaktáblán nevű, a kijelölt alkalmazást a címben) ablaktáblán válassza ki **tulajdonságok**.

    ![A Tulajdonságok parancs kiválasztása](./media/change-name-or-logo-portal/select-app.png)
7. Az a ***appname*** **-tulajdonságok** ablaktáblán, keresse meg egy fájlt egy új embléma használják, vagy szerkesztheti az alkalmazás neve, vagy mindkettőt.

    ![Az alkalmazás embléma vagy nameproperties parancs módosítása](./media/change-name-or-logo-portal/change-logo.png)
8. Válassza ki a **mentése** parancsot.

## <a name="next-steps"></a>További lépések
* [Összes saját csoportok](../active-directory-groups-view-azure-portal.md)
* [Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](assign-user-or-group-access-portal.md)
* [Egy felhasználó vagy csoport-hozzárendelés eltávolítása a vállalati alkalmazások](remove-user-or-group-access-portal.md)
* [Tiltsa le a felhasználói bejelentkezéseket a vállalati alkalmazás](disable-user-sign-in-portal.md)
