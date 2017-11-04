---
title: "Linux virtuális gépek Azure-ban karbantartási értesítések kezelése |} Microsoft Docs"
description: "Az Azure-ban futó Linux virtuális gépek karbantartási értesítések megtekintését, és indítsa el az önkiszolgáló karbantartási."
services: virtual-machines-linux
documentationcenter: 
author: zivraf
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/26/2017
ms.author: zivr
ms.openlocfilehash: be062ce9cfbe7486ef500dd9d27418cbf245d6e0
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2017
---
# <a name="handling-planned-maintenance-notifications-for-linux-virtual-machines"></a>A Linux virtuális gépek kezelési tervezett karbantartási értesítések

Azure rendszeres időközönként megbízhatóságát, teljesítményét és a virtuális gépek állomás infrastruktúra biztonságát javító frissítés. Frissítés esetén például az üzemeltetési környezetben javítását vagy frissítése és hardver leszerelése módosításokat. A legtöbb, a frissítések a futtatott virtuális gépek számára gyakorolt hatás nélkül kerül sor. Vannak azonban esetekben, amikor frissítések ütközés:

- A karbantartási nem igényel újraindítást, ha Azure helyben történő áttelepítés használatával a virtuális gép felfüggesztése, amíg a gazdagép frissül.

- Ha karbantartási újraindítást igényel, kap értesítést, ha a karbantartási tervezett. Ezekben az esetekben, lehetősége van egy olyan időkeretet, ahol megkezdheti a karbantartási magát, ha az Ön működik.


A rendszer újraindítását igénylő tervezett karbantartás hullámok van ütemezve. Minden egyes wave hatóköre különböző (régió).

- A hanghullám kezdődik-e egy értesítést, az ügyfél számára. Alapértelmezés szerint az előfizetés tulajdonosa és a társtulajdonosok értesítést küld. Az értesítések további címzettek és e-mailek, az SMS és Webhookokkal, például üzenetküldési beállítások adhat hozzá. 
- Az értesítés követően egy önkiszolgáló ablak van beállítva. Ezen időszak alatt található a virtuális gépek közül melyik megtalálható a hanghullám és kezdő karbantartási proaktív helyezze üzembe újra használatával. 
- Az önkiszolgáló ablak következő ütemezett karbantartási időszak kezdődik. Jelenleg az Azure ütemezi, és a szükséges karbantartás vonatkozik a virtuális gép.  

Rendelkezik két windows a célja, hogy a karbantartási indítása, és indítsa újra a virtuális gép tudatában, amikor Azure automatikusan elindul, karbantartási elegendő időt biztosítanak.

Az Azure parancssori felület, a PowerShell, a REST API-t és az Azure-portálon segítségével megkeresheti a karbantartási időszakok a virtuális gépek, és indítsa el az önkiszolgáló karbantartási.

 > [!NOTE]
 > Próbál indítsa el a karbantartási és sikertelen lesz, ha az Azure jelöli meg a virtuális Gépet állítsanak **kihagyva** , és indítsa újra a rendszert az ütemezett karbantartási időszak alatt. Ehelyett meg próbál kapcsolódni a később egy új ütemezéssel. 

## <a name="find-vms-scheduled-for-maintenance-using-cli"></a>Található virtuális gépek ütemezett karbantartás parancssori felület használatával

Tervezett karbantartás információk segítségével látható [get-példány-nézet az azure virtuális gép](/cli/azure/vm?view=azure-cli-latest#az_vm_get_instance_view).
 
Tervezett karbantartás esetén csak karbantartási információkat ad vissza. Ha nincs karbantartás ütemezve, hogy a virtuális gép hatások, a parancs nem ad vissza karbantartási információt. 

```azure-cli
az vm get-instance-view  - g rgName  -n vmName 
```

A következő értékek visszaadott MaintenanceRedeployStatus alatt: 

| Érték | Leírás   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Azt jelzi, hogy elindíthatja karbantartási a virtuális Gépre most ||
| PreMaintenanceWindowStartTime         | Önkiszolgáló időszak során a virtuális gépen is kezdeményezhető a karbantartási kezdete ||
| PreMaintenanceWindowEndTime           | Az önkiszolgáló időszak során a virtuális gépen is kezdeményezhető a karbantartási végén ||
| MaintenanceWindowStartTime            | A karbantartás kezdete ablak ütemezni, amikor karbantartási kezdeményezhet a virtuális gépen ||
| MaintenanceWindowEndTime              | Az ütemezett időszak során a virtuális gépen is kezdeményezhető a karbantartási végén ||
| LastOperationResultCode               | Kezdeményezni a virtuális gép karbantartása tett legutóbbi kísérlet eredménye ||


## <a name="start-maintenance-on-your-vm-using-cli"></a>Karbantartási elindítani a virtuális gép parancssori felület használatával

A következő hívást kezdeményez a virtuális gép karbantartási, ha `IsCustomerInitiatedMaintenanceAllowed` értéke TRUE.

```azure-cli
az vm perform-maintenance rgName vmName 
```

[!INCLUDE [virtual-machines-common-maintenance-notifications](../../../includes/virtual-machines-common-maintenance-notifications.md)]


## <a name="faq"></a>GYIK


**K: Miért van szüksége a virtuális gépek újraindítja most?**

**V:** során a frissítések és verziófrissítések az Azure platformra többsége nem befolyásolja a virtuális gép rendelkezésre állására, vannak esetek, ahol jelenleg nem újraindítására az Azure-ban üzemeltetett virtuális gépeket. A Microsoft rendelkezik halmozott több olyan változás, ehhez a számunkra, hogy indítsa újra a kiszolgálókat, ez a virtuális gépek újraindítás szükséges.

**K:, ha szeretnék kövesse a magas rendelkezésre állásra rendelkezésre állási csoport használatával, tudom biztonságos?**

**V:**egy rendelkezésre állási telepített virtuális gépek vagy virtuálisgép-méretezési csoportok rendelkezik a következő formátumban: a frissítési tartományok (UD). Ha karbantartást végez, Azure eleget tegyen a UD megkötés, és nem újraindul a virtuális gépek különböző UD (belül az azonos rendelkezésre állási csoport).  Azure is legalább 30 percet, mielőtt a következő csoportba tartozó virtuális gépek áthelyezése vár. 

További információ a magas rendelkezésre állású kezelése tekintse meg a Windows Azure virtuális gépek rendelkezésre állását, vagy Azure Linux virtuális gépek rendelkezésre állásának kezelése.

**K: van állítva egy másik régióban vész-helyreállítási. Tudom biztonságos?**

**V:** minden Azure-régióban párosított belül az azonos földrajzi hely (például az Egyesült Államok, Európa vagy Ázsia) egy másik régióban. A tervezett Azure-frissítések egyszerre csak a régiópár egyik tagján jelennek meg, ami csökkenti az állásidőt és az alkalmazáskimaradás kockázatát. Tervezett karbantartás közben Azure is ütemezheti a felhasználók számára a karbantartási start hasonló ablak, azonban az ütemezett karbantartási időszaknál párosított régiók közötti eltérőek lesznek.  

További információ az Azure-régiók régiók és az Azure virtuális gép rendelkezésre állási tájékozódhat.  Itt a regionális párok teljes listája látható.

**K: hogyan tegye I get értesítse a tervezett karbantartások?**

**V:** egy tervezett karbantartási wave elindul egy vagy több Azure-régiók egy ütemezés beállításával. Miután, e-mailben értesítést kap az előfizetésnél tulajdonos (egy e-mailek előfizetésenként). További csatorna és az értesítési címzettek sikerült konfigurálni az Tevékenységriasztásokat napló használatával. Abban az esetben, ha telepít egy virtuális gép egy régiót, ahol tervezett karbantartás már ütemezve van, nem kapják meg az értesítést, de ahelyett, hogy ellenőrizni kell a virtuális Gépet karbantartás állapotát.

**K: nem szerepel az összes arra utal, hogy a portálon, a Powershell vagy a parancssori felület, mi tervezett karbantartás?**

**V:** tervezett karbantartási kapcsolódó áll rendelkezésre információ csak a virtuális gépek, amelyek negatív hatással lehet az fog a tervezett karbantartások hullám során. Ez azt jelenti Ha adatokat nem jelenik meg, annak oka lehet, hogy a karbantartási wave rendelkezik már befejeződött (vagy nem kezdődött el) vagy az, hogy a virtuális gép már található egy frissített kiszolgálót.

**K: kell a karbantartási is elindítani a virtuális gépen?**

**V:** általában egy felhőalapú szolgáltatás, a rendelkezésre állási csoport vagy a virtuális gépek méretezési, telepítendő munkaterhelések rugalmasak tervezett karbantartás.  Tervezett karbantartás közben csak egyetlen frissítési tartományi egy adott időpontban van hatással. Vegye figyelembe, hogy a frissítési tartományok befolyásolja sorrendjét nem feltétlenül fordulhat elő egymás után.

Érdemes lehet start karbantartási saját magának a következő esetekben:
- Az alkalmazás egy virtuális gépen fut, és az összes karbantartás alatt munkaidőn kívüli alkalmaznia kell a
- A karbantartás ideje koordinálja a szolgáltatásiszint-szerződés részeként kell
- Kell több mint 30 perc közötti minden virtuális gép újraindítása még akkor is a rendelkezésre állási belül meg.
- A teljes alkalmazás (többrétegű konfigurációk, több frissítési tartományt) befejezéséhez gyorsabb karbantartás miatt le szeretné.

**K: van mód, pontosan, ha a virtuális gép érintett?**

**V:** az ütemezés beállítása, azt határozzák meg, időkerete pedig néhány nap. Azonban a kiszolgálók (és a virtuális gépek) belüli megakadályozásához pontos sorrendi állapota ismeretlen. Az ügyfelek, akik a virtuális gépek a pontos idő tudni szeretné használhatja ütemezett események és a virtuális gépekről a lekérdezés és a virtuális gép újraindításig eltelő 10 perc értesítést.
  
**K: mennyi ideig tart a virtuális gép újraindítását?**

**V:** attól függően, hogy a virtuális gép méretét, újraindítás is eltarthat néhány percig. Beállítja a Megjegyzés, abban az esetben használhatja a felhőszolgáltatások, méretezhető, vagy a rendelkezésre állási csoport, az aktiválási 30 perc, a virtuális gépek (UD) csoportjai között. 

**K: Mi lesz a felhőszolgáltatásokat, a méretezési készlet és a Service Fabric esetén a felhasználói élmény?**

**V:** ezekről a platformokról tervezett karbantartás által érintett, amíg ezek platformokat használó ügyfelek minősülnek kap, hogy csak virtuális gépek a egy egyetlen frissítési tartományi (UD) biztonságos csökkenhet az adott időpontban.  

**K: I hardver leállítására vonatkozó e-mailben kapott, megegyezik a tervezett karbantartások?**

**V:** hardver leszerelése az tervezett karbantartási esemény, amíg van még nincs előkészítve a használati eset az új felületet.  Várhatóan az ügyfelek számára Zavarba, abban az esetben, ha két különböző tervezett karbantartási hullámok kapcsolatos két hasonló e-mailt kapnak.

**K: nem szerepel az összes karbantartási információkat a virtuális gépeken. Mi a probléma?**

**V:** miért is nem lát minden karbantartási információt a a virtuális gépek számos oka lehet:
1.  A Microsoft belső jelölésű előfizetését használja.
2.  A virtuális gépek nem ütemezett karbantartás céljából. Lehet, hogy a karbantartási wave véget ért, megszakadt, vagy módosítható úgy, hogy a virtuális gépek által már nem érintettek.
3.  A virtuális gép listanézet hozzáadott "karbantartás" oszlop nem rendelkezik. Ez az oszlop alapértelmezett nézetbe jelentek meg, amíg az ügyfelek, akik nem alapértelmezett oszlopok megjelenítéséhez konfigurált manuálisan kell hozzáadni a **karbantartási** a virtuális gép listanézet alapját képező oszlop.

**K: a virtuális gép második ütemezett karbantartás céljából. Hogy miért?**

**V:** vannak a virtuális gép ütemezett karbantartási után már befejeződött a karbantartás-helyezze üzembe újra megjelenik néhány használati eseteket:
1.  Azt a karbantartási wave megszakítva és újraindítja azt egy másik hasznos. Annak oka az lehet, hogy hibás adattartalmat észleltünk, és egyszerűen kell telepíteni egy további hasznos.
2.  A virtuális Géphez lett *beforrott szolgáltatás* hardverhibája okozza egy másik csomópontjára
3.  Állítsa le a kijelölt (felszabadítása), és indítsa újra a virtuális gép
4.  Rendelkezik **automatikus leállítási** engedélyezve van a virtuális gép


## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan regisztrálhatja belül a virtuális gép használata a karbantartási események [ütemezett események](scheduled-events.md).