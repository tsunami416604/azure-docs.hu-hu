---
title: Licencek kiosztása egy csoportba – Azure Active Directory | Microsoft Docs
description: Licencek felhasználókhoz rendelése Azure Active Directory csoport licencelése révén
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
ms.openlocfilehash: 497efda857dcd7de3079d702be00a094d221b779
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72034828"
---
# <a name="assign-licenses-to-users-by-group-membership-in-azure-active-directory"></a>Licencek kiosztása a felhasználóknak csoporttagság szerint Azure Active Directory

Ebből a cikkből megtudhatja, hogyan rendelheti hozzá a licenceket a felhasználók csoportjához, és ellenőrizze, hogy az Azure Active Directory (Azure AD) megfelelő licenccel rendelkezik-e.

Ebben a példában a bérlő egy **HR részleg**nevű biztonsági csoportot tartalmaz. Ez a csoport tartalmazza az emberi erőforrások részleg összes tagját (1 000-es felhasználók). Office 365 Enterprise E3 licenceket szeretne hozzárendelni a teljes részleghez. A termékben található Yammer Enterprise szolgáltatást átmenetileg le kell tiltani, amíg a részleg készen nem áll az alkalmazás használatára. Enterprise Mobility + Security licenceket is telepítenie kell ugyanahhoz a felhasználói csoportba.

> [!NOTE]
> Nem minden Microsoft-szolgáltatás érhető el minden területen. Ahhoz, hogy egy licencet hozzá lehessen rendelni egy felhasználóhoz, a rendszergazdának meg kell adnia a használat helye tulajdonságot a felhasználónál.
>
> A csoport licencének hozzárendelésekor a megadott használati hely nélküli felhasználók öröklik a címtár helyét. Ha több helyen is vannak felhasználók, javasoljuk, hogy mindig a felhasználói létrehozási folyamat részeként adja meg a használat helyét az Azure AD-ben (például HRE-kapcsolat konfigurálásakor), amely biztosítja a licenc-hozzárendelés eredményét, és a felhasználók nem kapják meg olyan helyen lévő szolgáltatások, amelyek nem engedélyezettek.

## <a name="step-1-assign-the-required-licenses"></a>1\. lépés: a szükséges licencek kiosztása

1. Jelentkezzen be az [**Azure ad felügyeleti központba**](https://aad.portal.azure.com) egy licenc-rendszergazdai fiókkal. A licencek kezeléséhez a fióknak licenc-rendszergazdának, felhasználói rendszergazdának vagy globális rendszergazdának kell lennie.

1. Válassza a licencek lehetőséget egy lap megnyitásához, ahol megtekintheti és kezelheti a bérlőben lévő összes **engedélyezhető** terméket.

1. Az **összes termék**területen válassza ki az Office 365 Enterprise E5 és Enterprise Mobility + Security E3 elemet a terméknév kiválasztásával. A hozzárendelés elindításához válassza a **hozzárendelés** lehetőséget az oldal tetején.

   ![Válassza ki a termékeket, amelyekhez licenceket szeretne rendelni](./media/licensing-groups-assign/all-products-assign.png)
  
1. A **licencek kiosztása** lapon válassza a **felhasználók és csoportok** lehetőséget a felhasználók és csoportok listájának megnyitásához.

1. Válasszon ki egy felhasználót vagy csoportot, majd a lap alján található **kiválasztás** gombbal erősítse meg a kijelölést.

1. A **licenc hozzárendelése** lapon kattintson a **hozzárendelési beállítások**elemre, amely megjeleníti a korábban kiválasztott két termékhez tartozó összes szervizcsomagot. Keresse meg a **Yammer Enterprise** elemet, és kapcsolja **ki** , hogy letiltsa a szolgáltatást a termék licencéről. Erősítse meg a **licencelési lehetőségek**alján található **OK gombra** kattintva.

   ![szolgáltatási csomagok kiválasztása a licencekhez](./media/licensing-groups-assign/assignment-options.png)
  
1. A hozzárendelés befejezéséhez a **licenc kiosztása** lapon kattintson a lap alján található **hozzárendelés** elemre.

1. A jobb felső sarokban megjelenik egy értesítés, amely a folyamat állapotát és eredményét jeleníti meg. Ha a csoporthoz való hozzárendelés nem hajtható végre (például a csoport meglévő licencei miatt), kattintson az értesítésre a hiba részleteinek megtekintéséhez.

Ha licenceket rendel egy csoporthoz, az Azure AD feldolgozza a csoport összes meglévő tagját. Ez a folyamat hosszabb időt is igénybe vehet, és a csoport méretétől függően változhat. A következő lépés azt ismerteti, hogyan ellenőrizheti, hogy a folyamat befejeződött-e, és hogy van-e szükség további beavatkozásra a problémák megoldásához.

## <a name="step-2-verify-that-the-initial-assignment-has-finished"></a>2\. lépés: annak ellenőrzése, hogy befejeződött-e a kezdeti hozzárendelés

1. Válassza **Azure Active Directory** > **csoportok**lehetőséget. Válassza ki azt a csoportot, amelyhez a licencek hozzá lettek rendelve.

1. A csoport lapon válassza a **licencek**lehetőséget. Ezzel gyorsan megerősítheti, hogy a licencek teljes körűen vannak-e hozzárendelve a felhasználókhoz, és hogy vannak-e hibák, amelyekre szüksége van. A következő információ áll rendelkezésre:

   - A csoporthoz jelenleg hozzárendelt szolgáltatási licencek. Válasszon egy bejegyzést az engedélyezett szolgáltatások megjelenítéséhez, illetve a módosítások elvégzéséhez.

   - A legújabb licenc-változások állapotának frissítései, amelyek akkor érhetők el, ha a módosítások feldolgozása folyamatban van, vagy ha a feldolgozás befejeződött az összes felhasználói tag esetében.

   - A hibás állapotú felhasználói licenc-hozzárendelésekkel kapcsolatos információk.

   ![licencelési hibák és a licenc állapota](./media/licensing-groups-assign/assignment-errors.png)

1. A licencek feldolgozásával kapcsolatos részletesebb információkat a **Azure Active Directory** > **felhasználók és csoportok** > *csoport neve* > **naplók**szakaszban talál. Győződjön meg a következő tevékenységekről:

   - Tevékenység: `Start applying group based license to users`. Ez akkor kerül naplózásra, amikor a rendszer felveszi a licenc-hozzárendelés változását a csoportba, és elindítja az összes felhasználói tagra való alkalmazását. Az elvégzett módosítással kapcsolatos információkat tartalmaz.

   - Tevékenység: `Finish applying group based license to users`. Ez akkor kerül naplózásra, amikor a rendszer befejezi a csoport összes felhasználójának feldolgozását. Összefoglalja, hogy hány felhasználót sikerült feldolgozni, és hány felhasználót nem sikerült hozzárendelni a csoportos licencekhez.

   [Ebből a szakaszból](licensing-group-advanced.md#use-audit-logs-to-monitor-group-based-licensing-activity) megtudhatja, hogyan használhatók a naplók a csoport alapú licencelés által végzett módosítások elemzéséhez.

## <a name="step-3-check-for-license-problems-and-resolve-them"></a>3\. lépés: a licencelési problémák keresése és a megoldás feloldása

1. Lépjen **Azure Active Directory** > **csoportok**elemre, és keresse meg azt a csoportot, amelyhez a licencek hozzá lettek rendelve.
1. A csoport lapon válassza a **licencek**lehetőséget. A lap tetején látható értesítés azt mutatja, hogy a licencek 10 felhasználóhoz nem rendelhetők hozzá. Nyissa meg a csoportot a licencelési hiba állapotában lévő összes felhasználó listájának megtekintéséhez.
1. A **sikertelen hozzárendelések** oszlop azt jelzi, hogy mindkét licencet nem sikerült hozzárendelni a felhasználókhoz. A **hiba okának fő oka** a hiba okát tartalmazza. Ebben az esetben ez **ütköző szolgáltatási csomagok**.

   ![nem hozzárendelt licencek](./media/licensing-groups-assign/failed-assignments.png)

1. Válasszon ki egy felhasználót a felhasználó **licencek** lapjának megnyitásához. Ezen a lapon látható az összes olyan licenc, amely jelenleg hozzá van rendelve a felhasználóhoz. Ebben a példában a felhasználó rendelkezik az Office 365 Enterprise E1 licenccel, amely a **kioszk felhasználói** csoporttól örökölt. Ez ütközik azzal az E3 licenccel, amelyet a rendszer a **HR részleg** csoportjából próbált alkalmazni. Ennek eredményeképpen az adott csoportból származó licencek egyike sincs hozzárendelve a felhasználóhoz.

   ![Felhasználóhoz tartozó összes licencelési ütközés megtekintése](./media/licensing-groups-assign/user-license-view.png)

1. Az ütközés megoldásához távolítsa el a felhasználót a **kioszk felhasználók** csoportjából. Miután az Azure AD feldolgozza a változást, a **HR-részleg** licenceit helyesen rendeli hozzá a rendszer.

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a licenc-hozzárendelés csoportokkal való beállításáról, tekintse meg a következő cikkeket:

- [Mi a Azure Active Directory csoportos licencelése?](/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context)
- [A csoportok licencproblémáinak azonosítása és megoldása az Azure Active Directoryban](licensing-groups-resolve-problems.md)
- [Egyéni, licenccel rendelkező felhasználók migrálása csoportalapú licencelésre az Azure Active Directoryban](licensing-groups-migrate-users.md)
- [Felhasználók az Azure Active Directoryban Csoportalapú licencelést használ terméklicencek közötti migrálása](licensing-groups-change-licenses.md)
- [Az Azure Active Directory csoportalapú licencelésének további forgatókönyvei](../active-directory-licensing-group-advanced.md)
- [PowerShell forgatókönyvek Csoportalapú licenceléshez az Azure Active Directoryban](licensing-ps-examples.md)
