---
title: "Események ütemezett Linux virtuális gépek Azure-ban |} Microsoft Docs"
description: "Események ütemezése a Linux virtuális gépek Azure metaadat-szolgáltatás használatával."
services: virtual-machines-windows, virtual-machines-linux, cloud-services
documentationcenter: 
author: ericrad
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/14/2017
ms.author: ericrad
ms.openlocfilehash: 1f4c542c179f196e61457ddba6b9dfe76dd1dc79
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/24/2018
---
# <a name="azure-metadata-service-scheduled-events-for-linux-vms"></a>Azure metaadat-szolgáltatás: Linux virtuális gépek ütemezett eseménye

Ütemezett események egy Azure metaadat-szolgáltatás, amely az alkalmazás időpontot készíti elő a virtuális gép (VM) karbantartás. Információt ad a közelgő karbantartásokról események (például újraindítás), hogy az alkalmazás előkészítése őket, és korlátozza megszakítása. Érhető el az összes Azure virtuális gépek esetében, beleértve a PaaS és a Windows és Linux IaaS. 

További információk a Windows az ütemezett eseményekről: [ütemezett eseményeket a Windows virtuális gépek](../windows/scheduled-events.md).

> [!Note] 
> Általában ütemezett események érhető el az összes Azure-régió. Lásd: [verzióját és régiónkénti elérhetőség](#version-and-region-availability) legújabb kiadás információt.

## <a name="why-use-scheduled-events"></a>Ütemezett események miért érdemes használni?

Számos alkalmazás is kihasználhatja a le készíti elő a virtuális gép karbantartási idő. Az idő, amelyek javítják a rendelkezésre állási, megbízhatóságát és szervizelhetőségét, az alkalmazás-specifikus feladatok végrehajtására használható többek között: 

- Ellenőrzőpont- és visszaállítás.
- A kapcsolat kiürítését.
- Elsődleges replikák közötti feladatátvétel.
- Az Eltávolítás load balancer készletből.
- Események naplózása.
- Szabályos leállítást.

Az ütemezett esemény az alkalmazás felderíthetők arról, hogy a karbantartási mikor fog fordulhat elő, és korlátozza a hatását feladatot indít.  

Ütemezett események biztosítja az események a következő esetekben használja:

- Platform által kezdeményezett karbantartási (például a gazda operációs rendszer frissítése)
- A felhasználó által kezdeményezett karbantartási (például a felhasználó újraindítja vagy a virtuális gépek redeploys)

## <a name="the-basics"></a>Az alapok  

  Metaadat-szolgáltatás közzétesz egy REST-végpontot, amelyhez a virtuális Gépen belülről érhetők el a virtuális gépek futtatásával kapcsolatos információkat. Az információ áll rendelkezésre egy útválasztást IP-cím használatával, úgy, hogy a virtuális gép kívül nincs felfedve.

### <a name="scope"></a>Hatókör
Ütemezett kézbesíti az eseményeket:

- A virtuális gépeinek egy felhőalapú szolgáltatás.
- Minden a virtuális gépek rendelkezésre állási csoportba.
- A virtuális gépeinek a skála elhelyezési csoport beállítása. 

Ennek eredményeképpen, ellenőrizze a `Resources` mező mellett az esemény azonosításához, mely virtuális gépek vannak hatással.

### <a name="endpoint-discovery"></a>Végpont felderítése
A virtuális hálózat virtuális gépek engedélyezve van, metaadat-szolgáltatás érhető el a statikus útválasztást IP-címről `169.254.169.254`. A teljes végpont ütemezett események legújabb verziója van: 

 > `http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01`

Ha a virtuális gép nem hozza létre a virtuális hálózatról, az alapértelmezett eset felhőalapú szolgáltatásokhoz és a klasszikus virtuális gépeket, további logikát kell Fedezze fel az IP-cím használatára. Megtudhatja, hogyan [a gazdagép-végpont felderítése](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm), tekintse meg ezt a mintát.

### <a name="version-and-region-availability"></a>Verzió és régiónkénti elérhetőség
Az ütemezett események szolgáltatás nem rendszerverzióval ellátott. Verziók az alábbiak kötelező; a jelenlegi verzió: `2017-08-01`.

| Verzió | Kiadás típusa | Régiók | Kibocsátási megjegyzések | 
| - | - | - | - | 
| 2017-08-01 | Általános rendelkezésre állás | Összes | <li> $A aláhúzás távolítva erőforrásnevek Iaas virtuális gépekhez<br><li>Az összes kérelem érvényes metaadatok fejléc követelmény | 
| 2017-03-01 | Előzetes verzió | Összes | <li>Kezdeti kiadás


> [!NOTE] 
> Előző előzetes kiadásaiban ütemezett események {legújabb} támogatott api-verzióval. Ez a formátum már nem támogatott, és a jövőben elavulttá válik.

### <a name="enabling-and-disabling-scheduled-events"></a>Engedélyezése és letiltása ütemezett események
Ütemezett események engedélyezve van a szolgáltatás az első ideje eseményeket kérelmet használhat. Akár két perc alatt az első hívásában számíthat késleltetett választ.

Ütemezett események le van tiltva a szolgáltatás Ha nem tesz egy kérelmet 24 órán át.

### <a name="user-initiated-maintenance"></a>A felhasználó által kezdeményezett karbantartás
A felhasználó által kezdeményezett VM karbantartási az Azure-portálon, az API, a parancssori felület vagy a PowerShell ütemezett esemény eredményez. Majd tesztelheti a karbantartási előkészítése logika az alkalmazásban, és az alkalmazás előkészíti a felhasználó által kezdeményezett karbantartás.

Ha újraindítja a virtuális gép, egy eseményt, azzal a típussal `Reboot` van ütemezve. Ha egy virtuális Gépet, azzal a típussal esemény újbóli `Redeploy` van ütemezve.

## <a name="use-the-api"></a>Az API-val

### <a name="headers"></a>Fejlécek
Ha metaadat-szolgáltatás, meg kell adnia a fejléc `Metadata:true` annak érdekében, hogy a kérelem nem volt szándékos átirányítva. A `Metadata:true` fejléc szükség minden ütemezett események olyan kérelem esetében. Nem sikerült a fejlécet tartalmaz a kérelem a metaadat-szolgáltatás "Hibás kérelem" válaszára eredményez.

### <a name="query-for-events"></a>Lekérdezést eseményekhez.
A következő hívással is kereshet ütemezett eseményeket:

#### <a name="bash"></a>Bash
```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01
```

A válasz ütemezett események tömbjét tartalmazza. Üres tömb azt jelenti, hogy jelenleg események nem ütemezett.
Abban az esetben, ahol ütemezett események, a válasz események tömbjét tartalmazza. 
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
| EventType | Ez az esemény hatására hatása. <br><br> Értékek: <br><ul><li> `Freeze`: A virtuális gép felfüggesztése néhány másodpercig van ütemezve. A Processzor fel van függesztve, de nincs hatással a memória, a megnyitott fájlokat vagy a hálózati kapcsolat van. <li>`Reboot`: A virtuális gép ütemezett újraindítás. (Nem állandó memória elvész.) <li>`Redeploy`: A virtuális gép áthelyezése egy másik csomópontra van ütemezve. (A rövid élettartamú lemezeket is elvesznek.) |
| ResourceType | Ez az esemény érinti erőforrás típusát. <br><br> Értékek: <ul><li>`VirtualMachine`|
| További források| Ez az esemény érinti erőforrások listáját. A listában legalább egy gépeket tartalmazhat garantáltan [frissítési tartomány](manage-availability.md), de nem tartalmaz a UD összes gép. <br><br> Példa: <br><ul><li> ["FrontEnd_IN_0", "BackEnd_IN_0"] |
| EventStatus | Ez az esemény állapotát. <br><br> Értékek: <ul><li>`Scheduled`: Ez az esemény után a megadott ideig történő futásra van ütemezve a `NotBefore` tulajdonság.<li>`Started`: Ez az esemény elindult.</ul> Nem `Completed` vagy hasonló állapot valaha is biztosítja. Az esemény már nem ad vissza, amikor befejeződött az esemény.
| NotBefore| Idő elteltével ezt az eseményt elindíthatja. <br><br> Példa: <br><ul><li> 2016-09-19T18:29:47Z  |

### <a name="event-scheduling"></a>Esemény ütemezése
Minden esemény van ütemezve egy jövőbeli időpontot minimális mennyiségű esemény típusa alapján. Most megjelenik egy esemény `NotBefore` tulajdonság. 

|EventType  | Minimális értesítés |
| - | - |
| Rögzítése| 15 perc |
| Újraindítás | 15 perc |
| Ismételt üzembe helyezés | 10 perc |

### <a name="start-an-event"></a>Egy esemény indítása 

Miután egy jövőbeli esemény ismerje meg, és a biztonságos leállításának logikát Befejezés, a függőben lévő esemény jóváhagyhatja azáltal, hogy egy `POST` metaadat-ba irányuló hívás `EventId`. A hívás jelzi az Azure-ba, hogy azt a minimális értesítési lerövidíthető (amikor csak lehetséges). 

A következő JSON-mintát vár a `POST` kérelem törzse. A kérelemnek tartalmaznia kell a listája `StartRequests`. Minden egyes `StartRequest` tartalmaz `EventId` gyorsítása szeretné az esemény:
```
{
    "StartRequests" : [
        {
            "EventId": {EventId}
        }
    ]
}
```

#### <a name="bash-sample"></a>Bash minta
```
curl -H Metadata:true -X POST -d '{"DocumentIncarnation":"5", "StartRequests": [{"EventId": "f020ba2e-3bc0-4c40-a10b-86575a9eabd5"}]}' http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01
```

> [!NOTE] 
> Egy esemény igazolása lehetővé teszi, hogy folytatja az összes esemény `Resources` abban az esetben, nem csak a virtuális Gépet, amely elismeri az esemény. Ezért, dönthet úgy, koordinálhatja a nyugtázási, elképzelhető, hogy más dolga, mint az első machine vezető vállalat kiválasztják a `Resources` mező.

## <a name="python-sample"></a>Python-minta 

Az alábbi minta metaadat-szolgáltatás lekérdezi a ütemezett események, és hagyja jóvá a függőben lévő események:

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
- A Watch [Azure Friday események ütemezett](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance) egy bemutató megjelenítéséhez. 
- Tekintse át az ütemezett események mintakódok a [Azure példány metaadatok ütemezett események Github-tárházban](https://github.com/Azure-Samples/virtual-machines-scheduled-events-discover-endpoint-for-non-vnet-vm).
- További információk az API-k által biztosított a [példány metaadat-szolgáltatás](instance-metadata-service.md).
- További tudnivalók [a tervezett karbantartások Linux virtuális gépek Azure-ban](planned-maintenance.md).
