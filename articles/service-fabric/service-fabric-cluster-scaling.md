---
title: Azure Service Fabric fürtméretezés
description: Ismerje meg az Azure Service Fabric-fürtök méretezését, illetve ki- és leméretezését. Az alkalmazás igények változása, így a Service Fabric-fürtök is.
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: atsenthi
ms.openlocfilehash: 9dd60a5898b648215fc8b26e49a706a7b19dfeeb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258693"
---
# <a name="scaling-azure-service-fabric-clusters"></a>Azure Service Fabric-fürtök méretezése
A Service Fabric-fürt olyan virtuális vagy fizikai gépek hálózathoz csatlakoztatott készlete, amelybe a mikroszolgáltatások üzembe helyezése és kezelése történik. Egy gép vagy virtuális gép, amely egy fürt része, nevezzük csomópont. A fürtök potenciálisan több ezer csomópontot tartalmazhatnak. A Service Fabric-fürt létrehozása után vízszintesen (módosíthatja a csomópontok számát) vagy függőlegesen skálázhatja a fürtöt (módosíthatja a csomópontok erőforrásait).  A fürt bármikor méretezhető, még akkor is, ha a számítási feladatok futnak a fürtön.  A fürt méretezése, az alkalmazások automatikusan skálázható is.

Miért érdemes átméretezni a fürtöt? Az alkalmazás igények idővel változnak.  Előfordulhat, hogy növelnie kell a fürt erőforrásait, hogy megfeleljen a megnövekedett alkalmazásterhelésnek vagy hálózati forgalomnak, vagy csökkentse a fürterőforrásait, ha az igény csökken.

## <a name="scaling-in-and-out-or-horizontal-scaling"></a>Méretezés be- és kiméretezés, illetve vízszintes méretezés
Módosítja a csomópontjainak számát a fürtben.  Miután az új csomópontok csatlakoztak a fürthöz, a [fürterőforrás-kezelő](service-fabric-cluster-resource-manager-introduction.md) áthelyezi a szolgáltatásokat, amelyek csökkentik a meglévő csomópontok terhelését.  A csomópontok számát is csökkentheti, ha a fürt erőforrásait nem használja hatékonyan.  Ahogy a csomópontok elhagyják a fürtöt, a szolgáltatások elmozdulnak ezekről a csomópontokról, és a terhelés növekszik a fennmaradó csomópontokon.  Az Azure-ban futó fürt csomópontjaiszámának csökkentése pénzt takaríthat meg, mivel a használt virtuális gépek számáért fizet, és nem a virtuális gépek munkaterheléséért.  

- Előnyök: Végtelen skála, elméletben.  Ha az alkalmazás méretezhetőségre lett tervezve, további csomópontok hozzáadásával engedélyezheti a korlátlan növekedést.  A felhőalapú környezetekben használt eszközök megkönnyítik a csomópontok hozzáadását vagy eltávolítását, így könnyen módosíthatja a kapacitást, és csak a használt erőforrásokért kell fizetnie.  
- Hátrányok: Az alkalmazásokat [méretezhetőségre](service-fabric-concepts-scalability.md)kell tervezni.  Az alkalmazás-adatbázisok és a perzisztencia további architekturális munkát is igényelhet.  A Service Fabric állapotalapú szolgáltatásaimegbízható [gyűjtemények](service-fabric-reliable-services-reliable-collections.md) azonban sokkal könnyebbé teszik az alkalmazásadatok méretezését.

A virtuálisgép-méretezési csoportok egy Azure-számítási erőforrás, amely segítségével üzembe helyezheti és kezelheti a virtuális gépek gyűjteménye egy készlet. Az Azure-fürtben definiált minden csomóponttípus [külön méretezési csoportként van beállítva.](service-fabric-cluster-nodetypes.md) Ezután minden csomóponttípus egymástól függetlenül méretezhető, különböző portkészleteket nyithat meg, és különböző kapacitásmetrikákat is létrehozhat. 

Egy Azure-fürt méretezésekénél tartsa szem előtt az alábbi irányelveket:
- az éles számítási feladatokat futtató elsődleges csomóponttípusoknak mindig öt vagy több csomót kell rendelkezniük.
- az állapotalapú éles számítási feladatokat futtató nem elsődleges csomóponttípusoknak mindig öt vagy több csomót kell rendelkezniük.
- állapot nélküli éles számítási feladatokat futtató nem elsődleges csomóponttípusoknak mindig két vagy több csomót kell rendelkezniük.
- Az Arany vagy ezüst [bármilyen csomóponttípusának](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) mindig öt vagy több csomóval kell rendelkeznie.
- Ne távolítsa el a véletlenszerű virtuálisgép-példányokat/csomópontokat egy csomóponttípusból, mindig használja a virtuálisgép méretezési beállítási skálázási szolgáltatását. A véletlenszerű virtuálisgép-példányok törlése hátrányosan befolyásolhatja a rendszerek megfelelő terhelési egyensúlyának megfelelő terhelését.
- Automatikus skálázási szabályok használata esetén állítsa be a szabályokat úgy, hogy a méretezés (virtuálisgép-példányok eltávolítása) egyszerre egy csomópontra történik. Egyszerre több példány leskálázása nem biztonságos.

Mivel a fürtben lévő Service Fabric-csomóponttípusok a háttérrendszerben lévő virtuálisgép-méretezési készletekből állnak, [beállíthat automatikus méretezési szabályokat, vagy manuálisan skálázhatja](service-fabric-cluster-scale-up-down.md) az egyes csomóponttípusokat/virtuálisgép-méretezési csoportokat.

### <a name="programmatic-scaling"></a>Automatizált méretezés
Sok esetben [a fürt manuális vagy automatikus skálázási szabályok méretezése](service-fabric-cluster-scale-up-down.md) jó megoldás. A fejlettebb forgatókönyvek, bár, lehet, hogy nem a megfelelő illeszkedést. E megközelítések lehetséges hátrányai a következők:

- A manuális méretezéshez be kell jelentkeznie, és explicit módon kell kérnie a méretezési műveleteket. Ha skálázási műveletekre van szükség gyakran vagy előre nem látható időpontokban, ez a megközelítés nem lehet jó megoldás.
- Amikor az automatikus méretezési szabályok eltávolítanak egy példányt egy virtuálisgép-méretezési csoportból, nem távolítják el automatikusan az adott csomópont ismeretét a társított Service Fabric-fürtből, kivéve, ha a csomópont típusának tartóssági szintje Ezüst vagy Arany. Mivel az automatikus skálázási szabályok a méretezési szint szintjén működnek (nem a Service Fabric szintjén), az automatikus skálázási szabályok eltávolíthatják a Service Fabric-csomópontokat anélkül, hogy szabályosan leállítanák őket. Ez a durva csomópont eltávolítása elhagyja a "ghost" Service Fabric-csomópont állapota mögött a méretezési műveletek után. Egy személy (vagy egy szolgáltatás) kellene rendszeresen törölni eltávolított csomópont állapota a Service Fabric-fürtben.
- Az Arany vagy Ezüst tartóssági szinttel rendelkező csomóponttípus automatikusan megtisztítja az eltávolított csomópontokat, így nincs szükség további karbantartásra.
- Bár az automatikus méretezési szabályok [számos metrikát](../azure-monitor/platform/autoscale-common-metrics.md) támogatnak, még mindig korlátozott készlet. Ha a forgatókönyv az adott készletben nem szereplő metrika alapján méretezést igényel, akkor előfordulhat, hogy az automatikus skálázási szabályok nem megfelelőek.

A Service Fabric-skálázás megközelítésének módja a forgatókönyvtől függ. Ha a skálázás nem gyakori, a csomópontok manuális hozzáadása vagy eltávolítása valószínűleg elegendő. Összetettebb forgatókönyvek esetén az automatikus skálázási szabályok és az SDK-k lehetővé teszik a programozott méretezést, hatékony alternatívákat kínálnak.

Az Azure API-k léteznek, amelyek lehetővé teszik az alkalmazások számára, hogy programozott módon működjenek a virtuális gép méretezési készleteivel és a Service Fabric-fürtökkel. Ha a meglévő automatikus méretezési beállítások nem működnek a forgatókönyvhöz, ezek az API-k lehetővé teszik az egyéni skálázási logika megvalósítását. 

A "házi készítésű" automatikus skálázási funkció megvalósításának egyik megközelítése egy új állapotmentes szolgáltatás hozzáadása a Service Fabric-alkalmazáshoz a skálázási műveletek kezeléséhez. Saját skálázási szolgáltatás létrehozása biztosítja a legmagasabb fokú vezérlést és testreszabhatóságot az alkalmazás méretezési viselkedését. Ez olyan esetekben lehet hasznos, amelyek pontosan szabályoztatják, hogy egy alkalmazás mikor és hogyan méretezhető be vagy ki. Ez a vezérlő azonban a kód összetettségének kompromisszumával jár. Ezzel a megközelítéssel azt jelenti, hogy saját skálázási kódot kell használnia, amely nem triviális. A szolgáltatás metódusán `RunAsync` belül egy eseményindítók segítségével megállapíthatja, hogy szükség van-e méretezésre (beleértve a paraméterek, például a maximális fürtméret és a méretezési újratöltőállomások ellenőrzését).   

A virtuálisgép-méretezési csoport interakcióihoz használt API (mind a virtuálisgép-példányok aktuális számának ellenőrzéséhez, mind a módosításhoz) a [gördülékeny Azure Management Compute-könyvtár.](https://www.nuget.org/packages/Microsoft.Azure.Management.Compute.Fluent/) A folyékony számítási kódtár egy könnyen használható API-t biztosít a virtuálisgép-méretezési készletek kel.  A Service Fabric-fürttel való együttműködéshez használja a [System.Fabric.FabricClient programot.](/dotnet/api/system.fabric.fabricclient)

A skálázási kód nem kell futtatni, mint egy szolgáltatás a fürtben kell skálázni, mégis. `IAzure` Mindkettő, `FabricClient` és távolról is csatlakozhat a társított Azure-erőforrásokhoz, így a skálázási szolgáltatás könnyen lehet egy konzolalkalmazás vagy a Service Fabric-alkalmazáson kívüli Windows-szolgáltatás.

Ezen korlátozások alapján érdemes [testreszabottabb automatikus skálázási modelleket megvalósítani.](service-fabric-cluster-programmatic-scaling.md)

## <a name="scaling-up-and-down-or-vertical-scaling"></a>Fel- és leskálázás vagy függőleges méretezés 
Módosítja a fürt csomópontjainak erőforrásait (CPU, memória vagy tároló).
- Előnyök: A szoftver- és alkalmazásarchitektúra ugyanaz marad.
- Hátrányok: Véges skálázás, mivel van egy korlát, hogy mennyit növelheti az erőforrásokat az egyes csomópontokon. Állásidő, mert az erőforrások hozzáadásához vagy eltávolításához fizikai vagy virtuális gépeket offline állapotba kell helyeznie.

A virtuálisgép-méretezési csoportok egy Azure-számítási erőforrás, amely segítségével üzembe helyezheti és kezelheti a virtuális gépek gyűjteménye egy készlet. Az Azure-fürtben definiált minden csomóponttípus [külön méretezési csoportként van beállítva.](service-fabric-cluster-nodetypes.md) Ezután minden csomóponttípus külön-külön kezelhető.  Egy csomóponttípus fel- vagy leskálázása magában foglalja a méretezési csoport ban lévő virtuálisgép-példányok termékváltozatának módosítását. 

> [!WARNING]
> Azt javasoljuk, hogy ne módosítsa a virtuális gép termékváltozatának méretezési csoport/csomópont típusú, kivéve, ha [fut silver tartósság vagy nagyobb.](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) A virtuális gép termékváltozatának mérete egy adatromboló helybeni infrastruktúra-művelet. Anélkül, hogy a változás késleltethető vagy figyelhető lenne, lehetséges, hogy a művelet adatvesztést okozhat az állapotalapú szolgáltatások számára, vagy más előre nem látható működési problémákat okozhat, még állapotnélküli munkaterhelések esetén is. 
>

Egy Azure-fürt méretezésekénél tartsa szem előtt az alábbi iránymutatást:
- Ha egy elsődleges csomóponttípus leskálázása, soha ne méretezze le több, mint amit a [megbízhatósági szint](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) lehetővé teszi.

A csomóponttípus fel- vagy leskálázásának folyamata attól függően eltérő, hogy nem elsődleges vagy elsődleges csomóponttípusról van-e szó.

### <a name="scaling-non-primary-node-types"></a>Nem elsődleges csomóponttípusok méretezése
Hozzon létre egy új csomóponttípust a szükséges erőforrásokkal.  Frissítse a futó szolgáltatások elhelyezési korlátait, hogy tartalmazza az új csomóponttípust.  Fokozatosan (egyenként) csökkentse a régi csomóponttípus-példányok példányszámát nullára, hogy a fürt megbízhatóságát ne befolyásolja.  A szolgáltatások fokozatosan átkerülnek az új csomóponttípusra, ahogy a régi csomóponttípus le van szerelve.

### <a name="scaling-the-primary-node-type"></a>Az elsődleges csomóponttípus méretezése
Azt javasoljuk, hogy ne módosítsa az elsődleges csomóponttípus virtuálisgép-termékváltozatát. Ha több fürtkapacitásra van szüksége, javasoljuk, hogy adjon hozzá további példányokat. 

Ha ez nem lehetséges, létrehozhat egy új fürtöt, és [visszaállíthatja](service-fabric-reliable-services-backup-restore.md) az alkalmazás állapotát (ha van ilyen) a régi fürtből. Nem kell visszaállítani a rendszerszolgáltatás állapotát, azok újra létrejönnek, amikor telepíti az alkalmazásokat az új fürtre. Ha csak állapotmentes alkalmazásokat futtatott a fürtön, akkor mindössze annyit kell tennie, hogy telepíti az alkalmazásokat az új fürtre, nincs mit visszaállítania. Ha úgy dönt, hogy a nem támogatott útvonalat, és módosítani szeretné a virtuális gép Termékváltozat, majd módosítsa a virtuálisgép-méretezési csoport modell definíciója, hogy tükrözze az új termékváltozat. Ha a fürt csak egy csomóponttípus, majd győződjön meg arról, hogy az összes állapotalapú alkalmazások reagálnak az összes [szolgáltatás replika életciklus-események](service-fabric-reliable-services-lifecycle.md) (például a replika buildbe van ragasztva) időben, és hogy a szolgáltatás replika újraépítése időtartama kevesebb, mint öt perc (a Silver tartóssági szint). 

## <a name="next-steps"></a>További lépések
* További információ az [alkalmazások méretezhetőségéről.](service-fabric-concepts-scalability.md)
* [Azure-fürt méretezése be- és ki.](service-fabric-tutorial-scale-cluster.md)
* [Az Azure-fürtöket programozott módon méretezzük](service-fabric-cluster-programmatic-scaling.md) a gördülékeny Azure-beli SDK használatával.
* [Önálló fürt méretezése be- és kiméretezése](service-fabric-cluster-windows-server-add-remove-nodes.md).

