---
title: Az Azure Application Insights Profiler beállítások panel |} A Microsoft Docs
description: A profiler állapotának megtekintéséhez és a munkamenetek Profilkészítés indítása
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: d96b4a99b2ea66cdeff43f06c1789dd133c2c31a
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2018
ms.locfileid: "52723006"
---
# <a name="configure-application-insights-profiler"></a>Az Application Insights Profiler konfigurálása

## <a name="profiler-settings-page"></a>Profiler-beállítások lap

A profiler beállításai lapon billentyűkombináció lenyomásával is megnyithatók az Application Insights teljesítmény oldalról a **Profiler** gombra.

![a profiler ablaktábla-bejegyzés konfigurálása][configure-profiler-entry]

Az Application Insights Profiler konfigurálása lap négy funkciókat tartalmazza: 
1. **Most már profil** -profilkészítési munkamenetek kell elindítani a minden alkalmazás, amely kapcsolódik az Application Insights példányát erre a gombra kattintva okoz
1. **Társított alkalmazások** -profiler küld az Application Insights-erőforrás alkalmazások listája
1. **Folyamatban lévő munkamenet** – ha lenyomja **profil most**, a munkamenet állapota itt jelenik meg)
1. **Legutóbbi előadások profilkészítés** – elmúlt profilkészítési munkamenetek vonatkozó információkat jeleníti meg.

![Profiler igény szerinti][profiler-on-demand]

## <a name="app-service-environments-ase"></a>App Service Environment (ASE)
Attól függően, hogy az ASE van konfigurálva ellenőrizze az ügynök állapotát a hívás blokkolhatja. Ezen a lapon tudatja Önnel, hogy az ügynök nem fut, amikor éppen ténylegesen. A webjobs-feladatot, hogy is ellenőrizze az alkalmazás. De ha megfelelően vannak-e beállítva az alkalmazásbeállítások és az alkalmazás telepítve van az App Insights-webhelybővítményt, a profiler fog futni, és megjelenik legutóbbi profilkészítési előadások a listában, ha nincs a megfelelő forgalmat az alkalmazásához.

## <a id="profileondemand"></a> Profiler aktiválása manuálisan

Profiler manuálisan egy gombra kattintással is elindítható. Tegyük fel, hogy egy webes teljesítményt tesztet futtatja. Szüksége lesz a nyomkövetések segítségével megismerheti, hogyan működik a webes alkalmazás terhelés alatt. Szabályozhatja, mikor rögzítve lesznek a nyomkövetések kellene azért rendkívül fontos, mert a terhelési teszt fog futni, de a véletlenszerű mintavételi időköz előfordulhat, hogy hagyja ki tudja.
A következő lépések bemutatják, hogyan működik a ebben a forgatókönyvben:

### <a name="optional-step-1-generate-traffic-to-your-web-app-by-starting-a-web-performance-test"></a>(Nem kötelező) 1. lépés: A webalkalmazás-forgalom létrehozása egy webes teljesítménytesztelési elindításával

Ha a webalkalmazás már van a bejövő forgalmat, vagy ha csak át szeretné manuálisan létrehozni a forgalmat, kihagyhatja ezt a szakaszt, és folytassa a 2. lépés.

Application Insights portálon lépjen **konfigurálása > Teljesítménytesztelés**. Kattintson az új gombra egy új teljesítményteszt elindításához.

![Hozzon létre új teljesítményteszt][create-performance-test]

Az a **új teljesítményteszt** ablaktáblán, a teszt cél URL-cím konfigurálása. Fogadja el az összes alapértelmezett beállítást, és indítsa el a terhelési teszt.

![Terheléses teszt beállítása][configure-performance-test]

Látni fogja az új vizsgálat a várólistára első, "folyamatban" állapotú követ.

![terheléses teszt elküldve, és várólistára helyezve][load-test-queued]

![terheléses teszt folyamatban fut.][load-test-in-progress]

### <a name="step-2-start-profiler-on-demand"></a>2. lépés: Indítsa el a profiler igény szerinti

Ha a terhelési teszt már fut, a profiler rögzíthesse a hívásláncokat a webalkalmazásban fogadja betöltése közben is kezdődik.
Keresse meg a Profiler konfigurálása panelen:


### <a name="step-3-view-traces"></a>3. lépés: Nézet nyomkövetések

A profiler a lejáratot követően újrainduljon, ha kövesse a teljesítmény-nyomkövetés oldal és a nézet értesítésben.

## <a name="troubleshooting-on-demand-profiler"></a>Igény szerinti profiler hibaelhárítása

Néha láthatja el egy igény szerinti munkamenet után Profiler időtúllépés hibaüzenet jelenik meg:

![Profiler időtúllépési hiba][profiler-timeout]

Miért érdemes ezt a hibaüzenetet két oka lehet:

1. A profiler igény szerinti munkamenet sikeres volt, de az Application Insights az összegyűjtött adatok feldolgozásához hosszabb időt vett igénybe. Adatok feldolgozása folyamatban 15 percben nem fejeződött be, ha a portálon egy időtúllépési üzenet jelenik meg. Bár idővel Profiler nyomkövetések jelennek meg. Ha ez történik, csak hagyja figyelmen kívül a hibaüzenetet most. Aktívan dolgozunk a javítást.

1. A webalkalmazás, amely nem rendelkezik az igény szerinti szolgáltatás Profiler-ügynök egy korábbi verziója van. Ha korábban engedélyezte az Application Insights-profilban, valószínűleg a Profiler-ügynök az igény szerinti funkció használatához frissítenie kell.
  
Kövesse az alábbi lépéseket, és telepítse a legújabb Profiler:

1. App Services alkalmazás beállításaiban, és ellenőrizze, hogy ha a következő beállítást:
    * **Állítani az APPINSIGHTS_INSTRUMENTATIONKEY**: cserélje le a megfelelő rendszerállapotkulcsot az Application Insights.
    * **APPINSIGHTS_PORTALINFO**: ASP.NET
    * **APPINSIGHTS_PROFILERFEATURE_VERSION**: 1.0.0 amennyiben ezen beállítások bármelyike nincs megadva, nyissa meg az Application Insights engedélyezését ablaktábla a legújabb webhelybővítmény telepítése.

1. Nyissa meg az Application Insights paneljén App Services portálon.

    ![App Services portálon az Application Insights engedélyezése][enable-app-insights]

1. Ha egy "Frissítés" gombot a következő oldal jelenik meg, kattintson az Application Insights webhelybővítményt, amely telepíti a legújabb Profiler-ügynök frissítését.

    ![Webhelybővítmény frissítése][update-site-extension]

1. Kattintson a **módosítása** való gondoskodik róla, hogy a Profiler bekapcsolt, és válassza ki **OK** menti a módosításokat.

    ![Módosítsa és mentse az app insights][change-and-save-appinsights]

1. Lépjen vissza a **Alkalmazásbeállítások** lapon ellenőrizze a következő alkalmazás beállítások elemeinek az App Service vannak beállítva:
    * **Állítani az APPINSIGHTS_INSTRUMENTATIONKEY**: cserélje le a megfelelő rendszerállapotkulcsot az application insights.
    * **APPINSIGHTS_PORTALINFO**: ASP.NET
    * **APPINSIGHTS_PROFILERFEATURE_VERSION**: 1.0.0

    ![a profiler App beállításai][app-settings-for-profiler]

1. Szükség esetén ellenőrizze a bővítmény verziója, és gondoskodik róla, hogy nincs elérhető frissítés.

    ![Ellenőrizze a bővítmény frissítése][check-for-extension-update]

## <a name="next-steps"></a>További lépések
[Profiler engedélyezése és megtekintése a nyomkövetések](app-insights-profiler-overview.md?toc=/azure/azure-monitor/toc.json)

[profiler-on-demand]: ./media/app-insights-profiler/Profiler-on-demand.png
[configure-profiler-entry]: ./media/app-insights-profiler/configure-profiler-entry.png
[create-performance-test]: ./media/app-insights-profiler/new-performance-test.png
[configure-performance-test]: ./media/app-insights-profiler/configure-performance-test.png
[load-test-queued]: ./media/app-insights-profiler/load-test-queued.png
[load-test-in-progress]: ./media/app-insights-profiler/load-test-inprogress.png
[enable-app-insights]: ./media/app-insights-profiler/enable-app-insights-blade-01.png
[update-site-extension]: ./media/app-insights-profiler/update-site-extension-01.png
[change-and-save-appinsights]: ./media/app-insights-profiler/change-and-save-appinsights-01.png
[app-settings-for-profiler]: ./media/app-insights-profiler/appsettings-for-profiler-01.png
[check-for-extension-update]: ./media/app-insights-profiler/check-extension-update-01.png
[profiler-timeout]: ./media/app-insights-profiler/profiler-timeout.png