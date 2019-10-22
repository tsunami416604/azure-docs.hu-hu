---
title: Docker-alkalmazások figyelése az Azure Application Insightsban | Microsoft Docs
description: A Docker-teljesítményszámlálók, az események és a kivételek a Application Insightson, valamint a tároló alkalmazások telemetria is megjeleníthetők.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 03/14/2019
ms.openlocfilehash: 66a2481d25c863bbdbf4d72c4683a309918776db
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/21/2019
ms.locfileid: "72677920"
---
# <a name="monitor-docker-applications-in-application-insights-deprecated"></a>Docker-alkalmazások figyelése Application Insightsban (elavult)

> [!NOTE]
> Ez a megoldás elavult. Ha többet szeretne megtudni a tárolók monitorozásával kapcsolatos aktuális befektetésekről, javasoljuk, hogy a tárolók [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-overview)kijelentkezését ajánljuk.

A [Docker](https://www.docker.com/) -tárolókban található életciklus-események és teljesítményszámlálók Application Insights foglalhatók. Telepítse a [Application Insights](https://hub.docker.com/r/microsoft/applicationinsights/) lemezképet egy tárolóba a gazdagépen, és megjeleníti a gazdagéphez tartozó teljesítményszámlálókat, valamint a többi lemezképet is.

A Docker lehetővé teszi, hogy az alkalmazások egyszerű tárolókban legyenek kiterjesztve az összes függőséggel. Minden olyan gazdagépen futnak, amely Docker-motort futtat.

Ha a [Application Insights rendszerképet](https://hub.docker.com/r/microsoft/applicationinsights/) a Docker-gazdagépen futtatja, a következő előnyöket kapja:

* Életciklus-telemetria a gazdagépen futó összes tárolóról – indítás, leállítás és így tovább.
* Teljesítményszámlálók az összes tárolóhoz. CPU, memória, hálózati használat és sok más.
* Ha [telepítette Application INSIGHTS SDK](../../azure-monitor/app/java-get-started.md) -t a Javához a tárolókban futó alkalmazásokban, az alkalmazások összes telemetria további tulajdonságokkal fog rendelkezni a tároló és a gazdaszámítógép azonosításához. Így például, ha egynél több gazdagépen futó alkalmazás példányai vannak, egyszerűen szűrheti az alkalmazás telemetria a gazdagép alapján.

## <a name="set-up-your-application-insights-resource"></a>A Application Insights erőforrás beállítása

1. Jelentkezzen be [Microsoft Azure Portalba](https://azure.com) , és nyissa meg az alkalmazás Application Insights erőforrását; vagy [hozzon létre egy újat](../../azure-monitor/app/create-new-resource.md ). 
   
    *Melyik erőforrást használjam?* Ha a gazdagépen futó alkalmazásokat valaki más fejlesztette ki, akkor [létre kell hoznia egy új Application Insights erőforrást](../../azure-monitor/app/create-new-resource.md ). Itt tekintheti meg és elemezheti a telemetria. (Válassza az "általános" lehetőséget az alkalmazás típusához.)
   
    Ha azonban Ön fejleszti az alkalmazásokat, reméljük, hogy [hozzáadta Application INSIGHTS SDK](../../azure-monitor/app/java-get-started.md) -t mindegyikhez. Ha ezek mind egyetlen üzleti alkalmazás összetevői, akkor beállíthatja, hogy mindegyiket egy adott erőforráshoz telemetria el, és ugyanazt az erőforrást fogja használni a Docker-életciklus és a teljesítményadatok megjelenítéséhez. 
   
    A harmadik forgatókönyv, hogy a legtöbb alkalmazást fejlesztette ki, de külön erőforrásokat használ a telemetria megjelenítéséhez. Ebben az esetben valószínűleg egy külön erőforrást is létre kell hoznia a Docker-adatforráshoz.

2. Kattintson az **Essentials** legördülő listára, és másolja a kialakítási kulcsot. Ezzel közli az SDK-val, hogy hová szeretné elküldeni a telemetria.

Tartsa kéznél a böngészőablakot, mivel hamarosan visszatérhet a telemetria.

## <a name="run-the-application-insights-monitor-on-your-host"></a>A Application Insights figyelő futtatása a gazdagépen

Most, hogy elvégezte a telemetria megjelenítését, beállíthatja a begyűjtött és elküldhető tároló alkalmazást.

1. Kapcsolódjon a Docker-gazdagéphez.
2. Szerkessze a kialakítási kulcsot a parancsban, majd futtassa a következőt:
   
   ```
   
   docker run -v /var/run/docker.sock:/docker.sock -d microsoft/applicationinsights ikey=000000-1111-2222-3333-444444444
   ```

Docker-gazdagépen csak egy Application Insights rendszerkép szükséges. Ha az alkalmazás több Docker-gazdagépre van telepítve, akkor minden gazdagépen ismételje meg a parancsot.

## <a name="update-your-app"></a>Az alkalmazás frissítése
Ha az alkalmazása a [Javához készült Application INSIGHTS SDK](../../azure-monitor/app/java-get-started.md)-val van ellátva, adja hozzá a következő sort a projekt ApplicationInsights. XML fájljához a `<TelemetryInitializers>` elem alatt:

```xml

    <Add type="com.microsoft.applicationinsights.extensibility.initializer.docker.DockerContextInitializer"/> 
```

Ezzel hozzáadja a Docker-adatokat, például a tárolót és a gazdagép azonosítóját az alkalmazásból eljuttatott minden telemetria-elemhez.

## <a name="view-your-telemetry"></a>A telemetria megtekintése
Térjen vissza a Azure Portal Application Insights erőforrásához.

Kattintson a Docker csempére.

Hamarosan megtekintheti a Docker-alkalmazásból érkező adatok adatait, különösen akkor, ha a Docker-motoron fut más tárolók.

### <a name="docker-container-events"></a>Docker-tároló eseményei
![Például](./media/docker/13.png)

Az egyes események kivizsgálásához kattintson a [Keresés](../../azure-monitor/app/diagnostic-search.md)gombra. Keresés és szűrés a kívánt események megkereséséhez. További részletekért kattintson bármelyik eseményre.

### <a name="exceptions-by-container-name"></a>Kivételek a tároló neve alapján
![Például](./media/docker/14.png)

### <a name="docker-context-added-to-app-telemetry"></a>Az alkalmazás telemetria felvett Docker-környezet
Az AI SDK-val az alkalmazásból elkészített telemetria-kérések a Docker környezeti adataival gazdagodnak.

## <a name="q--a"></a>Kérdések és válaszok
*Mit jelent a Application Insights, hogy nem tudok beolvasni a Docker-ből?*

* Teljesítményszámlálók részletes részletezése tároló és rendszerkép alapján.
* Tároló-és alkalmazásadatok integrálása egyetlen irányítópulton.
* [Exportálja a telemetria](export-telemetry.md) egy adatbázis, Power bi vagy más irányítópult további elemzéséhez.

*Hogyan lekérni a telemetria az alkalmazásból?*

* Telepítse az Application Insights SDK-t az alkalmazásban. Útmutató: [Java Web Apps](../../azure-monitor/app/java-get-started.md), [Windows Web Apps](../../azure-monitor/app/asp-net.md).

## <a name="video"></a>Videó

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Következő lépések

* [Java-Application Insights](../../azure-monitor/app/java-get-started.md)
* [Application Insights a Node. js-hez](../../azure-monitor/app/nodejs.md)
* [ASP.NET Application Insights](../../azure-monitor/app/asp-net.md)
