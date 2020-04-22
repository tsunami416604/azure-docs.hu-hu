---
title: Jelszavak és telefonszámok kezelése a Saját személyzettel (előzetes verzió) - Azure AD | Microsoft dokumentumok
description: Jelszavak és telefonszámok kezelése a felhasználók számára a Saját személyzet segítségével
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.topic: article
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.date: 04/14/2020
ms.author: curtand
ms.reviewer: sahenry
ms.custom: oldportal;it-pro;
ms.openlocfilehash: 29c27c876e90bce6a38226f68adf44a26465cfea
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770852"
---
# <a name="delegate-user-management-with-my-staff-preview"></a>Felhasználói kezelés delegálása a Saját személyzettel (előzetes verzió)

A szervezet a **Saját személyzet** segítségével átruházhatja a felhasználókezelési feladatokat a hatósági adatokra, például egy üzletvezetőre vagy egy csoportvezetőre, hogy segítse a munkatársakat a szükséges alkalmazásokhoz való hozzáférésben. Ha a csapattag nem tud hozzáférni egy alkalmazáshoz, mert elfelejtik a jelszót, a hatékonyság elvész. Ez növeli a támogatási költségeket, és szűk keresztmetszetet okoz a felügyeleti folyamatokban.  A Saját személyzet segítségével a fiókjukhoz nem hozzáférő csapattag néhány kattintással visszanyerheti a hozzáférést, rendszergazdai segítség nélkül.

## <a name="manage-your-staff-in-my-staff"></a>A munkatársak kezelése a Saját személyzetben

A csapattagok kezelése a Saját személyzetben egyszerű. A kezdéshez [nyissa meg a Saját személyzet](https://aka.ms/mystaff)lehetőséget, jelöljön ki egy csapatot vagy helyet, majd jelöljön ki egy felhasználót. A helyeket és a csoporttagokat a rendszergazda határozza meg, és nem módosíthatja őket.

Ha egynél több helyet kezel, a Saját személyzet megkerülésekénél ki kell választania egy helyet a helyhez rendelt csapattag megtekintéséhez.

Ha még nem rendelkezik a Saját személyzet eléréséhez szükséges engedélyekkel, a következő üzenet jelenik meg: "Hoppá, úgy tűnik, jelenleg nem jogosult a Saját személyzet megtekintésére. További információért forduljon az adminisztrátorhoz."

### <a name="find-a-staff-member-in-my-staff"></a>Munkatárs keresése a Saját személyzetben

A kezelés megkezdése előtt meg kell nyitnia egy munkatárs profilját.

1. [Nyissa meg a Saját személyzet et,](https://aka.ms/mystaff) és szükség esetén válasszon ki egy helyet.

    ![A csapattag helyének kiválasztása a Saját személyzet ben](media/my-staff-team-manager/allaus.png)

1. A csapattag profiljának megnyitása.

    ![Válassza ki az egyik felhasználót egy adott helyen a Saját személyzet ben](media/my-staff-team-manager/aupage.png)

## <a name="reset-a-user-password"></a>Új felhasználói jelszó beállítása

Ha a szervezet engedélyt adott Önnek, alaphelyzetbe állíthatja a munkatársak jelszavait.

1. [Nyissa meg a munkatársaim .](https://aka.ms/mystaff)
1. Nyissa meg a munkatársak profilját.
1. Válassza **a Jelszó alaphelyzetbe állítása lehetőséget.**

    ![Felhasználói jelszó alaphelyzetbe állítása a munkatársak között](media/my-staff-team-manager/resetpassword1.png)

1. Az új jelszó létrehozása vagy beírása. Előfordulhat, hogy egy automatikusan generált ideiglenes jelszó jelenik meg, vagy meg kell adnia egy ideiglenes jelszót a felhasználószámára.

    ![Az ideiglenes felhasználói jelszó másolása a Saját személyzet alaphelyzetbe állítása után](media/my-staff-team-manager/resetpassword2.png)

Miután visszaállította a felhasználó jelszavát, adja meg a felhasználónak az ideiglenes jelszót. Amikor a felhasználó bejelentkezik az ideiglenes jelszavával, módosítania kell azt.

## <a name="manage-a-users-phone-number"></a>Felhasználó telefonszámának kezelése

Ha a szervezet engedélyt adott Önnek, kezelheti a munkatársak telefonszámait.

### <a name="add-a-phone-number"></a>Telefonszám hozzáadása

1. [Nyissa meg a munkatársaim .](https://aka.ms/mystaff)
1. Nyissa meg a munkatársak profilját.
1. Válassza **a Telefonszám hozzáadása lehetőséget.**

    ![Felhasználói telefonszám hozzáadása a Munkatársak között](media/my-staff-team-manager/addphone1.png)

1. Adja meg a telefonszámot, és válassza a **Mentés gombot.**

    ![A hozzáadott felhasználói telefonszám mentése a Saját személyzet ben](media/my-staff-team-manager/addphone2.png)

Miután regisztrált egy telefonszámot egy felhasználó számára, a szervezet beállításaitól függően betud jelentkezni SMS-ben, kétlépcsős ellenőrzést végezhet, vagy saját jelszóval állíthatja alaphelyzetbe a jelszavát.

![Új telefonszám regisztrálva a Saját személyzettel](media/my-staff-team-manager/addphone3.png)

### <a name="edit-a-phone-number"></a>Telefonszám szerkesztése

1. [Nyissa meg a munkatársaim .](https://aka.ms/mystaff)
1. Nyissa meg a munkatársak profilját.
1. Válassza **a Telefonszám szerkesztése lehetőséget.**

    ![Válassza a Szerkesztés lehetőséget a felhasználói profilból a Saját munkatársak között](media/my-staff-team-manager/editphone2.png)

1. Írja be az új telefonszámot, és válassza a **Mentés gombot.**

    ![Munkatárs telefonszámának szerkesztése a Saját személyzetben](media/my-staff-team-manager/editphone1.png)

### <a name="enable-phone-number-sign-in-for-a-user"></a>Felhasználó telefonszámának engedélyezése

Ha a felhasználónévként (SMS-bejelentkezés) egy telefonszám használatával történő bejelentkezés engedélyezve van a szervezetben, hozzáadhatja ezt a hitelesítést egy meglévő felhasználói telefonszámhoz.

1. [Nyissa meg a munkatársaim .](https://aka.ms/mystaff)
1. Nyissa meg a munkatársak profilját.
1. Ha a képernyő alján megjelenik egy üzenet arról, hogy a telefonszámával felhasználónévként való bejelentkezés elérhető, válassza az **Engedélyezés** lehetőséget a folyamat megkezdéséhez. Ez az üzenet akkor jelenik meg, ha a felhasználó nak engedélyezve van a telefonszámmal való bejelentkezés.

    ![Az üzenet megtekintése, ha a telefonbejelentkezés támogatott egy helyen a Saját személyzet ben](media/my-staff-team-manager/enableforms1.png)

1. Ha elkészült, válassza az **OK gombot.**

    ![Munkatárs telefonszámának eltávolítása a Saját személyzetből](media/my-staff-team-manager/enableforms2.png)

### <a name="remove-a-phone-number"></a>Telefonszám eltávolítása

1. [Nyissa meg a munkatársaim .](https://aka.ms/mystaff)
1. Nyissa meg a munkatársak profilját.
1. Válassza **a Telefonszám eltávolítása**lehetőséget.
1. Ha elkészült, válassza a **Törlés** lehetőséget.

    ![Munkatárs telefonszámának eltávolítása a Saját személyzetből](media/my-staff-team-manager/deletephone1.png)
