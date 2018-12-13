---
title: Stream és az Event Hubs Azure Diagnostics-adatok
description: Azure Diagnostics konfigurálása az Event hubs szolgáltatással, teljes körű, beleértve a közös forgatókönyvre vonatkozó útmutatást.
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 07/13/2017
ms.author: robb
ms.component: diagnostic-extension
ms.openlocfilehash: 1f0b6ecae68a732e7d4bdc313ce9b249b387be8b
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2018
ms.locfileid: "53326191"
---
# <a name="streaming-azure-diagnostics-data-in-the-hot-path-by-using-event-hubs"></a>A gyakori elérésű útvonal az Azure Diagnostics-adatok streamelés az Event Hubs használatával
Az Azure diagnosztikai metrikák és naplók gyűjtésére felhőalapú szolgáltatások virtuális gépeken (VM) és az eredmények átvitele az Azure Storage rugalmas megoldásokat kínál. A 2016. március (SDK 2.9) időkereten belül kezdődően Diagnostics küldése az vlastní zdroje dat és adatátvitel gyakori elérésű útvonal másodpercek használatával [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/).

Támogatott adattípusok a következők:

* A Windows esemény-nyomkövetés (ETW) eseményei
* Teljesítményszámlálók
* Windows-eseménynaplók,
* Alkalmazásnaplók
* Azure Diagnostics-infrastruktúranaplók,

Ez a cikk bemutatja, hogyan Azure Diagnostics konfigurálása az Event Hubs teljes körű. Emellett útmutatást a következő gyakori helyzetek:

* A naplók és mérőszámok, amelyek az Event hubs szolgáltatásba küldi el a rendszer testreszabása
* Minden környezetben konfigurációk módosítása
* Az Event Hubs-adatok streamelése megtekintése
* A kapcsolat hibaelhárítása  

## <a name="prerequisites"></a>Előfeltételek
Event Hubs receieving Azure Diagnostics adatait a Cloud Services, a virtuális gépek, a Virtual Machine Scale Sets és a kezdődően az Azure SDK 2.9- és a megfelelő Azure-eszközök Visual Studióhoz készült Service Fabric használata támogatott.

* Az Azure Diagnostics bővítmény 1.6-os ([Azure SDK for .NET 2.9 használatát, vagy később](https://azure.microsoft.com/downloads/) célozza ez alapértelmezés szerint)
* [A Visual Studio 2013-as vagy újabb](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
* Az Azure Diagnostics használatával egy alkalmazásban meglévő konfigurációk egy *.wadcfgx* fájl- és a következő módszerek egyikét:
  * A Visual Studio: [Konfiguruje se Diagnostika Pro az Azure Cloud Services és Virtual Machines](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines)
  * Windows PowerShell: [Diagnosztika engedélyezése az Azure Cloud Services szolgáltatással a PowerShell használatával](../../cloud-services/cloud-services-diagnostics-powershell.md)
* Event Hubs-névtér kiosztása a cikkenként [Event Hubs használatának első lépései](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

## <a name="connect-azure-diagnostics-to-event-hubs-sink"></a>Csatlakozzon az Event Hubs fogadó Azure Diagnostics
Alapértelmezés szerint az Azure Diagnostics mindig küld naplókat és mérőszámokat egy Azure Storage-fiókot. Egy alkalmazás is továbbíthatja az adatokat az Event Hubs egy új hozzáadásával **fogadók** szakaszba a **PublicConfig** / **WadCfg** eleme a *. wadcfgx* fájlt. A Visual Studióban a *.wadcfgx* fájlt a következő elérési úton tárolja: **Cloud Service-projekt** > **szerepkörök** > **(RoleName)** > **diagnostics.wadcfgx** fájlt.

```xml
<SinksConfig>
  <Sink name="HotPath">
    <EventHub Url="https://diags-mycompany-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" />
  </Sink>
</SinksConfig>
```
```JSON
"SinksConfig": {
    "Sink": [
        {
            "name": "HotPath",
            "EventHub": {
                "Url": "https://diags-mycompany-ns.servicebus.windows.net/diageventhub",
                "SharedAccessKeyName": "SendRule"
            }
        }
    ]
}
```

Ebben a példában a teljes az event hubs-névtérhez az event hub URL-cím van beállítva: Event Hubs-névtér + "/" + eseményközpont neve.  

Az event hubs URL-cím jelenik meg a [az Azure portal](https://go.microsoft.com/fwlink/?LinkID=213885) az Event Hubs-irányítópulton.  

A **fogadó** neve bármilyen érvényes karakterlánc beállítható, mindaddig, amíg ugyanazt az értéket használja következetesen a konfigurációs fájlban.

> [!NOTE]
> Előfordulhat, további fogadóként, mint például *applicationInsights* ebben a szakaszban konfigurálni. Az Azure Diagnostics lehetővé teszi, hogy egy vagy több fogadóként kell definiálni, ha egyes fogadó is deklarálni kell a **PrivateConfig** szakaszban.  
>
>

Az Event Hubs fogadó is kell deklarált és definiált a **PrivateConfig** szakaszában a *.wadcfgx* konfigurációs fájlban.

```XML
<PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <StorageAccount name="{account name}" key="{account key}" endpoint="{optional storage endpoint}" />
  <EventHub Url="https://diags-mycompany-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" SharedAccessKey="{base64 encoded key}" />
</PrivateConfig>
```
```JSON
{
    "storageAccountName": "{account name}",
    "storageAccountKey": "{account key}",
    "storageAccountEndPoint": "{optional storage endpoint}",
    "EventHub": {
        "Url": "https://diags-mycompany-ns.servicebus.windows.net/diageventhub",
        "SharedAccessKeyName": "SendRule",
        "SharedAccessKey": "{base64 encoded key}"
    }
}
```

A `SharedAccessKeyName` értéknek egyeznie kell egy közös hozzáférésű Jogosultságkód (SAS) és egy szabályzatot, amely definiálva van a **az Event Hubs** névtér. Tallózással keresse meg az Event Hubs irányítópult a [az Azure portal](https://portal.azure.com), kattintson a **konfigurálása** lapra, és a egy elnevezett-szabályzat beállítása (például "SendRule"), amely rendelkezik *küldése* engedélyeket. A **StorageAccount** is van deklarálva a **PrivateConfig**. Hiba esetén nem kell itt értékeket módosítsa, ha működnek. Ebben a példában azt az értéket üresen hagyja, ez annak a jele, hogy egy alsóbb rétegbeli eszközök állítja az értékeket. Ha például a *ServiceConfiguration.Cloud.cscfg* környezet konfigurációs fájl beállítja a környezet megfelelő neveivel és kulcsaival.  

> [!WARNING]
> Az Event Hubs SAS-kulcsot az egyszerű szövegként tárolt a *.wadcfgx* fájlt. Gyakran előfordul ezt a kulcsot rendszer ellenőrzi a verziókövetési, vagy a lemezképfájl-kiszolgálóján, az eszköz érhető el, így szükség szerint kell védelemmel. Azt javasoljuk, hogy használja-e egy SAS-kulcs itt a *csak küldése* engedélyeit, hogy egy rosszindulatú felhasználó írhat az event hubs, de nem figyeli a következő, vagy kezelheti.
>
>

## <a name="configure-azure-diagnostics-to-send-logs-and-metrics-to-event-hubs"></a>Naplók és mérőszámok küldése az Event Hubsba, az Azure Diagnostics konfigurálása
Az összes alapértelmezett és egyéni diagnosztika adatok korábban, említett, azt jelenti, metrikák és naplók, automatikusan elküldi az Azure Storage a beállított időközönként. Az Event Hubs és minden olyan további fogadó legfelső szintű vagy levél csomópont is megadhat, az event hubs kell küldeni a hierarchiában. Ez magában foglalja az ETW-események, a teljesítményszámlálókat, a Windows-eseménynaplók és az alkalmazásnaplókat.   

Fontos figyelembe venni, hogyan sok adatpont ténylegesen átvinni az Event hubs szolgáltatásba. Általában a fejlesztők adatátvitel közel valós idejű gyakori elérési útja, amely felhasználható és értelmezni, gyorsan kell. Rendszerek, riasztások, vagy az automatikus skálázási szabályok monitorozása, példákat. A fejlesztők is előfordulhat, hogy egy másik elemző tárolót konfigurálása vagy keressen rá az áruházban – például az Azure Stream Analytics, az Elasticsearch, egy egyéni monitorozási rendszernek vagy a kedvenc monitorozási rendszer más tagjaitól származó.

Az alábbiakban néhány példa konfigurációkra.

```xml
<PerformanceCounters scheduledTransferPeriod="PT1M" sinks="HotPath">
  <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
</PerformanceCounters>
```
```JSON
"PerformanceCounters": {
    "scheduledTransferPeriod": "PT1M",
    "sinks": "HotPath",
    "PerformanceCounterConfiguration": [
        {
            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Memory\\Available MBytes",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Web Service(_Total)\\ISAPI Extension Requests/sec",
            "sampleRate": "PT3M"
        }
    ]
}
```

A fenti példában a fogadó és a szülő érvényes **PerformanceCounters** a hierarchiában, ami azt jelenti, hogy minden gyermek csomópont **PerformanceCounters** küld az Event hubs szolgáltatásba.  

```xml
<PerformanceCounters scheduledTransferPeriod="PT1M">
  <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" sinks="HotPath" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" sinks="HotPath"/>
  <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" sinks="HotPath"/>
</PerformanceCounters>
```
```JSON
"PerformanceCounters": {
    "scheduledTransferPeriod": "PT1M",
    "PerformanceCounterConfiguration": [
        {
            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
            "sampleRate": "PT3M",
            "sinks": "HotPath"
        },
        {
            "counterSpecifier": "\\Memory\\Available MBytes",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Web Service(_Total)\\ISAPI Extension Requests/sec",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\ASP.NET\\Requests Rejected",
            "sampleRate": "PT3M",
            "sinks": "HotPath"
        },
        {
            "counterSpecifier": "\\ASP.NET\\Requests Queued",
            "sampleRate": "PT3M",
            "sinks": "HotPath"
        }
    ]
}
```

Az előző példában a fogadó csak három számláló a alkalmazni: **Várólistán kérelmek**, **elutasított kérelmek**, és **processzoridő**.  

Az alábbi példa bemutatja, hogyan fejlesztő korlátozhatja a kritikus metrikák, a service health-használt elküldött adatok mennyiségét.  

```XML
<Logs scheduledTransferPeriod="PT1M" sinks="HotPath" scheduledTransferLogLevelFilter="Error" />
```
```JSON
"Logs": {
    "scheduledTransferPeriod": "PT1M",
    "scheduledTransferLogLevelFilter": "Error",
    "sinks": "HotPath"
}
```

Ebben a példában a fogadó naplók a alkalmazni, és a szűrt csak hiba történt a nyomkövetési szint.

## <a name="deploy-and-update-a-cloud-services-application-and-diagnostics-config"></a>Üzembe helyezése és a egy Cloud Services-alkalmazás és a diagnosztika konfiguráció frissítése
A Visual Studio, telepítheti az alkalmazást, és az Event Hubs fogadó konfigurációs a legegyszerűbb útvonalat biztosít. Megtekintheti, és szerkessze a fájlt, nyissa meg a *.wadcfgx* fájlt a Visual Studióban, szerkesztheti és mentheti. Az elérési út **Felhőszolgáltatás-projekt** > **szerepkörök** > **(RoleName)** > **diagnostics.wadcfgx**.  

Ezen a ponton az összes központi telepítés és frissítés a Visual Studio, Visual Studio Team System, és minden parancsok vagy parancsfájlok, amelyek MSBuild és a használat alapján műveleteket a **/t: Közzététel** cél közé tartozik a *.wadcfgx* a csomagolási folyamatban. Emellett központi telepítések és frissítések a fájl az Azure-bA segítségével telepítheti a megfelelő Azure Diagnostics-ügynök bővítményt a virtuális gépeken.

Az alkalmazás és az Azure Diagnostics-konfiguráció üzembe helyezése után az irányítópulton az event hubs tevékenység azonnal látni fogja. Ez azt jelzi, hogy készen áll, továbbléphet a figyelő ügyfél- vagy elemzési választott eszközzel, a ritkáról gyakori elérésű útvonal adatok megjelenítését.  

Az alábbi ábrán az Event Hubs-irányítópult látható, kifogástalan állapotú küldése a diagnosztikai adatok az event hubs 23 óra némi várakozás után kezdődik. Ha ez az alkalmazás lett telepítve, a frissített *.wadcfgx* fájlt, és a fogadó lett megfelelően konfigurálva.

![][0]  

> [!NOTE]
> Az Azure Diagnostics-konfigurációs fájlban (.wadcfgx) hajtsa végre a frissítéseket, ajánlott, hogy leküldi a frissítéseket a teljes alkalmazás, valamint a konfiguráció a Visual Studio közzététel, vagy egy Windows PowerShell-parancsprogram használatával.  
>
>

## <a name="view-hot-path-data"></a>Gyakori elérési út adatok megtekintése
Korábban említett nincsenek figyelését, és az Event Hubs adatfeldolgozás számos alkalmazási.

Egy egyszerű módja, hogy hozzon létre egy rövid tesztet konzolalkalmazást az event hubs figyelésére, és nyomtassa ki a kimeneti adatfolyamba. A következő kódra, amely részletes kifejtett elhelyezhet [Event Hubs használatának első lépései](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)), egy konzolalkalmazás.  

Vegye figyelembe, hogy a konzolalkalmazást tartalmaznia kell a [Event Processor Host NuGet-csomag](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/).  

Ne felejtse el kicserélni az értékeket a csúcsos zárójeleket a **fő** függvény az erőforrások értékekkel.   

```csharp
//Console application code for EventHub test client
using System;
using System.Collections.Generic;
using System.Diagnostics;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using Microsoft.ServiceBus.Messaging;

namespace EventHubListener
{
    class SimpleEventProcessor : IEventProcessor
    {
        Stopwatch checkpointStopWatch;

        async Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
        {
            Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
            if (reason == CloseReason.Shutdown)
            {
                await context.CheckpointAsync();
            }
        }

        Task IEventProcessor.OpenAsync(PartitionContext context)
        {
            Console.WriteLine("SimpleEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);
            this.checkpointStopWatch = new Stopwatch();
            this.checkpointStopWatch.Start();
            return Task.FromResult<object>(null);
        }

        async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
        {
            foreach (EventData eventData in messages)
            {
                string data = Encoding.UTF8.GetString(eventData.GetBytes());
                    Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
                        context.Lease.PartitionId, data));

                foreach (var x in eventData.Properties)
                {
                    Console.WriteLine(string.Format("    {0} = {1}", x.Key, x.Value));
                }
            }

            //Call checkpoint every 5 minutes, so that worker can resume processing from 5 minutes back if it restarts.
            if (this.checkpointStopWatch.Elapsed > TimeSpan.FromMinutes(5))
            {
                await context.CheckpointAsync();
                this.checkpointStopWatch.Restart();
            }
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            string eventHubConnectionString = "Endpoint= <your connection string>”
            string eventHubName = "<Event hub name>";
            string storageAccountName = "<Storage account name>";
            string storageAccountKey = "<Storage account key>”;
            string storageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", storageAccountName, storageAccountKey);

            string eventProcessorHostName = Guid.NewGuid().ToString();
            EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, eventHubName, EventHubConsumerGroup.DefaultGroupName, eventHubConnectionString, storageConnectionString);
            Console.WriteLine("Registering EventProcessor...");
            var options = new EventProcessorOptions();
            options.ExceptionReceived += (sender, e) => { Console.WriteLine(e.Exception); };
            eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>(options).Wait();

            Console.WriteLine("Receiving. Press enter key to stop worker.");
            Console.ReadLine();
            eventProcessorHost.UnregisterEventProcessorAsync().Wait();
        }
    }
}
```

## <a name="troubleshoot-event-hubs-sinks"></a>Az Event Hubs fogadóként hibaelhárítása
* Az event hubs nem jelenik meg a bejövő vagy kimenő esemény tevékenység elvárt módon.

    Ellenőrizze, hogy az eseményközpont sikeresen van kiépítve. Az összes kapcsolati adatok a **PrivateConfig** szakaszában *.wadcfgx* az erőforrás értékének egyeznie kell a portálon látható módon. Győződjön meg arról, hogy rendelkezik-e egy SAS-szabályzat definiált (a példában "SendRule" jelöli) a portálon, és hogy *küldése* az engedélyt.  
* Egy adott frissítés után az event hubs már bejövő vagy kimenő eseményhez kapcsolódó tevékenység jeleníti meg.

    Először győződjön meg arról, hogy az event hubot és a konfigurációs adatok megfelelő, korábban leírtak. Egyes esetekben a **PrivateConfig** alaphelyzetbe áll egy központi telepítési frissítés. A javasolt javítás szükséges összes módosításokat egy *.wadcfgx* a projektet, majd leküldéses egy teljes alkalmazás frissítése. Ha ez nem lehetséges, győződjön meg róla, hogy a diagnosztikai frissítés teljes leküldések **PrivateConfig** , amely tartalmazza az SAS-kulcsot.  
* Megpróbáltam a javaslatok, és az event hubs még mindig nem működik.

    Nézzük meg az Azure Storage-táblába, maga az Azure diagnosztikai naplók és a hibákat tartalmazó: **WADDiagnosticInfrastructureLogsTable**. Az egyik lehetőség például egy eszközzel [Azure Storage Explorer](http://www.storageexplorer.com) csatlakozni ehhez a tárfiókhoz, tekintse meg ezt a táblázatot, és adjon hozzá egy lekérdezést az időbélyeg az elmúlt 24 órában. Az eszköz segítségével egy .csv-fájlba exportálni, és nyissa meg például a Microsoft Excel alkalmazásban. Az Excel megkönnyíti a keresendő karakterláncok kártya, például **EventHubs**, milyen hibaüzenet megtekintéséhez.  

## <a name="next-steps"></a>További lépések
• [További információ az Event Hubs](https://azure.microsoft.com/services/event-hubs/)

## <a name="appendix-complete-azure-diagnostics-configuration-file-wadcfgx-example"></a>A függelék: Végezze el az Azure Diagnostics-konfigurációs fájl (.wadcfgx) például
```xml
<?xml version="1.0" encoding="utf-8"?>
<DiagnosticsConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <WadCfg>
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="applicationInsights.errors">
        <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error" />
        <Directories scheduledTransferPeriod="PT1M">
          <IISLogs containerName="wad-iis-logfiles" />
          <FailedRequestLogs containerName="wad-failedrequestlogs" />
        </Directories>
        <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="HotPath">
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Requests/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Errors Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" />
        </PerformanceCounters>
        <WindowsEventLog scheduledTransferPeriod="PT1M">
          <DataSource name="Application!*" />
        </WindowsEventLog>
        <CrashDumps>
          <CrashDumpConfiguration processName="WaIISHost.exe" />
          <CrashDumpConfiguration processName="WaWorkerHost.exe" />
          <CrashDumpConfiguration processName="w3wp.exe" />
        </CrashDumps>
        <Logs scheduledTransferPeriod="PT1M" sinks="HotPath" scheduledTransferLogLevelFilter="Error" />
      </DiagnosticMonitorConfiguration>
      <SinksConfig>
        <Sink name="HotPath">
          <EventHub Url="https://diageventhub-py-ns.servicebus.windows.net/diageventhub-py" SharedAccessKeyName="SendRule" />
        </Sink>
        <Sink name="applicationInsights">
          <ApplicationInsights />
          <Channels>
            <Channel logLevel="Error" name="errors" />
          </Channels>
        </Sink>
      </SinksConfig>
    </WadCfg>
    <StorageAccount>ACCOUNT_NAME</StorageAccount>
  </PublicConfig>
  <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <StorageAccount name="{account name}" key="{account key}" endpoint="{storage endpoint}" />
    <EventHub Url="https://diageventhub-py-ns.servicebus.windows.net/diageventhub-py" SharedAccessKeyName="SendRule" SharedAccessKey="YOUR_KEY_HERE" />
  </PrivateConfig>
  <IsEnabled>true</IsEnabled>
</DiagnosticsConfiguration>
```

A kiegészítő *ServiceConfiguration.Cloud.cscfg* az ebben a példában a következőhöz hasonló.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceConfiguration serviceName="MyFixItCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="3" osVersion="*" schemaVersion="2015-04.2.6">
  <Role name="MyFixIt.WorkerRole">
    <Instances count="1" />
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="YOUR_CONNECTION_STRING" />
    </ConfigurationSettings>
  </Role>
</ServiceConfiguration>
```

Virtuális gépek egyenértékű JSON beállításait a következőképpen történik:

Nyilvános beállításai:
```JSON
{
    "WadCfg": {
        "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": 4096,
            "sinks": "applicationInsights.errors",
            "DiagnosticInfrastructureLogs": {
                "scheduledTransferLogLevelFilter": "Error"
            },
            "Directories": {
                "scheduledTransferPeriod": "PT1M",
                "IISLogs": {
                    "containerName": "wad-iis-logfiles"
                },
                "FailedRequestLogs": {
                    "containerName": "wad-failedrequestlogs"
                }
            },
            "PerformanceCounters": {
                "scheduledTransferPeriod": "PT1M",
                "sinks": "HotPath",
                "PerformanceCounterConfiguration": [
                    {
                        "counterSpecifier": "\\Memory\\Available MBytes",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\Web Service(_Total)\\ISAPI Extension Requests/sec",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\Web Service(_Total)\\Bytes Total/Sec",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\ASP.NET Applications(__Total__)\\Requests/Sec",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\ASP.NET Applications(__Total__)\\Errors Total/Sec",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\ASP.NET\\Requests Queued",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\ASP.NET\\Requests Rejected",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                        "sampleRate": "PT3M"
                    }
                ]
            },
            "WindowsEventLog": {
                "scheduledTransferPeriod": "PT1M",
                "DataSource": [
                    {
                        "name": "Application!*"
                    }
                ]
            },
            "Logs": {
                "scheduledTransferPeriod": "PT1M",
                "scheduledTransferLogLevelFilter": "Error",
                "sinks": "HotPath"
            }
        },
        "SinksConfig": {
            "Sink": [
                {
                    "name": "HotPath",
                    "EventHub": {
                        "Url": "https://diageventhub-py-ns.servicebus.windows.net/diageventhub-py",
                        "SharedAccessKeyName": "SendRule"
                    }
                },
                {
                    "name": "applicationInsights",
                    "ApplicationInsights": "",
                    "Channels": {
                        "Channel": [
                            {
                                "logLevel": "Error",
                                "name": "errors"
                            }
                        ]
                    }
                }
            ]
        }
    },
    "StorageAccount": "{account name}"
}

```

Védett beállításai:
```JSON
{
    "storageAccountName": "{account name}",
    "storageAccountKey": "{account key}",
    "storageAccountEndPoint": "{storage endpoint}",
    "EventHub": {
        "Url": "https://diageventhub-py-ns.servicebus.windows.net/diageventhub-py",
        "SharedAccessKeyName": "SendRule",
        "SharedAccessKey": "YOUR_KEY_HERE"
    }
}
```

## <a name="next-steps"></a>További lépések
Az alábbi webhelyeken további információt talál az Event Hubsról:

* [Event Hubs – áttekintés](../../event-hubs/event-hubs-about.md)
* [Eseményközpont létrehozása](../../event-hubs/event-hubs-create.md)
* [Event Hubs – gyakori kérdések](../../event-hubs/event-hubs-faq.md)

<!-- Images. -->
[0]: ../../event-hubs/media/event-hubs-streaming-azure-diags-data/dashboard.png
