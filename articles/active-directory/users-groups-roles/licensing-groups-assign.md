---
title: Egy csoport – Azure Active Directory-licencek hozzárendelése |} A Microsoft Docs
description: Licencek hozzárendelése a felhasználók révén az Azure Active Directory-csoport licencelésének
services: active-directory
keywords: Az Azure AD licencelése
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 02/25/2019
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6473b8d15d09251d22386036285019c3b55e4cb1
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58084115"
---
# <a name="assign-licenses-to-users-by-group-membership-in-azure-active-directory"></a>Licencek hozzárendelése a felhasználók által az Azure Active Directory biztonságicsoport-tagság

Ez a cikk végigvezeti a termék licencek hozzárendelése az Azure Active Directoryban (Azure AD) a felhasználók egy csoportjára, és majd ellenőrzése, hogy azok még licencelt megfelelően.

Ebben a példában a bérlő tartalmazza-e nevű biztonsági csoport **HR részleg**. Ez a csoport összes tagja az emberi erőforrások részleg (körülbelül 1000 felhasználó) tartalmazza. Office 365 nagyvállalati E3 csomag licenceket hozzárendelheti az egész részleg szeretné. A vállalati Yammer-szolgáltatás, amely megtalálható a termék kell lehet ideiglenesen le van tiltva addig, amíg a részleg készen áll a használatának megkezdéséhez. Akkor is telepíteni kívánja Enterprise Mobility + Security-licenc, az ugyanazon felhasználói csoporthoz.

> [!NOTE]
> Nem minden Microsoft-szolgáltatás érhető el minden területen. Mielőtt egy úgy lehet licencet a felhasználóhoz, a rendszergazda adja meg a használati helyet jelölő tulajdonsághoz meg a felhasználó rendelkezik.
> 
> A licenc-hozzárendelések bármely felhasználó felhasználás helyének megadása nélkül örökli a könyvtár helye. Ha több helyen felhasználóval rendelkezik, azt javasoljuk, hogy mindig állítsa a felhasználási hely része a felhasználói létrehozásának folyamatát, amely biztosítja a licenc-hozzárendelés eredménye (pl. keresztül az AAD Connect-konfiguráció) – Azure AD-ben mindig helyes, és a felhasználók nem kapnak szolgáltatások, a helyeken, amelyek nem engedélyezettek.

## <a name="step-1-assign-the-required-licenses"></a>1. lépés: A szükséges licencek hozzárendelése

1. Jelentkezzen be a [ **Azure AD felügyeleti központ** ](https://aad.portal.azure.com) egy licenc rendszergazdai fiókkal. Licencek kezeléséhez, a fiók egy licencek adminisztrátora, a felhasználó rendszergazda vagy a globális rendszergazda kell lennie.

2. Válassza ki **licencek** megnyílik egy panel, amelyen tekintse meg és kezelheti a bérlő összes licencelhető termék.

4. A **minden termék**, válassza ki az Office 365 nagyvállalati E5 csomag és a Enterprise Mobility + Security E3 terméknevek kiválasztásával. A hozzárendelés megkezdéséhez válasszon **hozzárendelése** a panel tetején.

   ![Minden termék licenc hozzárendelése](./media/licensing-groups-assign/all-products-assign.png)
  
5. Az a **licenc hozzárendelése** ablaktáblán válassza előbb **felhasználók és csoportok** felhasználók és csoportok listájának megnyitásához.

6. Válassza ki egy felhasználót vagy csoportot, és használja a **kiválasztása** gombra a kijelölés megerősítéséhez a panel alján.

7. Az a **licenc hozzárendelése** ablaktáblán kattintson a **hozzárendelési beállítások**, megjeleníti a két termék, amely a korábban kiválasztott szereplő minden szolgáltatáscsomag. Keresse meg **Yammer vállalati** kapcsolja **ki** , hogy a termék licence a szolgáltatás letiltása. Gombra kattintva erősítse meg **OK** alján **beállítások licenc**.

   ![Hozzárendelési beállítások](./media/licensing-groups-assign/assignment-options.png)
  
8. A hozzárendelés véglegesítéséhez kattintson a **Licenc hozzárendelése** panelen a lap alján található **Hozzárendelés** gombra.

9. Megjelenik egy értesítés, amely tartalmazza az állapot és a folyamat eredményéről jobb felső sarokban. Ha az a csoport-hozzárendelés (például a csoport már meglévő licenccel) miatt nem sikerült végrehajtani, kattintson az értesítés a hiba részleteinek megtekintéséhez.

Ha licenceket hozzárendelni a csoporthoz, az Azure AD dolgozza fel az adott csoport összes meglévő tag. Ez a folyamat eltarthat egy ideig, az a csoport mérete változó. A következő lépés ismerteti győződjön meg arról, hogy a folyamat véget ért, és döntse el, ha további figyelmet a problémák megoldásához szükséges.

## <a name="step-2-verify-that-the-initial-assignment-has-finished"></a>2. lépés: A hozzárendelés kezdeti befejeződésének ellenőrzése

1. Lépjen a **az Azure Active Directory** > **csoportok**. Válassza ki a csoportot, amely a licencek lettek hozzárendelve.

2. A csoport panelen válassza ki a **licencek**. Ez lehetővé teszi, hogy gyorsan arról, ha a rendszer teljes mértékben rendelt licenceket a felhasználók számára, és ha vannak olyan hibákat, amelyek meg kell. A következő információ áll rendelkezésre:

   - A csoport jelenleg hozzárendelt terméklicencek listája. Válassza ki egy bejegyzést, amelyeken engedélyezve van az egyes szolgáltatások megjelenítéséhez és a módosításokat.

   - A legújabb licenc elvégzett módosítások a csoportjához (Ha a módosítások feldolgozása folyamatban vagy a Ha a feldolgozás befejeződött az összes felhasználó tag) állapotát.

   - Felhasználók, akik hibás állapotú, mert nem sikerült hozzárendelni a licencet rájuk vonatkozó adatokat.

   ![Hozzárendelési beállítások](./media/licensing-groups-assign/assignment-errors.png)

3. Részletesebb információ a feldolgozás alatt licenc **Azure Active Directory** > **felhasználók és csoportok** > *csoportnév*  >  **Auditnaplók**. Vegye figyelembe a következő tevékenységeket:

   - Tevékenység: **Csoportalapú licenc felhasználókra érvényes Start**. Ez a rendszer naplózza a rendszer észleli a licenc-hozzárendelés módosítást a csoportban található, és elindítja az összes felhasználói tagok telepítené azt. A változás, amely történt kapcsolatos információkat tartalmaz.

   - Tevékenység: **A felhasználók csoport alapú licenc alkalmazásának befejezése**. Ez akkor kerül, amikor a rendszer befejezte a feldolgozása a csoportban lévő összes felhasználó számára. Hány felhasználó feldolgozása sikeresen megtörtént, és hány felhasználó nem sikerült hozzárendelni csoportok licenceire összegzését tartalmazza.

   [Ebben a szakaszban olvasható](licensing-group-advanced.md#use-audit-logs-to-monitor-group-based-licensing-activity) tudhat meg többet hogyan auditnaplók segítségével elemezheti a Csoportalapú licencelés által végrehajtott módosításokat.

## <a name="step-3-check-for-license-problems-and-resolve-them"></a>3. lépés: Kapcsolatos problémák ellenőrzése és a megoldásukkal

1. Lépjen a **Azure Active Directory** > **csoportok**, és keresse meg a csoportot, amely a licencek lettek hozzárendelve.
2. A csoport panelen válassza ki a **licencek**. Az értesítés a panel tetején látható, hogy 10 olyan felhasználót, hogy nem sikerült hozzárendelni a licencet. Nyissa meg a csoport licencelési hibás állapotú felhasználók listájának megtekintéséhez.
3. A **sikertelen hozzárendelések** oszlop tudatja velünk, hogy mindkét termékhez licenccel nem sikerült hozzárendelni a felhasználók számára. A **hiba okát az első** oszlop tartalmazza a hiba okát. Ebben az esetben van **ütköző szolgáltatási csomagok**.

   ![Sikertelen hozzárendelések](./media/licensing-groups-assign/failed-assignments.png)

4. Válassza ki a felhasználót, hogy nyissa meg a **licencek** ablaktáblán. Ezen a panelen látható minden olyan licenc, amely már hozzá vannak rendelve a felhasználóhoz. Ebben a példában a felhasználó rendelkezik Office 365 nagyvállalati E1 csomag licenc, amely öröklődött a **teljes képernyős felhasználók** csoport. Ez a E3-licenc, amely a rendszer megpróbálta alkalmazni a ütközik a **HR részleg** csoport. Ennek eredményeképpen a licenceket a csoport egyik sem lett hozzárendelve a felhasználóhoz.

   ![Egy felhasználó licencek megtekintése](./media/licensing-groups-assign/user-license-view.png)

5. Az ütközés elhárításához távolítsa el a felhasználót a **teljes képernyős felhasználók** csoport. Az Azure AD-folyamatok a váltás után a **HR részleg** licencek megfelelően vannak hozzárendelve.

   ![Megfelelő licenccel](./media/licensing-groups-assign/license-correctly-assigned.png)

## <a name="next-steps"></a>További lépések

Csoportokon keresztül licenckezelésre funkciókészlethez kapcsolatos további információkért tekintse meg a következő cikkeket:

* [Mit jelent a Csoportalapú licencelés az Azure Active Directoryban?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
* [A csoportok licencproblémáinak azonosítása és megoldása az Azure Active Directoryban](licensing-groups-resolve-problems.md)
* [Egyéni, licenccel rendelkező felhasználók migrálása csoportalapú licencelésre az Azure Active Directoryban](licensing-groups-migrate-users.md)
* [Felhasználók az Azure Active Directoryban Csoportalapú licencelést használ terméklicencek közötti migrálása](licensing-groups-change-licenses.md)
* [Az Azure Active Directory csoportalapú licencelésének további forgatókönyvei](../active-directory-licensing-group-advanced.md)
* [PowerShell forgatókönyvek Csoportalapú licenceléshez az Azure Active Directoryban](licensing-ps-examples.md)
