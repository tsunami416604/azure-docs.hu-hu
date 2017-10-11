---
title: "Azure Application Insights Docker alkalmazások figyelésének |} Microsoft Docs"
description: "Docker teljesítményszámlálói, eseményeket és kivételeket is megjeleníthetők az Application Insights együtt a telemetriai adatok indexelése alkalmazásokból."
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.assetid: 27a3083d-d67f-4a07-8f3c-4edb65a0a685
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: bwren
ms.openlocfilehash: b082e345ca1bb3b12c548e05e699474d3aa9306c
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2017
---
# <a name="monitor-docker-applications-in-application-insights"></a>Az Application Insightsban Docker-alkalmazások figyelése
A teljesítményszámlálók életciklus-események és a teljesítmény [Docker](https://www.docker.com/) tárolók is forrásadatok az Application insights szolgáltatással. Telepítse a [Application Insights](app-insights-overview.md) lemezkép a gazdagép és a tároló teljesítményszámlálók megjeleníti a gazdagép, valamint a többi képet.

Docker az egyszerűsített tárolók teljes összes függőségekkel rendelkező alkalmazások terjesztése. Ezek fogja futtatni, a gazdagépen, amelyen egy Docker-motorhoz.

Futtatásakor a [Application Insights kép](https://hub.docker.com/r/microsoft/applicationinsights/) a Docker állomáson ilyen előnyt beolvasása:

* Életciklus telemetriai adatainak fut az összes tároló a gazdagép - indítása, leállítása, és így tovább.
* A tárolók teljesítményszámlálók. Processzor, memória, hálózati használati és több.
* Ha Ön [Javához készült Application Insights SDK telepítve](app-insights-java-live.md) a tárolókban lévő futó alkalmazások, az alkalmazások telemetriai adatok azonosítása a tároló és a fogadó számítógép további tulajdonságainak rendelkeznek. Így például ha egy alkalmazás több gazdagépen fut, könnyen szűrheti az alkalmazás telemetriai állomás.

![Példa](./media/app-insights-docker/00.png)

## <a name="set-up-your-application-insights-resource"></a>Az Application Insights-erőforrás beállítása
1. Jelentkezzen be a [Microsoft Azure-portálon](https://azure.com) , és nyissa meg az Application Insights-erőforrást az alkalmazáshoz; vagy [hozzon létre egy újat](app-insights-create-new-resource.md). 
   
    *Erőforrás-érdemes használni?* Ha az alkalmazások, a gazdagépen futó fejlesztett valaki más, akkor kell [hozzon létre egy új Application Insights-erőforrást](app-insights-create-new-resource.md). Ez az megtekintése, és ahol a telemetriai adatok elemzése. (Válassza ki az alkalmazás típusa az "általános".)
   
    De ha Ön az alkalmazásokat a fejlesztői, majd Reméljük, hogy [Application Insights SDK hozzáadott](app-insights-java-live.md) hozzájuk. Ha minden valóban az egyetlen üzleti alkalmazások összetevői, akkor előfordulhat, hogy konfigurálja az összes telemetriai adatokat küldhet egy erőforrást, és azt ismertetjük, hogy ugyanaz az erőforrás a Docker életciklust és a teljesítmény adatok megjelenítéséhez. 
   
    Harmadik forgatókönyv, hogy az alkalmazások a legtöbb kidolgozott, de külön erőforrások használata a telemetriai adatok megjelenítéséhez. Ebben az esetben akkor valószínűleg is szeretné a Docker adatokat külön erőforrás létrehozása. 
2. Adja hozzá a Docker csempe: válasszon **vegye fel a csempe**, húzza a Docker csempe a gyűjteményből, és kattintson a **végzett**. 
   
    ![Példa](./media/app-insights-docker/03.png)
3. Kattintson a **Essentials** legördülő lista és a rendszerállapot-kulcs másolása. Ezzel, hogy az SDK helyét a telemetriai adatokat küldhet.

    ![Példa](./media/app-insights-docker/02-props.png)

Tartsa böngészőablakot lesz szüksége, akkor lesz térjen vissza úgy, hogy hamarosan nézze meg a telemetriai adatokat.

## <a name="run-the-application-insights-monitor-on-your-host"></a>A gazdagépen az Application Insights-figyelő futtatása
Most, hogy telepítve vannak-e valahol telemetriai adatok megjelenítéséhez, a tárolóalapú alkalmazást, amely gyűjt, és elküldi a állíthat be.

1. A Docker-állomással lépnek kapcsolatba. 
2. Szerkesztheti a instrumentation be ezt a parancsot, és futtassa azt:
   
   ```
   
   docker run -v /var/run/docker.sock:/docker.sock -d microsoft/applicationinsights ikey=000000-1111-2222-3333-444444444
   ```

Csak egy Application Insights-lemezkép szükség a Docker állomásonként. Ha az alkalmazás több Docker gazdagépen van telepítve, majd ismételje meg a parancs minden gazdagépen.

## <a name="update-your-app"></a>Az alkalmazás frissítése
Ha az alkalmazást a rendszer tagolva a [Javához készült Application Insights SDK](app-insights-java-get-started.md), adja meg a következő sort a ApplicationInsights.xml fájlba a projekt alatt a `<TelemetryInitializers>` elem:

```xml

    <Add type="com.microsoft.applicationinsights.extensibility.initializer.docker.DockerContextInitializer"/> 
```

Minden az alkalmazásból küldött telemetriai elem hozzáadása Docker információkat, például a tároló és az állomás azonosítója.

## <a name="view-your-telemetry"></a>A telemetria megtekintése
Térjen vissza az Application Insights-erőforrást az Azure portálon.

Kattintson a Docker csempén.

A Docker alkalmazásból érkező adatok hamarosan megjelenik, különösen akkor, ha más tárolók futó a Docker-motorhoz.

Íme néhány a nézetek kaphat.

### <a name="perf-counters-by-host-activity-by-image"></a>Állomás, kép tevékenység teljesítményszámlálói
![Példa](./media/app-insights-docker/10.png)

![Példa](./media/app-insights-docker/11.png)

Kattintson a további részletek gazdagép- vagy képfájl nevére.

A nézet testreszabásához kattintson bármelyik olyan diagram, a rács fejléc, vagy adja hozzá a diagram. 

[További információ a metrikaböngésző](app-insights-metrics-explorer.md).

### <a name="docker-container-events"></a>Docker-tároló események
![Példa](./media/app-insights-docker/13.png)

Vizsgálja meg az egyes események, kattintson a [keresési](app-insights-diagnostic-search.md). Keresés és szűrés kívánt események kereséséhez. Kattintson azon események segítségével további információkhoz juthat.

### <a name="exceptions-by-container-name"></a>Kivételek tároló neve
![Példa](./media/app-insights-docker/14.png)

### <a name="docker-context-added-to-app-telemetry"></a>Docker környezet hozzá lett adva, az alkalmazás telemetriai adat
AI SDK-t és Docker környezetben növelést tagolva az alkalmazásból küldött telemetriai kérelem:

![Példa](./media/app-insights-docker/16.png)

Processzor és memória teljesítményszámlálókat, dúsított, majd Docker-tároló neve szerint csoportosítva:

![Példa](./media/app-insights-docker/15.png)

## <a name="q--a"></a>Kérdések és válaszok
*Mit nem az Application Insights engedi, hogy nem jelenik meg a Docker?*

* Részletes információkat a tároló és a lemezkép teljesítményszámlálók.
* Integrálják a tároló és az alkalmazás adatokat egy irányítópulton.
* [Telemetriai adatok exportálása](app-insights-export-telemetry.md) adatbázis, a Power bi-ban vagy más irányítópult további elemzés céljából.

*Hogyan szerezhetek telemetriai magát az alkalmazásból?*

* Telepítse az Application Insights SDK az alkalmazásban. Megtudhatja, hogyan lehet a: [Java-webalkalmazások](app-insights-java-get-started.md), [Windows webalkalmazások](app-insights-asp-net.md).

## <a name="video"></a>Videó

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Következő lépések

* [Javához készült Application insights szolgáltatással](app-insights-java-get-started.md)
* [A Node.js Application insights szolgáltatással](app-insights-nodejs.md)
* [Az ASP.NET Application insights szolgáltatással](app-insights-asp-net.md)
