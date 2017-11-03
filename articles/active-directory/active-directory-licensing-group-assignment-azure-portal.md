---
title: "Licencek hozzárendelése az Azure Active Directory csoporthoz |} Microsoft Docs"
description: "Felhasználók segítségével az Azure Active Directory csoport licencelési licencek hozzárendelése"
services: active-directory
keywords: "Az Azure AD-licencelés"
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/05/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 42b18eab9cb419e6ada72ba72dc8be8d7f7b2eed
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="assign-licenses-to-users-by-group-membership-in-azure-active-directory"></a>A felhasználók számára az Azure Active Directory csoport tagsága szükséges licencek kiosztása

Ez a cikk útmutatást nyújt a termék licencek hozzárendelése az Azure Active Directory (Azure AD) a felhasználók egy csoportját, és győződjön meg arról, hogy azok még licencelt megfelelően.

Ebben a példában a bérlő tartalmazza nevű biztonsági csoport **HR-részleg**. Ez a csoport összes tagja az emberi erőforrások részleg (körülbelül 1000 felhasználó) tartalmazza. Office 365 nagyvállalati E3 csomag licenceket rendelhet a teljes részleg szeretné. A vállalati Yammer-szolgáltatás, amely megtalálható a termék kell ideiglenesen tiltott, amíg a részleg használatba készen áll. Akkor is telepíteni kívánja Enterprise Mobility + Security licencek a felhasználók egy csoportba.

> [!NOTE]
> Egyes Microsoft-szolgáltatások nem érhetők el az összes helyen. Licenc rendelhet egy felhasználót, mielőtt a rendszergazda határozza meg a felhasználó a használati helyet jelölő tulajdonsághoz.

> A licenc-hozzárendelést nélkül a megadott felhasználási hely összes felhasználója örökli a könyvtár helye. Amennyiben több helyen is rendelkezik felhasználókkal, azt javasoljuk, hogy mindig állítsa felhasználási hely, a felhasználó létrehozásának folyamata az Azure AD (pl. keresztül AAD Connect konfigurációjának) – Ez biztosítja a licenc-hozzárendelést eredményét részét mindig megfelelő, és nem jelenik meg felhasználók szolgáltatások a helyeken, amelyek nem engedélyezettek.

## <a name="step-1-assign-the-required-licenses"></a>1. lépés: A szükséges licencek kiosztása

1. Jelentkezzen be a [ **Azure-portálon** ](https://portal.azure.com) rendszergazdai fiókkal. A licencek kezelése, a fiók globális rendszergazdai szerepkört vagy felhasználói fiók rendszergazdai kell lennie.

2. Válassza ki **további szolgáltatások** a bal oldali navigációs panelen, és válassza a **Azure Active Directory**. Ez a panel felvétele a Kedvencek közé, vagy PIN-kód a portál Irányítópultjára.

3. Az a **Azure Active Directory** panelen válassza **licencek**. Ekkor megnyílik egy panel, ahol tekintse meg és kezelheti a bérlő összes licencelhető termékek.

4. A **összes**, válassza ki az Office 365 nagyvállalati E3 csomag és a nagyvállalati mobilitási + biztonsági terméknevek kiválasztásával. A hozzárendelés elindításához válassza ki a **hozzárendelése** a panel tetején.

   ![Minden termékek használatára jogosító licenc hozzárendeléséhez](media/active-directory-licensing-group-assignment-azure-portal/all-products-assign.png)

5. Az a **licenc hozzárendelése** panelen kattintson a **felhasználók és csoportok** megnyitásához a **felhasználók és csoportok** panelen. Keresse meg a csoport nevét *HR-részleg*, és válassza ki azt a csoportot, akkor ne feledje el gombra kattintva erősítse meg **válasszon** a panel alján.

   ![Válasszon ki egy csoportot](media/active-directory-licensing-group-assignment-azure-portal/select-a-group.png)

6. Az a **licenc hozzárendelése** panelen kattintson a **hozzárendelés beállításai (nem kötelező)**, szerepel a két termék, amely azt a korábban kiválasztott összes service-csomagokról jelenít meg. Található **Yammer vállalati** kapcsolja **ki** letiltása, hogy a termék licence szolgáltatást. Gombra kattintva erősítse meg **OK** alján **hozzárendelés beállításai**.

   ![Hozzárendelés beállításai](media/active-directory-licensing-group-assignment-azure-portal/assignment-options.png)

7. A hozzárendelés befejezéséhez az a **licenc hozzárendelése** panelen kattintson a **hozzárendelése** a panel alján.

8. Megjelenik egy értesítés, amely tartalmazza az állapot és a folyamat eredményéről jobb felső sarokban. Ha a csoport a hozzárendelés nem hajtható végre (például mert a csoport már meglévő licencek), kattintson az értesítésre a sikertelenség részleteinek megtekintése.

A Microsoft most megadott egy licencsablon a HR osztály csoporthoz. Az Azure AD háttérfolyamatként, hogy a csoport összes meglévő tagjai feldolgozása elindult. A kezdeti művelet eltarthat egy ideig, attól függően, hogy a csoport aktuális méretét. A következő lépésben azt kell azt ismertetik, hogyan győződjön meg arról, hogy a folyamat végét, és határozza meg, ha további figyelmet szükséges problémák megoldásához.

> [!NOTE]
> Egy másik elérési utat a azonos hozzárendelés indítható: **felhasználók és csoportok** Azure AD-ben. Ugrás a **Azure Active Directory** > **felhasználók és csoportok** > **összes csoport**. Majd keresse meg a csoportot, válassza ki azt, és válassza a **licencek** fülre. A **hozzárendelése** fölött a panel gombra kattintva megnyílik a licenc hozzárendelése panelen.

## <a name="step-2-verify-that-the-initial-assignment-has-finished"></a>2. lépés: Győződjön meg arról, hogy a kezdeti hozzárendelés befejeződött

1. Ugrás a **Azure Active Directory** > **felhasználók és csoportok** > **összes csoport**. Majd keresse meg a **HR-részleg** csoport, a hozzárendelt licenceket.

2. Az a **HR-részleg** csoport panelen válassza **licencek**. Ez lehetővé teszi gyorsan erősítse meg, ha licencek teljes hozzárendelt felhasználók és megismerhetők igénylő hiba történik. A következő információ áll rendelkezésre:

   - Jelenleg a csoporthoz hozzárendelt terméklicencek listája. Jelöljön ki egy bejegyzést, megjelenítése az egyes szolgáltatások, amelyeken engedélyezve van, és módosíthatja.

   - A legújabb licenc elvégzett módosítások a csoportjához (Ha a változások feldolgozott vagy feldolgozása befejeződik az összes felhasználó tag számára) állapotát.

   - Felhasználók, akik hibás állapotú, mert nem sikerült hozzárendelni a licenceket rájuk vonatkozó adatokat.

   ![Hozzárendelés beállításai](media/active-directory-licensing-group-assignment-azure-portal/assignment-errors.png)

3. Részletes információ a feldolgozása a licenc **Azure Active Directory** > **felhasználók és csoportok** > *csoportnév*  >  **Naplók**. Vegye figyelembe a következő tevékenységek:

   - Tevékenység: **indítsa el a felhasználók csoport licenc alkalmazása**. Ez naplóz, amikor a rendszer szerzi be a csoport a licenc-hozzárendelést változik, és elindítja a alkalmazása az összes felhasználói tagjai. A változás, amely történt információt tartalmaz.

   - Tevékenység: **Befejezés csoport licenc felhasználókra érvényes**. Ez a rendszer naplózza a rendszer befejezése után a csoport minden tagjára feldolgozása. Hány felhasználó feldolgozása sikeresen megtörtént, és hány felhasználó nem sikerült hozzárendelni a csoport licencek összegzését tartalmazza.

   [Ebben a szakaszban olvasható](./active-directory-licensing-group-advanced.md#use-audit-logs-to-monitor-group-based-licensing-activity) tudhat meg többet hogyan naplók segítségével elemezheti csoport alapú licencelési által végrehajtott módosítások.

## <a name="step-3-check-for-license-problems-and-resolve-them"></a>3. lépés: Ellenőrizze a licenc problémákat, és a megoldásukkal együtt

1. Lépjen **Azure Active Directory** > **felhasználók és csoportok** > **összes csoport**, és keresse meg a **HR-részleg** a hozzárendelt licencek csoport.
2. Az a **HR-részleg** csoport panelen válassza **licencek**. Az értesítés a panel tetején látható, hogy vannak-e 10 olyan felhasználót, hogy a licencek nem sikerült hozzárendelni. Kattintson rá megnyitja az összes olyan felhasználó listáját egy licencelési-hibaállapot ehhez a csoporthoz.
3. A **nem sikerült a hozzárendelés** oszlopból kiderül, hogy mindkét terméklicencek nem sikerült hozzárendelni a felhasználók számára. A **hiba okának felső** oszlop tartalmazza a hiba oka. Ebben az esetben van **ütköző service-csomagokról**.

   ![Nem sikerült hozzárendelések](media/active-directory-licensing-group-assignment-azure-portal/failed-assignments.png)

4. Válasszon ki egy felhasználót nyissa meg a **licencek** panelen. Ezen a panelen látható összes licencet a felhasználó már hozzá vannak rendelve. Ebben a példában a felhasználó rendelkezik-e az Office 365 nagyvállalati E1 csomag licenchez öröklődött a **teljes képernyős felhasználók** csoport. Ez ütközik a E3 licenc, amely a rendszer megpróbálta alkalmazza a **HR-részleg** csoport. Ennek eredményeképpen a licenceket a csoport egyik rendelték hozzá a felhasználót.

   ![Egy felhasználó licencek megtekintése](media/active-directory-licensing-group-assignment-azure-portal/user-license-view.png)

5. Oldja meg az ütközést, távolítsa el a felhasználót a **teljes képernyős felhasználók** csoport. Az Azure AD folyamatok a módosítás után a **HR-részleg** licencek megfelelően vannak hozzárendelve.

   ![-Licenccel megfelelően](media/active-directory-licensing-group-assignment-azure-portal/license-correctly-assigned.png)

## <a name="next-steps"></a>Következő lépések

A licenc-kezelő csoportokon keresztül beállítása szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

* [Mi az a csoport-alapú licencelése az Azure Active Directoryban?](active-directory-licensing-whatis-azure-portal.md)
* [Majd azonosítani és megoldani az Azure Active Directory csoport licenc problémák](active-directory-licensing-group-problem-resolution-azure-portal.md)
* [Az Azure Active Directory biztonságicsoport-alapú licencelési egyedi licenccel rendelkező felhasználók áttelepítése](active-directory-licensing-group-migration-azure-portal.md)
* [Az Azure Active Directory biztonságicsoport-alapú licencelési további helyzeteket is](active-directory-licensing-group-advanced.md)
