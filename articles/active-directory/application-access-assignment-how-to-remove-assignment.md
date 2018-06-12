---
title: A felhasználó hozzáférést egy alkalmazáshoz eltávolítása |} Microsoft Docs
description: Megtudhatja, hogyan távolítsa el a felhasználó hozzáférést egy alkalmazáshoz
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 3731049d2bc42c7fac492f7609f54b5d4a1443b6
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35292320"
---
# <a name="how-to-remove-a-users-access-to-an-application"></a>A felhasználó hozzáférést egy alkalmazáshoz eltávolítása

Ez a cikk segítségével megismerheti, hogyan távolíthatja el egy felhasználó hozzáférést egy alkalmazáshoz.

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>Egy alkalmazás egy adott felhasználó vagy csoport-hozzárendelés eltávolítása

Egy felhasználó vagy csoport-hozzárendelés alkalmazáshoz való eltávolításához kövesse a lépéseket a [egy felhasználó vagy csoport-hozzárendelés eltávolítása egy vállalati alkalmazás Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal) cikk.

. ## szeretném tiltani minden hozzáférést egy alkalmazáshoz minden felhasználó

Minden felhasználói bejelentkezések alkalmazáshoz való letiltásához kövesse a lépéseket a [tiltsa le a felhasználói bejelentkezések Azure Active Directory vállalati alkalmazás](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal) cikk.

## <a name="i-want-to-delete-an-application-entirely"></a>Az alkalmazás teljesen törlése

A **alkalmazás törlése**, kövesse az alábbi utasításokat:

1.  Nyissa meg a [ **Azure-portálon** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazda** vagy **Co-rendszergazda segítségét.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" a szűrő keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **összes alkalmazás** az alkalmazások listájának megtekintéséhez.

   * Ha azt szeretné, hogy itt megjelennek az alkalmazás nem látja, használja a **szűrő** vezérlő tetején a **összes alkalmazások listáját** és állítsa be a **megjelenítése** lehetőséggel **összes Alkalmazások.**

6.  Válassza ki a törölni kívánt alkalmazást.

7.  Ha az alkalmazás betölt, kattintson **törlése** ikonra a felső alkalmazás **áttekintése** ablaktáblán.

## <a name="i-want-to-disable-all-future-user-consent-operations-to-any-application"></a>Szeretném tiltani az összes jövőbeni felhasználói hozzájárulás műveletek bármely alkalmazás

Felhasználói hozzájárulás letiltása, az a teljes címtár megakadályozhatja a végfelhasználók számára hozzájárul ahhoz, hogy minden alkalmazás. A rendszergazdák továbbra is a felhasználó behalves is hozzájárulás. Tudjon meg többet a kérelem jóváhagyását, és ezért előfordulhat, hogy, vagy előfordulhat, hogy nem szeretne ehhez olvassa el a [ismertetése felhasználói és rendszergazdai hozzájárulási](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent).

A **tiltsa le a teljes címtár minden jövőbeni felhasználói hozzájárulás műveletei**, kövesse az alábbi utasításokat:

1.  Nyissa meg a [ **Azure-portálon** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" a szűrő keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

4.  Kattintson a **felhasználók és csoportok** a navigációs menü.

5.  Kattintson a **felhasználói beállítások**.

6.  Tiltsa le az összes jövőbeni felhasználói hozzájárulás műveletek úgy, hogy a **felhasználók is engedélyezi, hogy az alkalmazások hozzáférjenek az adataikhoz** kapcsolót **nem** , és kattintson a **mentése** gombra.


# <a name="next-steps"></a>További lépések
[Az alkalmazásokhoz való hozzáférés kezelése](manage-apps/what-is-access-management.md)
