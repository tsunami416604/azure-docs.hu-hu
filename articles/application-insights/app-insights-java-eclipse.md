---
title: "Ismerkedés az Azure Application Insights az eclipse-ben Java |} Microsoft docs"
description: "Az Eclipse beépülő modul segítségével adja hozzá a teljesítmény- és használat figyelését a Java-webhelyéhez az Application insights szolgáltatással"
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: e88c9f53-cd90-4abc-b097-1f170937908e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 12/12/2016
ms.author: mbullwin
ms.openlocfilehash: 616cbfed405454d2abbb6bb526166d2c72e4365d
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2017
---
# <a name="get-started-with-application-insights-with-java-in-eclipse"></a>Ismerkedés az Application Insights Java az eclipse-ben
Az Application Insights SDK, hogy elemezheti a használati és teljesítményadatokat küld a Java-webalkalmazások telemetriai adatokat. Az eclipse-ben az Application Insights beépülő modult úgy, hogy be kívül mezőben telemetriai adatok, valamint az API-k, amelyek segítségével egyéni telemetriai adatokat írni automatikusan telepíti az SDK-t a projektben.   

## <a name="prerequisites"></a>Előfeltételek
Jelenleg a beépülő modul működését Maven-projektek és dinamikus webes projektek az eclipse-ben.
([Application Insights hozzáadása a Java-projektet, más típusú][java].)

A következők szükségesek:

* Oracle JRE 1.6 vagy újabb verzió
* Egy [Microsoft Azure](https://azure.microsoft.com/)-előfizetés.
* [Java EE-fejlesztőknek IDE Holdas](http://www.eclipse.org/downloads/), Indigo vagy újabb.
* Windows 7 vagy újabb, vagy a Windows Server 2008 vagy újabb

## <a name="install-the-sdk-on-eclipse-one-time"></a>Telepítse az SDK Eclipse (egyszer)
Csak kell elvégezni a számítógép egy alkalommal. Ez a lépés telepíti egy eszközkészlet, amelyet ezután felvehet az SDK minden dinamikus webes projekt.

1. Az eclipse-ben kattintson a Súgó, új szoftverek telepítése.

    ![Súgó, új szoftverek telepítése](./media/app-insights-java-eclipse/0-plugin.png)
2. Az SDK http://dl.microsoft.com/eclipse Azure eszközkészlet alatt van.
3. Törölje a jelet **lépjen kapcsolatba az összes frissítés hely...**

    ![Application Insights SDK törölje lépjen kapcsolatba az összes frissítés helyek](./media/app-insights-java-eclipse/1-plugin.png)

Kövesse a hátralévő minden Java-projektet.

## <a name="create-an-application-insights-resource-in-azure"></a>Az Application Insights-erőforrás létrehozása az Azure-ban
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Hozzon létre egy új Application Insights-erőforrást. Állítsa be a Java webalkalmazás alkalmazástípust.  

    ![Kattintson a + gombra, és válassza az Application Insights lehetőséget.](./media/app-insights-java-eclipse/01-create.png)  

4. Keresse meg az új erőforrás kialakítási kulcsát. Ezt nemsokára a kódprojektbe kell illesztenie.  

    ![Az új erőforrás áttekintésében kattintson a Tulajdonságok gombra, és másolja le a kialakítási kulcsot](./media/app-insights-java-eclipse/03-key.png)  

## <a name="add-application-insights-to-your-project"></a>Az Application Insights hozzáadása a projekthez
1. Az Application Insights hozzáadása a Java-webalkalmazás projekt helyi menüjéből.

    ![Az új erőforrás áttekintésében kattintson a Tulajdonságok gombra, és másolja le a kialakítási kulcsot](./media/app-insights-java-eclipse/02-context-menu.png)
2. Illessze be a instrumentation kulcs portáltól kapott az Azure-portálon.

    ![Az új erőforrás áttekintésében kattintson a Tulajdonságok gombra, és másolja le a kialakítási kulcsot](./media/app-insights-java-eclipse/03-ikey.png)

A kulcsot minden telemetriai tétel együtt küldött, és közli az Application Insights meg azt az erőforrás.

## <a name="run-the-application-and-see-metrics"></a>Futtassa az alkalmazást, és tekintse meg a metrikák
Futtassa az alkalmazást.

Térjen vissza az Application Insights-erőforrást, a Microsoft Azure-ban.

A HTTP-kérelemadatok az áttekintési panelen jelennek meg. (Ha nincsenek ott, várjon néhány másodpercig, majd kattintson a Frissítés gombra.)

![Kiszolgáló válasza, az ügyfélkérelmek számát és a hibák ](./media/app-insights-java-eclipse/5-results.png)

Részletesebb mérőszámokért kattintson bármelyik diagramra.

![A kérelmek számát név szerint](./media/app-insights-java-eclipse/6-barchart.png)

[További információk a metrikákról.][metrics]

És egy kérelem tulajdonságainak megtekintésekor láthatja a telemetriai események például a kérelmek és kivételek társítva.

![A kérelemhez tartozó összes adat](./media/app-insights-java-eclipse/7-instance.png)

## <a name="client-side-telemetry"></a>Ügyféloldali telemetriai adat
Első lépések panelen kattintson a Get-kódot a weblapok figyeléséhez:

![Az alkalmazás áttekintési panelén válassza a Gyors üzembe helyezés, Kód letöltése a weblapok figyeléséhez lehetőséget. Másolja a szkriptet.](./media/app-insights-java-eclipse/02-monitor-web-page.png)

Helyezze be a kódrészletet a HTML-fájlok vezetője.

#### <a name="view-client-side-data"></a>Az ügyféloldali adatok megtekintése
Nyissa meg a frissített weblapok és használhatják azokat. Várjon egy percet, vagy két, majd térjen vissza az Application Insights, és a használati panel megnyitásához. (Áttekintése panelen görgessen lefelé, és kattintson a használati.)

A használati panelen lap nézet, a felhasználó és a munkamenet metrikák jelennek meg:

![Munkamenetek, a felhasználók és az oldalmegtekintéseket](./media/app-insights-java-eclipse/appinsights-47usage-2.png)

[További információ az ügyféloldali telemetriai beállítása.][usage]

## <a name="publish-your-application"></a>Az alkalmazás közzététele
Most tegye közzé az alkalmazást a kiszolgálón, hagyja, hogy mások használják, és nézze, ahogyan a telemetria megjelenik a portálon.

* Győződjön meg arról, hogy a tűzfal lehetővé teszi, hogy az alkalmazás telemetriát küldjön ezekre a portokra:

  * dc.services.visualstudio.com:443
  * dc.services.visualstudio.com:80
  * f5.services.visualstudio.com:443
  * f5.services.visualstudio.com:80
* Windows-kiszolgálókon telepítse a következőt:

  * [Microsoft Visual C++ újraterjeszthető csomag](http://www.microsoft.com/download/details.aspx?id=40784)

    (Ez lehetővé teszi a teljesítményszámlálókat.)

## <a name="exceptions-and-request-failures"></a>Kivételek és kérelemhibák
A rendszer a nem kezelt kivételeket automatikusan begyűjti:

![](./media/app-insights-java-eclipse/21-exceptions.png)

Adatok és más kivételek gyűjtésére két lehetősége van:

* [Helyezze be a kódot TrackException hívások](app-insights-api-custom-events-metrics.md#trackexception).
* [Telepítse a Java ügynököt a kiszolgálón](app-insights-java-agent.md). Válassza ki a megtekinteni kívánt metódusokat.

## <a name="monitor-method-calls-and-external-dependencies"></a>Metódushívások és külső függőségek megfigyelése
[Telepítse a Java ügynököt](app-insights-java-agent.md) a megadott belső módszerek és a JDBC-n keresztül végzett hívások naplózásához, időzítési adatokkal.

## <a name="performance-counters"></a>Teljesítményszámlálók
Az Áttekintés panelen görgessen lefelé, és kattintson a **kiszolgálók** csempére. Teljesítményszámlálók számos láthatja.

![Görgessen le, kattintson a kiszolgálók csempéje](./media/app-insights-java-eclipse/11-perf-counters.png)

### <a name="customize-performance-counter-collection"></a>Teljesítményszámláló-gyűjtemény testreszabása
A teljesítményszámlálók standard készlete gyűjtésének letiltásához adja a következő kódot az ApplicationInsights.xml fájl gyökércsomópontja alatt:

```XML

    <PerformanceCounters>
       <UseBuiltIn>False</UseBuiltIn>
    </PerformanceCounters>
```

### <a name="collect-additional-performance-counters"></a>További teljesítményszámlálók gyűjtése
További gyűjtendő teljesítményszámlálókat határozhat meg.

#### <a name="jmx-counters-exposed-by-the-java-virtual-machine"></a>JMX-számlálók (a Java virtuális gép által feltárt)

```XML

    <PerformanceCounters>
      <Jmx>
        <Add objectName="java.lang:type=ClassLoading" attribute="TotalLoadedClassCount" displayName="Loaded Class Count"/>
        <Add objectName="java.lang:type=Memory" attribute="HeapMemoryUsage.used" displayName="Heap Memory Usage-used" type="composite"/>
      </Jmx>
    </PerformanceCounters>
```

* `displayName`– Az Application Insights portálon megjelenő név.
* `objectName`– A JMX objektum neve.
* `attribute`– A JMX objektum nevének lehívni kívánt attribútuma
* `type`(választható) – A JMX objektum attribútumának típusa:
  * Alapértelmezett: egyszerű típus, például int vagy long.
  * `composite`: a teljesítményszámláló-adatok az „Attribútum.Adat” formátumban szerepelnek
  * `tabular`: a teljesítményszámláló-adatok táblázatsor formájában szerepelnek

#### <a name="windows-performance-counters"></a>Windows-teljesítményszámlálók
Mindegyik [Windows-teljesítményszámláló](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx) egy kategória tagja (ugyanúgy, ahogyan a mezők osztályok tagjai). A kategóriák globálisak lehetnek, vagy számozott vagy elnevezett példányokkal rendelkezhetnek.

```XML

    <PerformanceCounters>
      <Windows>
        <Add displayName="Process User Time" categoryName="Process" counterName="%User Time" instanceName="__SELF__" />
        <Add displayName="Bytes Printed per Second" categoryName="Print Queue" counterName="Bytes Printed/sec" instanceName="Fax" />
      </Windows>
    </PerformanceCounters>
```

* displayName – Az Application Insights portálon megjelenő név.
* categoryName – A teljesítményszámláló kategóriája (teljesítményobjektum), amelyhez ez a teljesítményszámláló társítva van.
* counterName – A teljesítményszámláló neve.
* instanceName – A teljesítményszámláló-kategória példányneve, vagy üres karakterlánc („”), ha a kategória egyetlen példányt tartalmaz. Ha a categoryName Folyamat, és a gyűjteni kívánt teljesítményszámláló az aktuális JVM folyamatról származik, amelyen az alkalmazása fut, adja meg a következőt: `"__SELF__"`.

A teljesítményszámlálói egyéni mérőszámokként láthatók a [Metrikaböngészőben][metrics].

![](./media/app-insights-java-eclipse/12-custom-perfs.png)

### <a name="unix-performance-counters"></a>Unix-teljesítményszámlálók
* [Telepítse a gyűjteményt az Application Insights beépülő modullal](app-insights-java-collectd.md) számos rendszer- és hálózati adat lekéréséhez.

## <a name="availability-web-tests"></a>Rendelkezésre állási webes tesztek
Az Application Insights rendszeres időközönként teszteli a webhelyét, hogy működik és jól válaszol-e. [Beállítása][availability], görgessen le a rendelkezésre állási elemre.

![Görgessen lefelé, kattintson a Rendelkezésre állás elemre, majd a Webes teszt hozzáadása elemre](./media/app-insights-java-eclipse/31-config-web-test.png)

Megkapja a válaszidők diagramjait, valamint e-mailes értesítéseket kap, ha a webhely leáll.

![Példa webes tesztre](./media/app-insights-java-eclipse/appinsights-10webtestresult.png)

[További információk a rendelkezésre állási webes tesztekről.][availability]

## <a name="diagnostic-logs"></a>Diagnosztikai naplók
Ha a Log4J vagy a Logback használata (1.2-es verzió vagy 2.0-s) a nyomkövetés, akkor a nyomkövetési naplók megtekintése az Application Insights ahol vizsgálatát, és keresse meg őket automatikusan elküldi.

[További információ a diagnosztikai naplók][javalogs]

## <a name="custom-telemetry"></a>Egyéni telemetria
Néhány sornyi kód beszúrása a Java-webalkalmazás megállapítása a felhasználók tevékenységeit vele, vagy a problémák diagnosztizálásához.

Kód weblapon JavaScript- és kiszolgálóoldali Java beszúrásához.

[További tudnivalók az egyéni telemetria][track]

## <a name="next-steps"></a>Következő lépések
#### <a name="detect-and-diagnose-issues"></a>Észlelheti és diagnosztizálhatja a problémákat
* [Adja hozzá a webes ügyfél telemetriai] [ usage] teljesítmény telemetriai adatainak lekérése a webes ügyféllel.
* [Beállíthat webes teszteket][availability] annak biztosításához, hogy az alkalmazás mindig elérhető és válaszkész legyen.
* [Eseményeket és naplókat kereshet][diagnostic], amelyek segítenek a problémák diagnosztizálásában.
* [Log4J vagy a Logback nyomkövetés rögzítése][javalogs]

#### <a name="track-usage"></a>Használat követése
* [Adja hozzá a webes ügyfél telemetriai] [ usage] figyelő Lapmegtekintések és alapszintű felhasználói metrikákat.
* [Egyéni események és metrikák nyomon](app-insights-web-track-usage.md) további információt az alkalmazás használatáról, mind az ügyfél és a kiszolgálón.

<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[java]: app-insights-java-get-started.md
[javalogs]: app-insights-java-trace-logs.md
[metrics]: app-insights-metrics-explorer.md
[track]: app-insights-api-custom-events-metrics.md
[usage]: app-insights-javascript.md
