---
title: Az adatfolyam Azure diagnosztikai adatokat az Event Hubs
description: Azure Diagnostics konfigurálása az Event Hubs végpontok közötti, beleértve a közös forgatókönyvre vonatkozó útmutatást.
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 07/13/2017
ms.author: robb
ms.component: diagnostic-extension
ms.openlocfilehash: 98e788d87b0ce03eece35868391aadd5233217b0
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35267713"
---
# <a name="streaming-azure-diagnostics-data-in-the-hot-path-by-using-event-hubs"></a>Adatfolyam-Azure diagnosztikai adatokat a gyakran használt adatok elérési útja az Event Hubs használatával
Az Azure Diagnostics metrikák és a naplók összegyűjtésére felhőalapú szolgáltatások virtuális gépek (VM) és az eredmények átvitele az Azure Storage rugalmas módszereket biztosítja. A 2016. március (SDK 2.9) időkereten belül-től kezdődően diagnosztika küldése egyéni adatforrások, működés közbeni elérési adatok átviteléhez az másodpercben használatával [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/).

Támogatott adattípusok a következők:

* A Windows esemény-nyomkövetés (ETW) eseményei
* Teljesítményszámlálók
* Windows-eseménynaplók,
* Alkalmazásnaplók
* Azure Diagnostics-infrastruktúranaplók,

Ez a cikk bemutatja, hogyan Azure Diagnostics konfigurálása az Event Hubs végpontok közötti. Útmutató a következő gyakori forgatókönyvek esetén is ismerteti:

* A naplók és metrikákat, az Event Hubs küldött testreszabása
* Minden környezetben konfigurációk módosítása
* Az Event Hubs adatfolyam adatok megtekintése
* A kapcsolat hibaelhárítása  

## <a name="prerequisites"></a>Előfeltételek
Event Hubs receieving Azure diagnosztikai adatait a Cloud Services, a virtuális gépek, a virtuálisgép-méretezési csoportok és a Service Fabric-től kezdve az Azure SDK 2.9 és a megfelelő Azure-eszközök Visual Studio támogatott.

* Az Azure Diagnostics bővítmény 1.6 ([Azure SDK for .NET 2.9 vagy újabb](https://azure.microsoft.com/downloads/) céloz ez alapértelmezés szerint)
* [A Visual Studio 2013 vagy újabb verzió](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
* Egy alkalmazás használatával az Azure Diagnostics meglévő konfigurációk egy *.wadcfgx* fájl- és a következő módszerek egyikét:
  * A Visual Studio: [diagnosztika konfigurálása az Azure Felhőszolgáltatások és virtuális gépek](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md)
  * A Windows PowerShell: [a PowerShell használata Azure Cloud Services diagnosztika engedélyezése](../cloud-services/cloud-services-diagnostics-powershell.md)
* Esemény hubok névtér kiosztása a cikkenként [Bevezetés az Event Hubs használatába](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

## <a name="connect-azure-diagnostics-to-event-hubs-sink"></a>Csatlakozás Azure Diagnostics Event Hubs fogadó
Alapértelmezés szerint Azure Diagnostics mindig küld naplókat, valamint a metrikák egy Azure Storage-fiókot. Egy alkalmazás is adatokat küldhet Event hubs egy új hozzáadásával **fogadók esetében** szakaszában a **PublicConfig** / **WadCfg** eleme a *. wadcfgx* fájlt. A Visual Studio a *.wadcfgx* fájl található a következő elérési úton: **Felhőszolgáltatás-projekt** > **szerepkörök** > **() RoleName)** > **diagnostics.wadcfgx** fájlt.

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

Ebben a példában az event hub URL-cím értéke az event hubs teljesen minősített névtere: az Event Hubs névtér + "/" + eseményközpont neveként.  

Az event hubs URL-cím jelenik meg a [Azure-portálon](http://go.microsoft.com/fwlink/?LinkID=213885) az Event Hubs irányítópulton.  

A **gyűjtése** mindaddig, amíg a ugyanazt az értéket használja a rendszer folyamatosan keresztül a konfigurációs fájl nevét állítható be bármilyen érvényes karakterláncot.

> [!NOTE]
> Előfordulhat, további felül, például a *applicationInsights* ebben a szakaszban konfigurálni. Az Azure Diagnostics lehetővé teszi, hogy egy vagy több mosdók kell megadni, ha mindegyik fogadó is deklarálva van a **PrivateConfig** szakasz.  
>
>

Az Event Hubs fogadó is kell deklarált és definiált a **PrivateConfig** szakasza a *.wadcfgx* konfigurációs fájlban.

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

A `SharedAccessKeyName` értékének meg kell felelnie egy közös hozzáférésű Jogosultságkód (SAS) és egy házirendet, amely definiálva van a **Event Hubs** névtér. Keresse meg az Event Hubs irányítópult a [Azure-portálon](https://portal.azure.com), kattintson a **konfigurálása** lapot, és hozzon létre egy elnevezett házirendet (például "SendRule"), amely rendelkezik *küldése* engedélyek. A **StorageAccount** deklarálva van a **PrivateConfig**. Nincs szükség itt értékeket módosíthatja, ha működnek. Ebben a példában azt az értéket üresen hagyja, ez az a jele, hogy egy alárendelt eszköz állítja be az értékeket. Például a *ServiceConfiguration.Cloud.cscfg* környezet konfigurációs fájl beállítja a környezet megfelelő neveket és a kulcsokat.  

> [!WARNING]
> Az Event Hubs SAS-kulcsot a egyszerű szövegként vannak tárolva a *.wadcfgx* fájlt. Gyakran ezt a kulcsot rendszer ellenőrzi, hogy verziókövetési vagy érhető el a build Server eszközként, szükség szerint kell védeni. Azt javasoljuk, hogy egy SAS-kulcsot használ itt a *csak küldése* engedélyeit, hogy egy rosszindulatú felhasználó írhat az event hubs, de nem hallgatni vagy az adatbázis felügyeletét.
>
>

## <a name="configure-azure-diagnostics-to-send-logs-and-metrics-to-event-hubs"></a>Azure diagnosztikai naplók és a metrikák küldeni az Event Hubs konfigurálása
Című szakaszban leírtaknak megfelelően korábban, az összes alapértelmezett és egyéni diagnosztikai adatainak, ez azt jelenti, metrikákat és a naplókat, automatikusan küldése Azure Storage beállított. Az Event Hubs és a további fogadó adja meg a gyökér vagy a levél csomópont az event hubs küldendő a hierarchiában. Ez magában foglalja az ETW-események, a teljesítményszámlálók, a Windows eseménynaplóiban keresse meg és az alkalmazásnaplók.   

Fontos figyelembe venni, hogy hány adatpontok ténylegesen átviszi az Event Hubs. Általában a fejlesztők adatátvitelt kis késleltetésű közbeni elérési útja, amely kell használni, és gyorsan értelmezi. Riasztások vagy az automatikus skálázási szabályok figyelő rendszerek példák. A fejlesztők is előfordulhat, hogy egy másik elemzési tároló konfigurálása és keressen rá az áruházban – például Azure Stream Analytics, Elasticsearch, egy egyéni figyelési rendszer vagy más kedvenc figyelési rendszer.

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

A fenti példában a fogadó és a szülő érvényes **PerformanceCounters** a hierarchiában, ami azt jelenti, hogy minden gyermek csomópont **PerformanceCounters** Event Hubs kapnak.  

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

Az előző példában a gyűjtő csak három számláló alkalmazzák: **kérései**, **elutasítja kérelmeket**, és **processzoridő százalékos**.  

A következő példa bemutatja, hogyan egy fejlesztő korlátozhatja a fontos metrikák e szolgáltatás állapotát a használt elküldött adatok mennyisége.  

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

Ebben a példában a gyűjtő naplók alkalmazott, és csak a hiba szintű nyomkövetési szűrve van.

## <a name="deploy-and-update-a-cloud-services-application-and-diagnostics-config"></a>Központi telepítése és a Cloud Services alkalmazás- és diagnosztikai konfiguráció frissítése
A Visual Studio az alkalmazás és az Event Hubs fogadó konfigurációs központi telepítése a legegyszerűbb útvonalat biztosít. Megtekintheti és szerkesztheti a fájlt, nyissa meg a *.wadcfgx* fájlt a Visual Studio, szerkesztheti és mentheti. Az elérési út **Felhőszolgáltatás-projekt** > **szerepkörök** > **(RoleName)** > **diagnostics.wadcfgx**.  

Ezen a ponton az összes telepítési és telepítési műveletek frissítése a Visual Studio, a Visual Studio Team System, és minden parancsokkal és parancsprogramokkal MSBuild és -felhasználási alapuló a **/t: közzététele** cél közé tartozik a *.wadcfgx* a csomagolási folyamatban. Emellett központi telepítések és frissítések a fájl Azure segítségével telepítheti a megfelelő Azure diagnosztikai ügynök bővítményt a virtuális gépeken.

Az alkalmazás és az Azure diagnosztikai konfiguráció telepítése után az irányítópulton az event hubs tevékenység azonnal látni fogja. Ez azt jelzi, hogy készen áll áthelyezése a közbeni elérési adatok megtekintése az Ön által választott figyelő ügyfél vagy elemző eszközben.  

Az alábbi ábrán az Event Hubs irányítópult jeleníti meg, kifogástalan küldése az event hubs némi várakozás után 23 óra kezdő diagnosztikai adatok. Ha ez az alkalmazás lett telepítve a frissített *.wadcfgx* fájlt, és a fogadó lett megfelelően konfigurálva.

![][0]  

> [!NOTE]
> Amikor módosításokat az Azure Diagnostics konfigurációs fájljában (.wadcfgx), javasoljuk, hogy leküldéses a frissítések a teljes alkalmazás, valamint a konfigurációs Visual Studio közzététel, vagy a Windows PowerShell-parancsfájl használatával.  
>
>

## <a name="view-hot-path-data"></a>Közbeni elérési adatok megtekintése
Korábban bemutatott, nincsenek sok használati esetek figyelését, és az Event Hubs adatok feldolgozása.

Egy egyszerű megoldás, az event hubs figyelését, valamint a kimeneti adatfolyamba nyomtatása egy kis teszt Konzolalkalmazás létrehozásához. Az alábbi kód, amely részletesen kifejtett elhelyezheti [Bevezetés az Event Hubs használatába](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)), egy konzolalkalmazásban.  

Vegye figyelembe, hogy a konzol alkalmazásnak tartalmaznia kell a [esemény processzor állomás NuGet-csomag](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/).  

Ne felejtse el lecserélni az értéket a csúcsos zárójelek a **fő** függvény erőforrások értékekkel.   

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

## <a name="troubleshoot-event-hubs-sinks"></a>Az Event Hubs mosdók hibaelhárítása
* Az event hubs nem szerepelnek a bejövő vagy kimenő eseményhez kapcsolódó tevékenység várt módon.

    Ellenőrizze, hogy az eseményközpont sikeresen lett kiépítve. Az összes Kapcsolatinformáció a **PrivateConfig** szakasza *.wadcfgx* az erőforrás értékének egyeznie kell, mint a portálon. Győződjön meg arról, hogy rendelkezik-e a biztonsági Társítások házirend lett meghatározva (a példában szereplő "SendRule" jelöli), valamint a portál *küldése* engedélyt.  
* Egy adott frissítés után az event hubs eseményközponthoz már nem bejövő vagy kimenő eseményhez kapcsolódó tevékenység jeleníti meg.

    Először ellenőrizze, hogy a event hub és konfigurációs adatokat megfelelő, korábban leírtak szerint. Egyes esetekben a **PrivateConfig** az egy központi telepítési alaphelyzetbe áll. A javasolt javítás szükséges összes módosításokat egy *.wadcfgx* a projektet, majd a teljes alkalmazás frissítés leküldéses. Ha ez nem lehetséges, győződjön meg arról, hogy a diagnosztika frissítés leküldéses értesítések teljes **PrivateConfig** , amely tartalmazza az SAS-kulcsot.  
* A javaslatok próbáltam, és az event hubs továbbra sem működik.

    Nézzük meg az Azure Storage táblázatban maga Azure diagnosztikai naplók és a hibákat tartalmazó: **WADDiagnosticInfrastructureLogsTable**. Egy elem egy eszközzel, mint [Azure Tártallózó](http://www.storageexplorer.com) csatlakozni ehhez a tárfiókhoz, ebben a táblázatban megtekintheti, és adja hozzá a lekérdezés az időbélyegzési az elmúlt 24 órában. Az eszköz segítségével exportálja egy CSV-fájlt, majd nyissa meg például a Microsoft Excel alkalmazásban. Excel segítségével egyszerűen-kártya karakterláncokat, például keresni **EventHubs**, hogy milyen hibát jelez.  

## <a name="next-steps"></a>További lépések
• [További információ az Event Hubs](https://azure.microsoft.com/services/event-hubs/)

## <a name="appendix-complete-azure-diagnostics-configuration-file-wadcfgx-example"></a>A függelék: Végezze el az Azure Diagnostics konfigurációs fájl (.wadcfgx) – Példa
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

A virtuális gépek egyenértékű alapú Json-beállítások a következőképpen történik:
```JSON
"settings": {
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


"protectedSettings": {
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

* [Event Hubs – áttekintés](../event-hubs/event-hubs-what-is-event-hubs.md)
* [Eseményközpont létrehozása](../event-hubs/event-hubs-create.md)
* [Event Hubs – gyakori kérdések](../event-hubs/event-hubs-faq.md)

<!-- Images. -->
[0]: ../event-hubs/media/event-hubs-streaming-azure-diags-data/dashboard.png
