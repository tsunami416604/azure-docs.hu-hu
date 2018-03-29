---
title: Alkalmazás-hozzárendelés Azure Application insightsban |} Microsoft Docs
description: Az alkalmazás-hozzárendelés rendelkező összetett alkalmazás topológiák figyelése
services: application-insights
documentationcenter: ''
author: SoubhagyaDash
manager: carmonm
ms.assetid: 3bf37fe9-70d7-4229-98d6-4f624d256c36
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: mbullwin
ms.openlocfilehash: 4b95cd947cd977208dee32d18fbc6a1fb099ff3b
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2018
---
# <a name="application-map-triage-distributed-applications"></a>Alkalmazás-hozzárendelés: Osztályozhatja elosztott alkalmazások
Alkalmazás-hozzárendelés segít direkt szűk keresztmetszetek vagy hiba csatlakozási pontokhoz termékcsalád összes tagjára vonatkozó az elosztott alkalmazás. Minden csomópont a térképen képviseli, alkalmazás-összetevő, illetve annak függőségeit; és KPI állapot és riasztások állapotát. Kattintva keresztül valamelyik összetevő a részletesebb diagnosztikai, például az Application Insights események. Ha az alkalmazás Azure-szolgáltatásokat használja, akkor is átkattintással is Azure Diagnostics, például az SQL Database Advisor-javaslatokra.

## <a name="what-is-a-component"></a>Mi az, hogy egy összetevő?

Összetevők az elosztott/mikroszolgáltatások alkalmazás egymástól függetlenül telepíthető részét képezik. A fejlesztők és a műveletek csapat kód szintű láthatóságot, vagy próbáljon elérni ezeket alkalmazás-összetevők által létrehozott telemetriai rendelkezik. 

* Összetevők eltérnek "megfigyelt" külső függőségei, például az SQL, EventHub stb., amelyek a csoport/szervezeti nem lehet hozzáférni a (kód vagy telemetria).
* Összetevők tetszőleges server/szerepkör/tároló példányát futtatni.
* Összetevők külön Application Insights instrumentation kulcsok (akkor is, ha előfizetések különböző) vagy egy Application Insights instrumentation kulcs jelentéskészítés külön szerepköröket is. A kép ábrázolási tapasztalat összetevők függetlenül attól, hogy azok be vannak állítva.

## <a name="composite-application-map-preview"></a>Összetett alkalmazás-hozzárendelés (előzetes verzió)
*Ez egy korai preview, és bővítjük több szolgáltatást ezen a térképen való. Szívesen meghallgatnánk a kérni a változásról. Válthat a kép és a klasszikus lép könnyen.*

Engedélyezhetik a "Összetett alkalmazás-hozzárendelés" a [előzetes lista](app-insights-previews.md), vagy kattintson a jobb felső sarkában a váltógomb "Előzetes térképen". A váltógomb segítségével átválthat a klasszikus felhasználói élmény.
![Előzetes térkép engedélyezése](media/app-insights-app-map/preview-from-classic.png)

>[!Note]
Ebben az előzetes verzióban a felváltja a korábbi "Több alkalmazás leképezést" előzetes verzió. Most ezzel a teljes topológia több szinten összetevő alkalmazásfüggőségek között. Küldje el visszajelzését, bővítjük további képességeket hasonló a hagyományos térkép támogatja.

A teljes alkalmazás topológia több szinten kapcsolódó alkalmazás-összetevők között tekintheti meg. Összetevők lehet különböző az Application Insights-erőforrások, vagy egyetlen belüli szerepkörei. Az alkalmazás térkép úgy összetevők alábbi HTTP függőségi hívások esetében az Application Insights SDK telepítve kiszolgálók között. 

Ez a felület összetevőt fokozatos felderítés kezdődik. Először betölteni az előzetes kiadásban, ha lekérdezések készlete lépnek működésbe. Ez az összetevő kapcsolódó összetevőket felderítéséhez. A bal felső sarokban lévő gomb felfedezett frissíti az alkalmazás összetevők száma. 
![Előnézet-leképezés](media/app-insights-app-map/preview.png)

Az "Frissítés leképezési összetevők" kattint, a térkép összes összetevőkkel is, hogy a pont frissülnek.
![Előzetes betöltése térkép](media/app-insights-app-map/components-loaded-hierarchical.png)

Ha az összetevők mindegyikét szerepkörök belül egyetlen Application Insights-erőforrást, majd a felderítési lépésre nincs szükség. A kezdeti betöltés az alkalmazáshoz fog rendelkezni az összetevők.

Az új felület a fő célkitűzéseket egyik tudni megjelenítése összetevők száz komplex topológiákba. Az új felület nagyítása támogatja, és hozzáadja a részletes, akkor nagyítás növelésére. Azt is ki további egy pillanat alatt, és továbbra is direkt összetevői a magasabb hiba díjszabás megtekintése. 

![Nagyítási szint](media/app-insights-app-map/zoom-levels.png)

Kattintson a kapcsolódó elemzések és a teljesítmény és az összetevő hiba osztályozás élmény összetevők közül bármelyik.

![Menü](media/app-insights-app-map/preview-flyout.png)


## <a name="classic-application-map"></a>Klasszikus alkalmazás-hozzárendelés

A térkép mutatja:

* Rendelkezésre állási tesztek
* Ügyféloldali összetevő (figyeli a JavaScript SDK-val)
* Kiszolgálóoldali összetevő
* Az ügyfél és kiszolgáló összetevők függőségei

![alkalmazás-leképezés](./media/app-insights-app-map/02.png)

Bontsa ki, és a függőségi hivatkozás elemcsoportok:

![összecsukás](./media/app-insights-app-map/03.png)

Ha egy típust (SQL, HTTP stb.) a számos függőségi, jelennek meg a csoportosított. 

![a csoportosított függőségek](./media/app-insights-app-map/03-2.png)

## <a name="spot-problems"></a>Problémák
Minden fürtcsomópont vonatkozó teljesítménymutatói, többek között a terhelés, teljesítmény és hiba sebesség az adott összetevő. 

Figyelmeztetés ikon jelölje ki a lehetséges problémákat. Egy narancssárga figyelmeztetést azt jelenti, hogy nincsenek sikertelen kérelmek Lapmegtekintések vagy függőségi hívások esetében. Piros azt jelenti, hogy egy több mint 5 % hibaaránya. Ha azt szeretné, hogy lehetőség ezen küszöbértékek módosítására, nyissa meg a beállításokat.

![Hiba ikonok](./media/app-insights-app-map/04.png)

Aktív riasztásainak is megjelenítése fel: 

![aktív riasztások](./media/app-insights-app-map/05.png)

SQL Azure használ, nincs-e egy ikon, amely mutatja, hogy mikor van kapcsolatos javaslatok hogyan javíthatja a teljesítményt. 

![az Azure javaslat](./media/app-insights-app-map/06.png)

Kattintson a bármely ikonra a további részletek:

![az Azure javaslat](./media/app-insights-app-map/07.png)

## <a name="diagnostic-click-through"></a>Diagnosztikai kattintson keresztül
A csomópontok a térképen kínál a célzott kattintson ide a diagnosztika. A beállítások a csomópont típusától függően eltérnek.

![kiszolgáló beállításai](./media/app-insights-app-map/09.png)

Az Azure-ban szolgáltatott összetevők a választható lehetőségek őket mutató közvetlen hivatkozásokat.

## <a name="filters-and-time-range"></a>Szűrők és időtartomány
Alapértelmezés szerint a térkép összes érhető el a kiválasztott időtartomány adatok összegzése látható. De szűrheti is, hogy csak a konkrét műveletek nevének és a függőségek.

* A művelet neve: Ide tartoznak a lapmegtekintések, és kiszolgálóoldali kéréstípusok. Ezzel a beállítással a térkép a kiszolgáló-vagy ügyféloldali csomóponton csak a kijelölt műveletek a fő Teljesítménymutatói jelennek meg. Azt mutatja, hogy a függőségek hívása a műveletek adott kontextusban.
* Függőség alapnévvel: Ez magában foglalja a AJAX böngésző és kiszolgálóoldali függőségei is. Ha készít jelentést az egyéni függőségi telemetria TrackDependency API-val, is szerepelnek itt. Kiválaszthatja a függőségeket a térképen megjelenítése. Ez a beállítás jelenleg nem végez a kiszolgálóoldali kérelmeket, vagy az ügyféloldali Lapmegtekintések.

![Szűrők beállítása](./media/app-insights-app-map/11.png)

## <a name="save-filters"></a>Szűrők mentése
Szeretné menteni a szűrőt, PIN-kód alakzatot szűrt nézet egy [irányítópult](app-insights-dashboards.md).

![Rögzítés az irányítópulton](./media/app-insights-app-map/12.png)

## <a name="error-pane"></a>Hiba ablaktábla
Ha a leképezés egy csomópont gombra kattint, egy hiba ablaktáblán a jobb oldalon, akiknél probléma van a csomópont összefoglalójához jelenik meg. Hibák először Műveletazonosító szerint csoportosítva, és ezután csoportosítva probléma azonosítóját.

![Hiba ablaktábla](./media/app-insights-app-map/error-pane.png)

Kattintson a hiba vesz igénybe, hogy az adott hiba utolsó példányát.

## <a name="resource-health"></a>Erőforrás állapota
Az egyes erőforrástípusok erőforrás állapota a hiba ablaktábla tetején jelenik meg. Például egy SQL-csomópontra kattintva jelennek meg az adatbázis állapotának és az riasztások kiváltó rendelkezik.

![Erőforrás állapota](./media/app-insights-app-map/resource-health.png)

Az erőforrás nevét, az adott erőforrás szabványos áttekintése metrikák megtekintéséhez rákattinthat.

## <a name="video"></a>Videó

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player] 

## <a name="feedback"></a>Visszajelzés
Adja meg a portál visszajelzési lehetőség visszajelzései.

![MapLink-1 kép](./media/app-insights-app-map/13.png)


## <a name="next-steps"></a>További lépések

* [Azure Portal](https://portal.azure.com)
