---
title: Felhasználó hozzáférésének eltávolítása Azure Active Directory
description: Megtudhatja, hogyan távolíthatja el egy felhasználó hozzáférését egy alkalmazáshoz Azure Active Directory
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 11/02/2020
ms.author: kenwith
ms.openlocfilehash: 28b31d98f283dc957927ab2a35f0ab95bf066473
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2020
ms.locfileid: "94654116"
---
# <a name="how-to-remove-a-users-access-to-an-application"></a>Felhasználó hozzáférésének eltávolítása egy alkalmazáshoz

Ebből a cikkből megtudhatja, hogyan távolíthatja el a felhasználók egy alkalmazáshoz való hozzáférését.

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>Egy adott felhasználó vagy csoport hozzárendelését szeretném eltávolítani egy alkalmazáshoz

Ha el szeretne távolítani egy felhasználói vagy csoport-hozzárendelést egy alkalmazáshoz, kövesse a [felhasználó vagy csoport kiosztásának eltávolítása vállalati alkalmazásból Azure Active Directory](./assign-user-or-group-access-portal.md) cikkben felsorolt lépéseket.

## <a name="i-want-to-disable-all-access-to-an-application-for-every-user"></a>Le szeretném tiltani egy alkalmazás összes hozzáférését minden felhasználó számára

Ha le szeretné tiltani az összes felhasználói bejelentkezést egy alkalmazásba, kövesse a [vállalati alkalmazás felhasználói bejelentkezésének letiltása Azure Active Directory](./disable-user-sign-in-portal.md) cikkben felsorolt lépéseket.

## <a name="i-want-to-delete-an-application-entirely"></a>Teljesen törölni szeretném az alkalmazást

Az [alkalmazások kezelésének](delete-application-portal.md) gyors üzembe helyezési sorozata útmutatást nyújt az alkalmazások Azure Active Directory bérlőből való törléséhez.

## <a name="i-want-to-disable-all-future-user-consent-operations-to-any-application"></a>Le szeretném tiltani az összes jövőbeli felhasználói beleegyező műveletet bármely alkalmazáshoz

A teljes címtár felhasználói beleegyezésének letiltása megakadályozza, hogy a végfelhasználók bármilyen alkalmazást fogadnak el. A rendszergazdák továbbra is engedélyezhetik a felhasználó nevében való hozzájárulásukat. Az alkalmazások beleegyezésével kapcsolatos további információkért, és miért nem kívánja ezt megtenni, olvassa el a [felhasználói és rendszergazdai beleegyezést](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent)ismertető témakört. Lásd még:, [engedélyek és beleegyezett](../develop/v2-permissions-and-consent.md).

Ha **le szeretné tiltani az összes jövőbeli felhasználói beleegyező műveletet a teljes címtárban**, kövesse az alábbi utasításokat:

1.  Nyissa meg a [**Azure Portalt**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként.**

2.  A **Azure Active Directory bővítmény** megnyitása 

3.  A navigációs menüben kattintson a **vállalati alkalmazások** elemre.

5.  Kattintson a **felhasználói beállítások** elemre.

6.  Beállíthatja, hogy a **felhasználók engedélyezhetik az alkalmazások számára, hogy a nevükben lévő vállalati adatcserét** **nem** értékre, majd kattintson a Save (Mentés) gombra.


## <a name="next-steps"></a>Következő lépések

[Az alkalmazásokhoz való hozzáférés kezelése](what-is-access-management.md)