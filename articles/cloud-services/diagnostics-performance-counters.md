---
title: "Az Azure Felhőszolgáltatások teljesítményszámlálók gyűjtése |} Microsoft Docs"
description: "Ismerje meg, használata és teljesítményszámlálók létrehozása az Azure Diagnostics és az Application Insights Felhőszolgáltatások útmutató."
services: cloud-services
documentationcenter: .net
author: thraka
manager: timlt
editor: 
ms.assetid: 
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/18
ms.author: adegeo
ms.openlocfilehash: 3e0af48c172fa912f0ac9e05b7b761dd7eaad795
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="collect-performance-counters-for-your-azure-cloud-service"></a>Az Azure felhőalapú szolgáltatás a teljesítményszámlálók adatainak összegyűjtése

Teljesítményszámlálók teszik lehetővé az, hogy nyomon kövesse az alkalmazás és a gazdagép mennyire működnek. A Windows Server hardver, alkalmazások, az operációs rendszer és több kapcsolódó számos különböző teljesítményszámlálók biztosít. Begyűjthetik, és a teljesítményszámlálók küldése az Azure-ba, elemezheti az adatait a jobb döntéseket. 

## <a name="discover-available-counters"></a>Elérhető számlálók felderítése

A teljesítményszámláló két részből áll, a készlet neve (más néven kategória) és egy vagy több számlálók épül fel. PowerShell használatával a rendelkezésre álló teljesítményszámlálók listáját:

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

A `CounterSetName` tulajdonság a beállítása (vagy kategória), és egy jól jelzi, hogy mi kapcsolódó a teljesítményszámlálókat. A `Paths` tulajdonság készletének teljesítményszámlálók gyűjteményét jelképezi. Is lehetett beolvasni a `Description` további információt a számlálókészlet tulajdonság.

Összes számlálót állítja be, amelyet a `CounterSetName` értékét, és bontsa ki a `Paths` gyűjtemény. Mindegyik elérési út elem lekérheti számlálót. Ahhoz például, hogy a rendelkezésre álló kapcsolatos számlálókat beolvasása a `Processor` beállításához bontsa ki a `Paths` gyűjtemény:

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

Ezen egyéni számláló elérési utak adhatók hozzá a diagnosztikai keretrendszer a felhőalapú szolgáltatás használja. A teljesítményszámláló-elérési út összeállított hogyan kapcsolatos további információkért lásd: [a számláló elérési útjának](https://msdn.microsoft.com/library/windows/desktop/aa373193(v=vs.85)).

## <a name="collect-a-performance-counter"></a>A teljesítményszámláló gyűjtése.

A teljesítményszámláló felveheti az Azure Diagnostics és az Application Insights felhőszolgáltatáshoz.

### <a name="application-insights"></a>Application Insights

Azure Application Insights Felhőszolgáltatásai számára lehetővé teszi, akkor adja meg, milyen gyűjtendő kérelemteljesítmény-számlálókat. Miután [Application Insights hozzáadása a projekthez](../application-insights/app-insights-cloudservices.md#sdk), nevű konfigurációs fájl **ApplicationInsights.config** hozzáadódik a Visual Studio-projektet. A konfigurációs fájl határozza meg, milyen típusú információkat az Application Insights gyűjt, és az Azure-bA küldése.

Nyissa meg a **ApplicationInsights.config** fájlt, és keresse a **ApplicationInsights** > **TelemetryModules** elemet. Minden egyes `<Add>` gyermekelem telemetriai adatokat gyűjthet, és a konfigurálás egy típusa határozza meg. A teljesítmény számláló telemetriai modul típusa `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector`. Ha ez az elem már definiálva van, nem adja hozzá azt még egyszer. Minden teljesítményszámláló gyűjtéséhez definiálva egy csomópontot a `<Counters>`. Íme egy példa a meghajtó teljesítményszámlálók összegyűjtő:

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

Teljesítményszámlálók egy jelenik meg egy `<Add>` elem alatt `<Counters>`. A `PerformanceCounter` attribútum adja meg, milyen teljesítményszámláló gyűjtéséhez. A `ReportAs` attribútum a cím megjelenítése a teljesítményszámláló az Azure portálon. Minden gyűjtött számláló helyezni, kategóriát **egyéni** a portálon. Azure Diagnostics eltérően nem állítható be az időköz, a teljesítményszámlálók gyűjtése és az Azure-bA küldése. Az Application insights szolgáltatással teljesítményszámlálók gyűjtése és küld percenként. 

Az Application Insights automatikusan gyűjti a következő teljesítményszámlálókkal:

* \Process(??APP_WIN32_PROC??)\% A processzor kihasználtsága
* \Memory\Available Bytes
* \.NET CLR-kivételek (??APP_CLR_PROC??)\# az összes kivétel közül másodpercenként
* \Process(??APP_WIN32_PROC??)\Private Bytes
* \Process(??APP_WIN32_PROC??)\IO Data Bytes/sec
* \Processor(_Total)\% Processor Time

További információkért lásd: [rendszerteljesítmény-számlálók az Application Insightsban](../application-insights/app-insights-performance-counters.md) és [Application Insights Azure-szolgáltatásokhoz](../application-insights/app-insights-cloudservices.md#performance-counters).

### <a name="azure-diagnostics"></a>Azure Diagnostics

> [!IMPORTANT]
> Ezeket az adatokat a tárfiók összesítve, amíg a portál does **nem** biztosít a Diagramadat natív módot. Erősen ajánlott, hogy egy másik diagnosztika szolgáltatást, mint az Application Insights az alkalmazásba integrálja.

Az Azure Diagnostics a Felhőszolgáltatások bővítménnyel megadhatja, milyen gyűjtendő kérelemteljesítmény-számlálókat. Azure Diagnostics beállításához tekintse meg a [felhőalapú szolgáltatás Figyelés áttekintése](cloud-services-how-to-monitor.md#setup-diagnostics-extension).

A gyűjtendő kérelemteljesítmény-számlálókat vannak definiálva a **diagnostics.wadcfgx** fájlt. A Visual Studio (meg van adva egy szerepkör) fájl megnyitása, és keresse a **DiagnosticsConfiguration** > **PublicConfig** > **WadCfg**  >  **DiagnosticMonitorConfiguration** > **PerformanceCounters** elemet. Adjon hozzá egy új **PerformanceCounterConfiguration** elem gyermek. Ez az elem két attribútumokkal rendelkezik: `counterSpecifier` és `sampleRate`. A `counterSpecifier` attribútum adja meg, melyik számláló értéke (az előző szakaszban itt ismertetett) gyűjt rendszer teljesítményét. A `sampleRate` érték azt jelzi, hogy milyen gyakran van kérdezi le azt az értéket. A teljes teljesítményszámlálók átkerülnek Azure megfelelően a szülő `PerformanceCounters` elem `scheduledTransferPeriod` attribútum értéke.

További információ a `PerformanceCounters` schema elemben, tekintse meg a [Azure Diagnostics séma](../monitoring-and-diagnostics/azure-diagnostics-schema-1dot3-and-later.md#performancecounters-element).

Az által megadott idő a `sampleRate` XML időtartama adattípus annak jelzésére, hogy milyen gyakran van kérdezi le azt a teljesítményszámláló attribútum használja. Az alábbi példában a sebesség értéke `PT3M`, ami azt jelenti, `[P]eriod[T]ime[3][M]inutes`: három percenként.

További információt az `sampleRate` és `scheduledTransferPeriod` vannak definiálva, tekintse meg a **időtartam adattípus** szakasz a [W3 XML dátum és idő dátum típusú](https://www.w3schools.com/XML/schema_dtypes_date.asp) oktatóanyag.

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

Egy új teljesítményszámláló hozható létre és használja a kódot. A kódot, amely létrehoz egy új teljesítményszámláló futnia kell emelt szintű, ellenkező esetben sikertelen lesz. A felhőalapú szolgáltatás `OnStart` indítási kódot hozhat létre a teljesítményszámláló, nincs szükség a szerepkör futtatásához emelt szintű környezetben. Vagy emelt szintű fut, és létrehozza a teljesítményszámláló indítási tevékenységhez hozhat létre. Indítási feladatokkal kapcsolatos további információkért lásd: [hogyan lehet konfigurálni és futtatni egy felhőalapú szolgáltatás indítási feladatokat](cloud-services-startup-tasks.md).

A szerepkör futtatásához emelt szintű konfigurálása, vegye fel a `<Runtime>` elem a [.csdef](cloud-services-model-and-package.md#servicedefinitioncsdef) fájlt.

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

Hozzon létre, és regisztrálni egy új teljesítményszámláló néhány sornyi kódot. Használja a `System.Diagnostics.PerformanceCounterCategory.Create` metódus többszörös definíciót, amely a kategória és a számláló mind hoz létre. Az alábbi kód először ellenőrzi, ha a kategória létezik, és ha hiányoznak, mind a kategóriát, és a számláló hoz létre.

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

Ha a számláló használni kívánt, hívja az `Increment` vagy `IncrementBy` metódust.

```csharp
// Increase the counter by 1
counterServiceUsed.Increment();
```

Most, hogy az alkalmazás által az egyéni számláló, Azure Diagnostics vagy az Application Insights nyomon követéséhez a számláló konfigurálni kell.


### <a name="application-insights"></a>Application Insights

Korábban is hangsúlyoztuk, a teljesítményszámlálók az Application Insights vannak definiálva a **ApplicationInsights.config** fájlt. Nyissa meg **ApplicationInsights.config** keresse meg a **ApplicationInsights** > **TelemetryModules** > **hozzáadása**  >  **Számlálók** elemet. Hozzon létre egy `<Add>` gyermekelem és beállítása a `PerformanceCounter` attribútumot a kategória és a kód létrehozott teljesítményszámláló nevét. Állítsa be a `ReportAs` attribútumot meg szeretné tekinteni a portálon rövid nevét.

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

Már korábban is említettük, mint a gyűjtendő kérelemteljesítmény-számlálókat vannak definiálva a **diagnostics.wadcfgx** fájlt. A Visual Studio (meg van adva egy szerepkör) fájl megnyitása, és keresse a **DiagnosticsConfiguration** > **PublicConfig** > **WadCfg**  >  **DiagnosticMonitorConfiguration** > **PerformanceCounters** elemet. Adjon hozzá egy új **PerformanceCounterConfiguration** elem gyermek. Állítsa be a `counterSpecifier` attribútumot a kategória és a kód létrehozott teljesítményszámláló nevét. 

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

- [Az Application Insights az Azure Cloud Services csomag](../application-insights/app-insights-cloudservices.md#performance-counters)
- [Az Application Insightsban rendszerteljesítmény-számlálók](../application-insights/app-insights-performance-counters.md)
- [A számláló elérési útjának](https://msdn.microsoft.com/library/windows/desktop/aa373193(v=vs.85))
- [Az Azure Diagnostics séma - teljesítményszámlálók](../monitoring-and-diagnostics/azure-diagnostics-schema-1dot3-and-later.md#performancecounters-element)