---
title: Licencek hozzárendelése egy csoporthoz az Azure Active Directoryban |} A Microsoft Docs
description: Licencek hozzárendelése a felhasználók révén az Azure Active Directory-csoport licencelésének
services: active-directory
keywords: Az Azure AD licencelése
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.component: users-groups-roles
ms.date: 06/05/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7a235851d7172d32d62c64b163e0b7635a1a47fd
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2018
ms.locfileid: "37861869"
---
# <a name="assign-licenses-to-users-by-group-membership-in-azure-active-directory"></a>Licencek hozzárendelése a felhasználók által az Azure Active Directory biztonságicsoport-tagság

Ez a cikk végigvezeti a termék licencek hozzárendelése az Azure Active Directoryban (Azure AD) a felhasználók egy csoportjára, és majd ellenőrzése, hogy azok még licencelt megfelelően.

Ebben a példában a bérlő tartalmazza-e nevű biztonsági csoport **HR részleg**. Ez a csoport összes tagja az emberi erőforrások részleg (körülbelül 1000 felhasználó) tartalmazza. Office 365 nagyvállalati E3 csomag licenceket hozzárendelheti az egész részleg szeretné. A vállalati Yammer-szolgáltatás, amely megtalálható a termék kell lehet ideiglenesen le van tiltva addig, amíg a részleg készen áll a használatának megkezdéséhez. Akkor is telepíteni kívánja Enterprise Mobility + Security-licenc, az ugyanazon felhasználói csoporthoz.

> [!NOTE]
> Bizonyos Microsoft-szolgáltatások nem érhetők el az összes helyen. Mielőtt egy úgy lehet licencet a felhasználóhoz, a rendszergazda adja meg a használati helyet jelölő tulajdonsághoz meg a felhasználó rendelkezik.

> A licenc-hozzárendelések bármely felhasználó felhasználás helyének megadása nélkül örökli a könyvtár helye. Ha több helyen felhasználóval rendelkezik, azt javasoljuk, hogy mindig állítsa a felhasználási hely része a felhasználói létrehozásának folyamatát, amely biztosítja a licenc-hozzárendelés eredménye (pl. keresztül az AAD Connect-konfiguráció) – Azure AD-ben mindig helyes, és a felhasználók nem kapnak szolgáltatások, a helyeken, amelyek nem engedélyezettek.

## <a name="step-1-assign-the-required-licenses"></a>1. lépés: A szükséges licencek hozzárendelése

1. Jelentkezzen be a [ **az Azure portal** ](https://portal.azure.com) rendszergazdai fiókkal. Licencek kezeléséhez, a fiók globális rendszergazdai szerepkört vagy felhasználói fiók rendszergazdai kell lennie.

2. Válassza ki **minden szolgáltatás** a bal oldali navigációs panelen, és válassza ki a **Azure Active Directory**. Ezen a panelen Hozzáadás a kedvencekhez, illetve rögzítheti a portál irányítópultján.

3. Az a **Azure Active Directory** ablaktáblán válassza előbb **licencek** megnyílik egy panel, amelyen tekintse meg és kezelheti a bérlő összes licencelhető termék.

4. A **minden termék**, válassza ki az Office 365 nagyvállalati E3 csomag és a Enterprise Mobility + Security terméknevek kiválasztásával. A hozzárendelés megkezdéséhez válasszon **hozzárendelése** a panel tetején.

   ![Minden termék licenc hozzárendelése](./media/licensing-groups-assign/all-products-assign.png)

5. Az a **licenc hozzárendelése** ablaktáblán kattintson a **felhasználók és csoportok** megnyitásához a **felhasználók és csoportok** ablaktáblán. Keresse meg a csoport nevének *HR részleg*, válassza ki azt a csoportot, és arra kattintva győződjön meg arról, hogy **kiválasztása** a panel alján.

   ![Csoport kiválasztása](./media/licensing-groups-assign/select-a-group.png)

6. Az a **licenc hozzárendelése** ablaktáblán kattintson a **hozzárendelési beállítások (nem kötelező)**, megjeleníti a két termék, amely a korábban kiválasztott szereplő minden szolgáltatáscsomag. Keresse meg **Yammer vállalati** kapcsolja **ki** , hogy a termék licence a szolgáltatás letiltása. Győződjön meg arról, kattintva **OK** alján **hozzárendelési beállítások**.

   ![Hozzárendelési beállítások](./media/licensing-groups-assign/assignment-options.png)

7. A hozzárendelés végrehajtása a **licenc hozzárendelése** ablaktáblán kattintson a **hozzárendelése** a panel alján.

8. Megjelenik egy értesítés, amely tartalmazza az állapot és a folyamat eredményéről jobb felső sarokban. Ha az a csoport-hozzárendelés (például a csoport már meglévő licenccel) miatt nem sikerült végrehajtani, kattintson az értesítés a hiba részleteinek megtekintéséhez.

Mi most megadott-licencsablon, a HR-osztály csoport. Az Azure ad-ben háttérfolyamatként feldolgozni az adott csoport összes meglévő tag el lett indítva. A kezdeti művelet a csoport aktuális méretétől függően némi időt is igénybe vehet. A következő lépés ismerteti győződjön meg arról, hogy a folyamat véget ért, és döntse el, ha további figyelmet a problémák megoldásához szükséges.

> [!NOTE]
> Az azonos hozzárendelés kezdhet egy másik helyre: **felhasználók és csoportok** az Azure ad-ben. Lépjen a **Azure Active Directory** > **felhasználók és csoportok** > **összes csoport**. Majd keresse meg a csoportot, válassza ki és nyissa meg a **licencek** fülre. A **hozzárendelése** gomb fölött a panel megnyitja a licenc-hozzárendelési ablaktáblán.

## <a name="step-2-verify-that-the-initial-assignment-has-finished"></a>2. lépés: Győződjön meg arról, hogy a hozzárendelés kezdeti befejeződött

1. Lépjen a **Azure Active Directory** > **felhasználók és csoportok** > **összes csoport**. Majd keresse meg a **HR részleg** csoportot, amely a licencek lettek hozzárendelve.

2. Az a **HR részleg** csoport ablaktáblán válassza **licencek**. Ez lehetővé teszi, hogy gyorsan arról, ha a rendszer teljes mértékben rendelt licenceket a felhasználók számára, és ha vannak olyan hibákat, amelyek meg kell. A következő információ áll rendelkezésre:

   - A csoport jelenleg hozzárendelt terméklicencek listája. Válassza ki egy bejegyzést, amelyeken engedélyezve van az egyes szolgáltatások megjelenítéséhez és a módosításokat.

   - A legújabb licenc elvégzett módosítások a csoportjához (Ha a módosítások feldolgozása folyamatban vagy a Ha a feldolgozás befejeződött az összes felhasználó tag) állapotát.

   - Felhasználók, akik hibás állapotú, mert nem sikerült hozzárendelni a licencet rájuk vonatkozó adatokat.

   ![Hozzárendelési beállítások](./media/licensing-groups-assign/assignment-errors.png)

3. Részletesebb információ a feldolgozás alatt licenc **Azure Active Directory** > **felhasználók és csoportok** > *csoportnév*  >  **Auditnaplók**. Vegye figyelembe a következő tevékenységeket:

   - Tevékenység: **indítsa el a Csoportalapú licenc felhasználókra érvényes**. Ez a rendszer naplózza a rendszer észleli a licenc-hozzárendelés módosítást a csoportban található, és elindítja az összes felhasználói tagok telepítené azt. A változás, amely történt kapcsolatos információkat tartalmaz.

   - Tevékenység: **felhasználók csoport alapú licenc alkalmazásának befejezése**. Ez akkor kerül, amikor a rendszer befejezte a feldolgozása a csoportban lévő összes felhasználó számára. Hány felhasználó feldolgozása sikeresen megtörtént, és hány felhasználó nem sikerült hozzárendelni csoportok licenceire összegzését tartalmazza.

   [Ebben a szakaszban olvasható](licensing-group-advanced.md#use-audit-logs-to-monitor-group-based-licensing-activity) tudhat meg többet hogyan auditnaplók segítségével elemezheti a Csoportalapú licencelés által végrehajtott módosításokat.

## <a name="step-3-check-for-license-problems-and-resolve-them"></a>3. lépés: Kapcsolatos problémák ellenőrzése és a megoldásukkal

1. Lépjen a **Azure Active Directory** > **felhasználók és csoportok** > **összes csoport**, és keresse meg a **HR részleg** licenc lett hozzárendelve a csoport.
2. Az a **HR részleg** csoport ablaktáblán válassza **licencek**. Az értesítés a panel tetején látható, hogy 10 olyan felhasználót, hogy nem sikerült hozzárendelni a licencet. Az összes olyan felhasználó listáját kattint, megnyílik egy licencelési – a hibás állapotú ehhez a csoporthoz.
3. A **sikertelen hozzárendelések** oszlop tudatja velünk, hogy mindkét termékhez licenccel nem sikerült hozzárendelni a felhasználók számára. A **hiba okát az első** oszlop tartalmazza a hiba okát. Ebben az esetben van **ütköző szolgáltatási csomagok**.

   ![Sikertelen hozzárendelések](./media/licensing-groups-assign/failed-assignments.png)

4. Válassza ki a felhasználót, hogy nyissa meg a **licencek** ablaktáblán. Ezen a panelen látható minden olyan licenc, amely már hozzá vannak rendelve a felhasználóhoz. Ebben a példában a felhasználó rendelkezik Office 365 nagyvállalati E1 csomag licenc, amely öröklődött a **teljes képernyős felhasználók** csoport. Ez a E3-licenc, amely a rendszer megpróbálta alkalmazni a ütközik a **HR részleg** csoport. Ennek eredményeképpen a licenceket a csoport egyik sem lett hozzárendelve a felhasználóhoz.

   ![Egy felhasználó licencek megtekintése](./media/licensing-groups-assign/user-license-view.png)

5. Az ütközés elhárításához távolítsa el a felhasználót a **teljes képernyős felhasználók** csoport. Az Azure AD-folyamatok a váltás után a **HR részleg** licencek megfelelően vannak hozzárendelve.

   ![Megfelelő licenccel](./media/licensing-groups-assign/license-correctly-assigned.png)

## <a name="next-steps"></a>További lépések

Csoportokon keresztül licenckezelésre funkciókészlethez kapcsolatos további információkért tekintse meg a következő cikkeket:

* [Mit jelent a Csoportalapú licencelés az Azure Active Directoryban?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
* [Az Azure Active Directory csoport kapcsolatos problémák észleléséhez és a](licensing-groups-resolve-problems.md)
* [Hogyan kell egyéni licenccel rendelkező felhasználók migrálása Csoportalapú licencelésre, az Azure Active Directoryban](licensing-groups-migrate-users.md)
* [Csoportalapú licencelés további forgatókönyvek az Azure Active Directory](../active-directory-licensing-group-advanced.md)
