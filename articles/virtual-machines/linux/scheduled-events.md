---
title: Linuxos virtuális gépek ütemezett eseményei az Azure-ban
description: Események ütemezése az Azure Metadata Service linuxos virtuális gépek használatával.
author: mimckitt
ms.service: virtual-machines-windows
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: mimckitt
ms.openlocfilehash: ee600d7524af27a0e9e2ce0176e7bd4d1f60bc3b
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758557"
---
# <a name="azure-metadata-service-scheduled-events-for-linux-vms"></a>Azure metaadatszolgáltatás: Ütemezett események Linuxos virtuális gépekhez

Ütemezett események egy Azure metaadat-szolgáltatás, amely időt ad az alkalmazásnak a virtuális gép (VM) karbantartásának előkészítésére. Tájékoztatást nyújt a közelgő karbantartási események (például újraindítás), hogy az alkalmazás felkészülhet rájuk, és korlátozza a megszakítások. Minden Azure virtuális géptípushoz elérhető, beleértve a PaaS-t és az IaaS-t Windows és Linux rendszeren is. 

A Windows ütemezett eseményeiről a Windows virtuális gépek ütemezett eseményei című [témakörben](../windows/scheduled-events.md)talál további információt.

> [!Note] 
> Az ütemezett események általában minden Azure-régióban elérhetők. A legfrissebb kiadási információkért tekintse meg [a Verzió és a régió elérhetőségét.](#version-and-region-availability)

## <a name="why-use-scheduled-events"></a>Miért érdemes az Ütemezett eseményeket használni?

Számos alkalmazás számára előnyös lehet a virtuális gépek karbantartására való felkészülés ideje. Az idő használható olyan alkalmazásspecifikus feladatok végrehajtására, amelyek javítják a rendelkezésre állást, a megbízhatóságot és a szervizelhetőséget, beleértve a következőket: 

- Ellenőrzőpont és visszaállítás.
- A kapcsolat elvezetését.
- Elsődleges replika feladatátvétel.
- Eltávolítás a terheléselosztó készletből.
- Eseménynaplózás.
- Kecses leállás.

Ütemezett események, az alkalmazás felderítheti, ha karbantartás történik, és a feladatok at, hogy korlátozza annak hatását.  

Az ütemezett események a következő használati esetekben biztosíteseményeket:

- [A platform által kezdeményezett karbantartás](https://docs.microsoft.com/azure/virtual-machines/linux/maintenance-and-updates) (például virtuális gép újraindítása, élő áttelepítés vagy a gazdagép frissítéseinek megőrzése)
- A virtuális gép [leromlott gazdahardveren](https://azure.microsoft.com/blog/find-out-when-your-virtual-machine-hardware-is-degraded-with-scheduled-events) fut, amely az előrejelzések szerint hamarosan meghibásodik
- Felhasználó által kezdeményezett karbantartás (például egy felhasználó újraindítja vagy újratelepíti a virtuális gép)
- [A virtuális gép](spot-vms.md) és a [direkt méretezési csoport](../../virtual-machine-scale-sets/use-spot.md) példányának kilakoltatása.

## <a name="the-basics"></a>Az alapok  

  A metaadat-szolgáltatás a virtuális gépek futtatásával kapcsolatos információkat a virtuális gépből elérhető REST-végpont használatával teszi elérhetővé. Az információ nem irányítható IP-cím, így nem érhető el a virtuális gépen kívül.

### <a name="scope"></a>Hatókör
Az ütemezett eseményeket a következő kezekbe szállítjuk:

- Önálló virtuális gépek.
- A felhőszolgáltatás ban lévő összes virtuális gép.
- A rendelkezésre állási csoport összes virtuális gépe.
- A méretezési csoport összes virtuális gépe. 

Ennek eredményeképpen ellenőrizze `Resources` a mezőt az eseményben, hogy azonosítsa, mely virtuális gépek érintettek.

### <a name="endpoint-discovery"></a>Végpont felderítése
A virtuális hálózatra engedélyezett virtuális gépek esetében a metaadat-szolgáltatás statikus, nem irányítható IP-címből érhető `169.254.169.254`el. Az ütemezett események legújabb verziójának teljes végpontja a következő: 

 > `http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01`

Ha a virtuális gép nem jön létre egy virtuális hálózaton belül, a felhőszolgáltatások és a klasszikus virtuális gépek alapértelmezett esetei, további logika szükséges a használandó IP-cím felderítéséhez. A [gazdagép-végpont felderítése](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm)című témakörben olvashat.

### <a name="version-and-region-availability"></a>Verzió és régió elérhetősége
Az Ütemezett események szolgáltatás verziószámverziója. A verziók kötelezőek; a jelenlegi `2019-01-01`verzió .

| Verzió | Kiadás típusa | Régiók | Kibocsátási megjegyzések | 
| - | - | - | - | 
| 2019-01-01 | Általános elérhetőség | Összes | <li> Hozzáadott támogatása virtuálisgép-méretezési készletek EventType "Terminate" |
| 2017-11-01 | Általános elérhetőség | Összes | <li> Hozzáadott támogatása Spot VM kilakoltatás EventType "Preempt"<br> | 
| 2017-08-01 | Általános elérhetőség | Összes | <li> Előhúzott aláhúzás eltávolítása az IaaS virtuális gépek erőforrásneveiből<br><li>A metaadatok fejléckövetelménye minden kérelemre kikényszerítve | 
| 2017-03-01 | Előzetes verzió | Összes | <li>Kezdeti kiadás |


> [!NOTE] 
> Az ütemezett események korábbi előzetes verziói a(z) {latest} api-verziót támogatták. Ez a formátum már nem támogatott, és a jövőben elavult lesz.

### <a name="enabling-and-disabling-scheduled-events"></a>Ütemezett események engedélyezése és letiltása
Az ütemezett események engedélyezve vannak a szolgáltatáshoz, amikor először kér eseményeket. Az első hívása akár két perces késleltetett válaszra is számíthat.

Az ütemezett események le vannak tiltva a szolgáltatáshoz, ha 24 órán keresztül nem nyújt be kérelmet.

### <a name="user-initiated-maintenance"></a>Felhasználó által kezdeményezett karbantartás
A felhasználó által kezdeményezett virtuális gép karbantartása az Azure Portalon, API-n, CLI-n vagy PowerShellen keresztül egy ütemezett eseményt eredményez. Ezután tesztelheti a karbantartási előkészítési logikát az alkalmazásban, és az alkalmazás felkészülhet a felhasználó által kezdeményezett karbantartásra.

Ha újraindít egy virtuális gépet, `Reboot` egy ilyen típusú esemény van ütemezve. Ha újratelepít egy virtuális gépet, egy `Redeploy` ilyen típusú esemény van ütemezve.

## <a name="use-the-api"></a>Az API használata

### <a name="headers"></a>Fejlécek
Metaadat-szolgáltatás lekérdezésekénél meg `Metadata:true` kell adnia a fejlécet, hogy megbizonyosodjon arról, hogy a kérelem nem volt véletlenül átirányítva. A `Metadata:true` fejléc minden ütemezett eseménykéréshez szükséges. Ha nem tartalmazza a fejlécet a kérelemben, a Metaadat-szolgáltatás "Hibás kérés" válasza lép fel.

### <a name="query-for-events"></a>Események lekérdezése
Az ütemezett eseményeket a következő hívással kérdezheti le:

#### <a name="bash"></a>Bash
```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01
```

A válasz ütemezett események tömbjét tartalmazza. Az üres tömb azt jelenti, hogy jelenleg nincsenek események ütemezése.
Abban az esetben, ha ütemezett események vannak, a válasz események tömbjét tartalmazza. 
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

### <a name="event-properties"></a>Esemény tulajdonságai
|Tulajdonság  |  Leírás |
| - | - |
| Napszállta | Az esemény globálisan egyedi azonosítója. <br><br> Példa: <br><ul><li>602d9444-d2cd-49c7-8624-8643e7171297  |
| EventType (Eseménytípus) | Az esemény hatására. <br><br> Értékek: <br><ul><li> `Freeze`: A virtuális gép néhány másodpercre szüneteltetésre van ütemezve. A processzor és a hálózati kapcsolat felfüggeszthető, de nincs hatással a memóriára vagy a megnyitott fájlokra.<li>`Reboot`: A virtuális gép újraindításra van ütemezve (a nem állandó memória elvész). <li>`Redeploy`: A virtuális gép a tervek szerint egy másik csomópontra kerül (a rövid élettartamú lemezek elvesznek). <li>`Preempt`: A virtuális hely törlése folyamatban van (az ideiglenes lemezek elvesznek). <li> `Terminate`: A virtuális gép törlését tervezik. |
| ResourceType | Az esemény által befolyásolt erőforrás típusa. <br><br> Értékek: <ul><li>`VirtualMachine`|
| További források| Az esemény által befolyásolt erőforrások listája. A lista garantáltan legfeljebb egy [frissítési tartományból](manage-availability.md)származó gépeket tartalmaz, de előfordulhat, hogy nem tartalmazza az ud összes gépét. <br><br> Példa: <br><ul><li> ["FrontEnd_IN_0", "BackEnd_IN_0"] |
| EventStatus | Az esemény állapota. <br><br> Értékek: <ul><li>`Scheduled`: Ez az esemény a `NotBefore` tulajdonságban megadott idő után kezdődik.<li>`Started`: Az esemény elkezdődött.</ul> Nincs `Completed` vagy hasonló állapot valaha is biztosított. Az esemény már nem kerül visszaadásra, amikor az esemény befejeződik.
| Nem előtte| Az az idő, amely után ez az esemény elkezdődhet. <br><br> Példa: <br><ul><li> Hét, 19 Szept 2016 18:29:47 GMT  |

### <a name="event-scheduling"></a>Eseményütemezés
Minden esemény ütemezése a jövőben egy minimális ideig van ütemezve az esemény típusa alapján. Ez az idő az esemény `NotBefore` tulajdonában tükröződik. 

|EventType (Eseménytípus)  | Minimális értesítés |
| - | - |
| Fázik| 15 perc |
| Újraindítás | 15 perc |
| Ismételt üzembe helyezés | 10 perc |
| Preempt (Előmutató) | 30 másodperc |
| Megszünteti | [Felhasználó konfigurálható:](../../virtual-machine-scale-sets/virtual-machine-scale-sets-terminate-notification.md#enable-terminate-notifications)5-15 perc |

> [!NOTE] 
> Bizonyos esetekben az Azure képes előre jelezni állomás hiba miatt leromlott hardver, és megpróbálja csökkenteni a szolgáltatás zavarait az áttelepítés ütemezésével. Az érintett virtuális gépek egy ütemezett `NotBefore` eseményt kapnak, amely általában néhány nappal a jövőben lesz. A tényleges idő az előre jelzett hibakockázat-értékeléstől függően változik. Az Azure megpróbálja 7 nappal előre, ha lehetséges, de a tényleges idő változik, és kisebb lehet, ha az előrejelzés, hogy nagy az esélye a hardver meghibásodása hamarosan. A szolgáltatás rakoncátlehető kockázat minimalizálása érdekében abban az esetben, ha a hardver meghibásodik a rendszer által kezdeményezett áttelepítés előtt, azt javasoljuk, hogy a lehető leghamarabb telepítse újra a virtuális gépet.

### <a name="start-an-event"></a>Esemény indítása 

Miután tudomást szerzett egy közelgő eseményről, és befejezte a szabályos leállítás logikáját, jóváhagyhatja a kiemelkedő eseményt, ha `POST` hívást kezdeményez a Metadata Szolgáltatáshoz a segítségével. `EventId` Ez a hívás azt jelzi az Azure-nak, hogy lerövidítheti a minimális értesítési időt (ha lehetséges). 

A következő JSON-minta `POST` várható a kérelem törzsében. A kérelemnek tartalmaznia `StartRequests`kell a listáját. Mindegyik `StartRequest` `EventId` tartalmazza a meggyorsítani kívánt eseményt:
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
curl -H Metadata:true -X POST -d '{"StartRequests": [{"EventId": "f020ba2e-3bc0-4c40-a10b-86575a9eabd5"}]}' http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01
```

> [!NOTE] 
> Egy esemény nyugtázása lehetővé `Resources` teszi, hogy az esemény az esemény minden esetben, nem csak a virtuális gép, amely elismeri az eseményt. Ezért választhat, hogy válasszon egy vezetőt, hogy koordinálja a nyugtázást, ami lehet olyan egyszerű, mint az első gép a `Resources` területen.

## <a name="python-sample"></a>Python-minta 

A következő minta lekérdezi a Metaadat-szolgáltatást az ütemezett eseményekhez, és jóváhagyja az egyes függőben lévő eseményeket:

```python
#!/usr/bin/python

import json
import socket
import urllib2

metadata_url = "http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01"
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
        notbefore = evt['NotBefore'].replace(" ", "_")
        if this_host in resources:
            print("+ Scheduled Event. This host " + this_host +
                " is scheduled for " + eventtype + " not before " + notbefore)
            # Add logic for handling events here


def main():
    data = get_scheduled_events()
    handle_scheduled_events(data)


if __name__ == '__main__':
    main()
```

## <a name="next-steps"></a>További lépések 
- Tekintse meg [az Ütemezett eseményeket az Azure-ban pénteken](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance) egy bemutató megtekintéséhez. 
- Tekintse át az ütemezett események kódmintáit az [Azure-példány metaadat-alapú github-adattárában.](https://github.com/Azure-Samples/virtual-machines-scheduled-events-discover-endpoint-for-non-vnet-vm)
- További információ a [példánymetaadat-szolgáltatásban](instance-metadata-service.md)elérhető API-król.
- Ismerje meg [a Linux os virtuális gépek tervezett karbantartását az Azure-ban.](planned-maintenance.md)
