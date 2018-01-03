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
ms.date: 12/15/2017
ms.author: zivr
ms.openlocfilehash: bb231b4a5210019b36bb4bb123795b4762374c66
ms.sourcegitcommit: 8fc9b78a2a3625de2cecca0189d6ee6c4d598be3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/29/2017
---
# <a name="handling-planned-maintenance-notifications-for-linux-virtual-machines"></a>A Linux virtuális gépek kezelési tervezett karbantartási értesítések

Azure rendszeres időközönként megbízhatóságát, teljesítményét és a virtuális gépek állomás infrastruktúra biztonságát javító frissítés. Frissítés esetén például az üzemeltetési környezetben javítását vagy frissítése és hardver leszerelése módosításokat. Ezek a frissítések többségét a futtatott virtuális gépek számára gyakorolt hatás nélkül kerül sor. Vannak azonban esetekben, amikor frissítések ütközés:

- A karbantartási nem igényel újraindítást, ha Azure helyben történő áttelepítés használatával a virtuális gép felfüggesztése, amíg a gazdagép frissül.

- Ha karbantartási újraindítást igényel, kap értesítést, ha a karbantartási tervezett. Ezekben az esetekben, lehetősége van egy olyan időkeretet, ahol megkezdheti a karbantartási magát, ha az Ön működik.


A rendszer újraindítását igénylő tervezett karbantartás hullámok van ütemezve. Minden egyes wave hatóköre különböző (régió).

- A hanghullám kezdődik-e egy értesítést, az ügyfél számára. Alapértelmezés szerint az előfizetés tulajdonosa és a társtulajdonosok értesítést küld. Adhat hozzá további címzettek és e-mailek, az SMS és webhookokkal, például üzenetküldési beállítások az Azure használatával értesítések [napló Tevékenységriasztásokat](../../monitoring-and-diagnostics/monitoring-overview-activity-logs.md).  
- Az értesítés időpontjában egy *önkiszolgáló ablak* szeretné elérhetővé tenni. Ezen időszak alatt található a virtuális gépek szerepelnek a hanghullám, és proaktív indítsa el a karbantartási ütemezési szükség szerint.
- Az önkiszolgáló ablak után egy *ütemezett karbantartási időszaknál* kezdődik. Ebben az ablakban során egy ponton Azure ütemezi, és a szükséges karbantartás vonatkozik a virtuális gép. 

Rendelkezik két windows a célja, hogy a karbantartási indítása, és indítsa újra a virtuális gép tudatában, amikor Azure automatikusan elindul, karbantartási elegendő időt biztosítanak.


Az Azure-portálon, a PowerShell, a REST API-t és a parancssori felület segítségével lekérdezheti a karbantartási időszakok a virtuális gépek, és indítsa el az önkiszolgáló karbantartási.

 > [!NOTE]
 > Ha karbantartási indításakor, és a kérés nem teljesíthető, Azure jelöli meg a virtuális Gépet állítsanak **kihagyva**. Rendszer már nem fogja tudni használni az ügyfél által kezdeményezett karbantartási lehetőséget. A virtuális Gépet újra kell majd indítani az Azure-ban az ütemezett karbantartási fázisban kell.


 
## <a name="should-you-start-maintenance-using-during-the-self-service-window"></a>Kell karbantartási használatának megkezdése az önkiszolgáló időszak alatt?  

A következő irányelveket kell segítségével döntse el, hogy kell ezzel a funkcióval és karbantartási kezdjék a saját idő.

> [!NOTE] 
> Önkiszolgáló karbantartási nem feltétlenül érhető el az összes, a virtuális gépek. Annak megállapítása, hogy proaktív helyezze üzembe újra a virtuális gép számára elérhető, keresse meg a **most** a karbantartási állapota. Önkiszolgáló karbantartási szolgáltatás jelenleg nem érhető el a Cloud Services (webes/munkavégző szerepkör), a Service Fabric és a virtuálisgép-méretezési készlet.


Önkiszolgáló karbantartási üzembe helyezése nem ajánlott **rendelkezésre állási készletek** mivel ezek a magas rendelkezésre állású beállításokat, ahol a csak egy frissítési tartományt kihatással van egy adott időpontban. 
    - Lehetővé teszik a karbantartási Azure eseményindító, de vegye figyelembe, hogy a frissítési tartományok befolyásolja sorrendjét nem feltétlenül fordulhat elő egymás után, és hogy van-e a 30 perces szünet frissítési tartományok között.
    - Átmenetileg megszakad a kapacitás (1/frissítési tartományok számának) néhány fontos, ha azt is könnyen lehet kompenzálni hozzáadása példány lefoglalása a karbantartási időszakban. 

**Nem** önkiszolgáló karbantartási a következő helyzetekben használhatja: 
    - Ha leállítja a virtuális gépek gyakran, akár manuálisan, használja a DevTest labs, az automatikus rendszerleállítást, vagy olyan ütemezés, azt nem sikerült a karbantartási állapot visszaállítása és ezért a további állásidőt okoz.
    - A rövid élettartamú virtuális gépeken is tudja, hogy törli a karbantartási wave lejárta előtt. 
    - A helyi (rövid élettartamú) lemezre, amely a frissítés után fenntartásához szükséges tárolt nagy állapotú munkaterhelésekhez. 
    - Olyan esetekben, ahol a virtuális gép átméretezésekor, gyakran ahogy térhet karbantartási állapotát. 
    - Ha az ütemezett események, amelyek proaktív feladatátvételi vagy biztonságos leállításának részét a munkaterhelésből, karbantartási leállítási kezdetét 15 percet lehetővé teszik alkalmazó

**Használjon** önkiszolgáló karbantartási, ha szeretne futtatni az ütemezett karbantartási fázisban megszakítás nélkül a virtuális Gépet, és a fent említett másik jelzések sem alkalmazható. 

A legcélszerűbb önkiszolgáló karbantartási használatát a következő esetekben:
    - A felügyeleti vagy záró-ügyfél egy pontos karbantartási időszak közölnie kell. 
    - Egy adott időpontig a karbantartás végrehajtásához szükséges. 
    - Szeretné karbantartási, például többrétegű alkalmazást garantálja a biztonságos helyreállítás sorrendjének szabályozásához.
    - Frissítési tartományok (UDs) között a virtuális gép helyreállítási idő több mint 30 perc van szüksége. Frissítési tartományok között eltelő idő szabályozásához indít el a kell karbantartási a virtuális gépek egy frissítési tartomány (UD) egyszerre.



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
| MaintenanceWindowStartTime            | Az ütemezett karbantartási időszakot, amelyben Azure indít el a virtuális Gépet a karbantartás kezdete ||
| MaintenanceWindowEndTime              | Az ütemezett karbantartási időszakot, amelyben Azure indít el a virtuális gép karbantartása vége ||
| LastOperationResultCode               | Kezdeményezni a virtuális gép karbantartása tett legutóbbi kísérlet eredménye ||




## <a name="start-maintenance-on-your-vm-using-cli"></a>Karbantartási elindítani a virtuális gép parancssori felület használatával

A következő hívást kezdeményez a virtuális gép karbantartási, ha `IsCustomerInitiatedMaintenanceAllowed` értéke TRUE.

```azure-cli
az vm perform-maintenance rgName vmName 
```

[!INCLUDE [virtual-machines-common-maintenance-notifications](../../../includes/virtual-machines-common-maintenance-notifications.md)]

## <a name="classic-deployments"></a>Klasszikus üzembe helyezés

Ha továbbra is fennáll, melyeket az örökölt virtuális gépek telepített, a klasszikus üzembe helyezési modellel is CLI 1.0 használatával lekérdezés virtuális gépek és karbantartási kezdeményezni.

Ellenőrizze, hogy a helyes mód írja be a klasszikus virtuális gép használható:

```
azure config mode asm
```

A karbantartási állapota a virtuális gépek nevű *myVM*, típus:

```
azure vm show myVM 
``` 

A klasszikus nevű virtuális gép elindítja a karbantartási *myVM* a a *myService* szolgáltatás és *myDeployment* telepítési, írja be:

```
azure compute virtual-machine initiate-maintenance --service-name myService --name myDeployment --virtual-machine-name myVM
```


## <a name="faq"></a>GYIK


**K: Miért van szüksége a virtuális gépek újraindítja most?**

**V:** során a frissítések és verziófrissítések az Azure platformra többsége nem befolyásolja a virtuális gép rendelkezésre állására, vannak esetek, ahol jelenleg nem újraindítására az Azure-ban üzemeltetett virtuális gépeket. A Microsoft rendelkezik halmozott több olyan változás, ehhez a számunkra, hogy indítsa újra a kiszolgálókat, ez a virtuális gépek újraindítás szükséges.

**K:, ha szeretnék kövesse a magas rendelkezésre állásra rendelkezésre állási csoport használatával, tudom biztonságos?**

**V:** egy rendelkezésre állási telepített virtuális gépek vagy virtuálisgép-méretezési csoportok rendelkezik a következő formátumban: a frissítési tartományok (UD). Ha karbantartást végez, Azure eleget tegyen a UD megkötés, és nem újraindul a virtuális gépek különböző UD (belül az azonos rendelkezésre állási csoport).  Azure is legalább 30 percet, mielőtt a következő csoportba tartozó virtuális gépek áthelyezése vár. 

További információ a magas rendelkezésre állású: [régiók és az Azure virtuális gép rendelkezésre állási](regions-and-availability.MD).

**K: hogyan tegye I get értesítse a tervezett karbantartások?**

**V:** egy tervezett karbantartási wave elindul egy vagy több Azure-régiók egy ütemezés beállításával. Miután, e-mailben értesítést kap az előfizetésnél tulajdonos (egy e-mailek előfizetésenként). További csatorna és az értesítési címzettek sikerült konfigurálni az Tevékenységriasztásokat napló használatával. Abban az esetben, ha telepít egy virtuális gép egy régiót, ahol tervezett karbantartás már ütemezve van, nem kapják meg az értesítést, de ahelyett, hogy ellenőrizni kell a virtuális Gépet karbantartás állapotát.

**K: nem szerepel az összes arra utal, hogy a portálon, a Powershell vagy a parancssori felület, mi tervezett karbantartás?**

**V:** tervezett karbantartási kapcsolódó áll rendelkezésre információ csak a virtuális gépek, amelyek negatív hatással lehet az fog a tervezett karbantartások hullám során. Ez azt jelenti Ha adatokat nem jelenik meg, annak oka lehet, hogy a karbantartási wave rendelkezik már befejeződött (vagy nem kezdődött el) vagy az, hogy a virtuális gép már található egy frissített kiszolgálót.

**K: van mód, pontosan, ha a virtuális gép érintett?**

**V:** az ütemezés beállítása, azt határozzák meg, időkerete pedig néhány nap. Azonban a kiszolgálók (és a virtuális gépek) belüli megakadályozásához pontos sorrendi állapota ismeretlen. Az ügyfelek, akik a pontos idő tudni, hogy a virtuális gépek szeretné használható [események ütemezett](scheduled-events.md) és a virtuális gépekről lekérdezés és a virtuális gép újraindítása előtt 15 perces értesítést.

**K: mennyi ideig tart a virtuális gép újraindítását?**

**V:** attól függően, hogy a virtuális gép méretét, újraindítás lehet, hogy több percig tarthat a önkiszolgáló karbantartási időszak alatt. Során az Azure által kezdeményezett újraindítások az ütemezett karbantartási időszaknál az újraindítás általában körülbelül 25 percig tart. Vegye figyelembe, hogy abban az esetben használja a Cloud Services (webes/munkavégző szerepkör), a virtuálisgép-skálázási készletekben vagy a rendelkezésre állási csoportok, az aktiválási 30 perc közötti minden csoport a virtuális gépek (UD) az ütemezett karbantartási időszak alatt.

**K: Mi található a Cloud Services (webes/munkavégző szerepkör), a Service Fabric és a virtuálisgép-méretezési csoportok esetében?**

**V:** ezekről a platformokról tervezett karbantartás által érintett, amíg ezek platformokat használó ügyfelek minősülnek kap, hogy csak virtuális gépek a egy egyetlen frissítési tartományi (UD) biztonságos csökkenhet az adott időpontban. Önkiszolgáló karbantartási szolgáltatás jelenleg nem érhető el a Cloud Services (webes/munkavégző szerepkör), a Service Fabric és a virtuálisgép-méretezési készlet.

**K: I hardver leállítására vonatkozó e-mailben kapott, megegyezik a tervezett karbantartások?**

**V:** hardver leszerelése az tervezett karbantartási esemény, amíg van még nincs előkészítve a használati eset az új felületet.  

**K: nem szerepel az összes karbantartási információkat a virtuális gépeken. Mi a probléma?**

**V:** miért is nem lát minden karbantartási információt a a virtuális gépek számos oka lehet:
1.  A Microsoft belső jelölésű előfizetését használja.
2.  A virtuális gépek nem ütemezett karbantartás céljából. Lehet, hogy a karbantartási wave véget ért, megszakadt, vagy módosítható úgy, hogy a virtuális gépek által már nem érintettek.
3.  Nem rendelkezik a **karbantartási** a virtuális gép listanézetre felvett oszloppal. Ez az oszlop alapértelmezett nézetbe jelentek meg, amíg az ügyfelek, akik nem alapértelmezett oszlopok megjelenítéséhez konfigurált manuálisan kell hozzáadni a **karbantartási** a virtuális gép listanézet alapját képező oszlop.

**K: a virtuális gép második ütemezett karbantartás céljából. Hogy miért?**

**V:** vannak a virtuális gép ütemezett karbantartási után már befejeződött a karbantartás-helyezze üzembe újra megjelenik néhány használati eseteket:
1.  Azt a karbantartási wave megszakítva és újraindítja azt egy másik hasznos. Annak oka az lehet, hogy hibás adattartalmat észleltünk, és egyszerűen kell telepíteni egy további hasznos.
2.  A virtuális Géphez lett *beforrott szolgáltatás* hardverhibája okozza egy másik csomópontjára
3.  Állítsa le a kijelölt (felszabadítása), és indítsa újra a virtuális gép
4.  Rendelkezik **automatikus leállítási** engedélyezve van a virtuális gép


**K: a rendelkezésre állási csoport karbantartási hosszú ideig tart, és a "kihagyva" állapot egyes a rendelkezésre állási beállítása példányok ekkor megjelenik. Hogy miért?** 

**V:** kattintott, a rendelkezésre állási készlet rövid egymás után több példány frissítéséhez, ha Azure ezek a kérelmek várólistára kerülnek, és egyszerre csak egy frissítési tartomány (UD) VMs frissítése elindítja. Azonban mivel előfordulhat, hogy egy szünet frissítési tartományok között, a frissítés úgy tűnhet, hogy tovább tart. Ha a frissítés várólista hosszabb 60 percnél hosszabb időt vesz igénybe, bizonyos esetekben jelennek-e a **kihagyva** állapot akkor is, ha azok sikeresen frissítve. Ez helytelen állapotot elkerülése érdekében a rendelkezésre állási készletek kattintva csak egy rendelkezésre állási példány frissítés beállítása, és várja meg a frissítés befejeződése után kattintson a különböző frissítési tartományban a következő virtuális gép esetében a.


## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan regisztrálhatja belül a virtuális gép használata a karbantartási események [ütemezett események](scheduled-events.md).
