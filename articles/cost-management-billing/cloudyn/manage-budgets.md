---
title: Cloudyn-költségvetések kezelése az Azure-ban
description: Ez a cikk segítséget nyújt a költségvetések gyors létrehozásához és a Cloudyn-ben való kezeléséhez.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/20/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: vitavor
ms.custom: seodec18
ms.openlocfilehash: 3572296da8e81602e905a3d9f515e7d49067cfa4
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75990592"
---
# <a name="manage-azure-budgets-with-cloudyn"></a>Azure-költségvetések kezelése a Cloudyn-mel

A költségvetések és a költségkeret-alapú riasztások beállítása segít a felhő irányításának és elszámoltathatóságának javításában. Ez a cikk segítséget nyújt a költségvetések gyors létrehozásához és a Cloudyn-ben való kezeléséhez.

Ha nagyvállalati vagy MSP-fiókkal rendelkezik, a hierarchikus költségű entitás struktúrájának használatával havi költségvetési kvótákat rendelhet a különböző üzleti egységekhez, részlegekhez vagy más Cost-entitásokhoz. Ha prémium szintű fiókkal rendelkezik, használhatja a költségvetés-kezelési funkciót, amelyet aztán a teljes Felhőbeli kiadásokra alkalmaz. Az összes költségvetést manuálisan rendeli hozzá a rendszer.

A hozzárendelt költségvetések alapján meghatározhatja a küszöbérték-riasztásokat a felhasznált költségvetés százaléka alapján, és meghatározhatja az egyes küszöbértékek súlyosságát.

A költségvetési jelentések a hozzárendelt költségvetést mutatják. A felhasználók megtekinthetik, hogy a kiadások mikor lettek felhasználva, alatta vagy névértéken, az idő múlásával. Ha a költségvetési jelentés tetején a **mezők megjelenítése/elrejtése** lehetőséget választja, megtekintheti a költségeket, a költségvetést, a halmozott költségeket vagy a teljes költségvetést.

Az Azure Cost Management a Cloudynhez hasonló funkcionalitást kínál. Az Azure Cost Management egy natív Azure költségkezelő megoldás. Segít kezelni a költségvetéseket, exportálni az adatokat, valamint áttekinteni és végrehajtani az optimalizálási javaslatokat pénzmegtakarítás céljából. A Cost Management költségvetésével kapcsolatos további információkért lásd: [költségvetések létrehozása és kezelése](../costs/tutorial-acm-create-budgets.md).

## <a name="create-budgets"></a>Költségvetések létrehozása

Ha létrehoz egy költségvetést, azt a pénzügyi évre állítja be, és egy adott entitásra vonatkozik.

Költségvetés létrehozása és társítása egy entitáshoz:

1. Navigáljon a **költségek** &gt; **Cost Management** &gt; **költségvetést**.
2. A költségvetés kezelése lap **entitások**területén válassza ki azt az entitást, amelyben létre kívánja hozni a költségvetést.
3. A költségvetési évben válassza ki azt az évet, amelyben létre kívánja hozni a költségvetést.
4. Minden hónapban állítson be egy költségvetési értéket. Ha elkészült, kattintson a **Mentés**gombra.
Ebben a példában a június 2018-es havi költségkeret értéke $135 000. Az év teljes költségvetése $1 615 000,00.
![hozzon létre egy költségvetési oldalt, amelyben minden hónapra vonatkozóan költségvetést állít be](./media/manage-budgets/set-budget.png)


Fájl importálása az éves költségvetésbe:

1. A **műveletek**területen válassza az **Exportálás** lehetőséget egy üres CSV-sablon letöltéséhez, amelyet a költségvetés alapjaként kíván használni.
2. Töltse ki a CSV-fájlt a költségvetési bejegyzéseivel, és mentse helyileg.
3. A **műveletek**területen válassza az **Importálás**lehetőséget.
4. Válassza ki a mentett fájlt, majd kattintson **az OK**gombra.

Ha a kész költségvetést CSV-fájlként szeretné exportálni, a **műveletek**területen válassza az **Exportálás** lehetőséget a fájl letöltéséhez.

## <a name="view-budget-in-reports"></a>Költségvetés megtekintése a jelentésekben

Ha elkészült, a költségkeret a legtöbb Cost-jelentésben a **költségek** &gt; a **Cost Analysis** és a Cost és a költségkeret időbeli változása című jelentésben látható. A jelentések a költségvetési küszöbértékek alapján is ütemezhetők a **műveletek**használatával.

Íme egy példa a Cost Analysis-jelentésre. Az év eleje óta a számítási feladatok és a használati típusok teljes költségvetését és költségét jeleníti meg.

![Példa Cost Analysis-jelentésre költségvetéssel](./media/manage-budgets/cost-analysis-budget-example.png)

Ebben a példában feltételezzük, hogy az aktuális dátum június 22. A $71 611,28. június 2018-os költség a $135 000-os havi költségvetéshez képest. A költség sokkal alacsonyabb a havi költségkeretnél, mert a hónap vége előtt még nyolc nap van hátra.

A jelentés megtekintésének egy másik módja a halmozott költségek és a költségkeret megkeresése. A halmozott költségek megtekintéséhez a **mezők megjelenítése/elrejtése**területen válassza a **halmozott költség** és a **teljes költségvetés**lehetőséget. Az alábbi példa az év eleje óta felhalmozott költségeket mutatja be.

![Példa a költségek és a költségkeret összesített költségére és a költségvetés időbeli alakulására – jelentés](./media/manage-budgets/accumulated-budget.png)

A jövőben a halmozott költségek meghaladják a költségvetést. Könnyebben láthatja, hogy ha megváltoztatja a diagram nézetet a _sor_ típusára.

![A Cost by months jelentés egy vonalas diagramban látható](./media/manage-budgets/budget-line.png)

## <a name="create-budget-alerts-for-a-filter"></a>Költségvetési riasztások létrehozása szűrőhöz

Az előző példában látható, hogy a halmozott költségek megközelítik a költségvetést. Automatikus költségvetési riasztásokat hozhat létre, hogy értesítést kapjon a megoldáshoz, vagy meghaladja a költségvetést. A riasztás alapvetően a küszöbértéket tartalmazó ütemezett jelentés. A költségvetési riasztás küszöbértékének mérőszámai a következők:

- Hátralévő költség vs. költségkeret – a pénznem értékének küszöbértékének megadása
- Költség százalékos aránya és költségvetése – százalékos érték küszöbértékének megadása

Lássunk egy példát.

A költség és a költségkeret időbeli alakulása jelentésben kattintson a **műveletek** , majd az **ütemezett jelentés**elemre. A küszöb lapon válassza ki a küszöbérték mérőszámát. Például a **Cost százalék vs költségvetés**. Válasszon ki egy riasztási típust, és adja meg a költségvetés százalékos értékét. Ha csak egyszer szeretne értesítést kapni, válassza **az egymást követő riasztások száma** lehetőséget, majd írja be az _1_értéket. Kattintson a **Mentés** gombra.

![Költségvetési riasztás létrehozása a jelentés mentése vagy beosztása mezőben](./media/manage-budgets/budget-alert.png)

## <a name="next-steps"></a>Következő lépések

- Ha még nem végezte el az első oktatóanyagot a Cloudyn, olvassa el a [használat és a költségek áttekintése](tutorial-review-usage.md)című cikk lépéseit.
- További információ a [Cloudyn-ben elérhető jelentésekről](use-reports.md).
