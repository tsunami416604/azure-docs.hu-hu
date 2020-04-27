---
title: Felhasználó hozzáférésének eltávolítása egy alkalmazáshoz | Microsoft Docs
description: Ismerje meg, hogyan távolíthatja el a felhasználók egy alkalmazáshoz való hozzáférését
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
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/26/2020
ms.locfileid: "65826102"
---
# <a name="how-to-remove-a-users-access-to-an-application"></a>Felhasználó hozzáférésének eltávolítása egy alkalmazáshoz

Ebből a cikkből megtudhatja, hogyan távolíthatja el a felhasználók egy alkalmazáshoz való hozzáférését.

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>Egy adott felhasználó vagy csoport hozzárendelését szeretném eltávolítani egy alkalmazáshoz

Ha el szeretne távolítani egy felhasználói vagy csoport-hozzárendelést egy alkalmazáshoz, kövesse a [felhasználó vagy csoport kiosztásának eltávolítása vállalati alkalmazásból Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal) cikkben felsorolt lépéseket.

## <a name="i-want-to-disable-all-access-to-an-application-for-every-user"></a>Le szeretném tiltani egy alkalmazás összes hozzáférését minden felhasználó számára

Ha le szeretné tiltani az összes felhasználói bejelentkezést egy alkalmazásba, kövesse a [vállalati alkalmazás felhasználói bejelentkezésének letiltása Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal) cikkben felsorolt lépéseket.

## <a name="i-want-to-delete-an-application-entirely"></a>Teljesen törölni szeretném az alkalmazást

**Egy alkalmazás törléséhez**kövesse az alábbi utasításokat:

1. Nyissa meg a [**Azure Portal**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként** vagy **társ-** rendszergazdaként.

2. Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3. Írja be a **"Azure Active Directory**" kifejezést a szűrő keresőmezőbe, és válassza ki a **Azure Active Directory** elemet.

4. Kattintson a **vállalati alkalmazások** lehetőségre a bal oldali navigációs menü Azure Active Directory.

5. Kattintson a **minden alkalmazás** elemre az alkalmazások listájának megtekintéséhez.

   * Ha nem látja a használni kívánt alkalmazást, használja a **minden alkalmazás lista** tetején található **szűrő** vezérlőelemet, és állítsa a **show (megjelenítés** ) lehetőséget az **összes alkalmazásra.**

6. Válassza ki a törölni kívánt alkalmazást.

7. Az alkalmazás betöltése után kattintson a **Törlés** ikonra a felső alkalmazás **Áttekintés** paneljén.

## <a name="i-want-to-disable-all-future-user-consent-operations-to-any-application"></a>Le szeretném tiltani az összes jövőbeli felhasználói beleegyező műveletet bármely alkalmazáshoz

A teljes címtár felhasználói beleegyezésének letiltása megakadályozza, hogy a végfelhasználók bármilyen alkalmazást fogadnak el. A rendszergazdák továbbra is engedélyezhetik a felhasználó nevében való hozzájárulásukat. Az alkalmazások beleegyezésével kapcsolatos további információkért, és miért nem kívánja ezt megtenni, olvassa el a [felhasználói és rendszergazdai beleegyezést](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent)ismertető témakört. Lásd még:, [engedélyek és beleegyezett](../develop/v2-permissions-and-consent.md).

Ha **le szeretné tiltani az összes jövőbeli felhasználói beleegyező műveletet a teljes címtárban**, kövesse az alábbi utasításokat:

1.  Nyissa meg a [**Azure Portalt**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként.**

2.  A **Azure Active Directory bővítmény** megnyitása 

3.  A navigációs menüben kattintson a **vállalati alkalmazások** elemre.

5.  Kattintson a **felhasználói beállítások**elemre.

6.  Beállíthatja, hogy a **felhasználók engedélyezhetik az alkalmazások számára, hogy a nevükben lévő vállalati adatcserét** **nem** értékre, majd kattintson a Save (Mentés) gombra.


## <a name="next-steps"></a>További lépések

[Az alkalmazásokhoz való hozzáférés kezelése](what-is-access-management.md)
