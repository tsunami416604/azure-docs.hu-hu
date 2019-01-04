---
title: Collect, a teljesítményszámlálók az Azure Cloud Servicesben |} A Microsoft Docs
description: Ismerje meg, hogyan derítheti fel, használata és teljesítményszámlálók létrehozása a Cloud Services, Azure Diagnostics és az Application Insights.
services: cloud-services
documentationcenter: .net
author: jpconnock
manager: timlt
editor: ''
ms.assetid: ''
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/18
ms.author: jeconnoc
ms.openlocfilehash: d332a792f693b099ce925e9b5705f09e52507522
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2019
ms.locfileid: "54000240"
---
# <a name="collect-performance-counters-for-your-azure-cloud-service"></a>Az Azure-felhőszolgáltatás teljesítményszámlálók gyűjtése

Teljesítményszámlálók nyújt arról, hogy az alkalmazás és a gazdagép hajt végre nyomon követéséhez. A Windows Server hardver, alkalmazások, az operációs rendszer és egyéb kapcsolódó számos különböző teljesítményszámlálókat biztosít. Begyűjti és a teljesítményszámlálók küldése az Azure-ba, elemezheti ezeket az információkat, így megalapozottabb döntéseket hozhat. 

## <a name="discover-available-counters"></a>Elérhető számlálók felderítése

A teljesítményszámláló két részből áll, a készlet nevét (más néven kategória) és a egy vagy több számlálók épül fel. PowerShell segítségével a rendelkezésre álló teljesítményszámlálók listája:

```PowerShell
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

A `CounterSetName` tulajdonság beállítása (vagy kategória) jelöli, és jól mutatja, milyen kapcsolódó a teljesítményszámlálókat. A `Paths` tulajdonság egy számlálót egy gyűjteményét képviseli. Sikerült érhet el a `Description` tulajdonság a számlálókészlet további információt.

A beállított összes számlálót lekéréséhez használja a `CounterSetName` értékét, és bontsa ki a `Paths` gyűjtemény. Mindegyik elérési út elem lekérdezheti, ha egy számlálót. Például az elérhető számlálók kapcsolatos lekérése a `Processor` állítsa be, bontsa ki a `Paths` gyűjtemény:

```PowerShell
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

Ezek az egyes számláló elérési utak adhatók hozzá a diagnosztikai keretrendszer a felhőszolgáltatás használja. A teljesítményszámláló-elérési út összeállított hogyan kapcsolatos további információkért lásd: [számláló elérési út megadását](https://msdn.microsoft.com/library/windows/desktop/aa373193(v=vs.85)).

## <a name="collect-a-performance-counter"></a>A teljesítményszámláló gyűjtése.

A cloud Services, Azure Diagnostics vagy az Application Insights-teljesítményszámláló is hozzáadhatók.

### <a name="application-insights"></a>Application Insights

A Cloud Services az Azure Application Insights lehetővé teszi, hogy Ön adja meg, hogy mely teljesítményszámlálókat szeretné gyűjteni. Miután [az Application Insights hozzáadása a projekthez](../azure-monitor/app/cloudservices.md#sdk), egy konfigurációs fájlt **ApplicationInsights.config** adnak hozzá a Visual Studio-projektben. A konfigurációs fájl határozza meg, milyen típusú információt az Application Insights összegyűjti, és az Azure-bA küldi.

Nyissa meg a **ApplicationInsights.config** fájlt és keresse meg a **ApplicationInsights** > **fájl TelemetryModules** elemet. Minden egyes `<Add>` gyermekelem egy telemetriai adatokat gyűjt, és a konfiguráció típusát határozza meg. A teljesítmény számláló telemetriai modultípus van `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector`. Ha ez az elem már definiálva van, vegye fel egy második alkalommal. Minden teljesítményszámláló gyűjtésére van definiálva egy nevű csomópont alatt `<Counters>`. Íme egy példa által gyűjtött teljesítményszámlálók meghajtó:

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

Minden teljesítményszámláló jelenik meg egy `<Add>` elemet `<Counters>`. A `PerformanceCounter` attribútum adja meg, hogy milyen teljesítményszámláló gyűjtése a. A `ReportAs` attribútum a cím megjelenítése a teljesítményszámláló az Azure Portalon. Bármilyen teljesítményszámláló gyűjtése, a rendszer elhelyezi egy kategóriát **egyéni** a portálon. Azure Diagnostics, ellentétben nem állítható be az időközt, a teljesítményszámlálók gyűjtése és Azure-ra. Az Application Insights teljesítményszámlálók gyűjtése és minden percben küldött. 

Az Application Insights automatikusan gyűjti a következő teljesítményszámlálókkal:

* \Process(??APP_WIN32_PROC??)\% A processzor kihasználtsága
* \Memory\Available Bytes
* \.NET CLR-kivételek (??APP_CLR_PROC??)\# az összes kivétel közül másodpercenként
* \Process(??APP_WIN32_PROC??)\Private Bytes
* \Process(??APP_WIN32_PROC??)\IO Data Bytes/sec
* \Processor(_Total)\% Processor Time

További információkért lásd: [rendszerteljesítmény-számlálók az Application Insights](../azure-monitor/app/performance-counters.md) és [Application Insights az Azure Cloud Services](../azure-monitor/app/cloudservices.md#performance-counters).

### <a name="azure-diagnostics"></a>Azure Diagnostics

> [!IMPORTANT]
> Ezeket az adatokat összesített értéket jelenít meg a tárfiókba, míg a portálon nem **nem** natív módon az adatokat diagram. Azt javasoljuk, hogy egy másik diagnostics szolgáltatást, például az Application Insights, az alkalmazásba integrálja.

A Cloud Services az Azure Diagnostics bővítmény lehetővé teszi, hogy Ön adja meg, hogy mely teljesítményszámlálókat szeretné gyűjteni. Azure Diagnostics beállításával kapcsolatban lásd: [Cloud Service-Figyelés áttekintése](cloud-services-how-to-monitor.md#setup-diagnostics-extension).

A gyűjtendő teljesítményszámlálókat meghatározott a **diagnostics.wadcfgx** fájlt. A Visual Studióban nyissa meg ezt a fájlt (van definiálva szerepkörönként), és keresse meg a **DiagnosticsConfiguration** > **PublicConfig** > **WadCfg**  >  **DiagnosticMonitorConfiguration** > **PerformanceCounters** elemet. Vegyen fel egy új **PerformanceCounterConfiguration** gyermek elemet. Ez az elem két attribútumokkal rendelkezik: `counterSpecifier` és `sampleRate`. A `counterSpecifier` attribútum adja meg, melyik gyűjtéséhez számláló (az előző szakaszban bekeretezett) beállítása a rendszer teljesítménye. A `sampleRate` érték azt jelzi, hogy milyen gyakran van kérdezi le azt az értéket. Egész minden teljesítményszámlálót átkerülnek az Azure-ban a szülő megfelelően `PerformanceCounters` elem `scheduledTransferPeriod` attribútum értéke.

További információ a `PerformanceCounters` sémaelem, tekintse meg a [Azure Diagnostics-séma](../azure-monitor/platform/diagnostics-extension-schema-1dot3.md#performancecounters-element).

A megadott időszakra a `sampleRate` az XML-időtartam adatokat írja be annak jelzésére, hogy milyen gyakran van kérdezi le azt a teljesítményszámláló attribútum használja. Az alábbi példában a sebesség értéke `PT3M`, ami azt jelenti, `[P]eriod[T]ime[3][M]inutes`: percen át 3 percenként.

További információ a `sampleRate` és `scheduledTransferPeriod` vannak meghatározott, lásd a **időtartama adattípus** című rész a [W3 XML dátum és idő dátum típusú](https://www.w3schools.com/XML/schema_dtypes_date.asp) oktatóanyag.

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

## <a name="create-a-new-perf-counter"></a>Hozzon létre egy új teljesítményszámlálója

Egy új teljesítményszámláló létrehozhatók és a kód által használt. A kód, amely létrehoz egy új teljesítményszámláló az emelt szintű, ellenkező esetben sikertelen lesz kell futnia. A felhőszolgáltatások `OnStart` indítási kódot a teljesítményszámláló, akkor a szerepkör futtatásához emelt szintű környezetben hozhat létre. Vagy létrehozhat egy indítási feladat, amely emelt szintű fut, és a teljesítményszámláló hoz létre. Indítási feladatok kapcsolatos további információkért lásd: [konfigurálása és a egy felhőalapú szolgáltatás indítási feladatok futtatásának](cloud-services-startup-tasks.md).

A szerepkör futtatásához emelt szintű konfigurálása, adjon hozzá egy `<Runtime>` elem a [.csdef](cloud-services-model-and-package.md#servicedefinitioncsdef) fájlt.

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

Hozzon létre, és regisztráljon egy új teljesítményszámláló néhány sornyi kóddal. Használja a `System.Diagnostics.PerformanceCounterCategory.Create` metódus túlterhelését, amely létrehozza a kategória és a számláló is. Az alábbi kód először ellenőrzi, ha a kategória létezik, és ha hiányoznak, mind a kategória, és ez a számláló hoz létre.

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

            // Define the cateogry and counter names.
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

Ha meg szeretné használni a számlálót, hívja a `Increment` vagy `IncrementBy` metódust.

```csharp
// Increase the counter by 1
counterServiceUsed.Increment();
```

Most, hogy az alkalmazás a. egyéni számláló használja, szüksége konfigurálása az Azure Diagnostics vagy az Application Insights nyomon követéséhez a számlálót.


### <a name="application-insights"></a>Application Insights

Korábban hangsúlyoztuk, a teljesítményszámlálók az Application Insights vannak meghatározva a **ApplicationInsights.config** fájlt. Nyissa meg **ApplicationInsights.config** , és keresse meg a **ApplicationInsights** > **fájl TelemetryModules** > **hozzáadása**  >  **Számlálók** elemet. Hozzon létre egy `<Add>` gyermekelemet és állítsa be a `PerformanceCounter` attribútumot a kategória és a teljesítményszámláló a kód szakaszban létrehozott nevét. Állítsa be a `ReportAs` attribútumot meg szeretné tekinteni a portálon egy rövid nevet.

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

Mint azt korábban említettük, a gyűjtendő teljesítményszámlálókat meghatározott a **diagnostics.wadcfgx** fájlt. A Visual Studióban nyissa meg ezt a fájlt (van definiálva szerepkörönként), és keresse meg a **DiagnosticsConfiguration** > **PublicConfig** > **WadCfg**  >  **DiagnosticMonitorConfiguration** > **PerformanceCounters** elemet. Vegyen fel egy új **PerformanceCounterConfiguration** gyermek elemet. Állítsa be a `counterSpecifier` attribútumot a kategória és a teljesítményszámláló a kód szakaszban létrehozott nevét. 

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

- [Az Application Insights az Azure Cloud Servicesben](../azure-monitor/app/cloudservices.md#performance-counters)
- [Az Application Insights rendszerteljesítmény-számlálók](../azure-monitor/app/performance-counters.md)
- [A számláló elérési út megadását](https://msdn.microsoft.com/library/windows/desktop/aa373193(v=vs.85))
- [Azure Diagnostics-séma - teljesítményszámlálók](../azure-monitor/platform/diagnostics-extension-schema-1dot3.md#performancecounters-element)