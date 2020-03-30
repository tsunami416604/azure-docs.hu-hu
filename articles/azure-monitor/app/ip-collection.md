---
title: Azure Application Insights IP-címgyűjtés | Microsoft dokumentumok
description: Az IP-címek és a földrajzi helymeghatározás kezelésének ismertetése az Azure Application Insights segítségével
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 969061ec89ddd0f13caa675bc324207c6c5d8843
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77656517"
---
# <a name="geolocation-and-ip-address-handling"></a>Földrajzi helymeghatározás és IP-címkezelés

Ez a cikk bemutatja, hogyan történik a földrajzi hely és az IP-cím kezelése az Application Insightsban, valamint az alapértelmezett viselkedés módosításának módját.

## <a name="default-behavior"></a>Alapértelmezett viselkedés

Alapértelmezés szerint az IP-címek ideiglenesen gyűjtik, de nem tárolja az Application Insights. A folyamat alapvetően a következőképpen történik:

Az IP-címek telemetriai adatok részeként kerülnek elküldésre az Application Insights ba. Amikor eléri a betöltési végpontot az Azure-ban, az IP-cím segítségével geolokációs keres a [GeoLite2 a MaxMind.](https://dev.maxmind.com/geoip/geoip2/geolite2/) A keresés eredményei a következő mezők `client_City`feltöltésére szolgálnak `client_CountryOrRegion` `client_StateOrProvince`. Ezen a ponton az IP-cím `0.0.0.0` elvetésre kerül, és a `client_IP` mezőbe kerül.

* Böngésző telemetriai adatok: Ideiglenesen gyűjtjük a feladó IP-címét. Az IP-címet a betöltési végpont számítja ki.
* Kiszolgálótelemetria: Az Application Insights modul ideiglenesen gyűjti az ügyfél IP-címét. A bevan `X-Forwarded-For` állítva, nem kerül összegyűjtésre.

Ez a viselkedés szándékosan segít elkerülni a személyes adatok szükségtelen gyűjtését. Amikor csak lehetséges, javasoljuk, hogy kerülje a személyes adatok gyűjtését. 

## <a name="overriding-default-behavior"></a>Az alapértelmezett viselkedés felülbírálása

Bár az alapértelmezett viselkedés a személyes adatok gyűjtésének minimalizálása, továbbra is rugalmasságot biztosítunk az IP-címadatok gyűjtéséhez és tárolásához. Mielőtt úgy döntenénk, hogy bármilyen személyes adatot, például IP-címeket tárolunk, javasoljuk, hogy ellenőrizzük, hogy ez nem szegi-e meg a megfelelőségi követelményeket vagy a helyi előírásokat, amelyekre önre vonatkozhat. Ha többet szeretne megtudni a személyes adatok kezeléséről az Application Insightsban, olvassa el a [személyes adatokra vonatkozó útmutatást.](https://docs.microsoft.com/azure/azure-monitor/platform/personal-data-mgmt)

## <a name="storing-ip-address-data"></a>IP-címadatok tárolása

Az IP-gyűjtés és -tárolás engedélyezéséhez az `DisableIpMasking` Application Insights `true`összetevő tulajdonságát be kell állítani. Ez a tulajdonság az Azure Resource Manager-sablonokon keresztül vagy a REST API hívásával állítható be. 

### <a name="azure-resource-manager-template"></a>Azure Resource Manager-sablon

```json
{
       "id": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/microsoft.insights/components/<resource-name>",
       "name": "<resource-name>",
       "type": "microsoft.insights/components",
       "location": "westcentralus",
       "tags": {
              
       },
       "kind": "web",
       "properties": {
              "Application_Type": "web",
              "Flow_Type": "Redfield",
              "Request_Source": "IbizaAIExtension",
              // ...
              "DisableIpMasking": true
       }
}
```

### <a name="portal"></a>Portál 

Ha csak egyetlen Application Insights-erőforrás viselkedését kell módosítania, a legegyszerűbb módja ennek az Azure Portalon keresztül.  

1. Az Application Insights-erőforrás > **beállítások** > **exportálási sablonja** 

    ![Sablon exportálása](media/ip-collection/export-template.png)

2. **Telepítés** kiválasztása

    ![A Telepítés gomb pirossal kiemelve](media/ip-collection/deploy.png)

3. Válassza **a Sablon szerkesztése lehetőséget.** (Ha a sablon további tulajdonságokkal vagy erőforrásokkal rendelkezik, amelyek nem jelennek meg ebben a példasablonban, körültekintően járjon el, és győződjön meg arról, hogy minden erőforrás növekményes módosításként/frissítésként fogadja el a sablon központi telepítését.)

    ![Sablon szerkesztése](media/ip-collection/edit-template.png)

4. Hajtsa végre az erőforrás jsonjának következő módosításait, majd kattintson a **Mentés**gombra:

    ![Képernyőkép hozzáad egy vesszőt után "IbizaAIExtension", és adjunk hozzá egy új sort az alábbi "DisableIpMasking": igaz](media/ip-collection/save.png)

    > [!WARNING]
    > Ha a következő hibaüzenetjelenik meg: ** _Az erőforráscsoport olyan helyen található, amelyet a sablon egy vagy több erőforrása nem támogat. Válasszon másik erőforráscsoportot._** Ideiglenesen válasszon ki egy másik erőforráscsoportot a legördülő menüből, majd válassza ki újra az eredeti erőforráscsoportot a hiba elhárításához.

5. Válassza **az Elfogadom** > **a vásárlást**lehetőséget. 

    ![Sablon szerkesztése](media/ip-collection/purchase.png)

    Ebben az esetben semmi új at vásárolnak, mi csak a meglévő Application Insights-erőforrás konfigurációjának frissítése.

6. A központi telepítés befejezése után a rendszer új telemetriai adatokat rögzít.

    Ha ismét kijelölné és szerkesztené a sablont, akkor csak az alapértelmezett sablon jelenik meg, és nem jelenik meg az újonnan hozzáadott tulajdonság és a hozzá tartozó érték. Ha nem látja az IP-cím adatokat, és szeretné megerősíteni, hogy `"DisableIpMasking": true` be van állítva. Futtassa a következő `Fabrikam-dev` PowerShellt: (Cserélje le a megfelelő erőforrás- és erőforráscsoportnévre.)
    
    ```powershell
    # If you aren't using the cloud shell you will need to connect to your Azure account
    # Connect-AzAccount 
    $AppInsights = Get-AzResource -Name 'Fabrikam-dev' -ResourceType 'microsoft.insights/components' -ResourceGroupName 'Fabrikam-dev'
    $AppInsights.Properties
    ```
    
    Ennek eredményeképpen a rendszer visszaadja a tulajdonságok listáját. Az egyik tulajdonság `DisableIpMasking: true`nak el kell olvasnia. Ha az új tulajdonság üzembe helyezése előtt futtatja a PowerShellt az Azure Resource Managerrel, a tulajdonság nem fog létezni.

### <a name="rest-api"></a>Rest API

A [Rest API](https://docs.microsoft.com/rest/api/azure/) hasznos adata ugyanazokat a módosításokat hajtsa végre:

```
PATCH https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/microsoft.insights/components/<resource-name>?api-version=2018-05-01-preview HTTP/1.1
Host: management.azure.com
Authorization: AUTH_TOKEN
Content-Type: application/json
Content-Length: 54

{
       "location": "<resource location>",
       "kind": "web",
       "properties": {
              "Application_Type": "web",
              "DisableIpMasking": true
       }
}
```

## <a name="telemetry-initializer"></a>Telemetriai inicializáló

Ha rugalmasabb alternatívára van `DisableIpMasking` szüksége, mint az IP-címek egészének vagy egy részének rögzítéséhez, [telemetriai inicializáló](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#addmodify-properties-itelemetryinitializer) használatával az IP-cím egészét vagy egy részét egy egyéni mezőbe másolhatja. 

### <a name="aspnet--aspnet-core"></a>ASP.NET / ASP.NET Core

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;

namespace MyWebApp
{
    public class CloneIPAddress : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            ISupportProperties propTelemetry = telemetry as ISupportProperties;

            if (propTelemetry !=null && !propTelemetry.Properties.ContainsKey("client-ip"))
            {
                string clientIPValue = telemetry.Context.Location.Ip;
                propTelemetry.Properties.Add("client-ip", clientIPValue);
            }
        }
    } 
}
```

> [!NOTE]
> Ha nem tudja `ISupportProperties`elérni, ellenőrizze, és győződjön meg arról, hogy az Application Insights SDK legújabb stabil kiadása fut. `ISupportProperties`magas számosságú értékekhez szolgálnak, míg `GlobalProperties` az alacsony számosságú értékekhez, például a régió nevéhez, a környezet nevéhez stb. 

### <a name="enable-telemetry-initializer-for-aspnet"></a>Telemetriai inicializáló engedélyezése ASP.NET

```csharp
using Microsoft.ApplicationInsights.Extensibility;


namespace MyWebApp
{
    public class MvcApplication : System.Web.HttpApplication
    {
        protected void Application_Start()
        {
              //Enable your telemetry initializer:
              TelemetryConfiguration.Active.TelemetryInitializers.Add(new CloneIPAddress());
        }
    }
}

```

### <a name="enable-telemetry-initializer-for-aspnet-core"></a>Telemetriai inicializáló engedélyezése ASP.NET Core számára

A telemetriai inicializálót ugyanúgy hozhatja létre ASP.NET Core esetében, mint ASP.NET de az inicializáló engedélyezéséhez használja a következő példát a hivatkozáshoz:

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, CloneIPAddress>();
}
```

### <a name="nodejs"></a>Node.js

```javascript
appInsights.defaultClient.addTelemetryProcessor((envelope) => {
    const baseData = envelope.data.baseData;
    if (appInsights.Contracts.domainSupportsProperties(baseData)) {
        const ipAddress = envelope.tags[appInsights.defaultClient.context.keys.locationIp];
        if (ipAddress) {
            baseData.properties["client-ip"] = ipAddress;
        }
    }
});
```

### <a name="client-side-javascript"></a>Ügyféloldali JavaScript

A kiszolgálóoldali SDK-kkal ellentétben az ügyféloldali Javascript SDK nem számítja ki az IP-címet. Alapértelmezés szerint az ügyféloldali telemetriai adatok IP-címszámítása az Azure-beli betöltési végponton történik telemetriai adatok érkezésekor. Ez azt jelenti, hogy ha ügyféloldali adatokat küldött egy proxynak, majd továbbította a betöltési végpontra, az IP-cím számítása a proxy IP-címét, és nem az ügyfelet jelenítheti meg. Ha nem használ proxyt, ez nem lehet probléma.

Ha szeretné kiszámítani az IP-címet közvetlenül az ügyféloldalon, akkor hozzá kell adnia a `ai.location.ip` saját egyéni logikát, hogy ezt a számítást, és használja az eredményt a címke beállításához. Ha `ai.location.ip` be van állítva, az IP-cím számítása nem hajtja végre a betöltési végpont, és a megadott IP-cím tiszteletben tartja, és a geo-alapú felmérés végrehajtásához használt. Ebben az esetben az IP-cím alapértelmezés szerint továbbra is nullázódik. 

Az egyéni logikából számított teljes IP-cím megőrzéséhez használhat telemetriai inicializálót, amely a megadott `ai.location.ip` IP-címadatokat egy külön egyéni mezőbe másolja. De ismét ellentétben a kiszolgálóoldali SDK-k, anélkül, hogy a 3rd party könyvtárak vagy a saját egyéni ügyféloldali IP-gyűjtemény logika az ügyféloldali SDK nem számítja ki az IP az Ön számára.    


```javascript
appInsights.addTelemetryInitializer((item) => {
    const ipAddress = item.tags && item.tags["ai.location.ip"];
    if (ipAddress) {
        item.baseData.properties = {
            ...item.baseData.properties,
            "client-ip": ipAddress
        };
    }
});

```  

### <a name="view-the-results-of-your-telemetry-initializer"></a>A telemetriai inicializáló eredményeinek megtekintése

Ha ezután új forgalmat indít el a webhelyén, és körülbelül 2–5 percet vár a betöltési idő biztosítására, futtathat egy Kusto-lekérdezést, hogy lássa, működik-e az IP-címgyűjtemény:

```kusto
requests
| where timestamp > ago(1h) 
| project appName, operation_Name, url, resultCode, client_IP, customDimensions.["client-ip"]
```

Az újonnan összegyűjtött IP-címeknek meg kell jelenniük az `customDimensions_client-ip` oszlopban. Az `client-ip` alapértelmezett oszlopban továbbra is mind a 4 oktett nullázódik, vagy csak az első három oktett jelenik meg attól függően, hogy hogyan konfigurálta az IP-címgyűjteményt az összetevő szintjén. Ha a telemetriai inicializáló végrehajtása után helyileg teszteli, és a látható `customDimensions_client-ip` érték `::1` ez a várt viselkedés. `::1`az IPv6 visszacsatolási címét jelöli. Ez egyenértékű `127.0.01` az IPv4-ben, és az eredmény látni fogja, ha a tesztelés localhost.

## <a name="next-steps"></a>Következő lépések

* További információ a [személyes adatok gyűjtéséről](https://docs.microsoft.com/azure/azure-monitor/platform/personal-data-mgmt) az Application Insightsban.

* További információ az [IP-címgyűjtemény](https://apmtips.com/blog/2016/07/05/client-ip-address/) működéséről az Application Insightsban. (Ez egy régebbi külső blogbejegyzést írta az egyik mérnök. Ez megelőzi az aktuális alapértelmezett viselkedés, `0.0.0.0`ahol az IP-cím rögzítésre kerül, de `ClientIpHeaderTelemetryInitializer`megy nagyobb mélységben a mechanika a beépített .)
