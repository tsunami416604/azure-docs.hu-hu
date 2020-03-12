---
title: Azure Government Monitoring és felügyelet | Microsoft Docs
description: Ez összehasonlítja a szolgáltatásokat és útmutatást a Azure Government alkalmazások fejlesztéséhez.
services: azure-government
cloud: gov
author: gsacavdm
ms.assetid: 4b7720c1-699e-432b-9246-6e49fb77f497
ms.service: azure-government
ms.topic: article
ms.workload: azure-government
ms.date: 12/11/2019
ms.author: gsacavdm
ms.openlocfilehash: 38eb44e6355ddcb5ac78f5d8bb2008ea5a0f0cdf
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2020
ms.locfileid: "79127940"
---
# <a name="azure-government-monitoring--management"></a>Azure Government Monitoring és felügyelet
Ez a cikk a Azure Government-környezet monitorozási és felügyeleti szolgáltatásainak változatait és szempontjait ismerteti.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="advisor"></a>Advisor
Az Advisor általánosan elérhető Azure Governmentban.

További információ: [Advisor Public dokumentáció](../advisor/advisor-overview.md).

### <a name="variations"></a>Változata
A következő Advisor-javaslatok jelenleg nem érhetők el Azure Governmentban:

* Magas rendelkezésre állás
  * VPN-átjáró konfigurálása aktív-aktív kapcsolati rugalmasságra
  * Azure Service Health riasztások létrehozása, amelyekről értesítést kap, ha az Azure-problémák érintik Önt
  * Traffic Manager végpontok konfigurálása a rugalmasság érdekében
  * Az Azure Storage-fiókhoz tartozó Soft delete használata
* Teljesítmény
  * A App Service teljesítményének és megbízhatóságának javítása
  * Csökkentse a DNS-időt a Traffic Manager-profilban való működésre, hogy gyorsabban átadja a feladatokat a megfelelő állapotú végpontoknak
  * SQL Data Warehouse teljesítmény javítása
  * Premium Storage használata
  * Telepítse át a Storage-fiókját Azure Resource Manager
* Költségek
  * Fenntartott Virtual Machines-példányok vásárlása az utólagos elszámolású költségek megtakarítása érdekében
  * Nem kiépített ExpressRoute-áramkörök megszüntetése
  * Inaktív virtuális hálózati átjárók törlése vagy újrakonfigurálása

A kiszámított virtuális gépek megfelelő méretének vagy leállításának ajánlott kiszámításához a következő Azure Government van szükség:

Az Advisor 7 napig figyeli a virtuális gépek használatát, és azonosítja az alacsony kihasználtságú virtuális gépeket. A virtuális gépek alacsony kihasználtságnak számítanak, ha a processzor kihasználtsága 5% vagy kevesebb, és a hálózati kihasználtsága kevesebb, mint 2%, vagy ha a jelenlegi számítási feladatot kisebb virtuálisgép-mérettel lehet elfogadni. Ha agresszíven szeretne lenni a kihasználatlan virtuális gépek azonosításához, a CPU-kihasználtsági szabályt előfizetések alapján is módosíthatja.

## <a name="automation"></a>Automatizálás
Az automatizálás általánosan elérhető Azure Governmentban.

További információt az [Automation nyilvános dokumentációjában](../automation/automation-intro.md)talál.

## <a name="azure-migrate"></a>Azure Migrate

A Azure Migrate általánosan elérhető Azure Governmentban.

További információ: [Azure Migrate dokumentáció](../migrate/migrate-overview.md).

### <a name="variations"></a>Változata
A következő Azure Migrate szolgáltatások jelenleg nem érhetők el a Azure Governmentban:

* A függőségi vizualizáció funkció nem érhető el Azure Governmentban, mert Azure Migrate a függőségi vizualizáció Service Map függ, amely jelenleg nem érhető el a Azure Governmentban.
* Csak a célként megadott régiókhoz és Azure Government ajánlatokhoz hozhat létre Azure Government értékeléseket.

## <a name="backup"></a>Biztonsági mentés
A biztonsági mentés általánosan elérhető a Azure Governmentban.

További információ: [Azure Government biztonsági mentés](documentation-government-services-backup.md).

## <a name="policy"></a>Szabályzat
A szabályzat általánosan elérhető Azure Governmentban.

További információ: [Azure Policy](../governance/policy/overview.md).

## <a name="site-recovery"></a>Site Recovery
A Azure Site Recovery általánosan elérhető Azure Governmentban.

További információ: [site Recovery kereskedelmi dokumentáció](../site-recovery/site-recovery-overview.md).

### <a name="variations"></a>Változata
A következő Site Recovery szolgáltatások jelenleg nem érhetők el a Azure Governmentban:
* E-mailes értesítés

| Site Recovery | Klasszikus | Resource Manager |
| --- | --- | --- |
| VMware/fizikai  | FE | FE |
| Hyper-V | FE | FE |
| Helyek közötti | FE | FE |

A Site Recovery következő URL-címei különböznek a Azure Governmentban:

| Azure Public | Azure Government | Megjegyzések |
| --- | --- | --- |
| \*.hypervrecoverymanager.windowsazure.com | \*. hypervrecoverymanager.windowsazure.us | Hozzáférés a Site Recovery szolgáltatáshoz |
| \*.backup.windowsazure.com  | \*. backup.windowsazure.us | Hozzáférés a védelmi szolgáltatáshoz |
| \*.blob.core.windows.net | \*. blob.core.usgovcloudapi.net | A VM-Pillanatképek tárolásához |
| https://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi | https://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi | A MySQL letöltése |

## <a name="monitor"></a>Monitoring
A Azure Monitor általánosan elérhető Azure Governmentban.

További információ: a [kereskedelmi dokumentáció monitorozása](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview).

### <a name="variations"></a>Változata
A következő részekben részletesen ismertetjük a Azure Government Azure Monitor funkcióinak különbségeit és megkerülő megoldásait:

#### <a name="action-groups"></a>Műveletcsoportok
A műveleti csoportok általánosan elérhetők Azure Governmentban, és nem különböznek a kereskedelmi Azure-tól.   

#### <a name="activity-log-alerts"></a>Tevékenységnapló-riasztások
A műveletnapló riasztásai általánosan elérhetők Azure Governmentban, és nem különböznek a kereskedelmi Azure-tól.

#### <a name="alerts-experience"></a>Riasztások felhasználói élmény
Az egyesített riasztások felhasználói felülete a metrikák és a naplózási riasztások Azure Governmentban érhető el.

#### <a name="autoscale"></a>Automatikus méretezés
Az autoskálázás általánosan elérhető a Azure Governmentban.

Ha a beállítások megadásához PowerShell/ARM/REST-hívásokat használ, állítsa az "location" (hely) beállítást "USA Korm. Virginia" vagy "USA Korm. Iowa" értékre. Az autoscale által megcélozható erőforrás bármely régióban létezhet. A beállítás példája a következő:

```powershell
$rule1 = New-AzAutoscaleRule -MetricName "Requests" -MetricResourceId "/subscriptions/S1/resourceGroups/RG1/providers/Microsoft.Web/sites/WebSite1" -Operator GreaterThan -MetricStatistic Average -Threshold 10 -TimeGrain 00:01:00 -ScaleActionCooldown 00:05:00 -ScaleActionDirection Increase -ScaleActionScaleType ChangeCount -ScaleActionValue "1"
$rule2 = New-AzAutoscaleRule -MetricName "Requests" -MetricResourceId "/subscriptions/S1/resourceGroups/RG1/providers/Microsoft.Web/sites/WebSite1" -Operator GreaterThan -MetricStatistic Average -Threshold 10 -TimeGrain 00:01:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Increase -ScaleActionScaleType ChangeCount -ScaleActionValue "2"
$profile1 = New-AzAutoscaleProfile -DefaultCapacity 2 -MaximumCapacity 10 -MinimumCapacity 2 -Rules $rule1, $rule2 -Name "MyProfile"
$webhook_scale = New-AzAutoscaleWebhook -ServiceUri https://example.com?mytoken=mytokenvalue
$notification1= New-AzAutoscaleNotification -CustomEmails myname@company.com -SendEmailToSubscriptionAdministrator -SendEmailToSubscriptionCoAdministrators -Webhooks $webhook_scale
Add-AzAutoscaleSetting -Location "USGov Virginia" -Name "MyScaleVMSSSetting" -ResourceGroup sdubeys-usgv -TargetResourceId /subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Web/serverFarms/ServerFarm1 -AutoscaleProfiles $profile1 -Notifications $notification1
```

Ha érdekli az erőforrásokra vonatkozó autoskálázás megvalósítása, a beállítások megadásához használja a PowerShell/ARM/Rest-hívásokat.

A PowerShell használatával kapcsolatos további információkért tekintse meg a [nyilvános dokumentációt](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-powershell-samples#create-and-manage-autoscale-settings).

#### <a name="metrics"></a>Mérőszámok
A metrikák általánosan elérhetők a Azure Governmentban. A többdimenziós metrikák azonban csak a REST APIon keresztül támogatottak. A [többdimenziós metrikák megjelenítésének](../azure-monitor/platform/metrics-charts.md) lehetősége a Azure Government-portál előzetes verziójában érhető el.

#### <a name="metric-alerts"></a>Metrikákhoz kapcsolódó riasztások
A metrikai riasztások első generációja általánosan elérhető a Azure Government és a kereskedelmi Azure-ban is. Az első generációnak *riasztás (klasszikus)* néven kell lennie. A metrikus riasztások második generációja (más néven az [egyesített riasztások felülete](../azure-monitor/platform/alerts-overview.md)) mostantól elérhető, de a [nyilvános felhőhöz képest](../azure-monitor/platform/alerts-metric-near-real-time.md)kevesebb erőforrás-szolgáltatóval. További részletek az idő múlásával lesznek hozzáadva. 

A második generációs riasztások felhasználói felületén jelenleg támogatott erőforrások a következők:
- Microsoft.ApiManagement/service
- Microsoft.Cache/redis
- Microsoft.Compute/virtualMachines
- Microsoft.DBforMySQL/servers
- Microsoft.DBforPostgreSQL/servers
- Microsoft.DBforMariaDB/servers
- Microsoft.Devices/IotHubs
- Microsoft.EventGrid/domains
- Microsoft.EventGrid/topics
- Microsoft.EventHub/clusters
- Microsoft.EventHub/namespaces
- Microsoft.Insights/components
- Microsoft.Network/dnsZones
- Microsoft.Network/loadBalancers
- Microsoft. Network/natGateways
- Microsoft. Network/privateEndpoints
- Microsoft. Network/privateLinkServices
- Microsoft.Network/trafficManagerProfiles
- Microsoft.OperationalInsights/workspaces
- Microsoft.PowerBIDedicated/capacities
- Microsoft.Relay/namespaces
- Microsoft.ServiceBus/namespaces
- Microsoft.Sql/managedInstances
- Microsoft.Sql/servers/databases
- Microsoft.Sql/servers/elasticPools
- Microsoft.Storage/storageAccounts
- Microsoft.Storage/storageAccounts/blobServices
- Microsoft.Storage/storageAccounts/fileServices
- Microsoft.Storage/storageAccounts/queueServices
- Microsoft.Storage/storageAccounts/tableServices
- Microsoft.Web/hostingEnvironments/multiRolePools
- Microsoft. Web/hostingEnvironments/workerPools
- Microsoft.Web/serverfarms
- Microsoft.Web/sites
- Microsoft.Web/sites/slots

> [!NOTE]
> A többerőforrásos metrika riasztási szabályainak létrehozása Virtual Machineson **jelenleg nem támogatott**. Ez a cikk azonnal frissülni fog, amint a funkció elérhetővé válik.

A [klasszikus riasztások](../azure-monitor/platform/alerts-classic.overview.md) továbbra is használhatók a riasztások második generációjában még nem elérhető erőforrásokhoz. 

Ha PowerShell-/ARM/Rest-hívásokat használ metrikai riasztások létrehozásához, be kell állítania a metrikus riasztás "helyét" a "USA Korm. Virginia" vagy "USA Korm. Iowa" értékre. A beállítás példája a következő:

```powershell
$actionEmail = New-AzAlertRuleEmail -CustomEmail myname@company.com
$actionWebhook = New-AzAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
Add-AzMetricAlertRule -Name vmcpu_gt_1 -Location "USGov Virginia" -ResourceGroup myrg1 -TargetResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.ClassicCompute/virtualMachines/my_vm1 -MetricName "Percentage CPU" -Operator GreaterThan -Threshold 1 -WindowSize 00:05:00 -TimeAggregationOperator Average -Actions $actionEmail, $actionWebhook -Description "alert on CPU > 1%"
```

A PowerShell használatával kapcsolatos további információkért tekintse meg a [nyilvános dokumentációt](../azure-monitor/platform/powershell-quickstart-samples.md).


## <a name="application-insights"></a>Application Insights

> [!NOTE]
> Az Azure App Services kód nélküli ügynök/bővítmény alapú figyelése **jelenleg nem támogatott**. Amint ez a funkció elérhetővé válik, a cikk frissülni fog.

Ez a szakasz a Azure Government Application Insights használatához szükséges kiegészítő konfigurációt ismerteti. Ha többet szeretne megtudni a Azure Monitorről, és Application Insights a [teljes dokumentáció](https://docs.microsoft.com/azure/azure-monitor/overview)kifizetését.

### <a name="enable-application-insights-for-aspnet--aspnet-core-with-visual-studio"></a>Application Insights engedélyezése a ASP.NET & a Visual Studióval ASP.NET Core

Jelenleg Azure Government ügyfelek számára, a Visual Studióban a hagyományos **Add Applications telemetria** gomb használatával csak kis manuális megoldást kell engedélyezni a Application Insights. Azok az ügyfelek, akik a kapcsolódó problémát tapasztalják, a következő hibaüzenetek jelenhetnek meg: _"nincs Azure-előfizetés társítva ehhez a fiókhoz_ , vagy _" a kiválasztott előfizetés nem támogatja a Application Insights_ annak ellenére, hogy a `microsoft.insights` erőforrás-szolgáltató rendelkezik az előfizetéshez regisztrált állapottal. A probléma megoldásához a következő lépéseket kell végrehajtania:

1. Váltson a Visual Studióra [a Azure Government felhő célzásához](https://docs.microsoft.com/azure/azure-government/documentation-government-get-started-connect-with-vs).

2. Hozzon létre (vagy ha már meglévő készletet) a AzureGraphApiVersion felhasználói környezeti változóját az alábbiak szerint: (felhasználói környezeti változó létrehozásához nyissa meg a **vezérlőpultot** > **rendszer** > **speciális rendszerbeállítások** > **speciális** > **környezeti változók**.)

    `Variable name: AzureGraphApiVersion` `Variable value: 2014-04-01`

3. A projekt típusától függően végezze el a megfelelő Application Insights SDK-végpontok módosítását a [ASP.net](https://docs.microsoft.com/azure/azure-government/documentation-government-services-monitoringandmanagement#net-with-applicationinsightsconfig) vagy a [ASP.net Corehoz](#aspnet-core) .

### <a name="snapshot-debugger"></a>Pillanatkép-hibakereső

Snapshot Debugger mostantól elérhető Azure Government ügyfelek számára. A Snapshot Debugger használatához az egyetlen további előfeltétel, hogy biztosítsa, hogy [Snapshot Collector 1.3.5](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.5-pre-1906.403) vagy újabb verziót használjon. Ezután egyszerűen kövesse a standard [Snapshot Debugger dokumentációját](https://docs.microsoft.com/azure/azure-monitor/app/snapshot-debugger).

### <a name="sdk-endpoint-modifications"></a>SDK-végpont módosításai

Ahhoz, hogy a Application Insights adatait a Azure Government régióba küldje, módosítania kell az Application Insights SDK-k által használt alapértelmezett végponti címeket. Az SDK-nak némileg eltérő módosításokat kell megadnia.

### <a name="net-with-applicationinsightsconfig"></a>.NET és applicationinsights. config

> [!NOTE]
> Az applicationinsights. config fájlt automatikusan felülírja a rendszer, amikor egy SDK-frissítést hajt végre. Az SDK-frissítés végrehajtása után mindenképpen adja meg újra a régió-specifikus végpontok értékeit.

```xml
<ApplicationInsights>
  ...
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <QuickPulseServiceEndpoint>https://quickpulse.applicationinsights.us/QuickPulseService.svc</QuickPulseServiceEndpoint>
    </Add>
  </TelemetryModules>
    ...
  <TelemetryChannel>
     <EndpointAddress>https://dc.applicationinsights.us/v2/track</EndpointAddress>
  </TelemetryChannel>
  ...
  <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
    <ProfileQueryEndpoint>https://dc.applicationinsights.us/api/profiles/{0}/appId</ProfileQueryEndpoint>
  </ApplicationIdProvider>
  ...
</ApplicationInsights>
```

### <a name="aspnet-core"></a>ASP.NET-mag

Módosítsa a appSettings. JSON fájlt a projektben az alábbiak szerint a fő végpont beállításához:

```json
"ApplicationInsights": {
    "InstrumentationKey": "instrumentationkey",
    "TelemetryChannel": {
      "EndpointAddress": "https://dc.applicationinsights.us/v2/track"
    }
  }
```

Az élő metrikák és a profil lekérdezési végpontjának értékei csak kód használatával állíthatók be. Ha az összes végpont értékének alapértelmezett értékét szeretné felülbírálni a kódban, hajtsa végre a következő módosításokat a `Startup.cs` fájl `ConfigureServices` metódusában:

```csharp
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId;
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel; //place at top of Startup.cs file

   services.ConfigureTelemetryModule<QuickPulseTelemetryModule>((module, o) => module.QuickPulseServiceEndpoint="https://quickpulse.applicationinsights.us/QuickPulseService.svc");

   services.AddSingleton<IApplicationIdProvider, ApplicationInsightsApplicationIdProvider>(_ => new ApplicationInsightsApplicationIdProvider() { ProfileQueryEndpoint = "https://dc.applicationinsights.us/api/profiles/{0}/appId" });

   services.AddSingleton<ITelemetryChannel>(_ => new ServerTelemetryChannel() { EndpointAddress = "https://dc.applicationinsights.us/v2/track" });

    //place in ConfigureServices method. If present, place this prior to   services.AddApplicationInsightsTelemetry("instrumentation key");
```

### <a name="azure-functions"></a>Azure Functions

Telepítse a következő csomagokat a Function projektbe:

- A Microsoft. ApplicationInsights verziója 2.10.0
- A Microsoft. ApplicationInsights. PerfCounterCollector verziója 2.10.0
- Microsoft. ApplicationInsights. WindowsServer. TelemetryChannel verzió 2.10.0

Továbbá adja hozzá (vagy módosítsa) a Function alkalmazás indítási kódját:

```csharp
[assembly: FunctionsStartup(typeof(Example.Startup))]
namespace Example
{
  class Startup : FunctionsStartup
  {
      public override void Configure(IFunctionsHostBuilder builder)
      {
          var quickPulseFactory = builder.Services.FirstOrDefault(sd => sd.ServiceType == typeof(ITelemetryModule) && 
                                               sd.ImplementationType == typeof(QuickPulseTelemetryModule));
          if (quickPulseFactory != null)
          {
              builder.Services.Remove(quickPulseFactory);
          }

          var appIdFactory = builder.Services.FirstOrDefault(sd => sd.ServiceType == typeof(IApplicationIdProvider));
          if (appIdFactory != null)
          {
              builder.Services.Remove(appIdFactory);
          }

          var channelFactory = builder.Services.FirstOrDefault(sd => sd.ServiceType == typeof(ITelemetryChannel));
          if (channelFactory != null)
          {
              builder.Services.Remove(channelFactory);
          }

          builder.Services.AddSingleton<ITelemetryModule, QuickPulseTelemetryModule>(_ =>
              new QuickPulseTelemetryModule
              {
                  QuickPulseServiceEndpoint = "https://quickpulse.applicationinsights.us/QuickPulseService.svc"
              });

          builder.Services.AddSingleton<IApplicationIdProvider, ApplicationInsightsApplicationIdProvider>(_ => new ApplicationInsightsApplicationIdProvider() { ProfileQueryEndpoint = "https://dc.applicationinsights.us/api/profiles/{0}/appId" });

          builder.Services.AddSingleton<ITelemetryChannel>(_ => new ServerTelemetryChannel() { EndpointAddress = "https://dc.applicationinsights.us/v2/track" });
      }
  }
}
```

### <a name="java"></a>Java

Módosítsa a applicationinsights. xml fájlt az alapértelmezett végponti címek módosításához.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <InstrumentationKey>ffffeeee-dddd-cccc-bbbb-aaaa99998888</InstrumentationKey>
  <TelemetryModules>
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
  </TelemetryModules>
  <TelemetryInitializers>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>
  </TelemetryInitializers>
  <!--Add the following Channel value to modify the Endpoint address-->
  <Channel type="com.microsoft.applicationinsights.channel.concrete.inprocess.InProcessTelemetryChannel">
  <EndpointAddress>https://dc.applicationinsights.us/v2/track</EndpointAddress>
  </Channel>
</ApplicationInsights>
```

### <a name="spring-boot"></a>Spring Boot

Módosítsa a `application.properties` fájlt, és adja hozzá a következőket:

```yaml
azure.application-insights.channel.in-process.endpoint-address= https://dc.applicationinsights.us/v2/track
```

### <a name="nodejs"></a>Node.js

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY');
appInsights.defaultClient.config.endpointUrl = "https://dc.applicationinsights.us/v2/track"; // ingestion
appInsights.defaultClient.config.profileQueryEndpoint = "https://dc.applicationinsights.us/api/profiles/{0}/appId"; // appid/profile lookup
appInsights.defaultClient.config.quickPulseHost = "https://quickpulse.applicationinsights.us/QuickPulseService.svc"; //live metrics
appInsights.Configuration.start();
```

A végpontokat környezeti változók használatával is konfigurálhatja:

```
Instrumentation Key: "APPINSIGHTS_INSTRUMENTATIONKEY"
Profile Endpoint: "https://dc.applicationinsights.us/api/profiles/{0}/appId"
Live Metrics Endpoint: "https://quickpulse.applicationinsights.us/QuickPulseService.svc"
```

### <a name="javascript"></a>JavaScript

```javascript
<script type="text/javascript">
   var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){
      function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/next/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t
   }({
      instrumentationKey:"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx"
      endpointUrl: "https://dc.applicationinsights.us/v2/track"
   });

   window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```

### <a name="firewall-exceptions"></a>Tűzfal-kivételek

Az Azure Application Insights szolgáltatás számos IP-címet használ. Előfordulhat, hogy ismernie kell ezeket a címeket, ha a figyelt alkalmazás tűzfal mögött található.

> [!NOTE]
> Habár ezek a címek statikusak, lehetséges, hogy időről időre módosítaniuk kell őket. Az összes Application Insights forgalom a kimenő forgalmat jelenti a rendelkezésre állás monitorozása és a webhookok kivételével, amelyeken bejövő tűzfalszabályok szükségesek.

### <a name="outgoing-ports"></a>Kimenő portok
Meg kell nyitnia néhány kimenő portot a kiszolgálója tűzfalán, hogy a Application Insights SDK és/vagy Állapotmonitor számára lehetővé váljon az adatküldés a portálra:

| Cél | URL-cím | IP | Portok |
| --- | --- | --- | --- |
| Telemetria | dc.applicationinsights.us | 23.97.4.113 | 443 |

## <a name="azure-monitor-logs"></a>Azure Monitor-naplók
Azure Monitor naplók általánosan elérhetők a Azure Governmentban.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

### <a name="variations"></a>Változata

* A Azure Governmentban elérhető megoldások a következők:
  * [Network Performance monitor (NPM)](https://blogs.msdn.microsoft.com/azuregov/2017/09/05/network-performance-monitor-general-availability/) – a NPM egy felhőalapú hálózati figyelési megoldás nyilvános és hibrid felhőalapú környezetekhez. A szervezetek a NPM segítségével figyelik a hálózat rendelkezésre állását a helyszíni és a felhőalapú környezetekben.  Endpoint monitor – a NPM alképessége, figyeli a hálózati kapcsolatot az alkalmazásokkal.

A következő Azure Monitor naplók szolgáltatásai és megoldásai jelenleg nem érhetők el a Azure Governmentban.

* A Microsoft Azure előzetes verziójában elérhető megoldások, beleértve a következőket:
  * Szolgáltatástérkép
  * Windows 10 Upgrade Analytics megoldás
  * Application Insights megoldás
  * Azure hálózati biztonsági csoport elemzési megoldása
  * Azure Automation elemzési megoldás
  * Key Vault Analytics megoldás
* A helyszíni szoftverek frissítését igénylő megoldások és szolgáltatások, beleértve a következőket:
  * Surface Hub megoldás
* Az előzetes verzióban elérhető funkciók a globális Azure-ban, beleértve a következőket:
  * Adatexportálás Power BIba
* Azure-metrikák és Azure Diagnostics

A Azure Monitor naplók URL-címei eltérnek a Azure Governmentban:

| Azure Public | Azure Government | Megjegyzések |
| --- | --- | --- |
| mms.microsoft.com |oms.microsoft.us |Log Analytics munkaterületek portál |
| *munkaterület azonosítója*. ODS.opinsights.Azure.com |*munkaterület azonosítója*. ODS.opinsights.Azure.us |[Adatgyűjtő API](../azure-monitor/platform/data-collector-api.md) |
| \*.ods.opinsights.azure.com |\*. ods.opinsights.azure.us |Ügynök kommunikációja – [tűzfalbeállítások konfigurálása](../log-analytics/log-analytics-proxy-firewall.md) |
| \*.oms.opinsights.azure.com |\*. oms.opinsights.azure.us |Ügynök kommunikációja – [tűzfalbeállítások konfigurálása](../log-analytics/log-analytics-proxy-firewall.md) |
| \*.blob.core.windows.net |\*. blob.core.usgovcloudapi.net |Ügynök kommunikációja – [tűzfalbeállítások konfigurálása](../log-analytics/log-analytics-proxy-firewall.md) |
| portal.loganalytics.io |portal.loganalytics.us |Speciális elemzési portál – [tűzfalbeállítások konfigurálása](../azure-monitor/log-query/portals.md) |
| api.loganalytics.io |api.loganalytics.us |Speciális elemzési portál – [tűzfalbeállítások konfigurálása](../azure-monitor/log-query/portals.md) |
| docs.loganalytics.io |docs.loganalytics.us |Speciális elemzési portál – [tűzfalbeállítások konfigurálása](../azure-monitor/log-query/portals.md) |
| \*.azure-automation.net |\*. azure-automation.us |Azure Automation – [tűzfalbeállítások konfigurálása](../azure-monitor/platform/log-analytics-agent.md#network-firewall-requirements) |
| N/A | *. usgovtrafficmanager.net | Azure Traffic Manager – [tűzfalbeállítások konfigurálása](../azure-monitor/platform/log-analytics-agent.md#network-firewall-requirements) |

A következő Azure Monitor-naplók funkciói eltérően viselkednek a Azure Governmentban:

* A System Center Operations Manager felügyeleti csoportnak Azure Monitor naplókhoz való összekapcsolásához le kell töltenie és importálnia kell a frissített felügyeleti csomagokat.
  + System Center Operations Manager 2016
    1. Telepítse [a 2. kumulatív frissítést a 2016-es System Center Operations Managerhoz](https://support.microsoft.com/help/3209591).
    2. Importálja a 2. kumulatív frissítés részét képező felügyeleti csomagokat Operations Managerba. További információ a felügyeleti csomagok lemezről történő importálásáról: [Operations Manager felügyeleti csomag importálása](https://technet.microsoft.com/library/hh212691.aspx).
    3. A Operations Manager Azure Monitor naplókhoz való összekapcsolásához kövesse a [kapcsolódás Operations Manager Azure monitor naplókhoz](../azure-monitor/platform/om-agents.md)című témakör lépéseit.
  + System Center Operations Manager 2012 R2 UR3 (vagy újabb)/Operations Manager 2012 SP1 HASZNÁLNA 7 (vagy újabb)
    1. Töltse le és mentse a [frissített felügyeleti csomagokat](https://go.microsoft.com/fwlink/?LinkId=828749).
    2. Bontsa ki a letöltött fájlt.
    3. Importálja a felügyeleti csomagokat Operations Managerba. További információ a felügyeleti csomagok lemezről történő importálásáról: [Operations Manager felügyeleti csomag importálása](https://technet.microsoft.com/library/hh212691.aspx).
    4. A Operations Manager Azure Monitor naplókhoz való összekapcsolásához kövesse a [kapcsolódás Operations Manager Azure monitor naplókhoz](../azure-monitor/platform/om-agents.md)című témakör lépéseit.

* További információ a számítógépcsoportok Configuration Manager használatával történő használatáról: [Configuration Manager Összekötése Azure monitor](../azure-monitor/platform/collect-sccm.md).

### <a name="frequently-asked-questions"></a>Gyakori kérdések
* Áttelepíthetem az adatok Azure Monitor naplókból Microsoft Azure a Azure Governmentre?
  * Nem. Az adatok vagy a munkaterület nem helyezhető át Microsoft Azureról Azure Governmentre.
* Válthatok a Microsoft Azure és Azure Government munkaterületek között az Operations Management Suite portálról?
  * Nem. A Microsoft Azure és Azure Government portálok különállóak, és nem osztják meg az adatokat.

További információ: [Azure monitor naplók nyilvános dokumentációja](../log-analytics/log-analytics-overview.md).

## <a name="scheduler"></a>Scheduler
A szolgáltatásról és használatáról az [Azure Scheduler dokumentációjában](../scheduler/index.md)olvashat bővebben.

## <a name="azure-portal"></a>Azure Portal
A Azure Government portál [itt](https://portal.azure.us)érhető el.

## <a name="azure-resource-manager"></a>Azure Resource Manager
További információ erről a szolgáltatásról és használatáról: [Azure Resource Manager dokumentáció](../azure-resource-manager/management/overview.md).

## <a name="next-steps"></a>Következő lépések
* Előfizetés a [Azure Government blogra](https://blogs.msdn.microsoft.com/azuregov/)
* Segítség kérése Stack Overflow az [Azure-gov](https://stackoverflow.com/questions/tagged/azure-gov) használatával
* Visszajelzés küldése vagy új funkciók kérése az [Azure Government visszajelzési fórumán](https://feedback.azure.com/forums/558487-azure-government) keresztül
