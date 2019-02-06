---
title: Az Azure Application Insights Alkalmazástérkép |} A Microsoft Docs
description: Az alkalmazástérkép topológiák összetett alkalmazások figyelése
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 3bf37fe9-70d7-4229-98d6-4f624d256c36
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/17/2018
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: f2bd1d863a7900b50712eb23c1088c6b271befa3
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2019
ms.locfileid: "55755889"
---
# <a name="application-map-triage-distributed-applications"></a>Alkalmazás-hozzárendelés: Az elosztott alkalmazások osztályozása

Alkalmazás-hozzárendelés segít a helyszíni teljesítmény szűk vagy hiba elérési pontokhoz történő termékcsalád összes tagjára vonatkozó az elosztott alkalmazás. Alkalmazás-összetevő vagy annak függőségeit; felel meg a térképen minden csomópont KPI állapot rendelkezik, és figyelmezteti állapota. Végigkattinthat valamelyik összetevő a részletesebb diagnosztikát, például az Application Insights-eseményeket. Ha az alkalmazás Azure-szolgáltatásokat használ, is kattinthat keresztül az Azure diagnostics, például az SQL Database Advisor javaslatait.

## <a name="what-is-a-component"></a>Mi az összetevő?

Összetevők egymástól függetlenül üzembe helyezhető az elosztott és mikroszolgáltatás-alkalmazások tartoznak. A fejlesztők és műveleti csapatok kód szintű láthatóság vagy a ezeket alkalmazás-összetevők által létrehozott telemetriát hozzáféréssel rendelkezik. 

* Összetevők eltérnek "a megfigyelt" külső függőségei, például az SQL-EventHub stb., amelyek nem lehet a csapat vagy szervezet (kód vagy telemetria) eléréséhez.
* Összetevők futtassa bármely server-szerepkör-tárolót példányok száma.
* Összetevők külön Application Insights-kialakítási kulcs (akkor is, ha az előfizetések különböző) vagy egy egyetlen az Application Insights-kialakítási kulcsot a jelentéskészítés különböző szerepköröket is. Előnézeti térkép tapasztalatok mutatja az összetevők függetlenül, hogyan állíthatók be.

## <a name="composite-application-map"></a>Összetett Alkalmazástérkép

A teljes Alkalmazástopológia kapcsolódó alkalmazás-összetevők szinteken átívelő megjelenik. Összetevők különböző Application Insights-erőforrások, és a egy adott erőforrásban a különböző szerepkörrel lehet. Az alkalmazástérkép összetevők a következő függőségi végzett HTTP-hívások az Application Insights SDK telepített kiszolgálók között talál. 

Ez a tapasztalat összetevők fokozatos felderítés kezdődik. Ha először tölt be az alkalmazás-hozzárendelés, a lekérdezések ehhez az összetevőhöz kapcsolódó összetevők felderítése aktiválódnak. A bal felső sarkában található gomb frissíteni fogja az alkalmazását az összetevők számát tudásukat azok felderítése. 

A "Frissítés térképösszetevők" gombra kattintva a térkép a felderített mutasson, amíg az összes összetevő frissülnek. Az alkalmazás összetettségétől függően ez eltarthat egy kis ideig, betölteni.

Ha az összetevők összes szerepkörét egy Application Insights-erőforrást, majd a felderítési lépése, nem szükséges. Az alkalmazáshoz a kezdeti betöltés összes összetevőjének fog rendelkezni.

![Képernyőkép az alkalmazásról térkép](media/app-map/001.png)

Több száz szilárd összetevők komplex topológiáit jeleníthetik az egyik fő célja a felhasználói élményét.

Kattintson a kapcsolódó elemzések és a teljesítmény és hibák osztályozási élmény az adott összetevő nyissa meg valamelyik összetevő.

![Úszó menü](media/app-map/application-map-001.png)

### <a name="investigate-failures"></a>Hibák vizsgálata

Válassza ki **hibavizsgálat** elindíthatja a hibák panelen.

![Képernyőkép a hibák gomb vizsgálata](media/app-map/investigate-failures.png)

![Képernyőkép a hibák élmény](media/app-map/failures.png)

### <a name="investigate-performance"></a>Teljesítmény vizsgálata

Válassza ki a teljesítménnyel kapcsolatos hibák elhárítására **teljesítményének vizsgálata**.

![Képernyőkép a teljesítmény gomb vizsgálata](media/app-map/investigate-performance.png)

![Képernyőkép a teljesítmény biztosítása érdekében](media/app-map/performance.png)

### <a name="go-to-details"></a>Részletek megnyitása

Válassza ki **lépjen a részletek** böngészhet a végpontok közötti tranzakció gyakorlat, amely a hívási verem szintre kész nézetek kínálnak.

![Képernyőkép a go-Részletek gombra](media/app-map/go-to-details.png)

![Képernyőkép a végpontok közötti tranzakció részletei](media/app-map/end-to-end-transaction.png)

### <a name="view-in-analytics"></a>Megtekintés az Analyticsben

Lekérdezése, és vizsgálja meg a további alkalmazások adatait, kattintson a **megtekintés az analyticsben**.

![Elemzés gomb nézet képernyőképe](media/app-map/view-in-analytics.png)

![Képernyőkép a analytics élmény](media/app-map/analytics.png)

### <a name="alerts"></a>Riasztások

Az aktív riasztások és az alapul szolgáló szabályok aktiválását a riasztásokat kiváltó megtekintéséhez jelölje ki **riasztások**.

![Riasztások gomb képernyőképe](media/app-map/alerts.png)

![Képernyőkép a analytics élmény](media/app-map/alerts-view.png)

## <a name="set-cloudrolename"></a>Set cloud_RoleName

Alkalmazás-hozzárendelés használja a `cloud_RoleName` tulajdonság a térképen az összetevők azonosításához. Az Application Insights SDK automatikusan hozzáadja a `cloud_RoleName` tulajdonság összetevők által kibocsátott telemetriai adatokat. Például az SDK hozzáad egy webhely neve vagy a szerepkör nevét a `cloud_RoleName` tulajdonság. Azonban előfordulhatnak olyan esetek, ahol lehetséges, hogy szeretné felülbírálni az alapértelmezett érték. Cloud_RoleName bírálja felül, és mi megjelenik a az alkalmazás-hozzárendelés módosítása:

### <a name="net"></a>.NET

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace CustomInitializer.Telemetry
{
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
            {
                //set custom role name here
                telemetry.Context.Cloud.RoleName = "RoleName";
            }
        }
    }
}
```

**Az inicializáló betöltése**

Az applicationinsights.config fájlban:

```xml
    <ApplicationInsights>
      <TelemetryInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="CustomInitializer.Telemetry.MyTelemetryInitializer, CustomInitializer"/>
        ...
      </TelemetryInitializers>
    </ApplicationInsights>
```

Alternatív módszert, hogy a kódban, például a Global.aspx.cs osztályból az inicializáló hozza létre:

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
    }
```

### <a name="nodejs"></a>Node.js

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY').start();
appInsights.defaultClient.context.tags["ai.cloud.role"] = "your role name";
appInsights.defaultClient.context.tags["ai.cloud.roleInstance"] = "your role instance";
```

### <a name="alternate-method-for-nodejs"></a>Alternatív módszert a node.js-ben

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY').start();

appInsights.defaultClient.addTelemetryProcessor(envelope => {
    envelope.tags["ai.cloud.role"] = "your role name";
    envelope.tags["ai.cloud.roleInstance"] = "your role instance"
});
```

### <a name="java"></a>Java

Spring Boot az Application Insights Spring Boot starter használja, az egyetlen szükséges változás-e az egyéni nevet az alkalmazás beállítása a application.properties fájlban.

`spring.application.name=<name-of-app>`

A Spring Boot starter cloudRoleName automatikusan hozzárendeli a spring.application.name tulajdonság mezőben.

További információk Java korrelációs és konfigurálása cloudRoleName nem SpringBoot alkalmazások kivételt ez [szakasz](https://docs.microsoft.com/azure/application-insights/application-insights-correlation#role-name) a korrelációs.

### <a name="clientbrowser-side-javascript"></a>/ Böngésző ügyféloldali JavaScript

```javascript
appInsights.queue.push(() => {
appInsights.context.addTelemetryInitializer((envelope) => {
  envelope.tags["ai.cloud.role"] = "your role name";
  envelope.tags["ai.cloud.roleInstance"] = "your role instance";
});
});
```

A telemetriai adatok inicializálók cloud_RoleName tulajdonság felülbírálása kapcsolatos további információkért lásd: [tulajdonságok hozzáadása: ITelemetryInitializer](api-filtering-sampling.md#add-properties-itelemetryinitializer).

## <a name="troubleshooting"></a>Hibaelhárítás

Ha problémába ütközik az első alkalmazás-hozzárendelés a várt módon működik, próbálja ki ezeket a lépéseket:

1. Győződjön meg róla, hogy hivatalosan támogatott SDK-t használ. Előfordulhat, hogy a nem támogatott/közösségi SDK-k nem támogatják a korrelációt.

    Ebben [cikk](https://docs.microsoft.com/azure/application-insights/app-insights-platforms) támogatott SDK-k listáját.

2. Az összes összetevő frissítését a legújabb SDK-t.

3. Az Azure Functions használata C#frissítsen a [funkciók V2](https://docs.microsoft.com/azure/azure-functions/functions-versions).

4. Győződjön meg róla [cloud_RoleName](app-map.md#set-cloudrolename) megfelelően van konfigurálva.

5. Ha valamelyik függőség hiányzik, ellenőrizze, hogy az [automatikusan gyűjtött függőségek](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies) listájában szerepel-e. Ha nem, manuálisan úgy is nyomon követheti egy [függőségek nyomon követése hívással](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency).

## <a name="portal-feedback"></a>Portál visszajelzés
Visszajelzést, használja a visszajelzés lehetőség.

![MapLink-1 image](./media/app-map/14-updated.png)

## <a name="next-steps"></a>További lépések

* [Korrelációs ismertetése](https://docs.microsoft.com/azure/application-insights/application-insights-correlation)
