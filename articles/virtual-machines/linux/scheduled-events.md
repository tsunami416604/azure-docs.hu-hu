---
title: Ütemezett események az Azure-ban Linux rendszerű virtuális gépekhez |} A Microsoft Docs
description: Ütemezett események a Linux rendszerű virtuális gépek az Azure Metadata Service használatával.
services: virtual-machines-windows, virtual-machines-linux, cloud-services
documentationcenter: ''
author: ericrad
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: ericrad
ms.openlocfilehash: f18892d32a385b41f3325623e21da1dd25af7253
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/23/2018
ms.locfileid: "42744707"
---
# <a name="azure-metadata-service-scheduled-events-for-linux-vms"></a>Az Azure Metadata szolgáltatás: Ütemezett események a Linux rendszerű virtuális gépekhez

Az ütemezett események az Azure Metadata szolgáltatás, amely révén az alkalmazás előkészítése a virtuális gép (VM) karbantartási idő. Információt ad a közelgő karbantartásokról események (például az újraindítás), hogy az alkalmazás előkészítése őket, és korlátozza megszakítása. Érhető el minden Azure virtuális gépek esetében, beleértve a PaaS és IaaS Windows és Linux rendszereken egyaránt. 

További információ a Windows az ütemezett eseményekről: [ütemezett események a Windows virtuális gépek](../windows/scheduled-events.md).

> [!Note] 
> Az ütemezett események szolgáltatás általánosan elérhető az összes Azure-régióban. Lásd: [verzió és -régiók rendelkezésre állása](#version-and-region-availability) a legújabb kibocsátási információi.

## <a name="why-use-scheduled-events"></a>Miért érdemes használni az ütemezett eseményekről?

Számos alkalmazás fel a virtuális gép karbantartási idő is kihasználhatják. Az idő, amelyek javítják a rendelkezésre állás, megbízhatóság és szervizelhetőségét, alkalmazás-specifikus feladatok végrehajtására használható többek között: 

- Ellenőrzőpont és a visszaállítás.
- -Kapcsolatának kiürítéséhez.
- Elsődleges replika feladatátvétel.
- Eltávolítás a load balancer készletből.
- Események naplózása.
- Szabályos leállítást.

Az ütemezett eseményekről az alkalmazás képes felderíteni, amikor karbantartási fog fordulhat elő, és korlátozhatja a hatása feladatok aktiválása.  

Az ütemezett események biztosítja az események a következő esetekben használja:

- Platform által kezdeményezett karbantartás (például a gazda operációs rendszer frissítése)
- Felhasználó által kezdeményezett karbantartás (például egy felhasználó újraindítása vagy újbóli üzembe helyezése egy virtuális gép)

## <a name="the-basics"></a>Alapismeretek  

  Metaadat-szolgáltatás egy REST-végpont, amely elérhető a virtuális gépen futó virtuális gépek információt tesz elérhetővé. Az adatokat egy útválasztást IP-címen keresztül érhető el, így azt nem érhető el a virtuális gép kívül.

### <a name="scope"></a>Hatókör
Ütemezett kézbesíti az eseményeket:

- Összes virtuális gép egy cloud service-ben.
- Összes virtuális gép egy rendelkezésre állási csoportban.
- Egy méretezési csoportban lévő összes virtuális gép elhelyezési csoport megadása 

Emiatt ellenőrizze a `Resources` az esemény azonosításához az érintett virtuális gépek mely mezőbe.

### <a name="endpoint-discovery"></a>Végpont felderítése
A virtuális Hálózatot használó virtuális gépekről, Metadata szolgáltatás érhető el a statikus útválasztást IP-cím `169.254.169.254`. A teljes végpont az ütemezett eseményekről legújabb verzióját a következő: 

 > `http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01`

Ha a virtuális gép nem jön létre egy virtuális hálózatban, az alapértelmezett esetben a felhőszolgáltatásokat és a klasszikus virtuális gépeket, további logikát Fedezze fel az IP-cím használata szükséges. Megtudhatja, hogyan [a gazdagép-végpont felderítése](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm), tekintse meg ezt a mintát.

### <a name="version-and-region-availability"></a>Verzió és -régiók rendelkezésre állása
Az ütemezett eseményekről szolgáltatás nem rendszerverzióval ellátott. Verziók a következők kötelező; a jelenlegi verzió `2017-08-01`.

| Verzió | Kiadás típusa | Régiók | Kibocsátási megjegyzések | 
| - | - | - | - | 
| 2017-08-01 | Általános rendelkezésre állás | Összes | <li> Aláhúzás kiegészített távolítva erőforrásnevek Iaas virtuális gépekhez<br><li>Metaadatok fejléc követelmény irányuló kérések kényszerítése | 
| 2017-03-01 | Előzetes verzió | Összes | <li>Kezdeti kiadás


> [!NOTE] 
> {Legújabb} api-verzió is támogatott, az ütemezett eseményekről korábbi előzetes kiadásokat. Ez a formátum már nem támogatott, és később elavulttá válik.

### <a name="enabling-and-disabling-scheduled-events"></a>Engedélyezése és letiltása az ütemezett események
Az ütemezett események a szolgáltatás az első alkalommal események kérelmet győződjön meg arról, engedélyezve van. Az első hívás akár két perc alatt számíthat késleltetett választ.

Az ütemezett események a szolgáltatás tiltva van, ha nem derül kérelem 24 órán keresztül.

### <a name="user-initiated-maintenance"></a>Felhasználó által kezdeményezett karbantartás
Felhasználó által kezdeményezett virtuális Gépet karbantartás az Azure Portalon, az API-t, a parancssori felület vagy a Powershellen keresztül ütemezett esemény eredményez. Ezután tesztelheti a karbantartási előkészítési logikát az alkalmazásban, és a felhasználó által kezdeményezett karbantartás céljából készítheti elő az alkalmazás.

Ha a virtuális gép, egy esemény típusú újraindítása `Reboot` van ütemezve. Ha egy virtuális gép, egy esemény típusa és ismételt üzembe `Redeploy` van ütemezve.

## <a name="use-the-api"></a>Az API-val

### <a name="headers"></a>Fejlécek
Előfordulhat, hogy metaadat-szolgáltatás, meg kell adnia a fejléc `Metadata:true` annak érdekében, hogy a kérelem nem volt szándékos átirányítva. A `Metadata:true` fejlécet meg kell adni az ütemezett események irányuló kérések. Nem sikerült a fejlécet tartalmazza a kérés egy "Hibás kérés" Metadata szolgáltatás válasza eredményez.

### <a name="query-for-events"></a>Lekérdezés-események
Ütemezett események azáltal, hogy a következő hívást lekérdezés:

#### <a name="bash"></a>Bash
```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01
```

A válasz az ütemezett események tömbjét tartalmazza. Üres tömb, az azt jelenti, hogy jelenleg nincsenek események ütemezve vannak.
Abban az esetben, ahol az ütemezett események, a válasz események tömbjét tartalmazza. 
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
| EventId | Globálisan egyedi azonosítóját az eseményhez. <br><br> Példa: <br><ul><li>602d9444-d2cd-49c7-8624-8643e7171297  |
| EventType | Ez az esemény hatására a hatás. <br><br> Értékek: <br><ul><li> `Freeze`: A virtuális gép úgy van ütemezve, szüneteltetésére néhány másodpercig. A Processzor fel van függesztve, de nem érintik a memória, a megnyitott fájlokat vagy a hálózati kapcsolatok. <li>`Reboot`: A virtuális gép újraindításra van ütemezve. (Nonpersistent memória elvész.) <li>`Redeploy`: A virtuális gép áthelyezése egy másik csomópontra van ütemezve. (A rövid élettartamú lemezeket is elvesznek.) |
| ResourceType | Ez az esemény érinti erőforrás típusát. <br><br> Értékek: <ul><li>`VirtualMachine`|
| További források| Ez az esemény érinti erőforrások listája. A lista tartalmaz gépeket legfeljebb egy garantáltan [frissítési tartományt](manage-availability.md), azonban nem tartalmaz a UD minden gépek. <br><br> Példa: <br><ul><li> ["FrontEnd_IN_0", "BackEnd_IN_0"] |
| EventStatus | Ez az esemény állapota. <br><br> Értékek: <ul><li>`Scheduled`: Ez az esemény után a megadott ideig történő futásra van ütemezve a `NotBefore` tulajdonság.<li>`Started`: Ez az esemény feldolgozása megkezdődött.</ul> Nem `Completed` vagy hasonló állapota minden eddiginél biztosítja. Az esemény már nem ad vissza az esemény befejezése.
| NotBefore| Az idő elteltével megkezdheti a ezt az eseményt. <br><br> Példa: <br><ul><li> 19 Sep 2016 hétfő, 18:29:47 GMT  |

### <a name="event-scheduling"></a>Esemény ütemezése
Minden esemény van ütemezve egy jövőbeli időpontot minimális mennyiségű esemény típusa alapján. Most megjelenik egy esemény `NotBefore` tulajdonság. 

|EventType  | Minimális értesítés |
| - | - |
| Rögzítése| 15 perc |
| Újraindítás | 15 perc |
| Ismételt üzembe helyezés | 10 perc |

### <a name="start-an-event"></a>Egy esemény indítása 

Miután egy közelgő esemény kapcsolatos további információért, és fejezze be a szabályos leállítást logika, a szálankénti függőben lévő eseményt jóváhagyhatja azáltal, hogy egy `POST` metaadat szolgáltatás hívása `EventId`. Ez a hívás azt jelzi, hogy az Azure-ba, lerövidítheti a minimális értesítési idő (ha lehet). 

A következő JSON-mintát vár a `POST` kérelem törzse. A kérés tartalmazhat listáját `StartRequests`. Minden egyes `StartRequest` tartalmaz `EventId` számára a kívánt esemény:
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
curl -H Metadata:true -X POST -d '{"StartRequests": [{"EventId": "f020ba2e-3bc0-4c40-a10b-86575a9eabd5"}]}' http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01
```

> [!NOTE] 
> Egy esemény bosszankodnak lehetővé teszi, hogy az összes folytatja az esemény `Resources` abban az esetben, nem csak a virtuális gép, amely elismeri az eseményt. Választhat, ezért a nyugtázás, amely lehet egyszerű: az első gép koordinálására vezető a `Resources` mező.

## <a name="python-sample"></a>Python-minta 

Az alábbi minta Metadata szolgáltatás lekérdezi az ütemezett események, és hagyja jóvá az egyes szálankénti függőben lévő események:

```python
#!/usr/bin/python

import json
import urllib2
import socket
import sys

metadata_url = "http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01"
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
- Tekintse meg [ütemezett események az Azure Friday](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance) bemutató megtekintéséhez. 
- Tekintse át az ütemezett eseményekről Kódminták a [Azure példány metaadatok ütemezett események Github-adattár](https://github.com/Azure-Samples/virtual-machines-scheduled-events-discover-endpoint-for-non-vnet-vm).
- További információ az elérhető API-k a [Instance Metadata szolgáltatás](instance-metadata-service.md).
- Ismerje meg [tervezett karbantartás az Azure-ban Linux rendszerű virtuális gépek](planned-maintenance.md).
