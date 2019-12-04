---
title: Scheduled Events a Windows rendszerű virtuális gépekhez az Azure-ban
description: Az Azure metadata szolgáltatást használó ütemezett események a Windows rendszerű virtuális gépeken.
services: virtual-machines-windows, virtual-machines-linux, cloud-services
documentationcenter: ''
author: ericrad
manager: gwallace
editor: ''
tags: ''
ms.assetid: 28d8e1f2-8e61-4fbe-bfe8-80a68443baba
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: ericrad
ms.openlocfilehash: f6e3e370201b49da149c09d87ed7cec63fef8ebf
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792249"
---
# <a name="azure-metadata-service-scheduled-events-for-windows-vms"></a>Azure Metadata Service: Scheduled Events a Windows rendszerű virtuális gépekhez

Scheduled Events egy Azure-Metadata Service, amely lehetővé teszi az alkalmazási idő előkészítését a virtuális gépek karbantartásához. Információt nyújt a közelgő karbantartási eseményekről (például újraindítás), így az alkalmazás felkészítheti őket, és korlátozhatja a megszakadást. Minden Azure-beli virtuálisgép-típushoz elérhető, beleértve a Pásti és a IaaS Windows és Linux rendszereken egyaránt. 

További információ a Linux rendszeren futó Scheduled Eventsről: [Scheduled Events Linux rendszerű virtuális gépekhez](../linux/scheduled-events.md).

> [!Note] 
> A Scheduled Events általánosan elérhető az összes Azure-régióban. A legfrissebb kiadási információkért lásd a [verzió és a régió rendelkezésre állását](#version-and-region-availability) .

## <a name="why-scheduled-events"></a>Miért Scheduled Events?

Számos alkalmazás kihasználhatja a virtuális gépek karbantartására való felkészülés idejét. Az idő a rendelkezésre állást, a megbízhatóságot és a használhatóságot javító alkalmazás-specifikus feladatok végrehajtásához használható, beleértve a következőket: 

- Ellenőrzőpont és visszaállítás
- Kapcsolatkiürítés
- Elsődleges replika feladatátvétele 
- Eltávolítás a terheléselosztó készletéből
- Események naplózása
- Biztonságos leállítás 

Scheduled Events alkalmazása képes észlelni, hogy mikor kerül sor a karbantartásra, és hogyan indíthat el feladatokat a hatás korlátozására. Az ütemezett események engedélyezésével a virtuális gép a karbantartási tevékenység végrehajtása előtt legalább ennyi időt biztosít. A részletekért tekintse meg az alábbi események ütemezése szakaszt.

Scheduled Events a következő használati esetekben nyújt eseményeket:
- [Platform által kezdeményezett karbantartás](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates) (például virtuális gép újraindítása, élő áttelepítés vagy memória megőrzése a gazdagépen)
- Csökkentett teljesítményű hardver
- A felhasználó által kezdeményezett karbantartás (például a felhasználó újraindítja vagy újratelepíti a virtuális gépet)
- [Helyszíni virtuális gép](spot-vms.md) és [direktszín-méretezési csoport](../../virtual-machine-scale-sets/use-spot.md) példányainak kizárása

## <a name="the-basics"></a>Az alapok  

Az Azure metaadat-szolgáltatás a virtuális gépről elérhető REST-végpont használatával teszi elérhetővé Virtual Machines futtatásával kapcsolatos információkat. Az információ nem irányítható IP-címen keresztül érhető el, hogy a virtuális gépen kívülről ne legyen kitéve.

### <a name="endpoint-discovery"></a>Végpont felderítése
A VNET-kompatibilis virtuális gépek esetében a metaadat-szolgáltatás statikus nem irányítható IP-címről érhető el, `169.254.169.254`. A Scheduled Events legújabb verziójának teljes végpontja a következő: 

 > `http://169.254.169.254/metadata/scheduledevents?api-version=2017-11-01`

Ha a virtuális gép nem egy Virtual Networkon belül jön létre, a Cloud Services és a klasszikus virtuális gépek esetében az alapértelmezett esetekben további logikára van szükség ahhoz, hogy felderítse a használni kívánt IP-címet. Tekintse át ezt a mintát, hogy megtudja, hogyan [derítheti fel a gazdagép végpontját](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm).

### <a name="version-and-region-availability"></a>Verzió és régió elérhetősége
A Scheduled Events szolgáltatás verziója. A verziók megadása kötelező, és az aktuális verzió `2017-11-01`.

| Verzió | Kiadás típusa | Térségek | Kibocsátási megjegyzések | 
| - | - | - | - |
| 2017-11-01 | Általános rendelkezésre állás | Mind | <li> Az alacsony prioritású virtuális gép kizárásának támogatása a "megelőzik" EventType<br> | 
| 2017-08-01 | Általános rendelkezésre állás | Mind | <li> Eltávolított előtagértéke aláhúzás a IaaS virtuális gépek erőforrásainak neveiből<br><li>Metaadatok fejlécére vonatkozó követelmények kényszerítve az összes kérelemhez | 
| 2017-03-01 | Előzetes verzió | Mind |<li>Kezdeti kiadás

> [!NOTE] 
> Az ütemezett események korábbi előzetes kiadásai ({Latest}) API-verzióként támogatottak. Ez a formátum már nem támogatott, és a jövőben elavulttá válik.

### <a name="enabling-and-disabling-scheduled-events"></a>Scheduled Events engedélyezése és letiltása
Az Scheduled Events engedélyezve van a szolgáltatáshoz, amikor az első alkalommal kérelmet készít az eseményekről. Egy késleltetett választ kell várnia az első hívásában akár két percig is. Időnként le kell kérdezni a végpontot a közelgő karbantartási események észleléséhez, valamint a végrehajtott karbantartási tevékenységek állapotáról.

A Scheduled Events le van tiltva a szolgáltatás esetében, ha 24 óráig nem végeznek kérést.

### <a name="user-initiated-maintenance"></a>Felhasználó által kezdeményezett karbantartás
A felhasználó a Azure Portal, az API, a CLI vagy a PowerShell használatával kezdeményezte a virtuális gép karbantartását egy ütemezett eseményen. Ez lehetővé teszi, hogy tesztelje a karbantartási előkészítési logikát az alkalmazásban, és lehetővé teszi, hogy az alkalmazás felkészüljön a felhasználó által kezdeményezett karbantartásra.

A virtuális gép újraindítása egy `Reboot`típusú eseményt ütemezhet. A virtuális gép újbóli üzembe helyezése egy `Redeploy`típusú eseményt ütemezhet.

## <a name="using-the-api"></a>Az API használata

### <a name="headers"></a>Fejlécek
Amikor lekérdezi a Metadata Service, meg kell adnia a fejlécet `Metadata:true` annak biztosítására, hogy a kérést nem szándékosan átirányítsák. Az összes ütemezett eseményre vonatkozó kérelemhez a `Metadata:true` fejléc szükséges. A kérelemben szereplő fejlécet nem lehet belefoglalni, mert a kérelem helytelen kérést fog eredményezni a Metadata Service.

### <a name="query-for-events"></a>Események lekérdezése
A következő hívással lehet lekérdezni Scheduled Events egyszerűen:

#### <a name="powershell"></a>PowerShell
```
curl http://169.254.169.254/metadata/scheduledevents?api-version=2017-11-01 -H @{"Metadata"="true"}
```

A válasz ütemezett események tömbjét tartalmazza. Az üres tömb azt jelenti, hogy jelenleg nincsenek ütemezett események.
Abban az esetben, ha ütemezett események vannak, a válasz események tömbjét tartalmazza: 
```
{
    "DocumentIncarnation": {IncarnationID},
    "Events": [
        {
            "EventId": {eventID},
            "EventType": "Reboot" | "Redeploy" | "Freeze" | "Preempt",
            "ResourceType": "VirtualMachine",
            "Resources": [{resourceName}],
            "EventStatus": "Scheduled" | "Started",
            "NotBefore": {timeInUTC},
        }
    ]
}
```
A DocumentIncarnation egy ETag, és egyszerűen megvizsgálhatja, hogy az események tartalma módosult-e az utolsó lekérdezés óta.

### <a name="event-properties"></a>Esemény tulajdonságai
|Tulajdonság  |  Leírás |
| - | - |
| Napszállta | Az esemény globálisan egyedi azonosítója. <br><br> Példa: <br><ul><li>602d9444-d2cd-49c7-8624-8643e7171297  |
| EventType | Ez az esemény okozza a hatását. <br><br> Értékek: <br><ul><li> `Freeze`: a virtuális gép néhány másodpercig szünetelteti az ütemezést. Előfordulhat, hogy a processzor és a hálózati kapcsolat fel van függesztve, de nincs hatással a memóriára vagy a megnyitott fájlokra. <li>`Reboot`: a virtuális gép újraindításra van ütemezve (nem állandó memória elvész). <li>`Redeploy`: a virtuális gép egy másik csomópontra való áthelyezésre van ütemezve (az ideiglenes lemezek elvesznek). <li>`Preempt`: az alacsony prioritású virtuális gép törlődik (az ideiglenes lemezek elvesznek).|
| ResourceType | Az esemény által gyakorolt erőforrás típusa <br><br> Értékek: <ul><li>`VirtualMachine`|
| Segédanyagok és eszközök| Az események által gyakorolt erőforrások listája. Ez garantáltan legfeljebb egy [frissítési tartományból](manage-availability.md)származó gépeket tartalmazhat, de nem tartalmazhatja az UD összes számítógépét. <br><br> Példa: <br><ul><li> ["FrontEnd_IN_0", "BackEnd_IN_0"] |
| Esemény állapota | Az esemény állapota. <br><br> Értékek: <ul><li>`Scheduled`: ez az esemény a `NotBefore` tulajdonságban megadott idő után indul el.<li>`Started`: ez az esemény megkezdődött.</ul> Még nincs megadva `Completed` vagy hasonló állapot; az eseményt a rendszer a továbbiakban nem adja vissza az esemény befejezésekor.
| NotBefore| Az az idő, amely után ez az esemény kezdődhet. <br><br> Példa: <br><ul><li> Hétfő, 19 Sep 2016 18:29:47 GMT  |

### <a name="event-scheduling"></a>Események ütemezése
Az egyes események ütemezése a jövőben az esemény típusa alapján várhatóan minimális idő. Ez az idő az esemény `NotBefore` tulajdonságában jelenik meg. 

|EventType  | Minimális figyelmeztetés |
| - | - |
| Fázik| 15 perc |
| Újraindítás | 15 perc |
| Ismételt üzembe helyezés | 10 perc |
| Megelőzik | 30 másodperc |

### <a name="event-scope"></a>Esemény hatóköre     
Az ütemezett események a következőre érkeznek:
 - Önálló Virtual Machines
 - A felhőalapú szolgáltatás összes Virtual Machines      
 - A rendelkezésre állási csoport összes Virtual Machines      
 - Egy méretezési csoport elhelyezési csoportjában lévő összes Virtual Machines.         

Ennek eredményeképpen tekintse meg az esemény `Resources` mezőjét annak azonosításához, hogy mely virtuális gépek lesznek hatással a rendszerre. 

### <a name="starting-an-event"></a>Esemény indítása 

Miután megismerte a közelgő eseményt, és elvégezte a logika kikapcsolását, jóváhagyhatja a függőben lévő eseményt úgy, hogy `POST` hívást indít a metaadat-szolgáltatásnak a `EventId`. Ez azt jelzi, hogy az Azure lerövidíti a minimálisan szükséges értesítési időt (ha lehetséges). 

A következő a JSON-t a `POST` kérelem törzsében kell megvárni. A kérésnek tartalmaznia kell `StartRequests`listáját. Minden `StartRequest` tartalmazza a felgyorsítani kívánt esemény `EventId`ét:
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
curl -H @{"Metadata"="true"} -Method POST -Body '{"StartRequests": [{"EventId": "f020ba2e-3bc0-4c40-a10b-86575a9eabd5"}]}' -Uri http://169.254.169.254/metadata/scheduledevents?api-version=2017-11-01
```

> [!NOTE] 
> Egy esemény elfogadásával engedélyezhető, hogy az esemény az esemény összes `Resources` folytassa, nem csak az eseményt visszaigazoló virtuális gépet. Ezért dönthet úgy, hogy egy vezetőt választ ki a nyugtázás koordinálására, amely a `Resources` mező első számítógépének egyszerűvé válhat.


## <a name="powershell-sample"></a>PowerShell-minta 

A következő minta lekérdezi a metaadat-szolgáltatást az ütemezett eseményekhez, és jóváhagyja az egyes kimaradt eseményeket.

```powershell
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
$scheduledEventURI = 'http://{0}/metadata/scheduledevents?api-version=2017-11-01' -f $localHostIP 

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

## <a name="next-steps"></a>Következő lépések 

- Tekintse meg az Azure Friday [Scheduled Events bemutatóját](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance) . 
- Tekintse át a Scheduled Events kód mintáit az [Azure-példány metaadatainak Scheduled Events GitHub-tárházban](https://github.com/Azure-Samples/virtual-machines-scheduled-events-discover-endpoint-for-non-vnet-vm)
- További információ a [példány metaadatainak szolgáltatásában](instance-metadata-service.md)elérhető API-król.
- Ismerje meg az [Azure-beli Windows rendszerű virtuális gépek tervezett karbantartását](planned-maintenance.md).
