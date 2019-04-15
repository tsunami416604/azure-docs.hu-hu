---
title: Tiltsa le a felhasználók bejelentkezési folyamatába egy vállalati alkalmazás az Azure Active Directoryban |} A Microsoft Docs
description: Vállalati alkalmazás letiltása, hogy az egyik felhasználó sem lehet bejelentkezni az Azure Active Directoryban
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: celested
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a168c3415996ff97137c4e312348f74a48e9531b
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2019
ms.locfileid: "59564992"
---
# <a name="disable-user-sign-ins-for-an-enterprise-app-in-azure-active-directory"></a>Tiltsa le a felhasználók bejelentkezési folyamatába egy vállalati alkalmazás az Azure Active Directoryban
Vállalati alkalmazás letiltásához, ezért nem lehet bejelentkezni, az Azure Active Directoryban (Azure AD) könnyebbé vált. A megfelelő engedélyekkel a vállalati alkalmazások kezelésére van szüksége. És a címtár globális rendszergazdának kell lennie.

## <a name="how-do-i-disable-user-sign-ins"></a>Hogyan tilthatom le a felhasználói bejelentkezéseket?
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) egy olyan fiókkal, amely a címtár globális rendszergazdája.
1. Válassza ki **minden szolgáltatás**, adja meg **Azure Active Directory** a szövegmezőbe, és válassza ki a **Enter**.
1. Az a **Azure Active Directory** -  ***directoryname*** (azaz a az Azure AD ablaktáblán a címtár kezelése) ablaktáblában válassza **vállalati alkalmazások**.
1. Az a **nagyvállalati alkalmazások – minden alkalmazás** panelen kezelheti az alkalmazások listájának megtekintéséhez. Válasszon ki egy alkalmazást.
1. Az a ***appname*** (azaz a panelen címében szerepel a kijelölt alkalmazás nevét) ablaktáblában válassza **tulajdonságok**.
1. Az a ***appname*** - **tulajdonságok** ablaktáblán válassza **nem** a **engedélyezett a felhasználók bejelentkezhetnek?**.
1. Válassza ki a **mentése** parancsot.

## <a name="next-steps"></a>További lépések
* [Tekintse meg az összes saját csoportok](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](assign-user-or-group-access-portal.md)
* [Egy felhasználó vagy csoport-hozzárendelés eltávolítása a vállalati alkalmazás](remove-user-or-group-access-portal.md)
* [A name vagy a vállalati alkalmazás emblémájának módosítása](change-name-or-logo-portal.md)
