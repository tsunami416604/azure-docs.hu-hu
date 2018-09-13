---
title: Több összetevői, mikroszolgáltatások és tárolók támogatása az Azure Application Insights |} A Microsoft Docs
description: Figyelési alkalmazásokat, amelyek több összetevők vagy a teljesítmény és használat szerepkörök állnak.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 05/17/2017
ms.author: mbullwin
ms.openlocfilehash: 191913500daf7f1ab20f92c7e951f58598d5d14e
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "35645240"
---
# <a name="monitor-multi-component-applications-with-application-insights-preview"></a>Az Application Insights (előzetes verzió) több összetevőből álló alkalmazások figyelése

Követheti az alkalmazások, amelyek több kiszolgáló-összetevők, szerepkörök vagy szolgáltatások állnak [Azure Application Insights](app-insights-overview.md). Az összetevők és a közöttük a kapcsolatok állapotát az egyetlen alkalmazás-hozzárendelés jelennek meg. Egyes műveletek automatikus HTTP korrelációkereséssel több összetevőből keresztül is nyomon követése. Tárolódiagnosztika is integrálva és Alkalmazáshasználati is vonatkozhatnak. Az alkalmazás összetevőit egyetlen Application Insights-erőforrást használja. 

![Több összetevőből álló alkalmazás-hozzárendelés](./media/app-insights-monitor-multi-role-apps/app-map.png)

Használjuk "összetevő" ide: minden olyan nagyméretű alkalmazások működőképes része. Például egy tipikus üzleti alkalmazás futó böngészők, az egyik legtöbbet beszélgető Ügyfélkód állhat, vagy több alkalmazás webszolgáltatásokhoz, amelyek a szerződéskötés után használhatják vissza háttérszolgáltatáshoz. Kiszolgáló-összetevők lehetnek a felhőben, egy üzemeltethető a helyszínen vagy lehet, hogy az Azure webes és feldolgozói szerepkörök, vagy futhat például a Dockert vagy a Service Fabric-tárolókban. 

### <a name="sharing-a-single-application-insights-resource"></a>Egy Application Insights-erőforrás megosztása 

A fő módszer küldött telemetriai adatokra minden összetevő ugyanarra az Application Insights erőforrásra az alkalmazásban, de a `cloud_RoleName` összetevők szükség esetén megkülönböztethetők tulajdonságot. Az Application Insights SDK hozzáadása a `cloud_RoleName` tulajdonság, a telemetriai adatok összetevők gridre bocsáthatja ki. Például az SDK hozzáad egy webhely nevét, vagy a szerepkör nevét a `cloud_RoleName` tulajdonság. Ezt az értéket a telemetryinitializer felül lehet bírálni. Az Alkalmazástérkép használja a `cloud_RoleName` tulajdonság a térképen az összetevők azonosításához.

További információ a felülbírálás a `cloud_RoleName` tulajdonság lásd [tulajdonságok hozzáadása: ITelemetryInitializer](app-insights-api-filtering-sampling.md#add-properties-itelemetryinitializer).  

Bizonyos esetekben ez nem lehet megfelelő, és előfordulhat, hogy inkább a különböző erőforrások különböző csoportjaihoz, összetevők. Szüksége lehet például a különböző erőforrások felügyeleti vagy számlázási célból. Külön erőforrásokat használó azt jelenti, hogy egyetlen alkalmazás-hozzárendelés; megjelenített összes összetevő nem jelenik meg és hogy Ön nem lehet lekérdezni az összetevőben [Analytics](app-insights-analytics.md). Akkor is, a különböző erőforrások beállítása.

Az adott ismeret fog feltételezzük, hogy ez a dokumentum több összetevőből származó adatokat egy Application Insights-erőforrást küldeni kívánt további részében található.

## <a name="configure-multi-component-applications"></a>Több összetevőből álló alkalmazások konfigurálása

Több összetevőből álló alkalmazástérkép lekéréséhez szüksége ezen célok eléréséhez:

* **Telepítse a legújabb kiadás előtti** az alkalmazás minden összetevője az Application Insights-csomagot. 
* **Egy Application Insights-erőforrás megosztása** az alkalmazás valamennyi összetevőnél.
* **Összetett Alkalmazástérkép engedélyezése** az előzetes verziók panelen.

Konfigurálja az egyes összetevői által az alkalmazás a megfelelő módszer használatával az ehhez a típushoz. ([ASP.NET](app-insights-asp-net.md), [Java](app-insights-java-get-started.md), [Node.js](app-insights-nodejs.md), [JavaScript](app-insights-javascript.md).)

### <a name="1-install-the-latest-pre-release-package"></a>1. Telepítse a legújabb kiadás előtti csomagot

Frissítse, vagy telepítse az Application Insights-csomagokat a projektben egyes kiszolgáló-összetevő. A Visual Studio használata:

1. Kattintson jobb gombbal a projektre, és válassza ki **NuGet-csomagok kezelése**. 
2. Válassza ki **előzetes verzió**.
3. Ha az Application Insights csomagok frissítések jelenik meg, válassza ki őket. 

    Ellenkező esetben keresése, és a megfelelő csomag telepítése:
    
    * Microsoft.ApplicationInsights.WindowsServer
    * Microsoft.ApplicationInsights.ServiceFabric - vendégrendszer futtatható fájljait és a Service Fabric-alkalmazást futtató Docker-tárolókban futó összetevők számára
    * A Microsoft.ApplicationInsights.ServiceFabric.Native - reliable Services ServiceFabric-alkalmazásokban
    * A Dockerben futnak Kubernetes-összetevők Microsoft.ApplicationInsights.Kubernetes

### <a name="2-share-a-single-application-insights-resource"></a>2. Egy Application Insights-erőforrás megosztása

* A Visual Studióban kattintson jobb gombbal a projektre, és válassza ki **Application Insights konfigurálása**, vagy **Application Insights > Konfigurálás**. Az első projekthez a varázsló segítségével hozzon létre egy Application Insights-erőforrást. Az ezt követő projektek esetében válassza ki ugyanazt az erőforrást.
* Ha nem az Application Insights menü, kézi konfigurálás:

   1. A [az Azure portal](https://portal,azure.com), nyisson meg egy másik összetevő már létrehozott Application Insights-erőforrást.
   2. Az áttekintő panelje, nyissa meg az Essentials legördülő fülre, és másolja a **kialakítási kulcsot.**
   3. A projektben nyissa meg az applicationinsights.config fájlt, és Beszúrás: `<InstrumentationKey>your copied key</InstrumentationKey>`

![A .config fájlt a kialakítási kulcs másolása](./media/app-insights-monitor-multi-role-apps/copy-instrumentation-key.png)


### <a name="3-enable-composite-application-map"></a>3. Összetett Alkalmazástérkép engedélyezése

Az Azure Portalon nyissa meg az erőforrást az alkalmazáshoz. KONFIGURÁLÁSA alárendelt fejléc alatt kattintson az előzetes verziók megnyitása az előzetes verziók panelen. Engedélyezze az előzetes verziók panelen *összetett Alkalmazástérkép*.

### <a name="4-enable-docker-metrics-optional"></a>4. Engedélyezze a Docker-mérőszámok (nem kötelező) 

Ha egy összetevő egy Docker-beli Windows virtuális gépen üzemeltetett fut, további metrikák is összegyűjtheti a tárolóból. Illessze be a a [az Azure diagnostics](../monitoring-and-diagnostics/azure-diagnostics.md) konfigurációs fájlban:

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

A `cloud_RoleName` összes telemetriai adat csatolt tulajdonság. Az összetevő - a szerepkör vagy szolgáltatás - telemetria származó azonosítja. (Ez még nem ugyanaz, mint cloud_RoleInstance, amely elkülöníti az azonos több kiszolgáló folyamatok vagy gépek párhuzamosan futó szerepkörök.)

A portálon szűrheti és szegmentálhatja a telemetriai adatok a tulajdonság használatával. Ebben a példában a hibák panelen megjelennek az csak a webes előtér-szolgáltatás, szűri ki a hibákat a CRM-API-háttérrendszerből szűrt:

![Felhőalapú szerepkör neve alapján szegmentált metrikadiagram](./media/app-insights-monitor-multi-role-apps/cloud-role-name.png)

## <a name="trace-operations-between-components"></a>Összetevők közötti műveletek nyomon követése

A másikba, egy egyéni művelet feldolgozása közben végzett hívásokat is nyomon követheti az egyes összetevők.


![Telemetria megjelenítése a következőhöz művelet](./media/app-insights-monitor-multi-role-apps/show-telemetry-for-operation.png)

Kattintson végig a telemetriai adatok ehhez a művelethez kapcsolódó listáját az előtér-webkiszolgáló és a háttérrendszeri API:

![Keresés a különböző összetevők](./media/app-insights-monitor-multi-role-apps/search-across-components.png)


## <a name="next-steps"></a>További lépések

* [A fejlesztési, tesztelési és éles külön telemetriáját](app-insights-separate-resources.md)
