---
title: "Események ütemezett Windows-alapú virtuális gépek Azure-ban |} Microsoft Docs"
description: "Ütemezett eseményeket az Azure-metaadatok szolgáltatás a Windows virtuális gépeken."
services: virtual-machines-windows, virtual-machines-linux, cloud-services
documentationcenter: 
author: ericrad
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
ms.author: ericrad
ms.openlocfilehash: c217934f835a3926a3246de881d31601a9d58e04
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/24/2018
---
# <a name="azure-metadata-service-scheduled-events-for-windows-vms"></a>Azure metaadat-szolgáltatás: Windows virtuális gépek ütemezett eseménye

Ütemezett események egy Azure metaadat-szolgáltatás, amely az alkalmazás időpontot készíti elő a virtuális gép karbantartása. Információt ad a közelgő karbantartásokról események (pl. újraindítás), az alkalmazás előkészítése őket, és korlátozza megszakítása. Akkor érhető el, beleértve a PaaS és a Windows és Linux IaaS minden Azure virtuális gép esetében. 

Linux ütemezett eseményekkel kapcsolatos információkért lásd: [ütemezett események Linux virtuális gépek](../linux/scheduled-events.md).

> [!Note] 
> Általában ütemezett események érhető el az összes Azure-régió. Lásd: [verzióját és régiónkénti elérhetőség](#version-and-region-availability) legújabb kiadás információt.

## <a name="why-scheduled-events"></a>Miért ütemezett eseményeket?

Számos alkalmazás is kihasználhatja a le készíti elő a virtuális gép karbantartási idő. Az idő, amelyek javítják a rendelkezésre állás, a megbízhatóság és a szervizelhetőségét például alkalmazás-specifikus feladatok végrehajtására használható: 

- Ellenőrzőpont- és visszaállítása
- A kapcsolat kiürítését
- Elsődleges replikák közötti feladatátvétel 
- A load balancer készlet eltávolítása
- Események naplózása
- Biztonságos leállításának 

Ha karbantartási fog fordulhat elő, és korlátozza a hatását feladatot indít felderíthetők az ütemezett eseményeket az alkalmazás használ.  

Ütemezett események biztosítja az események a következő esetekben használja:
- A platform által kezdeményezett karbantartási (pl. a gazda operációs rendszer frissítési)
- Felhasználó által kezdeményezett karbantartási (pl. felhasználó újraindítja vagy a virtuális gépek redeploys)

## <a name="the-basics"></a>Az alapok  

Azure metaadat-szolgáltatás elérhetővé teszi a virtuális gépek használatával érhető el a virtuális Gépen belül, a REST-végpont futtatásával kapcsolatos információkat. A érhető el információ egy nem átirányítható IP-cím segítségével, hogy nincs felfedve, a virtuális gép kívül.

### <a name="endpoint-discovery"></a>Végpont felderítése
Virtuális hálózat virtuális gépek engedélyezve van, a metaadat-szolgáltatás nem érhető el egy statikus nem irányítható IP-cím, `169.254.169.254`. A teljes végpont ütemezett események legújabb verziója van: 

 > `http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01`

Ha a virtuális gép nem hozza létre a virtuális hálózatról, az alapértelmezett eset felhőalapú szolgáltatásokhoz és a klasszikus virtuális gépeket, további logikát kell Fedezze fel az IP-cím használatára. Tekintse meg ezt a mintát megtudhatja, hogyan [a gazdagép-végpont felderítése](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm).

### <a name="version-and-region-availability"></a>Verzió és régiónkénti elérhetőség
Az ütemezett események szolgáltatás nem rendszerverzióval ellátott. Verziók kötelező, és a jelenlegi verzió: `2017-08-01`.

| Verzió | Kiadás típusa | Régiók | Kibocsátási megjegyzések | 
| - | - | - | - |
| 2017-08-01 | Általános rendelkezésre állás | Összes | <li> $A aláhúzás távolítva erőforrásnevek Iaas virtuális gépekhez<br><li>Az összes kérelem érvényes metaadat-fejléccel követelmény | 
| 2017-03-01 | Előzetes verzió | Összes |<li>Kezdeti kiadás

> [!NOTE] 
> Előző előzetes kiadásaiban ütemezett események {legújabb} támogatott api-verzióval. Ez a formátum már nem támogatott, és a jövőben elavulttá válik.

### <a name="enabling-and-disabling-scheduled-events"></a>Engedélyezése és letiltása ütemezett események
Ütemezett események engedélyezve van a szolgáltatás az első ideje eseményeket kérelmet használhat. Akár két perc alatt az első hívásában számíthat késleltetett választ.

Ütemezett események le van tiltva a szolgáltatás Ha nem tesz egy kérelmet 24 órán át.

### <a name="user-initiated-maintenance"></a>Felhasználó által kezdeményezett karbantartás
Felhasználó kezdeményezte a virtuális gép karbantartási Azure-portálon a, API-t CLI, vagy PowerShell ütemezett esemény eredményez. Ez lehetővé teszi, hogy a karbantartási előkészítése logika tesztelni az alkalmazás, és lehetővé teszi, hogy a felhasználó által kezdeményezett karbantartási előkészítése az alkalmazás.

A virtuális gép újraindítása ütemezi típusú esemény `Reboot`. Újratelepíteni a virtuális gépet ütemezi a következő típusú eseményre `Redeploy`.

## <a name="using-the-api"></a>Az API-val

### <a name="headers"></a>Fejlécek
Ha a metaadat-szolgáltatás, meg kell adnia a fejléc `Metadata:true` annak érdekében, hogy nem szándékos átirányítja a kérést. A `Metadata:true` fejléc szükség minden ütemezett események olyan kérelem esetében. Nem sikerült a fejlécet tartalmaz a kérelem a metaadat-szolgáltatás helytelen kérelem válaszára eredményez.

### <a name="query-for-events"></a>Lekérdezést eseményekhez.
Ütemezett események alapján is kereshet, egyszerűen azáltal, hogy a következő hívást:

#### <a name="powershell"></a>PowerShell
```
curl http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01 -H @{"Metadata"="true"}
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
| EventId | Ez az esemény globálisan egyedi azonosítóját. <br><br> Példa: <br><ul><li>602d9444-d2cd-49c7-8624-8643e7171297  |
| EventType | Ez az esemény hatására hatása. <br><br> Értékek: <br><ul><li> `Freeze`: A virtuális gép felfüggesztése néhány másodpercig van ütemezve. A Processzor fel van függesztve, de nincs hatással a memória, a megnyitott fájlokat vagy a hálózati kapcsolatok. <li>`Reboot`: Újraindítás van ütemezve a virtuális gép (nem állandó memória nem vesztek el). <li>`Redeploy`: A virtuális gép áthelyezése egy másik csomópontra van ütemezve (a rövid élettartamú lemezek elvesznek). |
| ResourceType | Ez az esemény hatással van erőforrás típusát. <br><br> Értékek: <ul><li>`VirtualMachine`|
| További források| Ez az esemény hatással van erőforrások listáját. Ez magában foglalja a gépeket legalább egy garantáltan [frissítési tartomány](manage-availability.md), azonban nem tartalmazhat a UD összes gép. <br><br> Példa: <br><ul><li> ["FrontEnd_IN_0", "BackEnd_IN_0"] |
| Esemény állapota | Ez az esemény állapotát. <br><br> Értékek: <ul><li>`Scheduled`: Ez az esemény után a megadott ideig történő futásra van ütemezve a `NotBefore` tulajdonság.<li>`Started`: Ez az esemény elindult.</ul> Nem `Completed` vagy hasonló állapot valaha is biztosítja; az esemény már nem adható vissza, ha az esemény befejeződött.
| NotBefore| Az idő elteltével kezdheti el ezt az eseményt. <br><br> Példa: <br><ul><li> 2016-09-19T18:29:47Z  |

### <a name="event-scheduling"></a>Esemény ütemezése
Minden esemény van ütemezve egy jövőbeli időpontot minimális mennyiségű esemény típusa alapján. Most megjelenik egy esemény `NotBefore` tulajdonság. 

|EventType  | Minimális értesítés |
| - | - |
| Rögzítése| 15 perc |
| Újraindítás | 15 perc |
| Ismételt üzembe helyezés | 10 perc |

### <a name="event-scope"></a>Az esemény hatóköre     
Ütemezett kézbesíti az eseményeket:        
 - Egy felhőalapú szolgáltatás szereplő összes virtuális gép      
 - Minden virtuális gépek rendelkezésre állási csoportba      
 - A méretezési készlet elhelyezési csoport összes virtuális gépnek.         

Ennek eredményeképpen, jelölje be a `Resources` mező mellett az esemény azonosításához, amely a virtuális gépek is érinthet szeretné. 

### <a name="starting-an-event"></a>Egy esemény indítása 

Miután egy jövőbeli esemény megtanulta, és a szabályos leállítást logikát befejeződött, a függőben lévő esemény jóváhagyhatja azáltal, hogy egy `POST` az metaadat-ba irányuló hívás a `EventId`. Ez azt jelzi, az Azure-ba, hogy azt a minimális értesítési lerövidíthető (amikor csak lehetséges). 

Az alábbiakban található a várt json a `POST` kérelem törzse. A kérelemnek tartalmaznia kell a listája `StartRequests`. Minden egyes `StartRequest` tartalmazza a `EventId` gyorsítása szeretné az esemény:
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
curl -H @{"Metadata"="true"} -Method POST -Body '{"DocumentIncarnation":"5", "StartRequests": [{"EventId": "f020ba2e-3bc0-4c40-a10b-86575a9eabd5"}]}' -Uri http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01
```

> [!NOTE] 
> Egy esemény igazolása lehetővé teszi, hogy folytatja az összes esemény `Resources` abban az esetben, nem csak a virtuális gépet, amely elismeri az esemény. Ezért választhatja, hogy egy vezető összehangolni a nyugtázási, akkor más dolga, mint az első machine kiválasztják a `Resources` mező.


## <a name="powershell-sample"></a>PowerShell-példa 

Az alábbi minta ütemezett események a metaadatok szolgáltatást, és hagyja jóvá a függőben lévő eseményekhez.

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
function Approve-ScheduledEvent($eventId, $docIncarnation, $uri)
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

function Handle-ScheduledEvents($scheduledEvents)
{
    # Add logic for handling events here
}

######### Sample Scheduled Events Interaction #########

# Set up the scheduled events URI for a VNET-enabled VM
$localHostIP = "169.254.169.254"
$scheduledEventURI = 'http://{0}/metadata/scheduledevents?api-version=2017-03-01' -f $localHostIP 

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
        Approve-ScheduledEvent $event.EventId $scheduledEvents.DocumentIncarnation $scheduledEventURI 
    }
}
``` 

## <a name="next-steps"></a>További lépések 

- Tekintse meg a [események bemutató ütemezett](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance) Azure péntekig. 
- Tekintse át az ütemezett események mintakódok a [Azure példány metaadatok ütemezett események Github-adattár](https://github.com/Azure-Samples/virtual-machines-scheduled-events-discover-endpoint-for-non-vnet-vm)
- További információk az elérhető API-kat a [példány metaadat-szolgáltatás](instance-metadata-service.md).
- További tudnivalók [a tervezett karbantartások Windows virtuális gépek Azure-ban](planned-maintenance.md).
