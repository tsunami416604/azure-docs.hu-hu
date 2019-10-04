---
title: Scheduled Events Linux rendszerű virtuális gépek számára az Azure-ban | Microsoft Docs
description: Eseményeket ütemezhet az Azure Metadata Service használatával a linuxos virtuális gépekhez.
services: virtual-machines-windows, virtual-machines-linux, cloud-services
documentationcenter: ''
author: ericrad
manager: gwallace
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: ericrad
ms.openlocfilehash: d427544ab9396211e4cbb247527a0eb848f42926
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70091279"
---
# <a name="azure-metadata-service-scheduled-events-for-linux-vms"></a>Azure Metadata Service: Scheduled Events Linux rendszerű virtuális gépekhez

Scheduled Events egy Azure-Metadata Service, amely lehetővé teszi az alkalmazási idő előkészítését a virtuális gép (VM) karbantartásához. Információt nyújt a közelgő karbantartási eseményekről (például újraindítás), hogy az alkalmazás előkészítse őket, és korlátozza a megszakadást. Minden Azure Virtual Machines típushoz elérhető, beleértve a Pásti és a IaaS-t is Windows és Linux rendszeren. 

A Windows Scheduled Eventsával kapcsolatos információkért lásd: [Scheduled Events a Windows](../windows/scheduled-events.md)rendszerű virtuális gépekhez.

> [!Note] 
> A Scheduled Events általánosan elérhető az összes Azure-régióban. A legfrissebb kiadási információkért lásd a [verzió és a régió rendelkezésre állását](#version-and-region-availability) .

## <a name="why-use-scheduled-events"></a>Miért érdemes Scheduled Events?

Számos alkalmazás kihasználhatja a virtuális gépek karbantartására való felkészülés idejét. Az idő a rendelkezésre állást, a megbízhatóságot és a használhatóságot javító alkalmazásspecifikus feladatok végrehajtásához használható, beleértve a következőket: 

- Ellenőrzőpont és visszaállítás.
- A kapcsolatok kiürítése.
- Elsődleges replika feladatátvétele.
- Eltávolítás a terheléselosztó készletéből.
- Eseménynaplózás.
- Biztonságos leállítás.

A Scheduled Events segítségével az alkalmazás képes észlelni, ha a karbantartás bekövetkezik, és a tevékenységek kiváltásával korlátozzák a hatásukat.  

Scheduled Events a következő használati esetekben nyújt eseményeket:

- [Platform által kezdeményezett karbantartás](https://docs.microsoft.com/azure/virtual-machines/linux/maintenance-and-updates) (például a virtuális gép újraindítása, élő áttelepítés vagy memória megőrzése a gazdagépen)
- Csökkentett teljesítményű hardver
- Felhasználó által kezdeményezett karbantartás (például egy felhasználó újraindítja vagy újratelepíti a virtuális gépet)
- [Alacsony prioritású virtuális](https://azure.microsoft.com/blog/low-priority-scale-sets) gépek kizárása a méretezési csoportokban

## <a name="the-basics"></a>Alapismeretek  

  A Metadata Service a virtuális gépekről elérhető REST-végpontok segítségével teszi elérhetővé a futó virtuális gépek információit. Az információk egy nem irányítható IP-címen keresztül érhetők el, hogy a virtuális gépen kívül ne legyen kitéve.

### <a name="scope"></a>Scope
Az ütemezett események a következőre érkeznek:

- Önálló Virtual Machines.
- Egy felhőalapú szolgáltatás összes virtuális gépe.
- Egy rendelkezésre állási csoportba tartozó összes virtuális gép.
- Egy méretezési csoport elhelyezési csoportjában lévő összes virtuális gép. 

Ennek eredményeképpen jelölje be `Resources` az esemény mezőjét az érintett virtuális gépek azonosításához.

### <a name="endpoint-discovery"></a>Végpont felderítése
A VNET-kompatibilis virtuális gépek esetében a Metadata Service statikus, `169.254.169.254`nem irányítható IP-címről érhető el. A Scheduled Events legújabb verziójának teljes végpontja a következő: 

 > `http://169.254.169.254/metadata/scheduledevents?api-version=2017-11-01`

Ha a virtuális gép nem egy Virtual Networkon belül jön létre, a Cloud Services és a klasszikus virtuális gépek esetében az alapértelmezett esetekben további logikára van szükség a használandó IP-cím felderítéséhez. A [gazdagép végpontjának felderítéséhez](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm)tekintse meg ezt a mintát.

### <a name="version-and-region-availability"></a>Verzió és régió elérhetősége
A Scheduled Events szolgáltatás verziója. A verziók megadása kötelező; a jelenlegi verzió `2017-11-01`:.

| Version | Kiadás típusa | Regions | Kibocsátási megjegyzések | 
| - | - | - | - | 
| 2017-11-01 | Általános rendelkezésre állás | Összes | <li> Az alacsony prioritású virtuális gép kizárásának támogatása a "megelőzik" EventType<br> | 
| 2017-08-01 | Általános rendelkezésre állás | Összes | <li> Eltávolított előtagértéke aláhúzás a IaaS virtuális gépek erőforrásainak neveiből<br><li>Metaadatok fejlécére vonatkozó követelmények kényszerítve az összes kérelemhez | 
| 2017-03-01 | Előzetes verzió | Összes | <li>Kezdeti kiadás


> [!NOTE] 
> A korábbi előzetes verziókban Scheduled Events támogatott {Latest} API-verzióként. Ez a formátum már nem támogatott, és a jövőben elavulttá válik.

### <a name="enabling-and-disabling-scheduled-events"></a>Scheduled Events engedélyezése és letiltása
Az Scheduled Events engedélyezve van a szolgáltatáshoz, amikor az első alkalommal kérelmet készít az eseményekről. Egy késleltetett választ kell várnia az első hívásában akár két percig is.

A Scheduled Events le van tiltva a szolgáltatás esetében, ha 24 óráig nem végeznek kérést.

### <a name="user-initiated-maintenance"></a>Felhasználó által kezdeményezett karbantartás
A felhasználó által kezdeményezett virtuális gépek karbantartása az Azure Portal, az API, a CLI vagy a PowerShell segítségével ütemezett eseményt eredményez. Ezután tesztelheti a karbantartási előkészítési logikát az alkalmazásban, és az alkalmazás előkészítheti a felhasználó által kezdeményezett karbantartást.

Ha újraindít egy virtuális gépet, a típussal `Reboot` rendelkező esemény ütemezve van. Ha újratelepít egy virtuális gépet, a típussal `Redeploy` rendelkező esemény ütemezve van.

## <a name="use-the-api"></a>Az API használata

### <a name="headers"></a>Fejlécek
Metadata Service lekérdezése során meg kell adnia a fejlécet `Metadata:true` annak biztosítására, hogy a kérés akaratlanul nem lett átirányítva. Az `Metadata:true` összes ütemezett eseményre vonatkozó kérelem fejlécét kötelező megadni. Nem sikerült belefoglalni a fejlécet a kérelembe a Metadata Service "hibás kérés" válaszában.

### <a name="query-for-events"></a>Események lekérdezése
Az ütemezett eseményeket a következő hívással kérdezheti le:

#### <a name="bash"></a>Bash
```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01
```

A válasz ütemezett események tömbjét tartalmazza. Az üres tömb azt jelenti, hogy jelenleg nincsenek ütemezett események.
Abban az esetben, ha ütemezett események vannak, a válasz események tömbjét tartalmazza. 
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

### <a name="event-properties"></a>Esemény tulajdonságai
|Tulajdonság  |  Leírás |
| - | - |
| EventId | Az esemény globálisan egyedi azonosítója. <br><br> Példa: <br><ul><li>602d9444-d2cd-49c7-8624-8643e7171297  |
| EventType | Ez az esemény okozza a hatását. <br><br> Értékek <br><ul><li> `Freeze`: A virtuális gép néhány másodpercig szünetelteti az ütemezést. Előfordulhat, hogy a processzor és a hálózati kapcsolat fel van függesztve, de nincs hatással a memóriára vagy a megnyitott fájlokra.<li>`Reboot`: A virtuális gép újraindításra van ütemezve (nem állandó memória elvész). <li>`Redeploy`: A virtuális gép egy másik csomópontra való áthelyezésre van ütemezve (az ideiglenes lemezek elvesznek). <li>`Preempt`: Az alacsony prioritású virtuális gép törlődik (az ideiglenes lemezek elvesznek).|
| Erőforrástípus | Az eseményt érintő erőforrás típusa. <br><br> Értékek <ul><li>`VirtualMachine`|
| További források| Az eseményt érintő erőforrások listája. A lista garantáltan legfeljebb egy [frissítési tartományból](manage-availability.md)származó gépeket tartalmazhat, de előfordulhat, hogy nem tartalmazza a UD összes számítógépét. <br><br> Példa: <br><ul><li> ["FrontEnd_IN_0", "BackEnd_IN_0"] |
| EventStatus | Az esemény állapota. <br><br> Értékek <ul><li>`Scheduled`: Ez az esemény a `NotBefore` tulajdonságban megadott idő után indul el.<li>`Started`: Ez az esemény megkezdődött.</ul> A `Completed` rendszer nem adott meg és nem is hasonló állapotot. Az eseményt a rendszer már nem adja vissza az esemény befejeződése után.
| NotBefore| Az esemény elindításának időpontja. <br><br> Példa: <br><ul><li> Hétfő, 19 Sep 2016 18:29:47 GMT  |

### <a name="event-scheduling"></a>Események ütemezése
Az események a jövőben az esemény típusa alapján a lehető legkevesebb időt ütemezik. Ez az idő egy esemény tulajdonságában jelenik meg `NotBefore` . 

|EventType  | Minimális figyelmeztetés |
| - | - |
| Fázik| 15 perc |
| Újraindítás | 15 perc |
| Újratelepítés | 10 perc |
| Megelőzik | 30 másodperc |

### <a name="start-an-event"></a>Esemény indítása 

Miután elolvasta a közelgő eseményt, és befejezi a logikát a zökkenőmentes leállítás érdekében, jóváhagyhatja a függő eseményt `POST` úgy, hogy meghívja a `EventId`metadata Service. Ez a hívás az Azure-ra utal, hogy lerövidítheti az értesítés minimális idejét (ha lehetséges). 

A `POST` kérelem törzsében a következő JSON-minta várható. A kérésnek tartalmaznia kell egy listát `StartRequests`. Mindegyik `StartRequest` a `EventId` felgyorsítani kívánt eseményhez tartalmaz:
```
{
    "StartRequests" : [
        {
            "EventId": {EventId}
        }
    ]
}
```

#### <a name="bash-sample"></a>Bash-minta
```
curl -H Metadata:true -X POST -d '{"StartRequests": [{"EventId": "f020ba2e-3bc0-4c40-a10b-86575a9eabd5"}]}' http://169.254.169.254/metadata/scheduledevents?api-version=2017-11-01
```

> [!NOTE] 
> Egy esemény elfogadásával lehetővé válik, hogy az esemény `Resources` minden esetben folytassa az eseményt, nem csak az eseményt visszaigazoló virtuális gépet. Ezért dönthet úgy, hogy egy vezetőt választ ki a nyugtázás koordinálására, ami a `Resources` mező első számítógépének egyszerűvé válhat.

## <a name="python-sample"></a>Python-minta 

Az alábbi lekérdezési Metadata Service az ütemezett eseményekhez, és jóváhagyja az egyes fennmaradó eseményeket:

```python
#!/usr/bin/python

import json
import urllib2
import socket
import sys

metadata_url = "http://169.254.169.254/metadata/scheduledevents?api-version=2017-11-01"
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
            print "+ Scheduled Event. This host " + this_host + " is scheduled for " + eventtype + " not before " + notbefore
            # Add logic for handling events here


def main():
   data = get_scheduled_events()
   handle_scheduled_events(data)

if __name__ == '__main__':
  main()
  sys.exit(0)
```

## <a name="next-steps"></a>További lépések 
- Tekintse [meg Scheduled Eventsét az Azure fridayban](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance) , és tekintse meg a bemutatót. 
- Tekintse át a Scheduled Events kód mintáit az [Azure-példány metaadatainak Scheduled Events GitHub](https://github.com/Azure-Samples/virtual-machines-scheduled-events-discover-endpoint-for-non-vnet-vm)-tárházban.
- További információ a [instance metadata Service](instance-metadata-service.md)elérhető API-król.
- Ismerje meg az [Azure-beli Linux rendszerű virtuális gépek tervezett karbantartását](planned-maintenance.md).
