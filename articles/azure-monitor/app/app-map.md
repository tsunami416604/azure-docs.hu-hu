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
ms.date: 03/15/2019
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: 89aa5006882680205816e7e5d1e7e55b9c4b2ab0
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59678539"
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

Ez a tapasztalat összetevők fokozatos felderítés kezdődik. Ha először tölt be az alkalmazás-hozzárendelés, lekérdezések akkor aktiválódik, ehhez az összetevőhöz kapcsolódó összetevők felderítése. A bal felső sarkában található gomb frissíteni fogja az alkalmazását az összetevők számát tudásukat azok felderítése. 

A "Frissítés térképösszetevők" gombra kattintva a térkép a felderített mutasson, amíg az összes összetevő frissülnek. Az alkalmazás összetettségétől függően ez eltarthat egy kis ideig, betölteni.

Ha az összetevők összes szerepkörét egy Application Insights-erőforrást, majd a felderítési lépése, nem szükséges. Az alkalmazáshoz a kezdeti betöltés összes összetevőjének fog rendelkezni.

![Képernyőkép az alkalmazásról térkép](media/app-map/app-map-001.png)

Több száz szilárd összetevők komplex topológiáit jeleníthetik az egyik fő célja a felhasználói élményét.

Kattintson a kapcsolódó elemzések és a teljesítmény és hibák osztályozási élmény az adott összetevő nyissa meg valamelyik összetevő.

![Úszó menü](media/app-map/application-map-002.png)

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

## <a name="set-cloud-role-name"></a>Felhőalapú szerepkör neve

Alkalmazás-hozzárendelés használja a **felhőalapú szerepkör neve** tulajdonság a térképen az összetevők azonosításához. Az Application Insights SDK automatikusan hozzáadja a felhőalapú szerepkör neve tulajdonság összetevők által kibocsátott telemetriai adatokat. Például az SDK adnak hozzá egy webhely vagy szolgáltatás szerepkör nevében az felhőalapú szerepkör neve tulajdonság. Azonban előfordulhatnak olyan esetek, ahol lehetséges, hogy szeretné felülbírálni az alapértelmezett érték. Felhőalapú szerepkör neve és milyen megjelenik a az alkalmazás-hozzárendelés módosítása:

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
                telemetry.Context.Cloud.RoleName = "Custom RoleName";
                telemetry.Context.Cloud.RoleInstance = "Custom RoleInstance"
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

A Spring Boot starter felhőalapú szerepkör neve automatikusan hozzárendeli a spring.application.name tulajdonság mezőben.

További információk a Java összefüggések keresésére és felhőalapú szerepkör konfigurálása nevét nem SpringBoot alkalmazások kivételt ez [szakasz](https://docs.microsoft.com/azure/application-insights/application-insights-correlation#role-name) a korrelációs.

### <a name="clientbrowser-side-javascript"></a>/ Böngésző ügyféloldali JavaScript

```javascript
appInsights.queue.push(() => {
appInsights.context.addTelemetryInitializer((envelope) => {
  envelope.tags["ai.cloud.role"] = "your role name";
  envelope.tags["ai.cloud.roleInstance"] = "your role instance";
});
});
```

### <a name="understanding-cloud-role-name-within-the-context-of-the-application-map"></a>Felhőalapú szerepkör neve az Alkalmazástérkép kontextusában ismertetése

Hogyan kell vennie, amennyire **felhőalapú szerepkör neve**, és tekintse meg az alkalmazás-hozzárendelés, amely több felhőalapú szerepkörök nevében található rendelkezik hasznos lehet:

![Képernyőkép az alkalmazásról térkép](media/app-map/cloud-rolename.png)

A fenti zöld mezőben szereplő nevek mindegyike Alkalmazástérkép értékek felhőalapú szerepkör neve az adott elosztott alkalmazás különböző aspektusainak a. Így az alkalmazás a szerepkörök állnak: `Authentication`, `acmefrontend`, `Inventory Management`, amely egy `Payment Processing Worker Role`. 

Esetén az alkalmazás egyes adott felhőalapú szerepkörök nevében is jelenti egy másik egyedi Application Insights-erőforrást a saját kialakítási kulcs. Mivel ez az alkalmazás tulajdonosa e négy különböző Application Insights-erőforrások mindegyike hozzáféréssel rendelkezik, alkalmazás-hozzárendelés el tudja összefűzheti a térképet az alapul szolgáló kapcsolatot.

Az a [hivatalos definíciók](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/39a5ef23d834777eefdd72149de705a016eb06b0/Schema/PublicSchema/ContextTagKeys.bond#L93):

```
   [Description("Name of the role the application is a part of. Maps directly to the role name in azure.")]
    [MaxStringLength("256")]
    705: string      CloudRole = "ai.cloud.role";
    
    [Description("Name of the instance where the application is running. Computer name for on-premises, instance name for Azure.")]
    [MaxStringLength("256")]
    715: string      CloudRoleInstance = "ai.cloud.roleInstance";
```

Azt is megteheti **felhőalapú szerepkörpéldány** esetekben hasznos lehet, ahol **felhőalapú szerepkör neve** jelzi, hogy a probléma valahol a webes előtér-, de előfordulhat, hogy a webes előtér-között fut több elosztott terhelésű kiszolgálókat úgy tudnak részletes Kusto-lekérdezések útján mélyebb rétegben, és ha a probléma van negatív hatással az összes webes előtér-kiszolgálók/példány, vagy csak egy rendkívül fontos lehet.

Egy olyan forgatókönyvet, ahol előfordulhat, hogy szeretné felülbírálni felhőalapú szerepkörpéldány értéke lehet, hogy az alkalmazás fut-e a tárolóalapú környezetben, csak az adott kiszolgálóhoz, hogy nem feltétlenül elegendő információt egy adott probléma keresse meg.

A felhőalapú szerepkör neve tulajdonság felülbírálása a telemetriai adatok inicializálók kapcsolatos további információkért lásd: [tulajdonságok hozzáadása: ITelemetryInitializer](api-filtering-sampling.md#add-properties-itelemetryinitializer).

## <a name="troubleshooting"></a>Hibaelhárítás

Ha problémába ütközik az első alkalmazás-hozzárendelés a várt módon működik, próbálja ki ezeket a lépéseket:

### <a name="general"></a>Általános kérdések

1. Győződjön meg róla, hogy hivatalosan támogatott SDK-t használ. Előfordulhat, hogy a nem támogatott/közösségi SDK-k nem támogatják a korrelációt.

    Ebben [cikk](https://docs.microsoft.com/azure/application-insights/app-insights-platforms) támogatott SDK-k listáját.

2. Az összes összetevő frissítését a legújabb SDK-t.

3. Az Azure Functions használata C#frissítsen a [funkciók V2](https://docs.microsoft.com/azure/azure-functions/functions-versions).

4. Győződjön meg róla [felhőalapú szerepkör neve](#set-cloud-role-name) megfelelően van konfigurálva.

5. Ha valamelyik függőség hiányzik, ellenőrizze, hogy az [automatikusan gyűjtött függőségek](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies) listájában szerepel-e. Ha nem, manuálisan úgy is nyomon követheti egy [függőségek nyomon követése hívással](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency).

### <a name="too-many-nodes-on-the-map"></a>Túl sok csomópontot a térképen

Alkalmazás-hozzárendelés minden egyedi felhőalapú szerepkör neve szerepel a kérelmek telemetriai adatai egy alkalmazás csomópont és a egy függőségi csomópont típusa, a cél és a függőségek telemetriáját a felhőalapú szerepkör neve minden egyes egyedi kombináció hoz létre. Ha több mint 10 000 csomópontok szerepelnek a telemetriai adatok, alkalmazás-hozzárendelés nem sikerült beolvasni a csomópontok és a hivatkozásokat, így a térkép nem lesz teljes. Ha ez történik, egy figyelmeztető üzenet jelenik meg a térkép megtekintésekor.

Emellett Alkalmazástérkép csak a megjelenített egyszerre legfeljebb 1000 külön nem csoportosított csomópontok támogatja. Alkalmazástérkép csökkenti, amelyek azonos típusú és hívóit függőségek csoportosításával vizuális összetettségének, de ha túl sok egyedi felhőalapú szerepkör neve vagy túl sok függőségi típusnál a telemetriai adatok, a csoportosítás nem elegendők a, és a leképezés nem tudja jelennek meg.

A probléma megoldásához lesz szüksége a kialakítási megfelelően beállítani a felhőalapú szerepkör neve, függőség típusa és a függőségi cél mezők módosításához.

* Függőségi cél felel a logikai neve, a függőség. Sok esetben megegyezik a kiszolgáló vagy a függőség, erőforrás neve. Például a HTTP-függőségek esetén Időintervallumként az állomásnevet. Nem tartalmazhat egyedi azonosítóját vagy a paraméterek, amelyeket egy kérelem egy másikra módosítani.

* Függőség típusa függőséget logikai típusú felel. Például a HTTP-, SQL- vagy Azure Blob olyan jellemző függőségi típusnál. Egyedi azonosítók nem kell tartalmaznia.

* Felhőalapú szerepkör neve célját ismerteti a [fenti szakasz](https://docs.microsoft.com/azure/azure-monitor/app/app-map#set-cloud-role-name).

## <a name="portal-feedback"></a>Portál visszajelzés

Visszajelzést, használja a visszajelzés lehetőség.

![MapLink-1 image](./media/app-map/14-updated.png)

## <a name="next-steps"></a>További lépések

* [Korrelációs ismertetése](https://docs.microsoft.com/azure/application-insights/application-insights-correlation)