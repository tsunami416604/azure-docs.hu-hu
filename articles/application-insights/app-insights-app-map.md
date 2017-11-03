---
title: "Alkalmazás-hozzárendelés Azure Application insightsban |} Microsoft Docs"
description: "Az alkalmazások összetevői között fennálló vizuális megjelenítését feliratú KPI-k és riasztásokkal."
services: application-insights
documentationcenter: 
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
ms.openlocfilehash: e1eb2177d6032142781e6e31af6c7f6313d38f4d
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2017
---
# <a name="application-map-in-application-insights"></a>Az Application Insightsban alkalmazás-hozzárendelés
A [Azure Application Insights](app-insights-overview.md), alkalmazás-hozzárendelés az alkalmazás-összetevői közötti függőségi kapcsolatokat visual elrendezés. Minden egyes összetevő KPI-k például a terhelés, teljesítmény, hibák és figyelmeztetések, segítségével megkeresheti az adott teljesítményprobléma vagy hiba, amely összetevők közül bármelyik jeleníti meg. Kattintva keresztül valamelyik összetevő a részletesebb diagnosztikai, például az Application Insights események. Ha az alkalmazás Azure-szolgáltatásokat használja, akkor is átkattintással is Azure Diagnostics, például az SQL Database Advisor-javaslatokra.

Más típusú diagramokkal, például PIN-kód egy alkalmazás-hozzárendelés az Azure irányítópultot, ahol is teljes körűen használható. 

## <a name="open-the-application-map"></a>Nyissa meg az alkalmazás-hozzárendelés
Nyissa meg a térképen az alkalmazáshoz – Áttekintés paneljéről:

![Nyissa meg az alkalmazás-leképezés](./media/app-insights-app-map/01.png)

![alkalmazás-leképezés](./media/app-insights-app-map/02.png)

A térkép mutatja:

* Rendelkezésre állási tesztek
* Ügyféloldali összetevő (figyeli a JavaScript SDK-val)
* Kiszolgálóoldali összetevő
* Az ügyfél és kiszolgáló összetevők függőségei

Bontsa ki, és a függőségi hivatkozás elemcsoportok:

![összecsukása](./media/app-insights-app-map/03.png)

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

## <a name="end-to-end-system-app-maps"></a>Végpontok közötti rendszer app maps

*SDK 2.3-as vagy újabb verziója szükséges*

Ha az alkalmazás több részből áll – például egy háttér-szolgáltatás emellett a webes alkalmazás -, akkor is megjeleníthetők az összes egy integrált alkalmazás térképen.

![Szűrők beállítása](./media/app-insights-app-map/multi-component-app-map.png)

Az alkalmazás térkép csomópontok bármely HTTP függőségi hívások esetében az Application Insights SDK telepítve a kiszolgálók közötti következő talál. Minden egyes Application Insights-erőforrás feltételezett, hogy egy kiszolgálót tartalmaz.

### <a name="multi-role-app-map-preview"></a>Több szerepkör app térkép (előzetes verzió)

Több szerepkör app térkép előnézet lehetővé teszi, hogy az alkalmazás térkép használja az adatok küldése az Application Insights-erőforrások több kiszolgáló / instrumentation kulcs. A térkép kiszolgálók vannak szegmentált telemetriai elemek cloud_RoleName tulajdonság által. Állítsa be *alkalmazás több szerepkör-hozzárendelés* való *a* ahhoz, hogy ez a konfiguráció az előzetes verziójú funkciók paneljén.

Ez a megközelítés kívánatos lehet egy micro-szolgáltatások alkalmazás, vagy a más helyzetekben, ahol szeretné események összefüggéseket egyetlen Application Insights-erőforrás belül több kiszolgáló között.

## <a name="video"></a>Videó

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player] 

## <a name="feedback"></a>Visszajelzés
Adja meg a portál visszajelzési lehetőség visszajelzései.

![MapLink-1 kép](./media/app-insights-app-map/13.png)


## <a name="next-steps"></a>Következő lépések

* [Azure Portal](https://portal.azure.com)
