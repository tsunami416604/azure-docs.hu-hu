---
title: Scheduled Events a Windows rendszerű virtuális gépekhez az Azure-ban
description: Az Azure metadata szolgáltatást használó ütemezett események a Windows rendszerű virtuális gépeken.
author: mimckitt
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 06/01/2020
ms.author: mimckitt
ms.openlocfilehash: 0d1aa15c572f8ddec38cef913b170ed795ba1505
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/02/2020
ms.locfileid: "84297921"
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
- A virtuális gép olyan [csökkentett teljesítményű gazdagép hardverén](https://azure.microsoft.com/blog/find-out-when-your-virtual-machine-hardware-is-degraded-with-scheduled-events) fut, amely hamarosan meghiúsul
- A felhasználó által kezdeményezett karbantartás (például a felhasználó újraindítja vagy újratelepíti a virtuális gépet)
- [Helyszíni virtuális gép](spot-vms.md) és [direktszín-méretezési csoport](../../virtual-machine-scale-sets/use-spot.md) példányainak kizárása

## <a name="the-basics"></a>Az alapok  

Az Azure metaadat-szolgáltatás a virtuális gépről elérhető REST-végpont használatával teszi elérhetővé Virtual Machines futtatásával kapcsolatos információkat. Az információ nem irányítható IP-címen keresztül érhető el, hogy a virtuális gépen kívülről ne legyen kitéve.

### <a name="endpoint-discovery"></a>Végpont felderítése
A VNET-kompatibilis virtuális gépek esetében a metaadat-szolgáltatás statikus, nem irányítható IP-címről érhető el `169.254.169.254` . A Scheduled Events legújabb verziójának teljes végpontja a következő: 

 > `http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01`

Ha a virtuális gép nem egy Virtual Networkon belül jön létre, a Cloud Services és a klasszikus virtuális gépek esetében az alapértelmezett esetekben további logikára van szükség ahhoz, hogy felderítse a használni kívánt IP-címet. Tekintse át ezt a mintát, hogy megtudja, hogyan [derítheti fel a gazdagép végpontját](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm).

### <a name="version-and-region-availability"></a>Verzió és régió elérhetősége
A Scheduled Events szolgáltatás verziója. A verziók kötelezőek, a jelenlegi verzió pedig `2019-01-01` .

| Verzió | Kiadás típusa | Régiók | Release Notes (Kibocsátási megjegyzések) | 
| - | - | - | - |
| 2019-08-01 | Általános elérhetőség | Mind | <li> EventSource-támogatás hozzáadva |
| 2019-04-01 | Általános elérhetőség | Mind | <li> Az esemény leírásának támogatása hozzáadva |
| 2019-01-01 | Általános elérhetőség | Mind | <li> A virtuálisgép-méretezési csoportok támogatásának támogatása a EventType leállításához |
| 2017-11-01 | Általános elérhetőség | Mind | <li> A (z) megelőzik helyszíni VM-kilakoltatás EventType támogatása<br> | 
| 2017-08-01 | Általános elérhetőség | Mind | <li> Eltávolított előtagértéke aláhúzás a IaaS virtuális gépek erőforrásainak neveiből<br><li>Metaadatok fejlécére vonatkozó követelmények kényszerítve az összes kérelemhez | 
| 2017-03-01 | Előnézet | Mind |<li>Kezdeti kiadás |

> [!NOTE] 
> Az ütemezett események korábbi előzetes kiadásai ({Latest}) API-verzióként támogatottak. Ez a formátum már nem támogatott, és a jövőben elavulttá válik.

### <a name="enabling-and-disabling-scheduled-events"></a>Ütemezett események engedélyezése és letiltása
Az Scheduled Events engedélyezve van a szolgáltatáshoz, amikor az első alkalommal kérelmet készít az eseményekről. Egy késleltetett választ kell várnia az első hívásában akár két percig is. Időnként le kell kérdezni a végpontot a közelgő karbantartási események észleléséhez, valamint a végrehajtott karbantartási tevékenységek állapotáról.

A Scheduled Events le van tiltva a szolgáltatás esetében, ha 24 óráig nem végeznek kérést.

### <a name="user-initiated-maintenance"></a>Felhasználó által kezdeményezett karbantartás
A felhasználó a Azure Portal, az API, a CLI vagy a PowerShell használatával kezdeményezte a virtuális gép karbantartását egy ütemezett eseményen. Ez lehetővé teszi, hogy tesztelje a karbantartási előkészítési logikát az alkalmazásban, és lehetővé teszi, hogy az alkalmazás felkészüljön a felhasználó által kezdeményezett karbantartásra.

A virtuális gép újraindítása egy típusú eseményt ütemezhet `Reboot` . Egy virtuális gép újbóli üzembe helyezése egy típusú eseményt ütemezhet `Redeploy` .

## <a name="using-the-api"></a>Az API használata

### <a name="headers"></a>Fejlécek
Amikor lekérdezi a Metadata Service, meg kell adnia a fejlécet `Metadata:true` annak biztosítására, hogy a kérést nem szándékosan átirányítsák. Az `Metadata:true` összes ütemezett eseményre vonatkozó kérelem fejlécét kötelező megadni. A kérelemben szereplő fejlécet nem lehet belefoglalni, mert a kérelem helytelen kérést fog eredményezni a Metadata Service.

### <a name="query-for-events"></a>Események lekérdezése
A következő hívással lehet lekérdezni Scheduled Events egyszerűen:

#### <a name="powershell"></a>PowerShell
```
curl http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01 -H @{"Metadata"="true"}
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
            "Description": {eventDescription},
            "EventSource" : "Platform" | "User",
        }
    ]
}
```
A DocumentIncarnation egy ETag, és egyszerűen megvizsgálhatja, hogy az események tartalma módosult-e az utolsó lekérdezés óta.

### <a name="event-properties"></a>Esemény tulajdonságai
|Tulajdonság  |  Leírás |
| - | - |
| Napszállta | Az esemény globálisan egyedi azonosítója. <br><br> Példa: <br><ul><li>602d9444-d2cd-49c7-8624-8643e7171297  |
| EventType | Ez az esemény okozza a hatását. <br><br> Értékek: <br><ul><li> `Freeze`: A virtuális gép néhány másodpercig szünetelteti az ütemezést. Előfordulhat, hogy a processzor és a hálózati kapcsolat fel van függesztve, de nincs hatással a memóriára vagy a megnyitott fájlokra. <li>`Reboot`: A virtuális gép újraindításra van ütemezve (nem állandó memória elvész). <li>`Redeploy`: A virtuális gép egy másik csomópontra való áthelyezésre van ütemezve (az ideiglenes lemezek elvesznek). <li>`Preempt`: A helyszíni virtuális gép törlődik (az ideiglenes lemezek elvesznek). <li> `Terminate`: A virtuális gép törlésre van ütemezve. |
| ResourceType | Az esemény által gyakorolt erőforrás típusa <br><br> Értékek: <ul><li>`VirtualMachine`|
| További források| Az események által gyakorolt erőforrások listája. Ez garantáltan legfeljebb egy [frissítési tartományból](manage-availability.md)származó gépeket tartalmazhat, de nem tartalmazhatja az UD összes számítógépét. <br><br> Példa: <br><ul><li> ["FrontEnd_IN_0", "BackEnd_IN_0"] |
| Esemény állapota | Az esemény állapota. <br><br> Értékek: <ul><li>`Scheduled`: Ez az esemény a tulajdonságban megadott idő után indul el `NotBefore` .<li>`Started`: Ez az esemény megkezdődött.</ul> A rendszer nem adta meg vagy nem hasonló állapotot, mert az eseményt a rendszer `Completed` már nem adja vissza az esemény befejezésekor.
| NotBefore| Az az idő, amely után ez az esemény kezdődhet. <br><br> Példa: <br><ul><li> Hétfő, 19 Sep 2016 18:29:47 GMT  |
| Leírás | Az esemény leírása. <br><br> Példa: <br><ul><li> A gazdagép-kiszolgáló karbantartás alatt áll. |
| EventSource | Az esemény kezdeményezője. <br><br> Példa: <br><ul><li> `Platform`: Ezt az eseményt a platfrom kezdeményezi. <li>`User`: Ezt az eseményt a felhasználó kezdeményezi. |

### <a name="event-scheduling"></a>Események ütemezése
Az egyes események ütemezése a jövőben az esemény típusa alapján várhatóan minimális idő. Ez az idő egy esemény tulajdonságában jelenik meg `NotBefore` . 

|EventType  | Minimális figyelmeztetés |
| - | - |
| Fázik| 15 perc |
| Újraindítás | 15 perc |
| Ismételt üzembe helyezés | 10 perc |
| Megelőzik | 30 másodperc |
| Felmondhatja | [Felhasználó által konfigurálható](../../virtual-machine-scale-sets/virtual-machine-scale-sets-terminate-notification.md#enable-terminate-notifications): 5 – 15 perc |

> [!NOTE] 
> Bizonyos esetekben az Azure képes megjósolni a gazdagép meghibásodását, mivel a hardver elromlott, és megkísérli a szolgáltatás megszakadásának enyhítését az áttelepítés ütemezésével. Az érintett virtuális gépek egy ütemezett eseményt kapnak, `NotBefore` amely általában néhány napig tart a jövőben. A tényleges idő a várható meghibásodási kockázattól függ. Az Azure a lehetségesnél 7 napos előzetes értesítést próbál megadni, de a tényleges idő változó, és kisebb is lehet, ha az előrejelzés szerint a hardver hamarosan leáll. Annak érdekében, hogy a rendszer az áttelepítés megkezdése előtt a hardver meghibásodása esetén is minimálisra csökkentse a szolgáltatás kockázatát, javasoljuk, hogy a lehető leghamarabb telepítse újra a virtuális gépet.

### <a name="event-scope"></a>Esemény hatóköre     
Az ütemezett események a következőre érkeznek:
 - Önálló Virtual Machines.
 - Minden Virtual Machines egy felhőalapú szolgáltatásban.     
 - Egy rendelkezésre állási csoport összes Virtual Machines.     
 - Az összes Virtual Machines egy méretezési csoport elhelyezési csoportjában (beleértve a Batch-t).       

Ennek eredményeképpen be kell jelölnie az `Resources` esemény mezőjét annak meghatározására, hogy mely virtuális gépek lesznek hatással a rendszerre. 

### <a name="starting-an-event"></a>Esemény indítása 

Miután megismerte a közelgő eseményt, és elvégezte a logikáját a zökkenőmentes leállítás érdekében, jóváhagyhatja a függőben lévő eseményt, ha a `POST` metaadat-szolgáltatást a segítségével hívja meg `EventId` . Ez azt jelzi, hogy az Azure lerövidíti a minimálisan szükséges értesítési időt (ha lehetséges). 

A következő a JSON-t a `POST` kérelem törzsében kell megvárni. A kérésnek tartalmaznia kell egy listát `StartRequests` . Mindegyik `StartRequest` tartalmazza a `EventId` felgyorsításhoz használni kívánt eseményt:
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
> Egy esemény elfogadásával lehetővé válik, hogy az esemény minden esetben folytassa az eseményt `Resources` , nem csak az eseményt visszaigazoló virtuális gépet. Ezért dönthet úgy, hogy egy vezetőt választ a nyugtázás koordinálására, amely a mező első számítógépének egyszerűvé válhat `Resources` .


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

- Tekintse meg az Azure Friday [Scheduled Events bemutatóját](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance) . 
- Tekintse át a Scheduled Events kód mintáit az [Azure-példány metaadatainak Scheduled Events GitHub-tárházban](https://github.com/Azure-Samples/virtual-machines-scheduled-events-discover-endpoint-for-non-vnet-vm)
- További információ a [példány metaadatainak szolgáltatásában](instance-metadata-service.md)elérhető API-król.
- Ismerje meg az [Azure-beli Windows rendszerű virtuális gépek tervezett karbantartását](planned-maintenance.md).
