---
title: Tiltsa le a felhasználók bejelentkezési folyamatába egy vállalati alkalmazás az Azure Active Directoryban |} A Microsoft Docs
description: Vállalati alkalmazás letiltása, hogy az egyik felhasználó sem lehet bejelentkezni az Azure Active Directoryban
services: active-directory
documentationcenter: ''
author: barbkess
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/28/2017
ms.author: barbkess
ms.reviewer: asteen
ms.custom: it-pro
ms.openlocfilehash: 4ae5fb2fac540c9dfe011eb7b118c06d87424287
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55153368"
---
# <a name="disable-user-sign-ins-for-an-enterprise-app-in-azure-active-directory"></a>Tiltsa le a felhasználók bejelentkezési folyamatába egy vállalati alkalmazás az Azure Active Directoryban
Tiltsa le a vállalati alkalmazásokhoz, így nem lehet bejelentkezni, az Azure Active Directoryban (Azure AD) könnyebbé vált. A vállalati alkalmazások kezelésére a megfelelő engedélyekkel kell rendelkeznie, és a címtár globális rendszergazdának kell lennie.

## <a name="how-do-i-disable-user-sign-ins"></a>Hogyan tilthatom le a felhasználói bejelentkezéseket?
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) egy olyan fiókkal, amely a címtár globális rendszergazdája.
2. Válassza ki **minden szolgáltatás**, adja meg **Azure Active Directory** a szövegmezőbe, és válassza ki a **Enter**.
3. Az a **Azure Active Directory** -  ***directoryname*** (azt jelenti, az Azure ad-ben ablaktáblán kezeli a könyvtár) ablaktáblában válassza **vállalati alkalmazások**.

    ![Vállalati alkalmazások megnyitása](./media/disable-user-sign-in-portal/open-enterprise-apps.png)
4. Az a **vállalati alkalmazások** ablaktáblán válassza előbb **minden alkalmazás**. Kezelheti az alkalmazások listáját láthatja.
5. Az a **nagyvállalati alkalmazások – minden alkalmazás** ablaktáblán jelöljön ki egy alkalmazást.
6. Az a ***appname*** (azaz a panelen címében szerepel a kijelölt alkalmazás nevét) ablaktáblában válassza **tulajdonságok**.

    ![Az alkalmazások minden parancs kiválasztása](./media/disable-user-sign-in-portal/select-app.png)
7. Az a ***appname*** - **tulajdonságok** ablaktáblán válassza **nem** a **engedélyezett a felhasználók bejelentkezhetnek?**.
8. Válassza ki a **mentése** parancsot.

## <a name="next-steps"></a>További lépések
* [Tekintse meg az összes saját csoportok](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](assign-user-or-group-access-portal.md)
* [Egy felhasználó vagy csoport-hozzárendelés eltávolítása a vállalati alkalmazás](remove-user-or-group-access-portal.md)
* [A name vagy a vállalati alkalmazás emblémájának módosítása](change-name-or-logo-portal.md)
