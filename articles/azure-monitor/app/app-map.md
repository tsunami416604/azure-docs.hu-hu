---
title: Alkalmazástérkép az Azure Application Insightsban | Microsoft dokumentumok
description: Összetett alkalmazás-topológiák figyelése az alkalmazástérképpel
ms.topic: conceptual
ms.date: 03/15/2019
ms.reviewer: sdash
ms.openlocfilehash: 7c5c9173704535b1e34ffde5867bd512e3e02ed8
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80989527"
---
# <a name="application-map-triage-distributed-applications"></a>Alkalmazás térkép: Triage elosztott alkalmazások

Az Alkalmazástérkép segítségével észlelheti a teljesítménybeli szűk keresztmetszeteket vagy a meghibásodási pontokat az elosztott alkalmazás összes összetevőjében. A térképminden csomópontja egy alkalmazás-összetevőt vagy annak függőségeit jelöli; és egészségügyi KPI-vel és riasztási állapottal rendelkezik. Bármelyik összetevőről átkattinthat a részletesebb diagnosztika, például az Application Insights-események között. Ha az alkalmazás Azure-szolgáltatásokat használ, átkattinthat az Azure-diagnosztikára is, például az SQL Database Advisor-javaslatokra.

## <a name="what-is-a-component"></a>Mi az összetevő?

Az összetevők egymástól függetlenül telepíthető részei az elosztott/mikroszolgáltatási alkalmazásnak. A fejlesztők és a műveleti csapatok kódszintű láthatósággal vagy hozzáféréssel rendelkeznek az alkalmazás-összetevők által létrehozott telemetriai adatokhoz. 

* Az összetevők eltérnek a "megfigyelt" külső függőségektől, például az SQL-től, az EventHub-tól stb., amelyekhez a csapat/szervezet nem férhet hozzá (kód vagy telemetria).
* Az összetevők tetszőleges számú kiszolgáló-/szerepkör-/tárolópéldányon futnak.
* Az összetevők lehetnek különálló Application Insights instrumentation kulcsok (akkor is, ha az előfizetések eltérőek) vagy különböző szerepkörök jelentési egyetlen Application Insights instrumentation kulcs. Az előnézeti térkép élménye az összetevőket a beállításuktól függetlenül jeleníti meg.

## <a name="composite-application-map"></a>Összetett alkalmazástérkép

A teljes alkalmazástopológiát a kapcsolódó alkalmazás-összetevők több szintjén is megtekintheti. Az összetevők lehetnek különböző Application Insights-erőforrások, vagy egy erőforrás különböző szerepkörei. Az alkalmazástérkép az összetevőket az Application Insights SDK-val rendelkező kiszolgálók közötti HTTP-függőségi hívások követésével keresi meg. 

Ez a tapasztalat az összetevők fokozatos felfedezésével kezdődik. Az alkalmazástérkép első betöltésekén a lekérdezések egy sora jelenik meg az összetevőhöz kapcsolódó összetevők felderítéséhez. A bal felső sarokban lévő gomb frissül az alkalmazás összetevőinek számával, amint azok felvannak derítve. 

A "Térkép-összetevők frissítése" gombra kattintva a térkép frissül az addig felderített összes összetevővel. Az alkalmazás összetettségétől függően ez egy percet is igénybe vehet.

Ha az összes összetevő szerepkörök egyetlen Application Insights-erőforrás, majd ez a felderítési lépés nem szükséges. Az ilyen alkalmazás kezdeti terhelése minden összetevővel rendelkezik.

![Alkalmazástérkép képernyőkép](media/app-map/app-map-001.png)

Az egyik legfontosabb célja ennek a tapasztalatnak az, hogy képes legyen elképzelni összetett topológiák több száz alkatrészek.

Kattintson bármelyik összetevőre a kapcsolódó elemzések megtekintéséhez, és folytassa az adott összetevő teljesítmény- és hibaosztályozási élményét.

![Flyout](media/app-map/application-map-002.png)

### <a name="investigate-failures"></a>Hibák vizsgálata

Válassza **ki a hibák kivizsgálását** a hibák ablaktábla elindításához.

![Képernyőkép a hibák kivizsgálásáról gomb](media/app-map/investigate-failures.png)

![Képernyőkép a hibák élményéről](media/app-map/failures.png)

### <a name="investigate-performance"></a>Teljesítmény vizsgálata

A teljesítménnyel kapcsolatos problémák elhárításához válassza **a Teljesítmény vizsgálata**lehetőséget.

![Képernyőkép a teljesítmény vizsgálatáról gomb](media/app-map/investigate-performance.png)

![Képernyőkép a teljesítményélményről](media/app-map/performance.png)

### <a name="go-to-details"></a>Részletek megugrása

Válassza **az ugrás a részletekre** lehetőséget a végpontok között folytatott tranzakciós élmény feltárásához, amely a hívási verem szintjéig kínál megtekintéseket.

![Képernyőkép a részletekhez gombról](media/app-map/go-to-details.png)

![Képernyőkép a végpontok között lebonyolított tranzakciók részleteiről](media/app-map/end-to-end-transaction.png)

### <a name="view-logs-analytics"></a>Naplók megtekintése (Analytics)

Az alkalmazások adatainak további lekérdezéséhez és vizsgálatához kattintson **a Nézet elemre a Naplók (Analytics) területen.**

![A nézet az Analytics-ben gomb képernyőképe](media/app-map/view-logs.png)

![Az elemzési élmény képernyőképe. A kérelem átlagos válaszidőtartamát összegző vonaldiagram az elmúlt 12 órában.](media/app-map/log-analytics.png)

### <a name="alerts"></a>Riasztások

Az aktív riasztások és az alapul szolgáló szabályok megtekintéséhez válassza a **riasztások at.**

![A riasztások képernyőképe gomb](media/app-map/alerts.png)

![Képernyőkép az elemzési élményről](media/app-map/alerts-view.png)

## <a name="set-cloud-role-name"></a>Felhőbeli szerepkör nevének beállítása

Az Application Map a **felhőszerepkör névtulajdonságát** használja a térképen található összetevők azonosítására. Az Application Insights SDK automatikusan hozzáadja a felhőbeli szerepkör név tulajdonságát az összetevők által kibocsátott telemetriai adatokhoz. Az SDK például hozzáad egy webhely nevet vagy szolgáltatásszerepkör-nevet a felhőbeli szerepkör névtulajdonságához. Vannak azonban olyan esetek, amikor felül szeretné bírni az alapértelmezett értéket. A felhőbeli szerepkör nevének felülbírálása és az alkalmazástérképen megjelenő értékek módosítása:

# <a name="netnetcore"></a>[.NET/.NetCore](#tab/net)

**Írjon egyéni TelemettryInitializer az alábbiak szerint.**

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
                telemetry.Context.Cloud.RoleInstance = "Custom RoleInstance";
            }
        }
    }
}
```

**ASP.NET alkalmazások: Initializer betöltése az aktív TelemetryConfiguration**

Az ApplicationInsights.config fájlban:

```xml
    <ApplicationInsights>
      <TelemetryInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="CustomInitializer.Telemetry.MyTelemetryInitializer, CustomInitializer"/>
        ...
      </TelemetryInitializers>
    </ApplicationInsights>
```

A webalkalmazások ASP.NET másik módja az inicializáló kódban való példányosítása, például Global.aspx.cs:

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
    }
```

> [!NOTE]
> Initializer hozzáadása `ApplicationInsights.config` használatával `TelemetryConfiguration.Active` vagy használatával nem érvényes ASP.NET Core alkalmazások. 

**core alkalmazások ASP.NET: Initializer betöltése a TelemettryConfiguration**

Az [ASP.NET core](asp-net-core.md#adding-telemetryinitializers) alkalmazások `TelemetryInitializer` hozzáadása egy új történik hozzáadásával, hogy a függőségi injektálás tároló, az alábbiak szerint. Ez történik `ConfigureServices` a módszer `Startup.cs` az osztályban.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```

# <a name="java"></a>[Java](#tab/java)

**Java ügynök**

A [Java agent 3.0](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent) esetében a felhőbeli szerepkör neve a következőképpen van beállítva:

```json
{
  "instrumentationSettings": {
    "preview": {
      "roleName": "my cloud role name"
    }
  }
}
```

A felhőbeli szerepkör nevét a környezeti ```APPLICATIONINSIGHTS_ROLE_NAME```változó használatával is beállíthatja.

**Java SDK**

Ha az SDK-t használja, kezdve az Application Insights Java SDK 2.5.0-val, megadhatja a felhőszerepkör nevét a `<RoleName>` `ApplicationInsights.xml` fájlhoz való hozzáadással, például.

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">
   <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>
   <RoleName>** Your role name **</RoleName>
   ...
</ApplicationInsights>
```

Ha a Spring Boot az Application Insights tavaszi rendszerindítási starter, az egyetlen szükséges változás az, hogy állítsa be az egyéni nevét az alkalmazás az application.properties fájlban.

`spring.application.name=<name-of-app>`

A Tavaszi rendszerindító automatikusan hozzárendeli a felhőszerepkör nevét a spring.application.name tulajdonsághoz megadott értékhez.

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY').start();
appInsights.defaultClient.context.tags["ai.cloud.role"] = "your role name";
appInsights.defaultClient.context.tags["ai.cloud.roleInstance"] = "your role instance";
```

### <a name="alternate-method-for-nodejs"></a>Alternatív módszer a Node.js-hez

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY').start();

appInsights.defaultClient.addTelemetryProcessor(envelope => {
    envelope.tags["ai.cloud.role"] = "your role name";
    envelope.tags["ai.cloud.roleInstance"] = "your role instance"
});
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
appInsights.queue.push(() => {
appInsights.addTelemetryInitializer((envelope) => {
  envelope.tags["ai.cloud.role"] = "your role name";
  envelope.tags["ai.cloud.roleInstance"] = "your role instance";
});
});
```
---

### <a name="understanding-cloud-role-name-within-the-context-of-the-application-map"></a>A felhőbeli szerepkör nevének ismertetése az alkalmazásleképezés környezetében

Ami a **felhőszerepkör-név**átgondolását illeti, hasznos lehet egy olyan alkalmazástérképet megtekinteni, amelytöbb felhőalapú szerepkörnevet is bemutat:

![Alkalmazástérkép képernyőkép](media/app-map/cloud-rolename.png)

A zöld mezőkben lévő nevek feletti alkalmazásleképezések felhőalapú szerepkörnév-értékek az adott elosztott alkalmazás különböző aspektusaihoz. Tehát ez app szerepei `Authentication`állnak: , `acmefrontend`, `Inventory Management`, a `Payment Processing Worker Role`. 

Ebben az alkalmazásban az egyes felhőszerepkör-nevek is egy másik egyedi Application Insights-erőforrást is képvisel nek saját instrumentation kulcsokkal. Mivel az alkalmazás tulajdonosa rendelkezik hozzáféréssel mind a négy különböző Application Insights-erőforrások, Application Map képes összefűzni egy térképet az alapul szolgáló kapcsolatok.

A [hivatalos meghatározások](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/39a5ef23d834777eefdd72149de705a016eb06b0/Schema/PublicSchema/ContextTagKeys.bond#L93)esetében:

```
   [Description("Name of the role the application is a part of. Maps directly to the role name in azure.")]
    [MaxStringLength("256")]
    705: string      CloudRole = "ai.cloud.role";
    
    [Description("Name of the instance where the application is running. Computer name for on-premises, instance name for Azure.")]
    [MaxStringLength("256")]
    715: string      CloudRoleInstance = "ai.cloud.roleInstance";
```

Másik lehetőségként a **felhőbeli szerepkörpéldány** hasznos lehet olyan esetekben, amikor a **felhőbeli szerepkör neve** azt mondja, hogy a probléma valahol a webes előtér-házban van, de előfordulhat, hogy a webes előtér-alapú kapcsolatot több terhelés-kiegyensúlyozott kiszolgálón futtatja, így a Kusto-lekérdezéseken keresztül mélyebbrétegben fúrhat, és tudni, hogy a probléma hatással van-e az összes webes előtér-kiszolgálóra/példányra, vagy csak egy rendkívül fontos lehet.

Egy forgatókönyv, ahol érdemes felülírni az értéket a felhőbeli szerepkör-példány lehet, ha az alkalmazás fut egy tárolóba egy tárolókörnyezetben, ahol csak ismerve az egyes kiszolgáló nem lehet elég információ, hogy keresse meg egy adott probléma.

A felhőbeli szerepkörnév-tulajdonság telemetriai inicializálókkal való felülbírálásáról további információt az [ITelemetryInitializer hozzáadása című](api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)témakörben talál.

## <a name="troubleshooting"></a>Hibaelhárítás

Ha nem sikerül az Alkalmazástérkép várt módon működnie, próbálkozzon az alábbi lépésekkel:

### <a name="general"></a>Általános kérdések

1. Győződjön meg róla, hogy hivatalosan támogatott SDK-t használ. Előfordulhat, hogy a nem támogatott/közösségi SDK-k nem támogatják a korrelációt.

    A támogatott SDK-k listáját ebben a [cikkben](https://docs.microsoft.com/azure/application-insights/app-insights-platforms) olvashatja.

2. Frissítse az összes összetevőt a legújabb SDK-verzióra.

3. Ha c#-os Azure Functions-et használ, frissítsen [a V2 függvényekre.](https://docs.microsoft.com/azure/azure-functions/functions-versions)

4. Ellenőrizze, hogy a [felhőbeli szerepkör neve](#set-cloud-role-name) megfelelően van-e konfigurálva.

5. Ha valamelyik függőség hiányzik, ellenőrizze, hogy az [automatikusan gyűjtött függőségek](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies) listájában szerepel-e. Ha nem, manuálisan úgy is nyomon követheti egy [függőségek nyomon követése hívással](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency).

### <a name="too-many-nodes-on-the-map"></a>Túl sok csomópont van a térképen

Az Application Map egy alkalmazáscsomópontot hoz létre a kérelem telemetriában szereplő minden egyes egyedi felhőszerepkör-névhez, és egy függőségi csomópontot a függőségi telemetriai adatokban a típus, a cél és a felhőszerepkör nevének minden egyes egyedi kombinációjához. Ha több mint 10 000 csomópont van a telemetriai adatokban, az Application Map nem lesz képes lekérni az összes csomópontot és hivatkozást, így a térkép nem lesz teljes. Ebben az esetben figyelmeztető üzenet jelenik meg a térkép megtekintésekor.

Ezenkívül az Alkalmazástérkép csak legfeljebb 1000 különálló, nem csoportosított csomópontot támogat egyszerre. Az Alkalmazástérkép csökkenti a vizuális összetettséget azáltal, hogy csoportosítja az azonos típusú és hívókkal rendelkező függőségeket, de ha a telemetriatúl sok egyedi felhőszerepkör-nevet vagy túl sok függőségi típust tartalmaz, akkor a csoportosítás nem lesz elegendő, és a térkép nem lesz képes megjeleníteni.

A probléma megoldásához módosítania kell a műszerezést a felhőbeli szerepkör nevének, a függőségtípusnak és a függőségi célmezőknek a megfelelő beállításához.

* A függőségi célnak egy függőség logikai nevét kell képviselnie. Sok esetben ez megegyezik a függőség kiszolgálójának vagy erőforrásnevének. Http-függőségek esetén például az állomásnév van beállítva. Nem tartalmazhat egyedi azonosítókat vagy paramétereket, amelyek egyik kérésről a másikra változnak.

* A függőségtípusnak a függőség logikai típusát kell képviselnie. Például a HTTP, SQL vagy Azure Blob tipikus függőségi típusok. Nem tartalmazhat egyedi azonosítókat.

* A felhőalapú szerepkörnevének célját a [fenti szakasz](https://docs.microsoft.com/azure/azure-monitor/app/app-map#set-cloud-role-name)ismerteti.

## <a name="portal-feedback"></a>A portál visszajelzései

Ha visszajelzést szeretne küldeni, használja a visszajelzési lehetőséget.

![MapLink-1 kép](./media/app-map/14-updated.png)

## <a name="next-steps"></a>További lépések

* Ha többet szeretne megtudni arról, hogyan működik a korreláció az Application Insightsban tekintse meg a [telemetriai korrelációs cikket.](correlation.md)
* A [végpontok közötti tranzakciódiagnosztikai felület](transaction-diagnostics.md) egyetlen nézetben korrelálja a kiszolgálóoldali telemetriai adatokat az összes Application Insights figyelt összetevőből.
* A speciális korrelációs forgatókönyvek ASP.NET Core és ASP.NET tekintse meg a [pálya egyéni műveletek](custom-operations-tracking.md) cikket.
