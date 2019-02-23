---
title: Az Azure Application Insights Docker-alkalmazások monitorozása |} A Microsoft Docs
description: Docker gyűjthető teljesítményszámlálókhoz, eseményekről és kivételeket is jelenjenek meg, az Application Insights, valamint a telemetriát a tárolóalapú alkalmazások.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 27a3083d-d67f-4a07-8f3c-4edb65a0a685
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 11/20/2018
ms.author: mbullwin
ms.openlocfilehash: 0c31d895c453a8272c337b78f01dc8342a4d0765
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2019
ms.locfileid: "56672508"
---
# <a name="monitor-docker-applications-in-application-insights"></a>Docker-alkalmazások figyelése az Application insights szolgáltatásban

Életciklus-események és a teljesítmény származó teljesítményszámlálók [Docker](https://www.docker.com/) tárolók ábrázolható is kell Application Insights. Telepítse a [Application Insights](https://hub.docker.com/r/microsoft/applicationinsights/) lemezkép a gazdagép és a egy tárolóban teljesítményszámlálók megjeleníti a gazdagép, valamint a további rendszerképeket.

A docker használatával akkor terjesztheti az alkalmazásokat a kisméretű tárolók teljes függőségeit. Kiszámíthatja, minden olyan gazdagépen, amelyen a Docker-motor.

Futtatásakor a [Application Insights-lemezkép](https://hub.docker.com/r/microsoft/applicationinsights/) a Docker-gazdagép, ezeket az előnyöket kap:

* Kapcsolatos futó összes tároló életciklus-telemetria a gazdagép - indítása, leállítása, és így tovább.
* Teljesítményszámlálók a az összes tárolót. CPU, memória, hálózati használati és egyéb.
* Ha Ön [Javához készült Application Insights SDK telepítése](../../azure-monitor/app/java-get-started.md) az alkalmazásokat, a tárolókban futó, ezek az alkalmazások összes telemetriai adat azonosítása a tároló és a gazdagépcsoport gép további tulajdonságokat is rendelkezik. Így például ha egynél több gazdagépen futó példánya, könnyen szűrheti az alkalmazás telemetriai adatainak a gazdagép által.

> [!NOTE]
> Ez a megoldás elavult. További információ a meglévő befektetéseit,-tárolók monitorozása a javasoljuk a kivételkor [-tárolókhoz az Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-overview).

## <a name="set-up-your-application-insights-resource"></a>Az Application Insights-erőforrás beállítása

1. Jelentkezzen be a [Microsoft Azure-portálon](https://azure.com) , és nyissa meg az alkalmazás; Application Insights-erőforrás vagy [hozzon létre egy új](../../azure-monitor/app/create-new-resource.md ). 
   
    *Melyik erőforrást kell használnom?* Ha valaki más által kifejlesztett az alkalmazásokat, amelyek a gazdagép futtat, akkor kell [hozzon létre egy új Application Insights-erőforrást](../../azure-monitor/app/create-new-resource.md ). Ez a, melyen megtekintheti és elemezheti a telemetriát. (Válasszon "Általános" az alkalmazástípushoz.)
   
    De ha az alkalmazások a fejlesztők, akkor Reméljük, hogy [hozzáadja az Application Insights SDK](../../azure-monitor/app/java-get-started.md) hozzájuk. Ha az összes valóban egyetlen üzleti alkalmazások összetevői, akkor előfordulhat, hogy konfigurálja azokat, hogy telemetriát küldjön egy erőforrás, és ugyanazt az erőforrást fog használni a Docker-életciklus-kezelés és a teljesítmény adatok megjelenítése. 
   
    Harmadik forgatókönyv, hogy az alkalmazások többségét fejlesztett, de külön erőforrásokat használ a telemetriai adatok megjelenítéséhez. Ebben az esetben, valószínűleg is szeretne létrehozni a Docker-adatok külön erőforrást.

2. Kattintson a **Essentials** listából, és másolja a kialakítási kulcsot. Ezzel, hogy az SDK-t, hova küldhetők a telemetriai adatokat.

Tartsa kényelmes, a böngészőablakot, mivel meg fogja térjen vissza a, hogy hamarosan tekintse meg a telemetriai adatok.

## <a name="run-the-application-insights-monitor-on-your-host"></a>A gazdagépen az Application Insights-figyelőjének futtatása

Most, hogy valahol a telemetriai adatok megjelenítéséhez konfiguráltunk, beállíthat a tárolóalapú alkalmazást, amely összegyűjti és elküldi azt.

1. Csatlakozhat a Docker-gazdagép.
2. A kialakítási kulcsot szerkesztése be ezt a parancsot, és futtassa azt:
   
   ```
   
   docker run -v /var/run/docker.sock:/docker.sock -d microsoft/applicationinsights ikey=000000-1111-2222-3333-444444444
   ```

Csak egy Application Insights-rendszerképet egy Docker-gazdagép megadása kötelező. Ha több Docker gazdagépen helyezi üzembe az alkalmazást, majd ismételje meg a parancsot minden gazdagépen.

## <a name="update-your-app"></a>Az alkalmazás frissítése
Ha az alkalmazás olyan van felszerelve a [Javához készült Application Insights SDK](../../azure-monitor/app/java-get-started.md), alatt adja hozzá a következő sort az ApplicationInsights.xml fájl a projektben a `<TelemetryInitializers>` elem:

```xml

    <Add type="com.microsoft.applicationinsights.extensibility.initializer.docker.DockerContextInitializer"/> 
```

Minden az alkalmazásból küldött telemetriai elem hozzáadása a Docker adatokat, például a tároló és az állomás azonosítóját.

## <a name="view-your-telemetry"></a>A telemetria megtekintése
Lépjen vissza az Application Insights-erőforrást az Azure Portalon.

Kattintson végig a Docker-csempét.

A Docker alkalmazásból érkező adatok hamarosan megjelenik, különösen akkor, ha más tárolókat a Docker-motor futó rendelkezik.

### <a name="docker-container-events"></a>Docker-tároló események
![Példa](./media/docker/13.png)

Egyéni események vizsgálata, kattintson a [keresési](../../azure-monitor/app/diagnostic-search.md). Keresés és szűrés a kívánt események kereséséhez. Kattintson valamely eseményre további részleteket.

### <a name="exceptions-by-container-name"></a>Kivételek a tároló neve
![Példa](./media/docker/14.png)

### <a name="docker-context-added-to-app-telemetry"></a>Hozzáadott alkalmazás telemetriai adatainak docker-környezetben
Az SDK-t, mesterséges Intelligencia révén utasított alkalmazás által küldött kérelmek telemetriai adatai renderelésre van Docker környezeti információkat.

## <a name="q--a"></a>Kérdések és válaszok
*Mi nem Application Insights engedi, hogy nem jelenik meg a Dockert?*

* Tároló- és képfájlok alapján teljesítményszámlálók részletes áttekintését.
* Integrálja a tároló és az alkalmazás adatokat egyetlen irányítópulton.
* [Telemetria exportálása](export-telemetry.md) adatbázis, a Power bi-ban vagy más olyan irányítópultot további elemzés céljából.

*Hogyan kaphatok telemetriai magát az alkalmazásból?*

* Az Application Insights SDK telepítése az alkalmazásban. Ismerje meg, hogyan számára: [Java-webalkalmazások](../../azure-monitor/app/java-get-started.md), [Windows webes alkalmazások](../../azure-monitor/app/asp-net.md).

## <a name="video"></a>Videó

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>További lépések

* [Application Insights for Java](../../azure-monitor/app/java-get-started.md)
* [NODE.js-hez készült Application Insights](../../azure-monitor/app/nodejs.md)
* [Az Application Insights az ASP.NET-hez](../../azure-monitor/app/asp-net.md)
