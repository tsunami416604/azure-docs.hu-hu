---
title: Licencek hozzárendelése csoporthoz – Azure Active Directory | Microsoft dokumentumok
description: Licencek hozzárendelése a felhasználókhoz az Azure Active Directory csoportlicencelésével
services: active-directory
keywords: Az Azure AD licencelése
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 130ce05e332f4705feb4acd54cbeb25d25a82532
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253064"
---
# <a name="assign-licenses-to-users-by-group-membership-in-azure-active-directory"></a>Licencek hozzárendelése a felhasználókhoz csoporttagság szerint az Azure Active Directoryban

Ez a cikk bemutatja a terméklicencek hozzárendelését egy felhasználói csoporthoz, és ellenőrzi, hogy azok megfelelő licenccel rendelkeznek-e az Azure Active Directoryban (Azure AD).

Ebben a példában a bérlő egy **HR-osztály**nevű biztonsági csoportot tartalmaz. Ez a csoport a humánerőforrás-osztály összes tagját (körülbelül 1000 felhasználót) foglalja magában. Office 365 Nagyvállalati E3-licenceket szeretne hozzárendelni a teljes részleghez. A termékben található Yammer Enterprise szolgáltatást ideiglenesen le kell tiltani, amíg a részleg készen nem áll a termék használatára. Az Enterprise Mobility + Security licenceket is ugyanahhoz a felhasználói csoporthoz szeretné telepíteni.

> [!NOTE]
> Nem minden Microsoft-szolgáltatás érhető el minden területen. Mielőtt egy licencet hozzá lehetne rendelni egy felhasználóhoz, a rendszergazdának meg kell adnia a felhasználó Használati hely tulajdonságát.
>
> Csoportlicenc-hozzárendelés esetén a megadott használati hely nélküli felhasználók öröklik a címtár helyét. Ha több helyen is vannak felhasználói, azt javasoljuk, hogy mindig állítsa be a használati helyet a felhasználói létrehozási folyamat részeként az Azure AD-ben (pl. AAD Connect konfiguráción keresztül), amely biztosítja, hogy a licenc-hozzárendelés eredménye mindig helyes legyen, és a felhasználók nem kapnak szolgáltatások olyan helyeken, amelyek nem engedélyezettek.

## <a name="step-1-assign-the-required-licenses"></a>1. lépés: A szükséges licencek hozzárendelése

1. Jelentkezzen be az [**Azure AD felügyeleti központba**](https://aad.portal.azure.com) egy licencrendszergazdai fiókkal. A licencek kezeléséhez a fióknak licencrendszergazdának, felhasználórendszergazdának vagy globális rendszergazdának kell lennie.

1. **Válassza a Licencek** lehetőséget egy olyan oldal megnyitásához, ahol a bérlőben lévő összes licencelhető termék látható és kezelhető.

1. A **Minden termék csoportban**válassza ki az Office 365 Nagyvállalati E5 és az Enterprise Mobility + Security E3 lehetőséget a terméknevek kiválasztásával. A hozzárendelés elindításához válassza a lap tetején a **Hozzárendelés** lehetőséget.

   ![A licenceket hozzárendelő termékek kiválasztása](./media/licensing-groups-assign/licenses-all-products-assign.png)
  
1. A **Licenc hozzárendelése** lapon válassza a **Felhasználók és csoportok** lehetőséget a felhasználók és csoportok listájának megnyitásához.

1. Jelöljön ki egy felhasználót vagy csoportot, majd a kijelölés megerősítéséhez használja a lap alján található **Kijelölés** gombot.

1. A **Licenc hozzárendelése** lapon kattintson a **Hozzárendelési beállítások**gombra, amely a korábban kiválasztott két termékben szereplő összes szolgáltatási tervet megjeleníti. Keresse meg a **Yammer Enterprise-t,** és kapcsolja **ki,** hogy letiltsa a szolgáltatást a terméklicencből. A megerősítéshez kattintson a **Licencbeállítások**alján található **OK** gombra.

   ![licenccsomagok szervizcsomagjainak kiválasztása](./media/licensing-groups-assign/assignment-options.png)
  
1. A hozzárendelés befejezéséhez kattintson a **Licenc hozzárendelése** lapon a lap alján található **Hozzárendelés** gombra.

1. A jobb felső sarokban megjelenik egy értesítés, amely a folyamat állapotát és eredményét mutatja. Ha a csoporthoz való hozzárendelés nem fejezhető be (például a csoportban már meglévő licencek miatt), kattintson az értesítésre a hiba részleteinek megtekintéséhez.

Amikor licencek hozzárendelése egy csoporthoz, az Azure AD feldolgozza a csoport összes meglévő tagját. Ez a folyamat eltarthat egy ideig, a csoport méretétől eltérően. A következő lépés azt ismerteti, hogy miként ellenőrizheti a folyamat befejezését, és hogyan állapítható meg, hogy szükség van-e további figyelemre a problémák megoldásához.

## <a name="step-2-verify-that-the-initial-assignment-has-finished"></a>2. lépés: Annak ellenőrzése, hogy a kezdeti hozzárendelés befejeződött-e

1. Nyissa meg az **Azure Active Directory-csoportok** > **at.** Válassza ki azt a csoportot, amelyhez a licenceket hozzárendelték.

1. A csoportlapon válassza a **Licencek**lehetőséget. Ez lehetővé teszi annak gyors megerősítését, hogy a licencek teljes mértékben hozzá vannak-e rendelve a felhasználókhoz, és hogy vannak-e olyan hibák, amelyeket meg kell vizsgálnia. A következő információk állnak rendelkezésre:

   - A csoporthoz jelenleg hozzárendelt szervizlicencek. Válasszon egy bejegyzést az engedélyezett szolgáltatások megjelenítéséhez és a módosításokhoz.

   - A legújabb licencmódosítások állapotfrissítései, amelyek akkor érhetők el, ha a módosítások feldolgozása folyamatban van, vagy ha a feldolgozás befejeződött az összes felhasználó tag számára.

   - Információ a hibás állapotú felhasználói licenc-hozzárendelésekről.

   ![licencelési hibák és licencállapot](./media/licensing-groups-assign/assignment-errors.png)

1. A licencfeldolgozással kapcsolatos részletesebb információkat az **Azure Active Directory** > **– felhasználók és csoportok** > *csoportnevének* > naplózási naplói című**témakörben talál.** Ellenőrizze a következő tevékenységeket:

   - Tevékenység: `Start applying group based license to users`. Ezt akkor naplózza a rendszer, amikor a rendszer felveszi a licenchozzárendelés-módosítást a csoporton, és elkezdi alkalmazni az összes felhasználótagra. Információkat tartalmaz a végrehajtott módosításról.

   - Tevékenység: `Finish applying group based license to users`. Ezt a rendszer akkor naplózza, amikor a rendszer befejezi a csoport összes felhasználójának feldolgozását. Összegzést tartalmaz arról, hogy hány felhasználót sikerült feldolgozni, és hány felhasználóhoz nem lehetett csoportlicenceket rendelni.

   [Ebből a szakaszból](licensing-group-advanced.md#use-audit-logs-to-monitor-group-based-licensing-activity) megtudhatja, hogy a naplónaplók hogyan használhatók a csoportalapú licencelés által végrehajtott módosítások elemzéséhez.

## <a name="step-3-check-for-license-problems-and-resolve-them"></a>3. lépés: Ellenőrizze a licencproblémákat, és oldja meg azokat

1. Nyissa meg az Azure Active > **Directory-csoportokat,** és keresse meg azt a csoportot, amelyhez a licenceket hozzárendelték. **Azure Active Directory**
1. A csoportlapon válassza a **Licencek**lehetőséget. A lap tetején látható értesítés azt mutatja, hogy 10 olyan felhasználó van, akihez a licenceket nem lehetett hozzárendelni. Nyissa meg a csoport licencelési hibaállapotában lévő összes felhasználó listáját.
1. A **sikertelen hozzárendelések** oszlop azt jelzi, hogy mindkét terméklicenc nem rendelhető hozzá a felhasználókhoz. A **hiba fő oka** oszlop tartalmazza a hiba okát. Ebben az esetben az **ütköző szolgáltatási csomagok**.

   ![nem hozzárendelhető licencek](./media/licensing-groups-assign/failed-assignments.png)

1. Jelöljön ki egy felhasználót a felhasználói licencek lap **megnyitásához.** Ezen a lapon a felhasználóhoz jelenleg hozzárendelt összes licenc látható. Ebben a példában a felhasználó rendelkezik az Office 365 Nagyvállalati E1-licenccel, amely a **Kioszk-felhasználók** csoporttól örökölt. Ez ütközik azzal az E3 licenccel, amelyet a rendszer a **HR-osztály** csoportból próbált alkalmazni. Ennek eredményeképpen az adott csoport egyetlen licence sem lett hozzárendelve a felhasználóhoz.

   ![Felhasználó összes licencütközésének megtekintése](./media/licensing-groups-assign/user-licence-conflicting-service-plans.png)

1. Az ütközés megoldásához távolítsa el a felhasználót a **Kioszk-felhasználók** csoportból. Miután az Azure AD feldolgozza a módosítást, a **HR-részleg** licencei megfelelően vannak hozzárendelve.

## <a name="next-steps"></a>További lépések

Ha többet szeretne tudni a csoportok használatával történő licenchozzárendelés szolgáltatáskészletéről, olvassa el az alábbi cikkeket:

- [Mi a csoportalapú licencelés az Azure Active Directoryban?](/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context)
- [A csoportok licencproblémáinak azonosítása és megoldása az Azure Active Directoryban](licensing-groups-resolve-problems.md)
- [Egyéni, licenccel rendelkező felhasználók migrálása csoportalapú licencelésre az Azure Active Directoryban](licensing-groups-migrate-users.md)
- [Felhasználók áttelepítése a terméklicencek között az Azure Active Directory csoportalapú licencelésével](licensing-groups-change-licenses.md)
- [Az Azure Active Directory csoportalapú licencelésének további forgatókönyvei](../active-directory-licensing-group-advanced.md)
- [Példák a Csoportalapú licenceléshez az Azure Active Directoryban](licensing-ps-examples.md)
