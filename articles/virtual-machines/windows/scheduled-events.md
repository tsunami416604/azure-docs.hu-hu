---
title: Ütemezett események Windows-beli virtuális gépek az Azure-ban |} A Microsoft Docs
description: Ütemezett események használata az Azure Metadata szolgáltatás számára a Windows virtuális gépeken.
services: virtual-machines-windows, virtual-machines-linux, cloud-services
documentationcenter: ''
author: ericrad
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: 28d8e1f2-8e61-4fbe-bfe8-80a68443baba
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: ericrad
ms.openlocfilehash: c9bd14128a6874f06983aa99ebb5a8a9a85843a2
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57550675"
---
# <a name="azure-metadata-service-scheduled-events-for-windows-vms"></a>Az Azure Metadata szolgáltatás: Windows virtuális gépeken ütemezett események

Az ütemezett események az Azure Metadata szolgáltatás, amely lehetővé teszi az alkalmazás idő fel a virtuális gép karbantartási. Információt ad a közelgő karbantartásokról események (például az újraindítás), az alkalmazás előkészítése őket, és korlátozza megszakítása. Érhető el minden olyan Azure virtuális gép esetében, beleértve a PaaS és IaaS Windows és Linux rendszereken egyaránt. 

A linuxon futó ütemezett eseményekkel kapcsolatos információkat, lásd: [ütemezett események Linux rendszerű virtuális gépek](../linux/scheduled-events.md).

> [!Note] 
> Az ütemezett események szolgáltatás általánosan elérhető az összes Azure-régióban. Lásd: [verzió és -régiók rendelkezésre állása](#version-and-region-availability) a legújabb kibocsátási információi.

## <a name="why-scheduled-events"></a>Miért ütemezett események?

Számos alkalmazás fel a virtuális gép karbantartási idő is kihasználhatják. Az idő, amelyek javítják a rendelkezésre állás, megbízhatóság és szervizelhetőségét beleértve alkalmazás adott feladatok elvégzéséhez használhatók: 

- Ellenőrzőpont és visszaállítás
- Kapcsolatkiürítés
- Elsődleges replika feladatátvétel 
- A load balancer készlet eltávolítása
- Eseménynaplózás
- Biztonságos leállításának 

Ütemezett események az alkalmazás használatával képes felderíteni, amikor karbantartási fog fordulhat elő, és korlátozhatja a hatása feladatok aktiválása. Az ütemezett események engedélyezése lehetővé teszi a virtuális gép egy minimális időtartama a karbantartási tevékenység végrehajtása előtt. Című esemény ütemezése alábbi részleteket.

Az ütemezett események biztosítja az események a következő esetekben használja:
- A platform által kezdeményezett karbantartás (pl. gazdagép operációsrendszer-frissítés)
- Felhasználó által kezdeményezett karbantartás (például felhasználói újraindítása vagy újbóli üzembe helyezése egy virtuális gép)

## <a name="the-basics"></a>Alapismeretek  

Az Azure Metadata szolgáltatás tesz elérhetővé az információkat a futó virtuális gépek hozzáférhetők-e a virtuális gép egy REST-végpont használatával. Az adatokat nem átirányítható IP-címen keresztül érhető el, így azt nem érhető el a virtuális gép kívül.

### <a name="endpoint-discovery"></a>Végpont felderítése
A virtuális Hálózatot használó virtuális gépekről, a metaadatok szolgáltatás érhető el statikus nem irányítható IP-cím, `169.254.169.254`. A teljes végpont az ütemezett eseményekről legújabb verzióját a következő: 

 > `http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01`

Ha a virtuális gép nem jön létre egy virtuális hálózatban, az alapértelmezett esetben a felhőszolgáltatásokat és a klasszikus virtuális gépeket, további logikát Fedezze fel az IP-cím használata szükséges. Tekintse meg ezt a mintát, megtudhatja, hogyan [a gazdagép-végpont felderítése](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm).

### <a name="version-and-region-availability"></a>Verzió és -régiók rendelkezésre állása
Az ütemezett események szolgáltatás nem rendszerverzióval ellátott. Verziók kötelező, és a jelenlegi verzió `2017-08-01`.

| Verzió | Kiadás típusa | Régiók | Kibocsátási megjegyzések | 
| - | - | - | - |
| 2017-08-01 | Általános rendelkezésre állás | Összes | <li> Aláhúzás kiegészített távolítva erőforrásnevek IaaS virtuális gépekhez<br><li>Metaadat-fejléc követelmény irányuló kérések kényszerítése | 
| 2017-03-01 | Előzetes verzió | Összes |<li>Kezdeti kiadás

> [!NOTE] 
> {Legújabb} api-verzió is támogatott, az ütemezett események korábbi előzetes kiadásokat. Ez a formátum már nem támogatott, és később elavulttá válik.

### <a name="enabling-and-disabling-scheduled-events"></a>Engedélyezése és letiltása az ütemezett események
Az ütemezett események a szolgáltatás az első alkalommal események kérelmet győződjön meg arról, engedélyezve van. Az első hívás akár két perc alatt számíthat késleltetett választ. Le kell kérdezni a végpontot, hogy rendszeres időközönként észleli a közelgő karbantartásokról eseményeket, valamint a karbantartási tevékenységek végrehajtása állapotát.

Az ütemezett események a szolgáltatás tiltva van, ha nem derül kérelem 24 órán keresztül.

### <a name="user-initiated-maintenance"></a>Felhasználó által kezdeményezett karbantartás
Felhasználó által kezdeményezett virtuális gépet karbantartás az Azure Portalon, API, CLI, vagy a PowerShell ütemezett esemény eredményez. Ez lehetővé teszi, hogy a karbantartási előkészítési logika tesztelheti az alkalmazásban, és lehetővé teszi, hogy az alkalmazás előkészítése a felhasználó által kezdeményezett karbantartás.

Egy esemény típusa és a virtuális gép újraindítása ütemezi `Reboot`. Egy esemény típusa és újbóli üzembe helyezés egy virtuális gép ütemezi `Redeploy`.

## <a name="using-the-api"></a>Az API-val

### <a name="headers"></a>Fejlécek
Előfordulhat, hogy a metaadat-szolgáltatás, meg kell adnia a fejléc `Metadata:true` annak érdekében, hogy a kérés nem volt szándékos átirányítva. A `Metadata:true` fejlécet meg kell adni az ütemezett események irányuló kérések. Nem sikerült a fejlécet tartalmazza a kérés a metaadatok szolgáltatásból hibás kérelem választ eredményez.

### <a name="query-for-events"></a>Lekérdezés-események
Ütemezett események a következő hívás által egyszerűen lekérdezés:

#### <a name="powershell"></a>PowerShell
```
curl http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01 -H @{"Metadata"="true"}
```

A válasz az ütemezett események tömbjét tartalmazza. Üres tömb, az azt jelenti, hogy jelenleg nem tartoznak események ütemezve.
Abban az esetben, ahol az ütemezett események, a válasz események tömbjét tartalmazza: 
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
A DocumentIncarnation ETag, és vizsgálja meg, ha az esemény hasznos adatai módosultak a legutóbbi lekérdezés óta egyszerű módot biztosít.

### <a name="event-properties"></a>Esemény tulajdonságai
|Tulajdonság  |  Leírás |
| - | - |
| EventId | Globálisan egyedi azonosítóját az eseményhez. <br><br> Példa: <br><ul><li>602d9444-d2cd-49c7-8624-8643e7171297  |
| EventType | Ez az esemény hatására a hatás. <br><br> Értékek: <br><ul><li> `Freeze`: A virtuális gép úgy van ütemezve, szüneteltetésére néhány másodpercig. A Processzor fel van függesztve, de nem érinti a memória, a megnyitott fájlokat vagy a hálózati kapcsolatok. <li>`Reboot`: A virtuális gép újraindításra van ütemezve (a nem állandó memória elvész). <li>`Redeploy`: A virtuális gép áthelyezése egy másik csomópontra van ütemezve (a rövid élettartamú lemezek elvesznek). |
| ResourceType | Ez az esemény hatással van az erőforrás típusát. <br><br> Értékek: <ul><li>`VirtualMachine`|
| További források| Ez az esemény hatással van az erőforrások listájában. Ez legfeljebb egy gépeket tartalmaznak garantáltan [frissítési tartományt](manage-availability.md), azonban nem tartalmazhat a UD minden gépek. <br><br> Példa: <br><ul><li> ["FrontEnd_IN_0", "BackEnd_IN_0"] |
| Eseményállapot | Ez az esemény állapota. <br><br> Értékek: <ul><li>`Scheduled`: Ez az esemény után a megadott ideig történő futásra van ütemezve a `NotBefore` tulajdonság.<li>`Started`: Ez az esemény feldolgozása megkezdődött.</ul> Nem `Completed` vagy hasonló állapota minden eddiginél áll rendelkezésre; az esemény már nem adható vissza, ha az esemény befejeződött.
| NotBefore| Az idő elteltével kezdheti el ezt az eseményt. <br><br> Példa: <br><ul><li> 19 Sep 2016 hétfő, 18:29:47 GMT  |

### <a name="event-scheduling"></a>Esemény ütemezése
Minden esemény van ütemezve egy jövőbeli időpontot minimális mennyiségű esemény típusa alapján. Most megjelenik egy esemény `NotBefore` tulajdonság. 

|EventType  | Minimális értesítés |
| - | - |
| Rögzítése| 15 perc |
| Újraindítás | 15 perc |
| Ismételt üzembe helyezés | 10 perc |

### <a name="event-scope"></a>Esemény hatókör     
Ütemezett kézbesíti az eseményeket:        
 - Összes virtuális gép egy Cloud Service-ben      
 - Összes virtuális gépet egy rendelkezésre állási csoportban      
 - Egy méretezési csoportban lévő összes virtuális gép elhelyezési csoport megadása         

Emiatt ellenőrizze a `Resources` a eseményhez, és azonosítani tudja az érintett virtuális gépek fogják mezőbe. 

### <a name="starting-an-event"></a>Egy esemény indítása 

Miután egy közelgő esemény megtanult és befejeződött a szabályos leállítást logika, a szálankénti függőben lévő eseményt jóváhagyhatja azáltal, hogy egy `POST` hívja a metaadatok szolgáltatáshoz a `EventId`. Ez azt jelzi, hogy az Azure-ba, lerövidítheti a minimális értesítési idő (ha lehet). 

Az alábbiakban található a várt json a `POST` kérelem törzse. A kérés tartalmazhat listáját `StartRequests`. Minden egyes `StartRequest` tartalmazza a `EventId` számára a kívánt esemény:
```
{
    "StartRequests" : [
        {
            "EventId": {EventId}
        }
    ]
}
```

#### <a name="powershell"></a>PowerShell
```
curl -H @{"Metadata"="true"} -Method POST -Body '{"StartRequests": [{"EventId": "f020ba2e-3bc0-4c40-a10b-86575a9eabd5"}]}' -Uri http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01
```

> [!NOTE] 
> Egy esemény bosszankodnak lehetővé teszi, hogy az összes folytatja az esemény `Resources` abban az esetben, nem csak a virtuális gép, amely elismeri az eseményt. Ezért dönthet úgy, hogy vezetőt koordinálása a visszaigazolás, az első gép egyszerűen, akkor a `Resources` mező.


## <a name="powershell-sample"></a>PowerShell-minta 

Az alábbi minta ütemezett események a metaadatok szolgáltatást, és hagyja jóvá a szálankénti függőben lévő eseményekhez.

```PowerShell
# How to get scheduled events 
function Get-ScheduledEvents($uri)
{
    $scheduledEvents = Invoke-RestMethod -Headers @{"Metadata"="true"} -URI $uri -Method get
    $json = ConvertTo-Json $scheduledEvents
    Write-Host "Received following events: `n" $json
    return $scheduledEvents
}

# How to approve a scheduled event
function Approve-ScheduledEvent($eventId, $uri)
{
    # Create the Scheduled Events Approval Document
    $startRequests = [array]@{"EventId" = $eventId}
    $scheduledEventsApproval = @{"StartRequests" = $startRequests} 
    
    # Convert to JSON string
    $approvalString = ConvertTo-Json $scheduledEventsApproval

    Write-Host "Approving with the following: `n" $approvalString

    # Post approval string to scheduled events endpoint
    Invoke-RestMethod -Uri $uri -Headers @{"Metadata"="true"} -Method POST -Body $approvalString
}

function Handle-ScheduledEvents($scheduledEvents)
{
    # Add logic for handling events here
}

######### Sample Scheduled Events Interaction #########

# Set up the scheduled events URI for a VNET-enabled VM
$localHostIP = "169.254.169.254"
$scheduledEventURI = 'http://{0}/metadata/scheduledevents?api-version=2017-08-01' -f $localHostIP 

# Get events
$scheduledEvents = Get-ScheduledEvents $scheduledEventURI

# Handle events however is best for your service
Handle-ScheduledEvents $scheduledEvents

# Approve events when ready (optional)
foreach($event in $scheduledEvents.Events)
{
    Write-Host "Current Event: `n" $event
    $entry = Read-Host "`nApprove event? Y/N"
    if($entry -eq "Y" -or $entry -eq "y")
    {
        Approve-ScheduledEvent $event.EventId $scheduledEventURI 
    }
}
``` 

## <a name="next-steps"></a>További lépések 

- Tekintse meg a [ütemezett események bemutató](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance) az Azure Friday. 
- Tekintse át az ütemezett eseményekről Kódminták a [Azure példány metaadatok ütemezett események GitHub-adattár](https://github.com/Azure-Samples/virtual-machines-scheduled-events-discover-endpoint-for-non-vnet-vm)
- További információ az elérhető API-k a [Instance Metadata szolgáltatás](instance-metadata-service.md).
- Ismerje meg [tervezett karbantartás az Azure-beli Windows virtuális gépek](planned-maintenance.md).
