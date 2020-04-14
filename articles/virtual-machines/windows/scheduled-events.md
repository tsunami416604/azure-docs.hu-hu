---
title: Windows-virtuális gépek ütemezett eseményei az Azure-ban
description: Ütemezett események az Azure metaadat-szolgáltatás a Windows virtuális gépeken.
services: virtual-machines-windows, virtual-machines-linux, cloud-services
documentationcenter: ''
author: mimckitt
manager: gwallace
editor: ''
tags: ''
ms.assetid: 28d8e1f2-8e61-4fbe-bfe8-80a68443baba
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: mimckitt
ms.openlocfilehash: c1e9ef8de65912c4f33e17ee2bb2175c76e7ea07
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81258680"
---
# <a name="azure-metadata-service-scheduled-events-for-windows-vms"></a>Azure metaadatszolgáltatás: Ütemezett események Windows virtuális gépekhez

Az ütemezett események egy Azure metaadat-szolgáltatás, amely időt ad az alkalmazásnak a virtuális gépek karbantartására való felkészülésre. Tájékoztatást nyújt a közelgő karbantartási eseményekről (pl. újraindítás), így az alkalmazás felkészülhet rájuk, és korlátozhatja a megszakítást. Minden Azure virtuálisgép-típushoz elérhető, beleértve a PaaS-t és az IaaS-t Windows és Linux rendszeren is. 

A Linuxon ütemezett eseményekről a [Linuxos virtuális gépek ütemezett eseményei](../linux/scheduled-events.md)című témakörben talál további információt.

> [!Note] 
> Az ütemezett események általában minden Azure-régióban elérhetők. A legfrissebb kiadási információkért tekintse meg [a Verzió és a régió elérhetőségét.](#version-and-region-availability)

## <a name="why-scheduled-events"></a>Miért ütemezett események?

Számos alkalmazás számára előnyös lehet a virtuális gépek karbantartására való felkészülés ideje. Az idő használható alkalmazásspecifikus feladatok végrehajtására, amelyek javítják a rendelkezésre állást, a megbízhatóságot és a szervizelhetőséget, beleértve a következőket: 

- Ellenőrzőpont és visszaállítás
- Kapcsolatkiürítés
- Elsődleges replika feladatátvétel 
- Eltávolítás a terheléselosztó készletből
- Eseménynaplózás
- Kecses leállítás 

Ütemezett események használatával az alkalmazás felderítheti, hogy mikor történik karbantartás, és a hatás korlátozására feladatokat indíthat el. Ütemezett események engedélyezése a virtuális gép minimális időt biztosít a karbantartási tevékenység végrehajtása előtt. A részleteket lásd alább az Eseményütemezés szakaszban.

Az ütemezett események a következő használati esetekben biztosíteseményeket:
- [A platform által kezdeményezett karbantartás](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates) (például virtuális gép újraindítása, élő áttelepítés vagy a gazdagép frissítéseinek megőrzése)
- A virtuális gép [leromlott gazdahardveren](https://azure.microsoft.com/blog/find-out-when-your-virtual-machine-hardware-is-degraded-with-scheduled-events) fut, amely az előrejelzések szerint hamarosan meghibásodik
- A felhasználó által kezdeményezett karbantartás (például a felhasználó újraindítja vagy újratelepíti a virtuális gép-
- [Azonnali virtuális gép](spot-vms.md) és [direkt méretezési csoport](../../virtual-machine-scale-sets/use-spot.md) példányának kilakoltatása

## <a name="the-basics"></a>Az alapok  

Az Azure Metadata szolgáltatás a virtuális gépről elérhető REST-végpont használatával futó virtuális gépek használatával kapcsolatos információkat tesz elérhetővé. Az információ nem irányítható IP-cím, így nem érhető el a virtuális gépen kívül.

### <a name="endpoint-discovery"></a>Végpont felderítése
Virtuális hálózatra engedélyezett virtuális gépek esetén a metaadat-szolgáltatás statikus, `169.254.169.254`nem irányítható IP-címből érhető el. Az ütemezett események legújabb verziójának teljes végpontja a következő: 

 > `http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01`

Ha a virtuális gép nem jön létre egy virtuális hálózaton belül, a felhőszolgáltatások és a klasszikus virtuális gépek alapértelmezett esetei, további logika szükséges a használandó IP-cím felderítéséhez. Ebből a mintából [megtudhatja, hogyan fedezheti fel a gazdagép végpontot.](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm)

### <a name="version-and-region-availability"></a>Verzió és régió elérhetősége
Az ütemezett események szolgáltatás verziószámverziója. A verziók kötelezőek, `2019-01-01`a jelenlegi verzió pedig .

| Verzió | Kiadás típusa | Régiók | Kibocsátási megjegyzések | 
| - | - | - | - |
| 2019-01-01 | Általános elérhetőség | Összes | <li> Hozzáadott támogatása virtuálisgép-méretezési készletek EventType "Terminate" |
| 2017-11-01 | Általános elérhetőség | Összes | <li> Hozzáadott támogatása Spot VM kilakoltatás EventType "Preempt"<br> | 
| 2017-08-01 | Általános elérhetőség | Összes | <li> Előhúzott aláhúzás eltávolítása az IaaS virtuális gépek erőforrásneveiből<br><li>A metaadatok fejlécének követelménye minden kérelemre kikényszerítve | 
| 2017-03-01 | Előzetes verzió | Összes |<li>Kezdeti kiadás |

> [!NOTE] 
> Az ütemezett események korábbi előzetes verziói a(z) {latest} api-verziót támogatták. Ez a formátum már nem támogatott, és a jövőben elavult lesz.

### <a name="enabling-and-disabling-scheduled-events"></a>Ütemezett események engedélyezése és letiltása
Az ütemezett események engedélyezve vannak a szolgáltatáshoz, amikor először kér eseményeket. Az első hívása akár két perces késleltetett válaszra is számíthat. Rendszeresen le kell kérdeznie a végpontot a közelgő karbantartási események, valamint a folyamatban lévő karbantartási tevékenységek állapotának észleléséhez.

Az ütemezett események le vannak tiltva a szolgáltatáshoz, ha 24 órán keresztül nem nyújt be kérelmet.

### <a name="user-initiated-maintenance"></a>Felhasználó által kezdeményezett karbantartás
A felhasználó virtuálisgép-karbantartást kezdeményezett az Azure Portalon, AZ API-n, a CLI-n vagy a PowerShellen keresztül egy ütemezett esemény eredménye. Ez lehetővé teszi, hogy tesztelje a karbantartási előkészítési logikát az alkalmazásban, és lehetővé teszi az alkalmazás számára, hogy felkészüljenek a felhasználó által kezdeményezett karbantartásra.

A virtuális gép újraindítása egy `Reboot`típusú eseményt ütemez. A virtuális gép újratelepítése egy típusú `Redeploy`eseményt ütemez.

## <a name="using-the-api"></a>Az API használata

### <a name="headers"></a>Fejlécek
A metaadat-szolgáltatás lekérdezésekénél meg `Metadata:true` kell adnia a fejlécet, hogy megbizonyosodjon arról, hogy a kérelem nem volt véletlenül átirányítva. A `Metadata:true` fejléc minden ütemezett eseménykéréshez szükséges. Ha nem veszi fel a fejlécet a kérelembe, az a metaadat-szolgáltatás hibás kérésre adott válaszát eredményezi.

### <a name="query-for-events"></a>Események lekérdezése
Az ütemezett eseményeket egyszerűen lekérdezheti a következő hívással:

#### <a name="powershell"></a>PowerShell
```
curl http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01 -H @{"Metadata"="true"}
```

A válasz ütemezett események tömbjét tartalmazza. Az üres tömb azt jelenti, hogy jelenleg nincsenek ütemezett események.
Abban az esetben, ha ütemezett események vannak, a válasz események tömbjét tartalmazza: 
```
{
    "DocumentIncarnation": {IncarnationID},
    "Events": [
        {
            "EventId": {eventID},
            "EventType": "Reboot" | "Redeploy" | "Freeze" | "Preempt" | "Terminate",
            "ResourceType": "VirtualMachine",
            "Resources": [{resourceName}],
            "EventStatus": "Scheduled" | "Started",
            "NotBefore": {timeInUTC},
        }
    ]
}
```
A DocumentInannináció egy ETag, és egyszerű módja annak vizsgálatának, hogy az események hasznos terhelése megváltozott-e az utolsó lekérdezés óta.

### <a name="event-properties"></a>Esemény tulajdonságai
|Tulajdonság  |  Leírás |
| - | - |
| Napszállta | Az esemény globálisan egyedi azonosítója. <br><br> Példa: <br><ul><li>602d9444-d2cd-49c7-8624-8643e7171297  |
| EventType (Eseménytípus) | Az esemény hatására. <br><br> Értékek: <br><ul><li> `Freeze`: A virtuális gép néhány másodpercre szüneteltetésre van ütemezve. A processzor és a hálózati kapcsolat felfüggeszthető, de nincs hatással a memóriára vagy a megnyitott fájlokra. <li>`Reboot`: A virtuális gép újraindításra van ütemezve (a nem állandó memória elvész). <li>`Redeploy`: A virtuális gép a tervek szerint egy másik csomópontra kerül (a rövid élettartamú lemezek elvesznek). <li>`Preempt`: A virtuális hely törlése folyamatban van (az ideiglenes lemezek elvesznek). <li> `Terminate`: A virtuális gép törlését tervezik. |
| ResourceType | Az esemény által érintett erőforrás típusa. <br><br> Értékek: <ul><li>`VirtualMachine`|
| További források| Az esemény által érintett erőforrások listája. Ez garantáltan legfeljebb egy [frissítési tartományból](manage-availability.md)származó gépeket tartalmaz, de nem feltétlenül tartalmazza az UD összes gépét. <br><br> Példa: <br><ul><li> ["FrontEnd_IN_0", "BackEnd_IN_0"] |
| Esemény állapota | Az esemény állapota. <br><br> Értékek: <ul><li>`Scheduled`: Ez az esemény a `NotBefore` tulajdonságban megadott idő után kezdődik.<li>`Started`: Az esemény elkezdődött.</ul> Soha `Completed` nem vagy hasonló státuszt nem adnak meg; az esemény a továbbiakban nem kerül visszaadásra, amikor az esemény befejeződik.
| Nem előtte| Az az idő, amely után ez az esemény elkezdődhet. <br><br> Példa: <br><ul><li> Hét, 19 Szept 2016 18:29:47 GMT  |

### <a name="event-scheduling"></a>Eseményütemezés
Minden esemény ütemezése a jövőben egy minimális ideig történik az esemény típusa alapján. Ez az idő az esemény `NotBefore` tulajdonában tükröződik. 

|EventType (Eseménytípus)  | Minimális értesítés |
| - | - |
| Fázik| 15 perc |
| Újraindítás | 15 perc |
| Ismételt üzembe helyezés | 10 perc |
| Preempt (Előmutató) | 30 másodperc |
| Megszünteti | [Felhasználó konfigurálható:](../../virtual-machine-scale-sets/virtual-machine-scale-sets-terminate-notification.md#enable-terminate-notifications)5-15 perc |

> [!NOTE] 
> Bizonyos esetekben az Azure képes előre jelezni állomás hiba miatt leromlott hardver, és megpróbálja csökkenteni a szolgáltatás zavarait az áttelepítés ütemezésével. Az érintett virtuális gépek egy ütemezett `NotBefore` eseményt kapnak, amely általában néhány nappal a jövőben lesz. A tényleges idő az előre jelzett hibakockázat-értékeléstől függően változik. Az Azure megpróbálja 7 nappal előre, ha lehetséges, de a tényleges idő változik, és kisebb lehet, ha az előrejelzés, hogy nagy az esélye a hardver meghibásodása hamarosan. A szolgáltatás rakoncátlehetésének minimalizálása érdekében, ha a hardver meghibásodik a rendszer áttelepítése előtt, javasoljuk, hogy a lehető leghamarabb telepítse újra a virtuális gépet.

### <a name="event-scope"></a>Esemény hatóköre     
Az ütemezett eseményeket a következő kezekbe szállítjuk:
 - Önálló virtuális gépek
 - A felhőszolgáltatás összes virtuális gépe      
 - Minden virtuális gép egy rendelkezésre állási csoportban      
 - A méretezési csoport elhelyezési csoportjában lévő összes virtuális gép.         

Ennek eredményeképpen ellenőrizze a `Resources` mezőt az eseményben, hogy azonosítsa, mely virtuális gépek lesznek hatással. 

### <a name="starting-an-event"></a>Esemény indítása 

Miután tudomást szerzett egy közelgő eseményről, és befejezte a szabályos leállítás `POST` logikáját, jóváhagyhatja a `EventId`kiemelkedő eseményt, ha felhívja a metaadat-szolgáltatást a. Ez azt jelzi az Azure számára, hogy lerövidítheti a minimális értesítési időt (ha lehetséges). 

A következő a json `POST` várható a kérelem törzs. A kérelemnek tartalmaznia `StartRequests`kell a listáját. Mindegyik `StartRequest` tartalmazza `EventId` a meggyorsítani kívánt eseményt:
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
curl -H @{"Metadata"="true"} -Method POST -Body '{"StartRequests": [{"EventId": "f020ba2e-3bc0-4c40-a10b-86575a9eabd5"}]}' -Uri http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01
```

> [!NOTE] 
> Egy esemény nyugtázása lehetővé `Resources` teszi, hogy az esemény az esemény minden részére folytatódjon, ne csak az eseményt nyugtázó virtuális gép rekedje. Ezért dönthet úgy, hogy vezetőt választ a visszaigazolás koordinálására, amely `Resources` lehet olyan egyszerű, mint az első gép a terepen.


## <a name="powershell-sample"></a>PowerShell-minta 

A következő minta lekérdezi a metaadat-szolgáltatást az ütemezett eseményekhez, és jóváhagyja az egyes függőben lévő eseményeket.

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
$scheduledEventURI = 'http://{0}/metadata/scheduledevents?api-version=2019-01-01' -f $localHostIP 

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

- Tekintse meg [az Ütemezett események bemutatóját](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance) az Azure-ban pénteken. 
- Tekintse át az ütemezett események kódmintáit az [Azure-példány metaadat-alapú ütemezési github-tárházban](https://github.com/Azure-Samples/virtual-machines-scheduled-events-discover-endpoint-for-non-vnet-vm)
- További információ a [példánymetaadat-szolgáltatásban](instance-metadata-service.md)elérhető API-król.
- Ismerje meg [a Windows virtuális gépek tervezett karbantartását az Azure-ban.](planned-maintenance.md)
