---
title: "Események ütemezett Windows-alapú virtuális gépek Azure-ban |} Microsoft Docs"
description: "Ütemezett eseményeket az Azure-metaadatok szolgáltatás a Windows virtuális gépeken."
services: virtual-machines-windows, virtual-machines-linux, cloud-services
documentationcenter: 
author: zivraf
manager: timlt
editor: 
tags: 
ms.assetid: 28d8e1f2-8e61-4fbe-bfe8-80a68443baba
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/14/2017
ms.author: zivr
ms.openlocfilehash: 7198fa8d1a512d10ca7022078aa2ea7bde3a4c02
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-metadata-service-scheduled-events-preview-for-windows-vms"></a>Az Azure metaadat-szolgáltatás: Ütemezett események (előzetes verzió) Windows virtuális gépek

> [!NOTE] 
> Az előzetes verziójú funkciók rendelkezésre álló, feltéve, hogy elfogadja a használati feltételeket. További részletekért lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

Ütemezett események egyike a subservices alatt az Azure metaadat-szolgáltatás. Felelős felszínre hozza a jövőbeni események kapcsolatos információk (például újraindítás), az alkalmazás előkészítése őket, és korlátozza megszakítása. Érhető el minden Azure virtuális gép esetében, beleértve a PaaS és IaaS. Ütemezett események időpontot a virtuális gép olyan esemény hatás minimalizálása érdekében megelőző feladatok elvégzéséhez. 

A Linux és a Windows virtuális gépek ütemezett események érhető el. Linux ütemezett eseményekkel kapcsolatos információkért lásd: [ütemezett események Linux virtuális gépek](../windows/scheduled-events.md).

## <a name="why-scheduled-events"></a>Miért ütemezett eseményeket?

Ütemezett események korlátozhatják a platform-intiated karbantartás vagy felhasználó által kezdeményezett műveletek a szolgáltatás lépéseket is igénybe vehet. 

Többpéldányos munkaterhelések esetén, amelyek replikációs technikák-állapot karbantartásához, védtelen maradhat a kimaradások esetén több példánya között történik. Pl. kimaradás költséges feladatokat (például újjáépítése indexek) vagy replika adatvesztést is okozhat. 

Sok egyéb esetben a teljes szolgáltatás rendelkezésre állása növelhető a szabályos leállítást sorozat végrehajtásával épp (vagy megszakítás alatt álló) üzenetsoroktól tranzakciók, a többi virtuális gép a fürtben (kézi feladatátvételre) feladatok újbóli, vagy a virtuális gép eltávolítása egy hálózati terheléselosztó-készlet betölteni. 

Előfordulhatnak olyan esetek, ahol egy jövőbeli eseményről rendszergazda értesítése vagy egy ilyen esemény naplózása érdekében a felhőalapú alkalmazások szervizelhetőségét javítása.

Az Azure metaadat-szolgáltatás ütemezett események Felfed, a következő esetekben használja:
-   A platform által kezdeményezett karbantartási (például a gazda operációs Rendszerével Bevezetés)
-   A felhasználó által kezdeményezett hívások (például felhasználó újraindítja vagy redeploys a virtuális gépek)


## <a name="the-basics"></a>Az alapok  

Azure metaadat-szolgáltatás elérhetővé teszi a virtuális gépek használatával érhető el a virtuális Gépen belül, a REST-végpont futtatásával kapcsolatos információkat. A érhető el információ egy nem átirányítható IP-cím segítségével, hogy nincs felfedve, a virtuális gép kívül.

### <a name="scope"></a>Hatókör
Ütemezett események illesztett összes virtuális gépet egy felhőalapú szolgáltatás, vagy egy rendelkezésre állási csoportban lévő összes virtuális gépet. Ennek eredményeképpen, jelölje be a `Resources` mező mellett az esemény azonosításához, amely a virtuális gépek is érinthet szeretné. 

### <a name="discovering-the-endpoint"></a>A végpont felderítése
Abban az esetben, ahol a rendszer létrehoz egy virtuális gép egy virtuális hálózatot (VNet), a metaadatok szolgáltatás nem érhető el egy statikus nem irányítható IP-cím, `169.254.169.254`.
Ha a virtuális gép nem hozza létre a virtuális hálózatról, az alapértelmezett eset felhőalapú szolgáltatásokhoz és a klasszikus virtuális gépeket, további logikát kell használandó végpont felderítése. Tekintse meg ezt a mintát megtudhatja, hogyan [a gazdagép-végpont felderítése](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm).

### <a name="versioning"></a>Verziókezelés 
A példány metaadat-szolgáltatás nem rendszerverzióval ellátott. Verziók kötelező, és a jelenlegi verzió: `2017-03-01`.

> [!NOTE] 
> Előző előzetes kiadásaiban ütemezett események {legújabb} támogatott api-verzióval. Ez a formátum már nem támogatott, és a jövőben elavulttá válik.

### <a name="using-headers"></a>Fejlécek használata
Ha a metaadat-szolgáltatás, meg kell adnia a fejléc `Metadata: true` annak érdekében, hogy nem szándékos átirányítja a kérést.

### <a name="enabling-scheduled-events"></a>Ütemezett események engedélyezése
Az első alkalommal ütemezett események kérelmet használhat Azure implicit módon lehetővé teszi, hogy a szolgáltatás a virtuális gépen. Ennek eredményeképpen kell késleltetett választ várt akár két perc alatt az első hívásakor.

### <a name="user-initiated-maintenance"></a>Felhasználó által kezdeményezett karbantartás
Felhasználó kezdeményezte a virtuális gép karbantartási Azure-portálon a, API-t CLI, vagy PowerShell ütemezett esemény eredményez. Ez lehetővé teszi, hogy a karbantartási előkészítése logika tesztelni az alkalmazás, és lehetővé teszi, hogy a felhasználó által kezdeményezett karbantartási előkészítése az alkalmazás.

A virtuális gép újraindítása ütemezi típusú esemény `Reboot`. Újratelepíteni a virtuális gépet ütemezi a következő típusú eseményre `Redeploy`.

> [!NOTE] 
> Jelenleg legfeljebb 10 felhasználó által kezdeményezett karbantartási műveletek egyidejűleg ütemezhető. Ezt a határt fog enyhíteni ütemezett események általánosan rendelkezésre álló előtt.

> [!NOTE] 
> Felhasználó által kezdeményezett karbantartást ütemezett esemény jelenleg nem konfigurálható. Beállíthatóság tervezünk-e egy későbbi kiadásban.

## <a name="using-the-api"></a>Az API-val

### <a name="query-for-events"></a>Lekérdezést eseményekhez.
Ütemezett események alapján is kereshet, egyszerűen azáltal, hogy a következő hívást:

```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2017-03-01
```

A válasz ütemezett események tömbjét tartalmazza. Üres tömb azt jelenti, hogy nincsenek-e jelenleg ütemezett események.
Abban az esetben, ahol ütemezett események, a válasz események tömbjét tartalmazza: 
```
{
    "DocumentIncarnation": {IncarnationID},
    "Events": [
        {
            "EventId": {eventID},
            "EventType": "Reboot" | "Redeploy" | "Freeze",
            "ResourceType": "VirtualMachine",
            "Resources": [{resourceName}],
            "EventStatus": "Scheduled" | "Started",
            "NotBefore": {timeInUTC},              
        }
    ]
}
```

### <a name="event-properties"></a>Esemény tulajdonságai
|Tulajdonság  |  Leírás |
| - | - |
| Eseményazonosító | Ez az esemény globálisan egyedi azonosítóját. <br><br> Példa: <br><ul><li>602d9444-d2cd-49c7-8624-8643e7171297  |
| Esemény típusa | Ez az esemény hatására hatása. <br><br> Értékek: <br><ul><li> `Freeze`: A virtuális gép felfüggesztése néhány másodpercig van ütemezve. A Processzor fel van függesztve, de nincs hatással a memória, a megnyitott fájlokat vagy a hálózati kapcsolatok. <li>`Reboot`: Újraindítás van ütemezve a virtuális gép (nem állandó memória nem vesztek el). <li>`Redeploy`: A virtuális gép áthelyezése egy másik csomópontra van ütemezve (a rövid élettartamú lemezek elvesznek). |
| ResourceType | Ez az esemény hatással van erőforrás típusát. <br><br> Értékek: <ul><li>`VirtualMachine`|
| Erőforrások| Ez az esemény hatással van erőforrások listáját. Ez magában foglalja a gépeket legalább egy garantáltan [frissítési tartomány](manage-availability.md), azonban nem tartalmazhat a UD összes gép. <br><br> Példa: <br><ul><li> ["FrontEnd_IN_0", "BackEnd_IN_0"] |
| Esemény állapota | Ez az esemény állapotát. <br><br> Értékek: <ul><li>`Scheduled`: Ez az esemény után a megadott ideig történő futásra van ütemezve a `NotBefore` tulajdonság.<li>`Started`: Ez az esemény elindult.</ul> Nem `Completed` vagy hasonló állapot valaha is biztosítja; az esemény már nem adható vissza, ha az esemény befejeződött.
| NotBefore| Az idő elteltével kezdheti el ezt az eseményt. <br><br> Példa: <br><ul><li> 2016-09-19T18:29:47Z  |

### <a name="event-scheduling"></a>Esemény ütemezése
Minden esemény van ütemezve egy jövőbeli időpontot minimális mennyiségű esemény típusa alapján. Most megjelenik egy esemény `NotBefore` tulajdonság. 

|Esemény típusa  | Minimális értesítés |
| - | - |
| Rögzítése| 15 perc |
| Újraindítás | 15 perc |
| Ismételt üzembe helyezés | 10 perc |

### <a name="starting-an-event"></a>Egy esemény indítása 

Miután egy jövőbeli esemény megtanulta, és a szabályos leállítást logikát befejeződött, a függőben lévő esemény jóváhagyhatja azáltal, hogy egy `POST` az metaadat-ba irányuló hívás a `EventId`. Ez azt jelzi, az Azure-ba, hogy azt a minimális értesítési lerövidíthető (amikor csak lehetséges). 

```
curl -H Metadata:true -X POST -d '{"DocumentIncarnation":"5", "StartRequests": [{"EventId": "f020ba2e-3bc0-4c40-a10b-86575a9eabd5"}]}' http://169.254.169.254/metadata/scheduledevents?api-version=2017-03-01
```

> [!NOTE] 
> Egy esemény igazolása lehetővé teszi, hogy folytatja az összes esemény `Resources` abban az esetben, nem csak a virtuális gépet, amely elismeri az esemény. Ezért választhatja, hogy egy vezető összehangolni a nyugtázási, akkor más dolga, mint az első machine kiválasztják a `Resources` mező.


## <a name="powershell-sample"></a>PowerShell-példa 

Az alábbi minta ütemezett események a metaadatok szolgáltatást, és hagyja jóvá a függőben lévő eseményekhez.

```PowerShell
# How to get scheduled events 
function GetScheduledEvents($uri)
{
    $scheduledEvents = Invoke-RestMethod -Headers @{"Metadata"="true"} -URI $uri -Method get
    $json = ConvertTo-Json $scheduledEvents
    Write-Host "Received following events: `n" $json
    return $scheduledEvents
}

# How to approve a scheduled event
function ApproveScheduledEvent($eventId, $docIncarnation, $uri)
{    
    # Create the Scheduled Events Approval Document
    $startRequests = [array]@{"EventId" = $eventId}
    $scheduledEventsApproval = @{"StartRequests" = $startRequests; "DocumentIncarnation" = $docIncarnation} 
    
    # Convert to JSON string
    $approvalString = ConvertTo-Json $scheduledEventsApproval

    Write-Host "Approving with the following: `n" $approvalString

    # Post approval string to scheduled events endpoint
    Invoke-RestMethod -Uri $uri -Headers @{"Metadata"="true"} -Method POST -Body $approvalString
}

function HandleScheduledEvents($scheduledEvents)
{
    # Add logic for handling events here
}

######### Sample Scheduled Events Interaction #########

# Set up the scheduled events URI for a VNET-enabled VM
$localHostIP = "169.254.169.254"
$scheduledEventURI = 'http://{0}/metadata/scheduledevents?api-version=2017-03-01' -f $localHostIP 

# Get events
$scheduledEvents = GetScheduledEvents $scheduledEventURI

# Handle events however is best for your service
HandleScheduledEvents $scheduledEvents

# Approve events when ready (optional)
foreach($event in $scheduledEvents.Events)
{
    Write-Host "Current Event: `n" $event
    $entry = Read-Host "`nApprove event? Y/N"
    if($entry -eq "Y" -or $entry -eq "y")
    {
        ApproveScheduledEvent $event.EventId $scheduledEvents.DocumentIncarnation $scheduledEventURI 
    }
}
``` 


## <a name="c-sample"></a>C\# minta 

Az alábbi minta nem egyszerű ügyfél, amely a metaadatokat szolgáltatással kommunikál.

```csharp
public class ScheduledEventsClient
{
    private readonly string scheduledEventsEndpoint;
    private readonly string defaultIpAddress = "169.254.169.254"; 

    // Set up the scheduled events URI for a VNET-enabled VM
    public ScheduledEventsClient()
    {
        scheduledEventsEndpoint = string.Format("http://{0}/metadata/scheduledevents?api-version=2017-03-01", defaultIpAddress);
    }

    // Get events
    public string GetScheduledEvents()
    {
        Uri cloudControlUri = new Uri(scheduledEventsEndpoint);
        using (var webClient = new WebClient())
        {
            webClient.Headers.Add("Metadata", "true");
            return webClient.DownloadString(cloudControlUri);
        }   
    }

    // Approve events
    public void ApproveScheduledEvents(string jsonPost)
    {
        using (var webClient = new WebClient())
        {
            webClient.Headers.Add("Content-Type", "application/json");
            webClient.UploadString(scheduledEventsEndpoint, jsonPost);
        }
    }
}
```

Ütemezett események jelölhető ki, a következő adatstruktúrák használatával:

```csharp
public class ScheduledEventsDocument
{
    public string DocumentIncarnation;
    public List<CloudControlEvent> Events { get; set; }
}

public class CloudControlEvent
{
    public string EventId { get; set; }
    public string EventStatus { get; set; }
    public string EventType { get; set; }
    public string ResourceType { get; set; }
    public List<string> Resources { get; set; }
    public DateTime? NotBefore { get; set; }
}

public class ScheduledEventsApproval
{
    public string DocumentIncarnation;
    public List<StartRequest> StartRequests = new List<StartRequest>();
}

public class StartRequest
{
    [JsonProperty("EventId")]
    private string eventId;

    public StartRequest(string eventId)
    {
        this.eventId = eventId;
    }
}
```

Az alábbi minta ütemezett események a metaadatok szolgáltatást, és hagyja jóvá a függőben lévő eseményekhez.

```csharp
public class Program
{
    static ScheduledEventsClient client;

    static void Main(string[] args)
    {
        client = new ScheduledEventsClient();

        while (true)
        {
            string json = client.GetDocument();
            ScheduledEventsDocument scheduledEventsDocument = JsonConvert.DeserializeObject<ScheduledEventsDocument>(json);

            HandleEvents(scheduledEventsDocument.Events);

            // Wait for user response
            Console.WriteLine("Press Enter to approve executing events\n");
            Console.ReadLine();

            // Approve events
            ScheduledEventsApproval scheduledEventsApprovalDocument = new ScheduledEventsApproval()
            {
                DocumentIncarnation = scheduledEventsDocument.DocumentIncarnation
            };
        
            foreach (CloudControlEvent event in scheduledEventsDocument.Events)
            {
                scheduledEventsApprovalDocument.StartRequests.Add(new StartRequest(event.EventId));
            }

            if (scheduledEventsApprovalDocument.StartRequests.Count > 0)
            {
                // Serialize using Newtonsoft.Json
                string approveEventsJsonDocument =
                    JsonConvert.SerializeObject(scheduledEventsApprovalDocument);

                Console.WriteLine($"Approving events with json: {approveEventsJsonDocument}\n");
                client.ApproveScheduledEvents(approveEventsJsonDocument);
            }

            Console.WriteLine("Complete. Press enter to repeat\n\n");
            Console.ReadLine();
            Console.Clear();
        }
    }

    private static void HandleEvents(List<CloudControlEvent> events)
    {
        // Add logic for handling events here
    }
}
```

## <a name="python-sample"></a>Python-minta 

Az alábbi minta ütemezett események a metaadatok szolgáltatást, és hagyja jóvá a függőben lévő eseményekhez.

```python
#!/usr/bin/python

import json
import urllib2
import socket
import sys

metadata_url = "http://169.254.169.254/metadata/scheduledevents?api-version=2017-03-01"
headers = "{Metadata:true}"
this_host = socket.gethostname()

def get_scheduled_events():
   req = urllib2.Request(metadata_url)
   req.add_header('Metadata', 'true')
   resp = urllib2.urlopen(req)
   data = json.loads(resp.read())
   return data

def handle_scheduled_events(data):
    for evt in data['Events']:
        eventid = evt['EventId']
        status = evt['EventStatus']
        resources = evt['Resources']
        eventtype = evt['EventType']
        resourcetype = evt['ResourceType']
        notbefore = evt['NotBefore'].replace(" ","_")
        if this_host in resources:
            print "+ Scheduled Event. This host is scheduled for " + eventype + " not before " + notbefore
            # Add logic for handling events here

def main():
   data = get_scheduled_events()
   handle_scheduled_events(data)
   
if __name__ == '__main__':
  main()
  sys.exit(0)
```

## <a name="next-steps"></a>Következő lépések 

- További információk az elérhető API-kat a [példány metaadat-szolgáltatás](instance-metadata-service.md).
- További tudnivalók [a tervezett karbantartások Windows virtuális gépek Azure-ban](planned-maintenance.md).

