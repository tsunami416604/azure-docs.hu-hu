---
title: Teljesítményszámlálók gyűjtése az Azure Cloud Servicesben | Microsoft dokumentumok
description: Ismerje meg, hogyan fedezhet fel, használhat és hozhat létre teljesítményszámlálókat a Felhőszolgáltatásokban az Azure Diagnostics and Application Insights segítségével.
services: cloud-services
documentationcenter: .net
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 02/02/2018
ms.author: tagore
ms.openlocfilehash: 3b4028a09f69acd5d7a6579b4610785ed32e227d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77469527"
---
# <a name="collect-performance-counters-for-your-azure-cloud-service"></a>Teljesítményszámlálók gyűjtése az Azure Cloud Szolgáltatáshoz

A teljesítményszámlálók leutat biztosítanak az alkalmazás és a gazdagép teljesítményének nyomon követéséhez. A Windows Server számos különböző teljesítményszámlálót kínál a hardverhez, az alkalmazásokhoz, az operációs rendszerhez és egyebekhez kapcsolódóan. Teljesítményszámlálók gyűjtésével és elküldésével az Azure-ba elemezheti ezeket az információkat a jobb döntések érdekében. 

## <a name="discover-available-counters"></a>Az elérhető számlálók felfedezése

A teljesítményszámláló két részből áll, egy készletnévből (más néven kategóriából) és egy vagy több számlálóból. A PowerShell segítségével lekaphatja az elérhető teljesítményszámlálók listáját:

```powershell
Get-Counter -ListSet * | Select-Object CounterSetName, Paths | Sort-Object CounterSetName

CounterSetName                                  Paths
--------------                                  -----
.NET CLR Data                                   {\.NET CLR Data(*)\SqlClient...
.NET CLR Exceptions                             {\.NET CLR Exceptions(*)\# o...
.NET CLR Interop                                {\.NET CLR Interop(*)\# of C...
.NET CLR Jit                                    {\.NET CLR Jit(*)\# of Metho...
.NET Data Provider for Oracle                   {\.NET Data Provider for Ora...
.NET Data Provider for SqlServer                {\.NET Data Provider for Sql...
.NET Memory Cache 4.0                           {\.NET Memory Cache 4.0(*)\C...
AppV Client Streamed Data Percentage            {\AppV Client Streamed Data ...
ASP.NET                                         {\ASP.NET\Application Restar...
ASP.NET Apps v4.0.30319                         {\ASP.NET Apps v4.0.30319(*)...
ASP.NET State Service                           {\ASP.NET State Service\Stat...
ASP.NET v2.0.50727                              {\ASP.NET v2.0.50727\Applica...
ASP.NET v4.0.30319                              {\ASP.NET v4.0.30319\Applica...
Authorization Manager Applications              {\Authorization Manager Appl...

#... results cut to save space ...
```

A `CounterSetName` tulajdonság egy készletet (vagy kategóriát) jelöl, és jó mutatója annak, hogy a teljesítményszámlálók mihez kapcsolódnak. A `Paths` tulajdonság egy készlet számlálóinak gyűjteményét jelöli. A számlálókészlettel `Description` kapcsolatos további információkért a tulajdonságot is beszerezheti.

Egy készlet összes számlálójának lekérnie, használja `CounterSetName` `Paths` az értéket, és bontsa ki a gyűjteményt. Minden elérési út elem egy lekérdezhető számláló. Ha például a `Processor` készlethez kapcsolódó rendelkezésre álló `Paths` számlálókat szeretné beszerezni, bontsa ki a gyűjteményt:

```powershell
Get-Counter -ListSet * | Where-Object CounterSetName -eq "Processor" | Select -ExpandProperty Paths

\Processor(*)\% Processor Time
\Processor(*)\% User Time
\Processor(*)\% Privileged Time
\Processor(*)\Interrupts/sec
\Processor(*)\% DPC Time
\Processor(*)\% Interrupt Time
\Processor(*)\DPCs Queued/sec
\Processor(*)\DPC Rate
\Processor(*)\% Idle Time
\Processor(*)\% C1 Time
\Processor(*)\% C2 Time
\Processor(*)\% C3 Time
\Processor(*)\C1 Transitions/sec
\Processor(*)\C2 Transitions/sec
\Processor(*)\C3 Transitions/sec
```

Ezek az egyes számlálóelérési utak hozzáadhatók a felhőszolgáltatás által használt diagnosztikai keretrendszerhez. A teljesítményszámláló elérési útjának felépítéséről a [Számláló elérési útjának megadása](https://msdn.microsoft.com/library/windows/desktop/aa373193(v=vs.85))című témakörben talál további információt.

## <a name="collect-a-performance-counter"></a>Teljesítményszámláló gyűjtése

Teljesítményszámláló t adhat hozzá a felhőszolgáltatáshoz az Azure Diagnostics vagy az Application Insights számára.

### <a name="application-insights"></a>Application Insights

Az Azure Application Insights for Cloud Services lehetővé teszi, hogy adja meg, milyen teljesítményszámlálókat szeretne gyűjteni. Miután [hozzáadta az Application Insights-ot a projekthez,](../azure-monitor/app/cloudservices.md#sdk)egy **ApplicationInsights.config** nevű konfigurációs fájl kerül a Visual Studio-projektbe. Ez a konfigurációs fájl határozza meg, hogy az Application Insights milyen típusú adatokat gyűjt és küld az Azure-nak.

Nyissa meg az **ApplicationInsights.config** fájlt, és keresse meg az **ApplicationInsights** > **TelemetryModules** elemet. Minden `<Add>` gyermekelem meghatározza a telemetriai adatok egy típusát, amelyet a konfigurációval együtt kell gyűjteni. A teljesítményszámláló telemetriai `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector`modultípusa. Ha ez az elem már definiálva van, ne adja hozzá még egyszer. Az egyes begyűjtendő teljesítményszámlálókat `<Counters>`egy nevű csomópont határozza meg. Íme egy példa, amely összegyűjti a meghajtó teljesítményszámlálóit:

```xml
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">

  <TelemetryModules>

    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
      <Counters>
        <Add PerformanceCounter="\LogicalDisk(C:)\Disk Write Bytes/sec" ReportAs="Disk write (C:)" />
        <Add PerformanceCounter="\LogicalDisk(C:)\Disk Read Bytes/sec" ReportAs="Disk read (C:)" />
      </Counters>
    </Add>

  </TelemetryModules>

<!-- ... cut to save space ... -->
```

Minden teljesítményszámláló `<Add>` elemként jelenik meg a. `<Counters>` Az `PerformanceCounter` attribútum határozza meg, hogy melyik teljesítményszámlálót kell összegyűjteni. Az `ReportAs` attribútum a teljesítményszámláló Azure Portalon megjelenítendő cím. Minden teljesítményszámláló, amelyet gyűjt, egy **Egyéni** nevű kategóriába kerül a portálon. Az Azure Diagnostics szolgáltatástól eltérően nem állíthatja be, hogy ezek a teljesítményszámlálók gyűjtik és elküldjék az Azure-ba. Az Application Insights segítségével a teljesítményszámlálók percről percre gyűjtik és küldik el. 

Az Application Insights automatikusan összegyűjti a következő teljesítményszámlálókat:

* \Process(?? APP_WIN32_PROC??) \% Processzor idő
* \Memory\Available Bytes
* \.NET CLR-kivételek (??APP_CLR_PROC??)\# az összes kivétel közül másodpercenként
* \Process(??APP_WIN32_PROC??)\Private Bytes
* \Process(??APP_WIN32_PROC??)\IO Data Bytes/sec
* \Processor(_Total)\% Processor Time

További információ: [System performance counters in Application Insights](../azure-monitor/app/performance-counters.md) and [Application Insights for Azure Cloud Services.](../azure-monitor/app/cloudservices.md#performance-counters)

### <a name="azure-diagnostics"></a>Azure Diagnostics

> [!IMPORTANT]
> Bár az összes ilyen adatok a tárfiókba összesítve vannak, a portál **nem** biztosít natív módot az adatok ábrázolására. Erősen ajánlott, hogy egy másik diagnosztikai szolgáltatás, például az Application Insights integrálása az alkalmazásba.

Az Azure Diagnostics bővítmény a Cloud Services lehetővé teszi, hogy adja meg, milyen teljesítményszámlálók at szeretne gyűjteni. Az Azure Diagnostics beállításához olvassa el a [Felhőszolgáltatás figyelésének áttekintése című témakört.](cloud-services-how-to-monitor.md#setup-diagnostics-extension)

Az összegyűjteni kívánt teljesítményszámlálókat a **diagnostics.wadcfgx** fájl határozza meg. Nyissa meg ezt a fájlt (szerepkörenként definiálva) a Visual Studióban, és keresse meg a **DiagnosticsConfiguration** > **PublicConfig** > **WadCfg** > **DiagnosticMonitorConfiguration** > **PerformanceCounters** elemet. Új **PerformanceCounterConfiguration** elem hozzáadása gyermekként. Ennek az elemnek `counterSpecifier` `sampleRate`két tulajdonsága van: és . Az `counterSpecifier` attribútum határozza meg, hogy az előző szakaszban melyik rendszerteljesítmény-számlálókészletet kell összegyűjteni. Az `sampleRate` érték azt jelzi, hogy milyen gyakran, hogy az érték lekérdezése. Összességében az összes teljesítményszámláló a szülőelem `PerformanceCounters` `scheduledTransferPeriod` attribútumértéke szerint kerül át az Azure-ba.

A sémaelemről az `PerformanceCounters` Azure [Diagnosztikai séma című](../azure-monitor/platform/diagnostics-extension-schema-windows.md#performancecounters-element)témakörben talál további információt.

Az attribútum által `sampleRate` meghatározott időszak az XML-időtartam adattípusát használja a teljesítményszámláló lekérdezésének gyakrani meghatározásához. Az alábbi példában a díj `PT3M`a `[P]eriod[T]ime[3][M]inutes`beállításra van állítva, ami azt jelenti: hárompercenként.

A definiálás `sampleRate` módjáról `scheduledTransferPeriod` a [W3 XML-dátum- és idődátumtípusok](https://www.w3schools.com/XML/schema_dtypes_date.asp) oktatóanyag **Időtartam adattípusa** című szakaszában talál további információt.

```xml
<?xml version="1.0" encoding="utf-8"?>
<DiagnosticsConfiguration  xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <PublicConfig>
    <WadCfg>
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096">

        <!-- ... cut to save space ... -->

        <PerformanceCounters scheduledTransferPeriod="PT1M">
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Requests/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Errors Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" />

          <!-- This is a new perf counter which will track the C: disk read activity in bytes per second, every minute -->
          <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(C:)\Disk Read Bytes/sec" sampleRate="PT1M" />

        </PerformanceCounters>
      </DiagnosticMonitorConfiguration>
    </WadCfg>
    
    <!-- ... cut to save space ... -->

  </PublicConfig>
</DiagnosticsConfiguration>
```

## <a name="create-a-new-perf-counter"></a>Új perf számláló létrehozása

A kód új teljesítményszámlálót hozhat létre és használhat. Az új teljesítményszámlálót létrehozó kódnak emelt szintű futtatást kell futtatnia, ellenkező esetben sikertelen lesz. A felhőszolgáltatás `OnStart` indítási kódja létrehozhatja a teljesítményszámlálót, amely megköveteli, hogy a szerepkört emelt szintű környezetben futtassa. Vagy létrehozhat egy indítási feladatot, amely emelt szintű futtatást és a teljesítményszámlálót hozza létre. Az indítási feladatokról további információt a [Felhőszolgáltatások indítási feladatainak konfigurálása és futtatása](cloud-services-startup-tasks.md)című témakörben talál.

Ha a szerepkört emelt szintű `<Runtime>` futtatásra szeretné beállítani, adjon hozzá egy elemet a [.csdef](cloud-services-model-and-package.md#servicedefinitioncsdef) fájlhoz.

```xml
<ServiceDefinition name="CloudServiceLoadTesting" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRoleWithSBQueue1" vmsize="Large">

    <!-- ... cut to save space ... -->

    <Runtime executionContext="elevated">
      
    </Runtime>

    <!-- ... cut to save space ... -->

  </WorkerRole>
</ServiceDefinition>
```

Néhány sornyi kóddal új teljesítményszámlálót hozhat létre és regisztrálhat. Használja `System.Diagnostics.PerformanceCounterCategory.Create` a metódus túlterhelés, amely létrehozza mind a kategóriát, és a számláló. A következő kód először ellenőrzi, hogy a kategória létezik-e, és ha hiányzik, létrehozza a kategóriát és a számlálót is.

```csharp
using System.Diagnostics;
using Microsoft.WindowsAzure;
using Microsoft.WindowsAzure.ServiceRuntime;

namespace WorkerRoleWithSBQueue1
{
    public class WorkerRole : RoleEntryPoint
    {
        // Perf counter variable representing times service was used.
        private PerformanceCounter counterServiceUsed;

        public override bool OnStart()
        {
            // ... Other startup code here ...

            // Define the category and counter names.
            string perfCounterCatName = "MyService";
            string perfCounterName = "Times Used";

            // Create the counter if needed. Our counter category only has a single counter.
            // Both the category and counter are created in the same method call.
            if (!PerformanceCounterCategory.Exists(perfCounterCatName))
            {
                PerformanceCounterCategory.Create(perfCounterCatName, "Collects information about the cloud service.", 
                                                  PerformanceCounterCategoryType.SingleInstance, 
                                                  perfCounterName, "How many times the cloud service was used.");
            }

            // Get reference to our counter
            counterServiceUsed = new PerformanceCounter(perfCounterCatName, perfCounterName);
            counterServiceUsed.ReadOnly = false;
            
            return base.OnStart();
        }

        // ... cut class code to save space
    }
}
```

Ha használni szeretné a számlálót, hívja meg a `Increment` vagy `IncrementBy` metódust.

```csharp
// Increase the counter by 1
counterServiceUsed.Increment();
```

Most, hogy az alkalmazás az egyéni számlálót használja, konfigurálnia kell az Azure Diagnostics vagy az Application Insights szolgáltatást a számláló nyomon követéséhez.


### <a name="application-insights"></a>Application Insights

Ahogy korábban említették, az Application Insights teljesítményszámlálói az **ApplicationInsights.config** fájlban vannak definiálva. Nyissa meg **az ApplicationInsights.config alkalmazást,** és keresse meg az **ApplicationInsights** > **TelemetryModules** > **Számlálók** **hozzáadása** > elemet. Hozzon `<Add>` létre egy `PerformanceCounter` gyermekelemet, és állítsa be az attribútumot a kódban létrehozott teljesítményszámláló kategóriájára és nevére. Állítsa `ReportAs` az attribútumot egy rövid névre, amelyet a portálon szeretne látni.

```xml
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">

  <TelemetryModules>

    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
      <Counters>
        <!-- ... cut other perf counters to save space ... -->

        <!-- This new perf counter matches the [category name]\[counter name] defined in your code -->
        <Add PerformanceCounter="\MyService\Times Used" ReportAs="Service used counter" />
      </Counters>
    </Add>

  </TelemetryModules>

<!-- ... cut to save space ... -->
```

### <a name="azure-diagnostics"></a>Azure Diagnostics

Ahogy korábban említettük, az összegyűjteni kívánt teljesítményszámlálók at a **diagnostics.wadcfgx** fájl határozza meg. Nyissa meg ezt a fájlt (szerepkörenként definiálva) a Visual Studióban, és keresse meg a **DiagnosticsConfiguration** > **PublicConfig** > **WadCfg** > **DiagnosticMonitorConfiguration** > **PerformanceCounters** elemet. Új **PerformanceCounterConfiguration** elem hozzáadása gyermekként. Állítsa `counterSpecifier` be az attribútumot a kódban létrehozott teljesítményszámláló kategóriájára és nevére. 

```xml
<?xml version="1.0" encoding="utf-8"?>
<DiagnosticsConfiguration  xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <PublicConfig>
    <WadCfg>
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096">

        <!-- ... cut to save space ... -->

        <PerformanceCounters scheduledTransferPeriod="PT1M">
          <!-- ... cut other perf counters to save space ... -->
          
          <!-- This new perf counter matches the [category name]\[counter name] defined in your code -->
          <PerformanceCounterConfiguration counterSpecifier="\MyService\Times Used" sampleRate="PT1M" />

        </PerformanceCounters>
      </DiagnosticMonitorConfiguration>
    </WadCfg>
    
    <!-- ... cut to save space ... -->

  </PublicConfig>
</DiagnosticsConfiguration>
```

## <a name="more-information"></a>További információ

- [Application Insights az Azure Cloud Servicesben](../azure-monitor/app/cloudservices.md#performance-counters)
- [Rendszerteljesítmény-számlálók az Application Insightsban](../azure-monitor/app/performance-counters.md)
- [Számláló elérési útjának megadása](https://msdn.microsoft.com/library/windows/desktop/aa373193(v=vs.85))
- [Azure diagnosztikai séma – teljesítményszámlálók](../azure-monitor/platform/diagnostics-extension-schema-windows.md#performancecounters-element)



