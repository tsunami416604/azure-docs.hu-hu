---
title: Az Azure Application Insights Profiler beállítások panelen |} A Microsoft Docs
description: Profiler állapotának megtekintéséhez és a munkamenetek Profilkészítés indítása
services: application-insights
documentationcenter: ''
author: cweining
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 08/06/2018
ms.author: cweining
ms.openlocfilehash: 9603c45443c6339a127f977600eeff2ba57a283f
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58884159"
---
# <a name="configure-application-insights-profiler"></a>Az Application Insights Profiler konfigurálása

## <a name="profiler-settings-pane"></a>Profiler beállítások panel

Az Azure Application Insights Profiler-beállítások panel megnyitásához nyissa meg az Application Insights teljesítménye paneljén, és válassza ki a **Profiler** gombra.

![A Profiler panel konfigurálása][configure-profiler-entry]

A **Application Insights Profiler konfigurálása** panelen négy funkciókat tartalmazza: 
* **Most már profil**: Elindítja a profilkészítés minden olyan alkalmazás, amely kapcsolódik a példány az Application Insights-munkamenetet.
* **Társított alkalmazások**: Profilkészítés küldő alkalmazás fel van sorolva az Application Insights-erőforrást az adatokat.
* **Folyamatban lévő munkamenet**: Amikor kiválasztja a munkamenet állapotát jeleníti meg **profil most**. 
* **Legutóbbi előadások profilkészítés**: Elmúlt profilkészítési munkamenetek információit jeleníti meg.

![Profiler igény szerinti][profiler-on-demand]

## <a name="app-service-environment"></a>App Service-környezet
Az Azure App Service-környezet konfigurációjától függően érdemes lehet blokkolni a hívást, ellenőrizze az ügynök állapotát. A panel, amely az ügynök nem fut, akkor is, ha az üzenetet jeleníthet meg. Győződjön meg arról, hogy, ellenőrizze az alkalmazás a webjobs-feladatot. Ha az alkalmazás beállítási értékei helyesek, és az alkalmazás telepítve van az Application Insights-webhelybővítményt, fut. a Profiler. Ha az alkalmazás elegendő forgalmat fogad, legutóbbi profilkészítési előadások üzenetnek kell megjelennie a listában.

## <a id="profileondemand"></a> Profiler aktiválása manuálisan

### <a name="minimum-requirements"></a>Minimális követelmények 
Egy felhasználó manuálisan indítható a profiler munkamenet igényelnek legalább "write" hozzáférés az Application Insights-összetevő a szerepkör. A legtöbb esetben ez hozzáférést kap automatikusan, és nincs további feladata van szükség. Ha problémák merülnek fel, az előfizetés hatókör szerepkör hozzáadása az "Application Insights-összetevő közreműködő" szerepkör lesz. [Talál további információt a szerepkör hozzáférés-vezérlés az Azure Monitoring](https://docs.microsoft.com/en-us/azure/azure-monitor/app/resources-roles-access-control).

Profiler manuálisan is aktiválhatja egyetlen kattintással. Tegyük fel, hogy a webes teljesítménytesztelési futtat. Nyomkövetési információk segítségével megismerheti, hogyan működik a webes alkalmazás terhelés alatt kell. Szabályozhatja, mikor rögzítve lesznek a nyomkövetések kellene elengedhetetlen, mivel a terhelési teszt futtatásakor. De a véletlenszerű mintavételi időköz előfordulhat, hogy hagyja ki.

A következő szakaszok bemutatják, hogyan működik a ebben a forgatókönyvben:

### <a name="step-1-optional-generate-traffic-to-your-web-app-by-starting-a-web-performance-test"></a>1. lépés: (Nem kötelező) A webes teljesítménytesztelési elindításával forgalmat a webes alkalmazás készítése

Ha a webalkalmazás már van a bejövő forgalmat, vagy ha csak át szeretné manuálisan létrehozni a forgalmat, kihagyhatja ezt a szakaszt, és folytassa a 2. lépés.

1. Válassza ki az Application Insights portálon **konfigurálása** > **Teljesítménytesztelés**. 

1. Új teljesítményteszt elindításához válassza ki a **új** gombra.

   ![Hozzon létre új teljesítményteszt][create-performance-test]

1. Az a **új teljesítményteszt** ablaktáblán, a teszt cél URL-cím konfigurálása. Fogadja el az összes alapértelmezett beállítást, és válassza ki **Futtatás teszt** a terhelési teszt futtatni.

    ![Terheléses teszt beállítása][configure-performance-test]

    Az új vizsgálat van először a várólistára, a állapotát követi *folyamatban*.

    ![terheléses teszt elküldve, és várólistára helyezve][load-test-queued]

    ![terheléses teszt folyamatban fut.][load-test-in-progress]

### <a name="step-2-start-a-profiler-on-demand-session"></a>2. lépés: A Profiler igény szerinti munkamenet indítása

1. A terhelési teszt fut, indítsa el az Profiler rögzíthesse a hívásláncokat a webalkalmazásban fogadja betöltése közben.

1. Nyissa meg a **Profiler konfigurálása** ablaktáblán.


### <a name="step-3-view-traces"></a>3. lépés: Nyomok megtekintése

Profiler végeztével kövesse a teljesítmény-nyomkövetés ablaktábla és a nézet értesítésben.

## <a name="troubleshoot-the-profiler-on-demand-session"></a>A Profiler igény szerinti munkamenet hibaelhárítása

Egy igény szerinti munkamenet után egy Profiler időtúllépés hibaüzenetet kaphat:

![Profiler időtúllépési hiba][profiler-timeout]

A kettő közül a következő okok miatt előfordulhat, hogy ezt a hibaüzenetet kapja:

* Az igény szerinti Profiler munkamenet sikeres volt, de az Application Insights az összegyűjtött adatok feldolgozása a vártnál hosszabb időt vett igénybe.  

  Ha az adatok feldolgozása a 15 percen belül nem, a portál megjeleníti egy időtúllépési üzenet. Idővel azonban Profiler nyomkövetések jelennek meg. Ha hibaüzenetet kap, prozatím ignorovat. Aktívan dolgozunk a javítást.

* A webalkalmazás, amely nem rendelkezik az igény szerinti szolgáltatás Profiler-ügynök egy korábbi verziója van.  

  Ha korábban engedélyezte az Application Insights Profiler, szüksége lehet frissíteni a Profiler-ügynök az igény szerinti szolgáltatás használatának megkezdéséhez.
  
Nyissa meg az App Services **Alkalmazásbeállítások** ablaktáblán, és keresse a következő beállításokat:
* **ÁLLÍTANI AZ APPINSIGHTS_INSTRUMENTATIONKEY**: Cserélje le a megfelelő rendszerállapotkulcsot az Application Insights.
* **APPINSIGHTS_PORTALINFO**: ASP.NET
* **APPINSIGHTS_PROFILERFEATURE_VERSION**: 1.0.0

Ha az előző értékeket nem, telepítse a legújabb webhelybővítmény az alábbiak szerint:

1. Nyissa meg a **Application Insights** ablaktáblán az App Services portálon.

    ![Az App Services-portálról az Application Insights engedélyezése][enable-app-insights]

1. Ha a **Application Insights** ablaktáblán megjelennek azok az **frissítése** gombra, válassza ki azt az Application Insights-webhelybővítményt, amely telepíti a legújabb Profiler-ügynök frissítéséhez.

    ![Webhelybővítmény frissítése][update-site-extension]

1. Győződjön meg arról, hogy a Profiler van-e kapcsolva, jelölje be **módosítása**, majd válassza ki **OK** menti a módosításokat.

    ![Módosítsa és mentse az app insights][change-and-save-appinsights]

1. Lépjen vissza a **Alkalmazásbeállítások** panelje az App Service, győződjön meg arról, hogy vannak-e beállítva a következő értékeket:
   * **ÁLLÍTANI AZ APPINSIGHTS_INSTRUMENTATIONKEY**: Cserélje le a megfelelő rendszerállapotkulcsot az application insights.
   * **APPINSIGHTS_PORTALINFO**: ASP.NET 
   * **APPINSIGHTS_PROFILERFEATURE_VERSION**: 1.0.0

     ![Profiler App beállításai][app-settings-for-profiler]

1. Bejelölheti **bővítmények**, majd ellenőrizze a bővítmény verzióját, és határozza meg, hogy a frissítés érhető el.

    ![Ellenőrizze a bővítmény frissítése][check-for-extension-update]

## <a name="next-steps"></a>További lépések
[Profiler engedélyezése és nyomok megtekintése](profiler-overview.md?toc=/azure/azure-monitor/toc.json)

[profiler-on-demand]: ./media/profiler-settings/Profiler-on-demand.png
[configure-profiler-entry]: ./media/profiler-settings/configure-profiler-entry.png
[create-performance-test]: ./media/profiler-settings/new-performance-test.png
[configure-performance-test]: ./media/profiler-settings/configure-performance-test.png
[load-test-queued]: ./media/profiler-settings/load-test-queued.png
[load-test-in-progress]: ./media/profiler-settings/load-test-inprogress.png
[enable-app-insights]: ./media/profiler-settings/enable-app-insights-blade-01.png
[update-site-extension]: ./media/profiler-settings/update-site-extension-01.png
[change-and-save-appinsights]: ./media/profiler-settings/change-and-save-appinsights-01.png
[app-settings-for-profiler]: ./media/profiler-settings/appsettings-for-profiler-01.png
[check-for-extension-update]: ./media/profiler-settings/check-extension-update-01.png
[profiler-timeout]: ./media/profiler-settings/profiler-timeout.png
