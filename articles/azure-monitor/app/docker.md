---
title: Docker-alkalmazások figyelése az Azure Application Insightsban | Microsoft dokumentumok
description: Docker perf számlálók, események és kivételek jeleníthetők meg az Application Insights, valamint a telemetriai adatok a tárolóba az alkalmazásokból.
ms.topic: conceptual
ms.date: 03/14/2019
ms.openlocfilehash: 5ff3d6a2b31425a3fb6857c8c6aed45f7ef18d64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669607"
---
# <a name="monitor-docker-applications-in-application-insights-deprecated"></a>Docker-alkalmazások figyelése az Application Insightsban (elavult)

> [!NOTE]
> Ez a megoldás elavult. Ha többet szeretne megtudni a tárolófigyelésbe történő jelenlegi befektetéseinkről, javasoljuk, hogy tekintse meg [az Azure Monitort a tárolókhoz.](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-overview)

A [Docker-tárolók](https://www.docker.com/) életciklus-eseményei és teljesítményszámlálói az Application Insights ban ábrázolhatók. Telepítse az [Application Insights-lemezképet](https://hub.docker.com/r/microsoft/applicationinsights/) a gazdagép egy tárolójában, és megjeleníti a teljesítményszámlálókat a gazdagép, valamint a többi lemezképek számára.

A Docker segítségével az összes függőséggel kiegészített, könnyű tárolókban terjesztheti alkalmazásait. Minden olyan gazdagépen futnak, amely Docker-motort futtat.

Amikor futtatja az [Application Insights-rendszerképet](https://hub.docker.com/r/microsoft/applicationinsights/) a Docker-gazdagépen, az alábbi előnyöket kapja:

* Életciklus-telemetria a gazdagépen futó összes tárolóról – indítás, leállítás és így tovább.
* Teljesítményszámlálók az összes tárolóhoz. Processzor, memória, hálózati használat és egyebek.
* Ha [telepítette az Application Insights SDK Java-hoz](../../azure-monitor/app/java-get-started.md) a tárolókban futó alkalmazásokban, az alkalmazások összes telemetriai adatok további tulajdonságokat a tároló és a gazdagép azonosítására. Így például ha egy alkalmazás példányai több gazdagépen futnak, könnyen szűrheti az alkalmazás telemetriai adatait gazdagép szerint.

## <a name="set-up-your-application-insights-resource"></a>Az Application Insights-erőforrás beállítása

1. Jelentkezzen be a [Microsoft Azure Portalra,](https://azure.com) és nyissa meg az alkalmazás Application Insights-erőforrását; vagy [hozzon létre egy újat](../../azure-monitor/app/create-new-resource.md ). 
   
    *Melyik erőforrást használjam?* Ha a gazdagépen futtatott alkalmazásokat valaki más fejlesztette ki, akkor létre kell [hoznia egy új Application Insights-erőforrást.](../../azure-monitor/app/create-new-resource.md ) Itt tekintheti meg és elemezheti a telemetriai adatokat. (Válassza az "Általános" lehetőséget az alkalmazástípushoz.)
   
    Ha azonban Ön az alkalmazások fejlesztője, akkor reméljük, hogy mindegyikhez hozzáadta az [Application Insights SDK-t.](../../azure-monitor/app/java-get-started.md) Ha ezek valóban egyetlen üzleti alkalmazás összetevői, akkor konfigurálhatja az összeset, hogy telemetriát küldjön egy erőforrásnak, és ugyanazt az erőforrást fogja használni a Docker életciklusa és teljesítményadatainak megjelenítéséhez. 
   
    A harmadik forgatókönyv az, hogy a legtöbb alkalmazást fejlesztette, de külön erőforrásokat használ a telemetriai adatok megjelenítéséhez. Ebben az esetben valószínűleg egy külön erőforrást is létre szeretne hozni a Docker-adatokhoz.

2. Kattintson az **Essentials** legördülő menüre, és másolja a Műszerezési kulcsot. Ezzel megmondja az SDK-nak, hogy hová küldje a telemetriai adatokat.

Tartsa kéznél a böngészőablakot, mivel hamarosan visszatér a telemetriai adatokhoz.

## <a name="run-the-application-insights-monitor-on-your-host"></a>Az Application Insights-figyelő futtatása a gazdagépen

Most, hogy már van valahol megjeleníteni a telemetriai adatokat, beállíthatja a tárolós alkalmazást, amely összegyűjti és elküldi azt.

1. Csatlakozzon a Docker-gazdagéphez.
2. A műszerezési kulcs szerkesztése ebbe a parancsba, majd futtassa a következőparancsot:
   
   ```
   
   docker run -v /var/run/docker.sock:/docker.sock -d microsoft/applicationinsights ikey=000000-1111-2222-3333-444444444
   ```

Docker-gazdagépenként csak egy Application Insights-rendszerkép szükséges. Ha az alkalmazás több Docker-gazdagépen van telepítve, majd ismételje meg a parancsot minden állomáson.

## <a name="update-your-app"></a>Alkalmazás frissítése
Ha az alkalmazás a [Java-hoz készült Application Insights SDK-val](../../azure-monitor/app/java-get-started.md)van instrumentálva, adja `<TelemetryInitializers>` hozzá a következő sort a projekt ApplicationInsights.xml fájljába a következő elem alatt:

```xml

    <Add type="com.microsoft.applicationinsights.extensibility.initializer.docker.DockerContextInitializer"/> 
```

Ez hozzáadja a Docker-adatokat, például a tároló és a gazdagép azonosítóját az alkalmazásból küldött telemetriai elemekhez.

## <a name="view-your-telemetry"></a>A telemetria megtekintése
Lépjen vissza az Application Insights-erőforráshoz az Azure Portalon.

Kattintson a Docker csempére.

Hamarosan látni fogja a Docker-alkalmazásból érkező adatokat, különösen akkor, ha más tárolók futnak a Docker-motoron.

### <a name="docker-container-events"></a>Docker-tárolóesemények
![Példa](./media/docker/13.png)

Az egyes események vizsgálatához kattintson a [Keresés](../../azure-monitor/app/diagnostic-search.md)gombra. Keresés és szűrés a kívánt események megkereséséhez. Kattintson bármelyik eseményre, hogy részletesebben.

### <a name="exceptions-by-container-name"></a>Kivételek tárolónév szerint
![Példa](./media/docker/14.png)

### <a name="docker-context-added-to-app-telemetry"></a>Az alkalmazástelemetriai adatokhoz hozzáadott Docker-környezet
Az AI SDK-val instrumentált alkalmazásból küldött kérelemtelemetria i.

## <a name="q--a"></a>Kérdések és válaszok
*Mit ad az Application Insights, amit nem kaphat meg a Dockertől?*

* A teljesítményszámlálók részletes bontása tároló és kép szerint.
* Tároló- és alkalmazásadatok integrálása egyetlen irányítópulton.
* [Telemetriai adatok exportálása](export-telemetry.md) további elemzés céljából egy adatbázisba, A Power BI-ba vagy más irányítópultra.

*Hogyan juthatok le magából az alkalmazásból a telemetriai adatok?*

* Telepítse az Application Insights SDK-t az alkalmazásban. További információ: [Java webalkalmazások](../../azure-monitor/app/java-get-started.md), [Windows-webalkalmazások](../../azure-monitor/app/asp-net.md).

## <a name="video"></a>Videó

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>További lépések

* [Java-hoz szóló alkalmazáselemzések](../../azure-monitor/app/java-get-started.md)
* [Node.js-hez készült Application Insights](../../azure-monitor/app/nodejs.md)
* [Alkalmazáselemzési adatok ASP.NET](../../azure-monitor/app/asp-net.md)
