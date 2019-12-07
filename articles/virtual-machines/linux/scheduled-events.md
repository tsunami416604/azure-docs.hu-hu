---
title: Scheduled Events Linux rendszerű virtuális gépekhez az Azure-ban
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
ms.openlocfilehash: c0b30ecb9bc2b029141e528139f2b8a308c3a8dd
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2019
ms.locfileid: "74892838"
---
# <a name="azure-metadata-service-scheduled-events-for-linux-vms"></a>Azure Metadata Service: Linux rendszerű virtuális gépekhez Scheduled Events

Scheduled Events egy Azure-Metadata Service, amely lehetővé teszi az alkalmazási idő előkészítését a virtuális gép (VM) karbantartásához. Információt nyújt a közelgő karbantartási eseményekről (például újraindítás), hogy az alkalmazás előkészítse őket, és korlátozza a megszakadást. Minden Azure Virtual Machines típushoz elérhető, beleértve a Pásti és a IaaS-t is Windows és Linux rendszeren. 

A Windows Scheduled Eventsával kapcsolatos információkért lásd: [Scheduled Events a Windows rendszerű virtuális gépekhez](../windows/scheduled-events.md).

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

- [Platform által kezdeményezett karbantartás](https://docs.microsoft.com/azure/virtual-machines/linux/maintenance-and-updates) (például virtuális gép újraindítása, élő áttelepítés vagy memória megőrzése a gazdagépen)
- Csökkentett teljesítményű hardver
- Felhasználó által kezdeményezett karbantartás (például egy felhasználó újraindítja vagy újratelepíti a virtuális gépet)
- [Helyszíni virtuális gép](spot-vms.md) és [direktszín-méretezési csoport](../../virtual-machine-scale-sets/use-spot.md) példányainak kizárása.

## <a name="the-basics"></a>Az alapok  

  A Metadata Service a virtuális gépekről elérhető REST-végpontok segítségével teszi elérhetővé a futó virtuális gépek információit. Az információk egy nem irányítható IP-címen keresztül érhetők el, hogy a virtuális gépen kívül ne legyen kitéve.

### <a name="scope"></a>Hatókör
Az ütemezett események a következőre érkeznek:

- Önálló Virtual Machines.
- Egy felhőalapú szolgáltatás összes virtuális gépe.
- Egy rendelkezésre állási csoportba tartozó összes virtuális gép.
- Egy méretezési csoport elhelyezési csoportjában lévő összes virtuális gép. 

Ennek eredményeképpen tekintse meg az esemény `Resources` mezőjét az érintett virtuális gépek azonosításához.

### <a name="endpoint-discovery"></a>Végpont felderítése
A VNET-kompatibilis virtuális gépek esetében a Metadata Service statikus, nem irányítható IP-címről érhető el, `169.254.169.254`. A Scheduled Events legújabb verziójának teljes végpontja a következő: 

 > `http://169.254.169.254/metadata/scheduledevents?api-version=2017-11-01`

Ha a virtuális gép nem egy Virtual Networkon belül jön létre, a Cloud Services és a klasszikus virtuális gépek esetében az alapértelmezett esetekben további logikára van szükség a használandó IP-cím felderítéséhez. A [gazdagép végpontjának felderítéséhez](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm)tekintse meg ezt a mintát.

### <a name="version-and-region-availability"></a>Verzió és régió elérhetősége
A Scheduled Events szolgáltatás verziója. A verziók megadása kötelező; az aktuális verzió `2017-11-01`.

| Verzió | Kiadás típusa | Térségek | Kibocsátási megjegyzések | 
| - | - | - | - | 
| 2017-11-01 | Általános rendelkezésre állás | Mind | <li> A (z) megelőzik helyszíni VM-kilakoltatás EventType támogatása<br> | 
| 2017-08-01 | Általános rendelkezésre állás | Mind | <li> Eltávolított előtagértéke aláhúzás a IaaS virtuális gépek erőforrásainak neveiből<br><li>Metaadatok fejlécére vonatkozó követelmények kényszerítve az összes kérelemhez | 
| 2017-03-01 | Előzetes verzió | Mind | <li>Kezdeti kiadás


> [!NOTE] 
> A korábbi előzetes verziókban Scheduled Events támogatott {Latest} API-verzióként. Ez a formátum már nem támogatott, és a jövőben elavulttá válik.

### <a name="enabling-and-disabling-scheduled-events"></a>Scheduled Events engedélyezése és letiltása
Az Scheduled Events engedélyezve van a szolgáltatáshoz, amikor az első alkalommal kérelmet készít az eseményekről. Egy késleltetett választ kell várnia az első hívásában akár két percig is.

A Scheduled Events le van tiltva a szolgáltatás esetében, ha 24 óráig nem végeznek kérést.

### <a name="user-initiated-maintenance"></a>Felhasználó által kezdeményezett karbantartás
A felhasználó által kezdeményezett virtuális gépek karbantartása az Azure Portal, az API, a CLI vagy a PowerShell segítségével ütemezett eseményt eredményez. Ezután tesztelheti a karbantartási előkészítési logikát az alkalmazásban, és az alkalmazás előkészítheti a felhasználó által kezdeményezett karbantartást.

Ha újraindít egy virtuális gépet, a `Reboot` típusú eseményt ütemezi a rendszer. Ha újratelepít egy virtuális gépet, a `Redeploy` típusú eseményt ütemezi a rendszer.

## <a name="use-the-api"></a>Az API használata

### <a name="headers"></a>Fejlécek
Metadata Service lekérdezése során meg kell adnia a fejlécet `Metadata:true` annak biztosítására, hogy a kérést nem szándékosan átirányítsák. Az összes ütemezett eseményre vonatkozó kérelemhez a `Metadata:true` fejléc szükséges. Nem sikerült belefoglalni a fejlécet a kérelembe a Metadata Service "hibás kérés" válaszában.

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
| Napszállta | Az esemény globálisan egyedi azonosítója. <br><br> Példa: <br><ul><li>602d9444-d2cd-49c7-8624-8643e7171297  |
| EventType | Ez az esemény okozza a hatását. <br><br> Értékek: <br><ul><li> `Freeze`: a virtuális gép néhány másodpercig szünetelteti az ütemezést. Előfordulhat, hogy a processzor és a hálózati kapcsolat fel van függesztve, de nincs hatással a memóriára vagy a megnyitott fájlokra.<li>`Reboot`: a virtuális gép újraindításra van ütemezve (nem állandó memória elvész). <li>`Redeploy`: a virtuális gép egy másik csomópontra való áthelyezésre van ütemezve (az ideiglenes lemezek elvesznek). <li>`Preempt`: a helyszíni virtuális gép törlődik (az ideiglenes lemezek elvesznek).|
| ResourceType | Az eseményt érintő erőforrás típusa. <br><br> Értékek: <ul><li>`VirtualMachine`|
| Segédanyagok és eszközök| Az eseményt érintő erőforrások listája. A lista garantáltan legfeljebb egy [frissítési tartományból](manage-availability.md)származó gépeket tartalmazhat, de előfordulhat, hogy nem tartalmazza a UD összes számítógépét. <br><br> Példa: <br><ul><li> ["FrontEnd_IN_0", "BackEnd_IN_0"] |
| EventStatus | Az esemény állapota. <br><br> Értékek: <ul><li>`Scheduled`: ez az esemény a `NotBefore` tulajdonságban megadott idő után indul el.<li>`Started`: ez az esemény megkezdődött.</ul> Még nincs megadva `Completed` vagy hasonló állapot. Az eseményt a rendszer már nem adja vissza az esemény befejeződése után.
| NotBefore| Az esemény elindításának időpontja. <br><br> Példa: <br><ul><li> Hétfő, 19 Sep 2016 18:29:47 GMT  |

### <a name="event-scheduling"></a>Események ütemezése
Az események a jövőben az esemény típusa alapján a lehető legkevesebb időt ütemezik. Ez az idő az esemény `NotBefore` tulajdonságában jelenik meg. 

|EventType  | Minimális figyelmeztetés |
| - | - |
| Fázik| 15 perc |
| Újraindítás | 15 perc |
| Ismételt üzembe helyezés | 10 perc |
| Megelőzik | 30 másodperc |

### <a name="start-an-event"></a>Esemény indítása 

Miután elolvasta a közelgő eseményt, és befejezi a logikát a zökkenőmentes leállítás érdekében, jóváhagyhatja a függőben lévő eseményt úgy, hogy `POST` hívást indít el Metadata Service a `EventId`. Ez a hívás az Azure-ra utal, hogy lerövidítheti az értesítés minimális idejét (ha lehetséges). 

A következő JSON-minta várható a `POST` kérelem törzsében. A kérésnek tartalmaznia kell `StartRequests`listáját. Minden `StartRequest` a felgyorsítani kívánt eseményhez `EventId` tartalmaz:
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
> Egy esemény elfogadásával engedélyezhető, hogy az esemény az esemény összes `Resources` folytassa, nem csak az eseményt visszaigazoló virtuális gépet. Ezért dönthet úgy, hogy egy vezetőt választ ki a nyugtázás koordinálására, amely a `Resources` mező első számítógépének egyszerűvé válhat.

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

## <a name="next-steps"></a>Következő lépések 
- Tekintse [meg Scheduled Eventsét az Azure fridayban](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance) , és tekintse meg a bemutatót. 
- Tekintse át a Scheduled Events kód mintáit az [Azure-példány metaadatainak Scheduled Events GitHub-tárházban](https://github.com/Azure-Samples/virtual-machines-scheduled-events-discover-endpoint-for-non-vnet-vm).
- További információ a [instance metadata Service](instance-metadata-service.md)elérhető API-król.
- Ismerje meg az [Azure-beli Linux rendszerű virtuális gépek tervezett karbantartását](planned-maintenance.md).
