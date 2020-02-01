---
title: Adatfolyam Azure Diagnosticsi az adatEvent Hubs
description: Event Hubs végpontok közötti Azure Diagnostics konfigurálása, beleértve a gyakori forgatókönyvekhez kapcsolódó útmutatást.
ms.service: azure-monitor
ms.subservice: diagnostic-extension
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/13/2017
ms.openlocfilehash: 111fab880887b54b2415d433bda2368c951381bd
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2020
ms.locfileid: "76901213"
---
# <a name="streaming-azure-diagnostics-data-in-the-hot-path-by-using-event-hubs"></a>Azure Diagnostics adatok továbbítása a gyors elérési úton a Event Hubs használatával
A Azure Diagnostics rugalmas módszereket biztosít a Cloud Services virtuális gépekről származó mérőszámok és naplók gyűjtésére, valamint az eredmények Azure Storage-ba történő átvitelére. Az 2016-as (SDK 2,9) időkerettől kezdve a diagnosztika elküldhető az egyéni adatforrásokhoz, és másodpercek alatt átviheti az elérési utat az [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)használatával.

A támogatott adattípusok a következők:

* A Windows esemény-nyomkövetés (ETW) eseményei
* Teljesítményszámlálók
* Windows-eseménynaplók, beleértve az alkalmazások naplóit a Windows eseménynaplóban
* Azure Diagnostics-infrastruktúranaplók

Ebből a cikkből megtudhatja, hogyan konfigurálhatja a Azure Diagnosticst Event Hubs a végpontok között. A következő gyakori forgatókönyvekhez is útmutatást nyújtunk:

* A Event Hubs eljuttatott naplók és metrikák testreszabása
* Konfigurációk módosítása az egyes környezetekben
* Event Hubs stream-adattartalom megtekintése
* A kapcsolódás hibáinak megoldása  

## <a name="prerequisites"></a>Előfeltételek
Az Azure Diagnostics Event Hubs adatok fogadása támogatott a Cloud Services, a virtuális gépek, a Virtual Machine Scale Sets és a Service Fabric esetében az Azure SDK 2,9-es és a Visual Studio-hoz kapcsolódó Azure-eszközök használatával.

* Azure Diagnostics Extension 1,6 ([Az Azure SDK for .net 2,9 vagy újabb verzió](https://azure.microsoft.com/downloads/) , amely alapértelmezés szerint ezt célozza meg)
* [Visual Studio 2013 vagy újabb](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
* A Azure Diagnostics meglévő konfigurációk egy alkalmazásban *. wadcfgx* -fájl és az alábbi módszerek egyikének használatával:
  * Visual Studio: [diagnosztika konfigurálása az Azure Cloud Services és Virtual Machines](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines)
  * Windows PowerShell: [diagnosztika engedélyezése az Azure-ban Cloud Services a PowerShell használatával](../../cloud-services/cloud-services-diagnostics-powershell.md)
* Event Hubs a cikk alapján kiépített névtér – első [lépések Event Hubs](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

## <a name="connect-azure-diagnostics-to-event-hubs-sink"></a>Azure Diagnostics összekötése Event Hubs fogadóval
Alapértelmezés szerint a Azure Diagnostics mindig egy Azure Storage-fiókba küldi a naplókat és a metrikákat. Az alkalmazások az Event Hubs számára is küldhetnek egy új **mosogató** szakaszt a *. wadcfgx* fájl **PublicConfig** / **WadCfg** eleme alatt. A Visual Studióban a *. wadcfgx* fájlt a következő elérési úton tárolja: **Cloud Service Project** > **roles** >  **(RoleName)**  > **Diagnostics. wadcfgx** fájl.

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

Ebben a példában az Event hub URL-címe az Event hub teljesen minősített névterére van beállítva: Event Hubs névtér + "/" + Event hub neve.  

Az Event hub URL-címe az Event Hubs irányítópultjának [Azure Portal](https://go.microsoft.com/fwlink/?LinkID=213885) jelenik meg.  

A **fogadó neve bármely** érvényes karakterláncra beállítható, ha a konfigurációs fájlban ugyanazt az értéket használja.

> [!NOTE]
> További mosogatók is lehetnek, például az ebben a szakaszban konfigurált *applicationInsights* . Azure Diagnostics egy vagy több elsüllyedés definiálását teszi lehetővé, ha az egyes gyűjtők is deklarálva vannak a **PrivateConfig** szakaszban.  
>
>

A Event Hubs fogadót is deklarálni kell, és meg kell határozni a *. wadcfgx* konfigurációs fájl **PrivateConfig** szakaszában.

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

A `SharedAccessKeyName` értéknek meg kell egyeznie egy megosztott elérési aláírás (SAS) kulcsával és a **Event Hubs** névtérben definiált házirenddel. Keresse meg a Event Hubs irányítópultot a [Azure Portalban](https://portal.azure.com), kattintson a **configure (Konfigurálás** ) fülre, és állítson be egy elnevezett szabályzatot (például "SendRule"), amely a *küldési* engedélyekkel rendelkezik. A **StorageAccount** a **PrivateConfig**-ben is deklarálva van. Itt nem kell módosítania az értékeket, ha működnek. Ebben a példában az értékeket üresen hagyjuk, ami azt a jele, hogy egy alsóbb rétegbeli eszköz beállítja az értékeket. Például a *ServiceConfiguration. Cloud. cscfg* környezeti konfigurációs fájl állítja be a környezetet – a megfelelő neveket és kulcsokat.  

> [!WARNING]
> A Event Hubs SAS-kulcsot egyszerű szövegként tárolja a *. wadcfgx* fájlban. Ez a kulcs gyakran be van jelölve a forráskód vezérlőelembe, vagy a Build-kiszolgáló eszközeként érhető el, ezért szükség szerint gondoskodni kell a védelemről. Javasoljuk, hogy itt *csak a küldési* engedélyek használatával használjon sas-kulcsot, hogy egy rosszindulatú felhasználó írjon az Event hub-ba, de ne hallgassa meg vagy ne kezelje.
>
>

## <a name="configure-azure-diagnostics-to-send-logs-and-metrics-to-event-hubs"></a>Azure Diagnostics konfigurálása naplók és metrikák küldéséhez Event Hubs
Ahogy korábban már említettük, az összes alapértelmezett és egyéni diagnosztikai adat, azaz a metrikák és naplók automatikusan az Azure Storage-ba lesznek küldve a beállított intervallumokban. A Event Hubs és bármely további fogadó használatával megadhatja a hierarchiában az Event hub-ba küldendő összes gyökér-vagy levél-csomópontot. Ide tartozik a ETW-események, a teljesítményszámlálók, a Windows-eseménynaplók és az alkalmazások naplói.   

Fontos figyelembe venni, hogy hány adatpontot kell ténylegesen átvinni a Event Hubsba. A fejlesztők általában az alacsony késésű, gyors elérésű, gyorsan felhasználható és értelmezhető adatok átvitelét használják. A riasztásokat vagy az autoskálázási szabályokat figyelő rendszerek példák. Egy fejlesztő alternatív elemzési tárolót vagy keresési tárolót is konfigurálhat – például Azure Stream Analytics, Elasticsearch, egyéni figyelési rendszer vagy a másoktól származó kedvenc figyelési rendszer.

Az alábbiakban néhány példa konfiguráció látható.

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

A fenti példában a rendszer a fogadót a hierarchia szülő **PerformanceCounters** csomópontján alkalmazza, ami azt jelenti, hogy az összes gyermek **PerformanceCounters** Event Hubs lesz elküldve.  

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

Az előző példában a fogadó csak három számlálóra van alkalmazva: a **kérelmek várólistára**helyezése, **visszautasított kérések**és a **processzoridő%-os ideje**.  

Az alábbi példa bemutatja, hogyan korlátozhatja a fejlesztő az elküldett adatok mennyiségét a szolgáltatás állapotához használt kritikus mérőszámoknak.  

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

Ebben a példában a fogadó a naplókra van alkalmazva, és csak a hiba szintű nyomkövetésre van szűrve.

## <a name="deploy-and-update-a-cloud-services-application-and-diagnostics-config"></a>Cloud Services alkalmazás és diagnosztika konfigurációjának központi telepítése és frissítése
A Visual Studio biztosítja az alkalmazás üzembe helyezéséhez és Event Hubs fogadó konfigurációjának legegyszerűbb elérési útját. A fájl megtekintéséhez és szerkesztéséhez nyissa meg a *. wadcfgx* fájlt a Visual Studióban, szerkessze, majd mentse. Az elérési út a **Cloud Service Project** > **roles** >  **(RoleName)**  > **Diagnostics. wadcfgx**.  

Ezen a ponton a Visual Studióban, a Visual Studio Team Systemben és az összes központi telepítési és központi telepítési frissítési művelet, valamint az MSBuild-on alapuló összes parancs vagy parancsfájl, valamint a `/t:publish` cél a *. wadcfgx* is tartalmazza a csomagolási folyamat során. Emellett a központi telepítések és frissítések az Azure-ba helyezik üzembe a fájlt a megfelelő Azure Diagnostics ügynök-bővítmény használatával a virtuális gépeken.

Az alkalmazás központi telepítése és a Azure Diagnostics konfigurálása után azonnal megjelenik a tevékenység az Event hub irányítópultján. Ez azt jelzi, hogy készen áll arra, hogy az Ön által választott figyelő-ügyfélen vagy elemzési eszközön megtekintse a gyors elérési út adatait.  

A következő ábrán a Event Hubs irányítópult a diagnosztikai adatok kifogástalan küldését mutatja be az Event hub számára 11 óra után. Ekkor az alkalmazás egy frissített *. wadcfgx* fájllal lett telepítve, és a fogadó megfelelően lett konfigurálva.

![][0]  

> [!NOTE]
> Ha frissíti a Azure Diagnostics konfigurációs fájlját (. wadcfgx), azt javasoljuk, hogy a frissítéseket a teljes alkalmazásra, valamint a konfigurációt a Visual Studio Publishing vagy egy Windows PowerShell-parancsfájl használatával küldje el.  
>
>

## <a name="view-hot-path-data"></a>A gyors elérési út adatok megtekintése
Ahogy korábban már említettük, számos felhasználási eset áll rendelkezésre Event Hubs adatainak figyelésére és feldolgozására. Egy egyszerű módszer egy kisméretű tesztelési konzolos alkalmazás létrehozása az Event hub figyeléséhez és a kimeneti adatfolyam kinyomtatásához. 

#### <a name="net-sdk-latest-500-or-latertablatest"></a>[.NET SDK – legújabb (5.0.0 vagy újabb)](#tab/latest)
A következő kódot helyezheti el, amely részletesen ismerteti az első [lépések a Event Hubs](../../event-hubs/get-started-dotnet-standard-send-v2.md)használatával című részt a konzol alkalmazásban.

```csharp
using System;
using System.Text;
using System.Threading.Tasks;
using Azure.Storage.Blobs;
using Azure.Messaging.EventHubs;
using Azure.Messaging.EventHubs.Processor;
namespace Receiver1204
{
    class Program
    {
        private static readonly string ehubNamespaceConnectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";
        private static readonly string eventHubName = "EVENT HUB NAME";
        private static readonly string blobStorageConnectionString = "AZURE STORAGE CONNECTION STRING";
        private static readonly string blobContainerName = "BLOB CONTAINER NAME";

        static async Task Main()
        {
            // Read from the default consumer group: $Default
            string consumerGroup = EventHubConsumerClient.DefaultConsumerGroupName;

            // Create a blob container client that the event processor will use 
            BlobContainerClient storageClient = new BlobContainerClient(blobStorageConnectionString, blobContainerName);

            // Create an event processor client to process events in the event hub
            EventProcessorClientOptions options = new EventProcessorClientOptions { }
            EventProcessorClient processor = new EventProcessorClient(storageClient, consumerGroup, ehubNamespaceConnectionString, eventHubName);

            // Register handlers for processing events and handling errors
            processor.ProcessEventAsync += ProcessEventHandler;
            processor.ProcessErrorAsync += ProcessErrorHandler;

            // Start the processing
            await processor.StartProcessingAsync();

            // Wait for 10 seconds for the events to be processed
            await Task.Delay(TimeSpan.FromSeconds(10));

            // Stop the processing
            await processor.StopProcessingAsync();
        }

        static Task ProcessEventHandler(ProcessEventArgs eventArgs)
        {
            Console.WriteLine("\tRecevied event: {0}", Encoding.UTF8.GetString(eventArgs.Data.Body.ToArray()));
            return Task.CompletedTask;
        }

        static Task ProcessErrorHandler(ProcessErrorEventArgs eventArgs)
        {
            Console.WriteLine($"\tPartition '{ eventArgs.PartitionId}': an unhandled exception was encountered. This was not expected to happen.");
            Console.WriteLine(eventArgs.Exception.Message);
            return Task.CompletedTask;
        }
    }
}
```

#### <a name="net-sdk-legacy-410-or-earliertablegacy"></a>[.NET SDK örökölt (4.1.0 vagy korábbi)](#tab/legacy)

A következő kódot helyezheti el, amely részletesen ismerteti az első [lépések a Event Hubs](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)használatával című részt a konzol alkalmazásban. Vegye figyelembe, hogy a konzol alkalmazásnak tartalmaznia kell az [Event Processor Host Nuget-csomagot](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/). Ne felejtse el lecserélni az értékeket a **fő** függvény szögletes zárójelei között az erőforrások értékeivel.   

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
            string eventHubConnectionString = "Endpoint= <your connection string>";
            string eventHubName = "<Event hub name>";
            string storageAccountName = "<Storage account name>";
            string storageAccountKey = "<Storage account key>";
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
---

## <a name="troubleshoot-event-hubs-sinks"></a>Event Hubs mosogatók hibáinak megoldása
* Az Event hub a várt módon nem jeleníti meg a bejövő vagy kimenő események tevékenységeit.

    Győződjön meg arról, hogy az Event hub sikeresen kiépítve. A *. Wadcfgx* **PrivateConfig** szakaszában található összes kapcsolatbiztonsági információnak meg kell egyeznie az erőforrásnak a portálon látható értékeivel. Győződjön meg arról, hogy a portálon van definiálva SAS-szabályzat ("SendRule"), és hogy a *küldési* engedély meg van adva.  
* A frissítés után az Event hub már nem jeleníti meg a bejövő vagy kimenő események tevékenységeit.

    Először ellenőrizze, hogy az Event hub és a konfigurációs adatok helyesek-e a korábban leírtak szerint. Előfordulhat, hogy a **PrivateConfig** alaphelyzetbe áll egy központi telepítési frissítésben. A javasolt javítás az, hogy az összes módosítást végrehajtsa a projektben *. wadcfgx* , majd leküldi a teljes alkalmazás frissítését. Ha ez nem lehetséges, győződjön meg arról, hogy a diagnosztikai frissítés leküld egy teljes **PrivateConfig** , amely tartalmazza az SAS-kulcsot.  
* Megpróbáltam a javaslatokat, és az Event hub még nem működik.

    Próbálja ki az Azure Storage-táblázatot, amely a naplókat és a hibákat tartalmazza Azure Diagnostics magáról: **WADDiagnosticInfrastructureLogsTable**. Az egyik lehetőség egy olyan eszköz használata, mint például a [Azure Storage Explorer](https://www.storageexplorer.com) az ehhez a Storage-fiókhoz való kapcsolódáshoz, a táblázat megtekintése és az időbélyegzőhöz tartozó lekérdezés hozzáadása az elmúlt 24 órában. Az eszközzel exportálhat egy. csv-fájlt, és megnyithatja azt egy alkalmazásban, például a Microsoft Excelben. Az Excel megkönnyíti a hívó kártyás karakterláncok, például a **EventHubs**keresését a jelentett hibák megtekintéséhez.  

## <a name="next-steps"></a>Következő lépések
• [További információ a Event Hubs](https://azure.microsoft.com/services/event-hubs/)

## <a name="appendix-complete-azure-diagnostics-configuration-file-wadcfgx-example"></a>Függelék: Azure Diagnostics konfigurációs fájl (. wadcfgx) teljes végrehajtása – példa
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

Az ehhez a példához tartozó kiegészítő *ServiceConfiguration. Cloud. cscfg* a következőhöz hasonlóan néz ki.

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

A virtuális gépek egyenértékű JSON-beállításai a következők:

Nyilvános beállítások:
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

Védett beállítások:
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

## <a name="next-steps"></a>Következő lépések
Az alábbi webhelyeken további információt talál az Event Hubsról:

* [Event Hubs – áttekintés](../../event-hubs/event-hubs-about.md)
* [Eseményközpont létrehozása](../../event-hubs/event-hubs-create.md)
* [Event Hubs – gyakori kérdések](../../event-hubs/event-hubs-faq.md)

<!-- Images. -->
[0]: ../../event-hubs/media/event-hubs-streaming-azure-diags-data/dashboard.png

