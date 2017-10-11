---
title: "Azure Application Insights támogatja a több összetevőt, a mikroszolgáltatások létrehozására és a tárolók |} Microsoft Docs"
description: "Figyelési alkalmazásokat, amelyek több összetevők vagy a teljesítmény- és használati szerepkörök állnak."
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/17/2017
ms.author: bwren
ms.openlocfilehash: ca1bb8ee886c4b4e69be9dd653d6a52b874e1f5a
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2017
---
# <a name="monitor-multi-component-applications-with-application-insights-preview"></a>Az Application Insights (előzetes verzió) több összetevőt alkalmazások figyelése

Figyelheti az alkalmazások, amelyek több kiszolgáló-összetevők, szerepkörök vagy szolgáltatások állnak [Azure Application Insights](app-insights-overview.md). Az összetevők és a köztük lévő viszonyt is egyetlen alkalmazás térképként jelennek meg. Egyes műveletek – több összetevő automatikus HTTP korrelációkereséssel vezethető vissza. Tároló diagnosztika integrált, és szorosan összefügg az alkalmazás telemetriai adatokat. Az alkalmazás összetevőit egyetlen Application Insights-erőforrást használjon. 

![Több összetevőt alkalmazás-hozzárendelés](./media/app-insights-monitor-multi-role-apps/app-map.png)

Használjuk "összetevő" ide: a nagyméretű alkalmazások bármely működő része. Például böngészők, ha egy futó Ügyfélkód állhat, a szokásos üzleti alkalmazások, vagy további web app, használó szolgáltatások pedig vissza záró szolgáltatások. Kiszolgáló-összetevők lehet üzemeltethető a helyszínen a felhőben, lehet, hogy az Azure webes és feldolgozói szerepkörök, vagy a tárolókhoz, például Docker vagy a Service Fabric futtathatnak. 

### <a name="sharing-a-single-application-insights-resource"></a>Egyetlen Application Insights-erőforrás megosztása 

A fő módszer telemetriai adatokat küldhet a minden összetevő ugyanahhoz az Application Insights-erőforráshoz az alkalmazásban, de a `cloud_RoleName` tulajdonság segítségével különböztetheti meg egymástól a szükséges összetevőket. Az Application Insights SDK hozzáadása a `cloud_RoleName` tulajdonság a telemetria-összetevőkkel hozható létre. Például adja hozzá az SDK-t egy webhely neve, vagy a szerepkör nevét a `cloud_RoleName` tulajdonság. Ezt az értéket egy telemetryinitializer felülbírálható. Az alkalmazás-hozzárendelés használja a `cloud_RoleName` tulajdonság a térképen összetevők azonosításához.

További információ a felülírják a `cloud_RoleName` tulajdonság lásd [tulajdonságok hozzáadása: ITelemetryInitializer](app-insights-api-filtering-sampling.md#add-properties-itelemetryinitializer).  

Néhány esetben ez nem lehet megfelelő, és célszerű külön erőforrásainak használatához összetevők különböző csoportjai számára. Például előfordulhat, hogy kell használni a különböző erőforrások felügyeleti vagy számlázási célokra. Külön erőforrásokat használó azt jelenti, hogy egyetlen alkalmazás térképen; összetevőit nem jelenik meg és hogy Ön nem tudja lekérdezni a összetevői között [Analytics](app-insights-analytics.md). Akkor is, a különböző erőforrások beállítása.

Az adott ismeret feltételezzük, ez a dokumentum több összetevő adatokat küldeni egy Application Insights-erőforrás kívánt további részében.

## <a name="configure-multi-component-applications"></a>Több összetevőt alkalmazások konfigurálása

Ahhoz, hogy egy több összetevőt alkalmazás-hozzárendelés, szüksége ezen célok eléréséhez:

* **Telepítse a legújabb előzetes** az alkalmazás minden összetevője az Application Insights-csomagot. 
* **Egyetlen Application Insights-erőforrás megosztása** az alkalmazás valamennyi összetevőnél.
* **Engedélyezze az alkalmazás több szerepkör-hozzárendelés** az előzetes verziójú funkciók a panelen.

Minden ehhez a típushoz a megfelelő módszer segítségével az alkalmazás-összetevő konfigurálása. ([ASP.NET](app-insights-asp-net.md), [Java](app-insights-java-get-started.md), [Node.js](app-insights-nodejs.md), [JavaScript](app-insights-javascript.md).)

### <a name="1-install-the-latest-pre-release-package"></a>1. Telepítse a legújabb előzetes csomagot

Frissítés, vagy a portot Insights csomagok telepítése minden egyes kiszolgáló-összetevő a projektben. Visual Studio használata:

1. Kattintson jobb gombbal a projektre, és válassza ki **NuGet-csomagok kezelése**. 
2. Válassza ki **közé tartoznak az előzetes**.
3. Ha az Application Insights csomagok frissítések jelenik meg, jelölje ki őket. 

    Ha nem tallózással keresse meg, és a megfelelő telepítéséhez:
    
    * Microsoft.ApplicationInsights.WindowsServer
    * Microsoft.ApplicationInsights.ServiceFabric - összetevők futtatásához használt Vendég végrehajtható fájlok és a Docker-tárolók a Service Fabric-alkalmazás fut
    * Microsoft.ApplicationInsights.ServiceFabric.Native - ServiceFabric alkalmazások megbízható szolgáltatásokhoz
    * Microsoft.ApplicationInsights.Kubernetes futó Kubernetes Docker-összetevők

### <a name="2-share-a-single-application-insights-resource"></a>2. Egyetlen Application Insights-erőforrás megosztása

* A Visual Studióban, kattintson jobb gombbal a projektre, és válassza ki **konfigurálja az Application Insights**, vagy **Application Insights > konfigurálása**. Az első projekt a varázsló segítségével hozzon létre egy Application Insights-erőforrást. Az ezt követő projektek esetében válassza ki az ugyanazon erőforrást.
* Ha nincs Application Insights menü, kézi konfigurálása:

   1. A [Azure-portálon](https://portal,azure.com), nyissa meg az Application Insights-erőforrást egy másik összetevő már létrehozott.
   2. A panel áttekintése, nyissa meg az Essentials legördülő fülre, és másolja a **Instrumentation kulcs.**
   3. A projektben nyissa meg az ApplicationInsights.config, és helyezze be:`<InstrumentationKey>your copied key</InstrumentationKey>`

![Instrumentation kulcsának az átmásolása a .config-fájlhoz](./media/app-insights-monitor-multi-role-apps/copy-instrumentation-key.png)


### <a name="3-enable-multi-role-application-map"></a>3. Alkalmazás több szerepkör-hozzárendelés engedélyezése

Nyissa meg az alkalmazás erőforrást az Azure-portálon. Az előzetes verziójú funkciók panelen engedélyezése *alkalmazás több szerepkör-hozzárendelés*.

### <a name="4-enable-docker-metrics-optional"></a>4. Engedélyezze a Docker-metrikák (nem kötelező) 

Ha egy összetevő egy Docker egy Windows Azure virtuális gépen futó fut, további metrikák gyűjtheti a tárolóból. Ezt a Beszúrás a [Azure diagnostics](../monitoring-and-diagnostics/azure-diagnostics.md) konfigurációs fájlban:

```
"DiagnosticMonitorConfiguration": {
        ...
        "sinks": "applicationInsights",
        "DockerSources": {
                "Stats": {
                    "enabled": true,
                    "sampleRate": "PT1M"
                }
            },
        ...
    }
    ...   
    "SinksConfig": {
        "Sink": [{
            "name": "applicationInsights",
            "ApplicationInsights": "<your instrumentation key here>"
        }]
    }
    ...
}

```

## <a name="use-cloudrolename-to-separate-components"></a>Összetevők a cloud_RoleName használatával

A `cloud_RoleName` összes telemetriai adat csatolt tulajdonság. A telemetriai származó összetevő - szerepkör vagy szolgáltatás - azonosítja. (Nincs azonos cloud_RoleInstance, amely elválasztja az azonos több kiszolgáló folyamatok vagy gépek párhuzamosan futó szerepkörök.)

A portál kiszűrhetik vagy szegmentálja a telemetriai adatok e tulajdonság használatával. Ebben a példában a hibák panel úgy szűri, hogy az előtér-webkiszolgáló szolgáltatás, a CRM API háttérrendszerből hibák kiszűrése csak adatainak megjelenítése:

![Felhő szerepkör neve szegmentált metrika diagram](./media/app-insights-monitor-multi-role-apps/cloud-role-name.png)

## <a name="trace-operations-between-components"></a>Összetevők közötti nyomkövetési műveletek

Az egyik összetevő a másikra, az egyedi művelet feldolgozása közben felé indított hívások vezethető vissza.


![Telemetria művelet megjelenítése](./media/app-insights-monitor-multi-role-apps/show-telemetry-for-operation.png)

Kattintson a telemetriai adat ehhez a művelethez kapcsolódó listáját az előtér-webkiszolgáló és a háttér-API:

![Keresés összetevői között](./media/app-insights-monitor-multi-role-apps/search-across-components.png)


## <a name="next-steps"></a>Következő lépések

* [A fejlesztői, tesztelési és éles külön telemetria](app-insights-separate-resources.md)
