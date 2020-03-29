---
title: A felhasználó alkalmazáshoz való hozzáférésének eltávolítása | Microsoft dokumentumok
description: A felhasználó alkalmazáshoz való hozzáférésének eltávolítása
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/17/2018
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b69502995eff88df53af3671a8e611809f83e59
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "65826102"
---
# <a name="how-to-remove-a-users-access-to-an-application"></a>A felhasználó alkalmazáshoz való hozzáférésének eltávolítása

Ez a cikk segít megérteni, hogyan távolíthatja el a felhasználó hozzáférést egy alkalmazáshoz.

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>El szeretném távolítani egy adott felhasználó vagy csoport hozzárendelését egy alkalmazáshoz

Ha el szeretne távolítani egy felhasználó- vagy csoporthozzárendelést egy alkalmazáshoz, kövesse a [Felhasználó vagy csoport hozzárendeléseltávolítása vállalati alkalmazásból](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal) az Azure Active Directory cikkben szereplő lépéseket.

## <a name="i-want-to-disable-all-access-to-an-application-for-every-user"></a>Azt akarom, hogy tiltsa le az összes hozzáférést egy alkalmazás minden felhasználó számára

Az alkalmazásba való összes felhasználói bejelentkezés letiltásához kövesse a [vállalati alkalmazások felhasználói bejelentkezéseinek letiltása az Azure Active Directory-cikkben](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal) szereplő lépéseket.

## <a name="i-want-to-delete-an-application-entirely"></a>Teljesen törölni szeretnék egy alkalmazást

**Egy alkalmazás törléséhez**kövesse az alábbi utasításokat:

1. Nyissa meg az [**Azure Portalt,**](https://portal.azure.com/) és jelentkezzen be **globális rendszergazdaként** vagy **társrendszergazdaként.**

2. Nyissa meg az **Azure Active Directory-bővítményt** a bal oldali főnavigációs menü tetején található **Összes szolgáltatás** elemre kattintva.

3. Írja be az **"Azure Active Directory"** kifejezést a szűrő keresőmezőjébe, és válassza ki az **Azure Active Directory-elemet.**

4. Kattintson az Azure Active Directory bal oldali navigációs **menüjében** található Vállalati alkalmazások parancsra.

5. Kattintson **a Minden alkalmazás** elemre az összes alkalmazás listájának megtekintéséhez.

   * Ha nem látja a kívánt alkalmazást, használja a **Szűrő** vezérlőt a **Minden alkalmazás lista** tetején, és állítsa a **Megjelenítés** beállítást **minden alkalmazásra.**

6. Jelölje ki a törölni kívánt alkalmazást.

7. Miután az alkalmazás betöltődik, kattintson a **Törlés** ikonra a felső alkalmazás **áttekintő** ablaktábláján.

## <a name="i-want-to-disable-all-future-user-consent-operations-to-any-application"></a>Azt akarom, hogy tiltsa le az összes jövőbeli felhasználói hozzájárulási műveletek bármely alkalmazás

A teljes címtár felhasználói hozzájárulásának letiltása megakadályozza, hogy a végfelhasználók bármely alkalmazáshoz hozzájáruljanak. A rendszergazdák továbbra is beleegyezhetnek a felhasználó nevében. Az alkalmazás beleegyezésével, valamint azzal kapcsolatban, hogy miért kívánja ezt megtenni, olvassa el [a Felhasználói és rendszergazdai jóváhagyás ismertetése című témakört.](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent) Lásd még: [Engedélyek és hozzájárulás](../develop/v2-permissions-and-consent.md).

A **teljes címtárban a felhasználó hozzájárulási műveleteinek letiltásához**kövesse az alábbi utasításokat:

1.  Nyissa meg az [**Azure Portalt,**](https://portal.azure.com/) és jelentkezzen be **globális rendszergazdaként.**

2.  Az **Azure Active Directory-bővítmény megnyitása** 

3.  Kattintson a navigációs menü **Vállalati alkalmazások parancsára.**

5.  Kattintson **a Felhasználói beállítások gombra.**

6.  Állítsa be a **felhasználók engedélyezhetik, hogy az alkalmazások hozzáférjenek a vállalati adatokhoz a nevükben,** és ne-re **kapcsoljanak,** és kattintson a Mentés gombra.


## <a name="next-steps"></a>További lépések

[Alkalmazásokhoz való hozzáférés kezelése](what-is-access-management.md)
