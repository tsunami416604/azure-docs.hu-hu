---
title: Az Azure Service Fabric fürtméretezés |} Microsoft Docs
description: További információk a Service Fabric-fürtök a vagy kimenő, és felfelé vagy lefelé méretezés.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: aljo
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/09/2018
ms.author: ryanwi
ms.openlocfilehash: f9e3f190decdc907cf57a0235b9d7142081bd2f1
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34208029"
---
# <a name="scaling-service-fabric-clusters"></a>Méretezési Service Fabric-fürtök
A Service Fabric-fürt olyan hálózathoz csatlakozó virtuális vagy fizikai gépek, amelybe a mikroszolgáltatások telepíteni és felügyelni. Egy számítógép vagy virtuális Gépet, amely egy fürt része egy csomópont neve. Fürtök esetlegesen több ezer is tartalmazhat. Miután létrehozta a Service Fabric-fürt, a fürt horizontálisan méretezhető (csomópontok számának módosítása) vagy függőlegesen (a csomópontok erőforrások módosításához).  Méretezheti a fürt bármikor, még akkor is, ha munkaterhelések fut a fürtön.  Méretezi a fürtön, mivel az alkalmazások automatikus méretezése is.

Miért érdemes méretezni a fürtön? Terhelésekig változnak az idők.  Előfordulhat, hogy nagyobb alkalmazás munkaterhelés vagy a hálózati forgalom teljesítésére, vagy csökkentse a fürt erőforrásait, amikor elutasítja azokat az igény szerinti fürterőforrások növelnie kell.

### <a name="scaling-in-and-out-or-horizontal-scaling"></a>Méretezés és, vagy horizontális skálázás
A fürtben található csomópontok számának módosítása.  Miután az új csomópontok csatlakoztatását a fürthöz, a [fürt erőforrás-kezelő](service-fabric-cluster-resource-manager-introduction.md) szolgáltatások helyezi el, amely csökkenti a meglévő csomópontok terhelése.  Ha a fürt erőforrásai nem használ a hatékonyan csökkentheti a csomópontok számát.  Mivel a csomópontok adja meg a fürt, szolgáltatások ki azokat a csomópontokat, helyezze át, és a terhelés növekedésével a többi csomóponton.  Azure-beli fürtben található csomópontok számának csökkentése takaríthat meg pénz, mivel kell fizetnie a virtuális gépek számát akkor használhatja, és nem a virtuális gépek munkaterhelést.  

- Előnyeit: Végtelen skála elméletileg.  Ha az alkalmazás végzi a méretezhetőséget, engedélyezheti korlátlan növekedési további fürtcsomópontok hozzáadásához.  A felhőalapú környezetben tooling megkönnyíti a csomópontok hozzáadásához és eltávolításához, így könnyen a kapacitás, és csak kell fizetnie használt erőforrások.  
- Hátrányai: Csak azok az alkalmazások [méretezhetőséghez tervezett](service-fabric-concepts-scalability.md).  Alkalmazás-adatbázisok és az adatmegőrzési méretezési, valamint további architekturális munka lehet szükség.  [Megbízható gyűjtemények](service-fabric-reliable-services-reliable-collections.md) a Service Fabric állapotalapú szolgáltatások azonban sokkal könnyebb az alkalmazásadatok horizontális.

### <a name="scaling-up-and-down-or-vertical-scaling"></a>Skálázás felfelé és lefelé vagy függőleges skálázás 
Az erőforrások (Processzor, memória, vagy tárolási) a fürtben található csomópontok változik.
- Előnyeit: Szoftver és az alkalmazásarchitektúrát is ugyanaz marad.
- Hátrányai: Véges méretezési, mivel a megadott korlát milyen mértékben növelhető erőforrások az egyes csomópontokon. Állásidő, mert, fizikai vagy virtuális gépek offline hozzáadásához és eltávolításához erőforrások szüksége lesz.

## <a name="scaling-an-azure-cluster-in-or-out"></a>Egy Azure-fürttel bejövő vagy kimenő skálázás
Virtuálisgép-méretezési csoportok olyan Azure számítási erőforrás, melyekkel telepíthetnek és kezelhetnek olyan virtuális gépek gyűjteménye. Minden csomópont-típus egy Azure-fürttel definiált [beállítani egy külön méretezési](service-fabric-cluster-nodetypes.md). Az egyes csomóponttípusok majd méretezhetők a kimenő portok nyitva különböző tulajdonságkészletekkel rendelkező egymástól függetlenül, illetve különböző teljesítmény-mérőszámait rendelkezhet. 

Amikor skálázás egy Azure-fürttel, tartsa szem előtt az alábbi irányelveket:
- termelési számítási feladatokhoz futtató elsődleges csomóponttípusok mindig meg kell adni legalább öt csomópontot.
- állapot-nyilvántartó termelési számítási feladatokhoz rendszerű nem elsődleges csomóponttípusok mindig meg kell adni legalább öt csomópontot.
- állapot nélküli termelési számítási feladatokhoz rendszerű nem elsődleges csomóponttípusok mindig meg kell adni legalább két csomóponttal.
- Minden csomópont típusú [tartóssági szint](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) arany vagy ezüst mindig meg kell adni legalább öt csomópontot.
- Nem véletlenszerű Virtuálisgép-példányok/csomópontok eltávolítása a csomóponttípus, mindig használjon a virtuálisgép-méretezési készlet méretezési funkció le. Véletlenszerű Virtuálisgép-példány törlésének is akadályozhatják rendszerek megfelelő terheléselosztása érdekében.
- Ha az automatikus skálázási szabályok segítségével állítsa be úgy a szabályok, a Méretezés (Virtuálisgép-példány eltávolítása) történik, egy csomópont egyszerre. Skálázás le több példány egyszerre is nem biztonságos.

Mivel a Service Fabric csomóponttípusok a fürt álló virtuálisgép-méretezési beállítja a háttér, érdemes [automatikus méretezése szabályokat, vagy manuálisan méretezhető](service-fabric-cluster-scale-up-down.md) minden csomópont típusú virtuális gépek méretezési készlet.

### <a name="programmatic-scaling"></a>Programozott skálázás
A sok esetben [fürt skálázás manuális vagy automatikus skálázási szabályok](service-fabric-cluster-scale-up-down.md) jó megoldás. Speciális forgatókönyvek esetén azonban nem feltétlenül a megfelelő méretezése. Ezek a módszerek lehetséges hátrányai a következők:

- Manuális skálázás meg kell-e jelentkezni, és igényelhetnek a skálázási műveletek. Ha a skálázási műveletek szükségesek gyakran vagy időpontban előre nem látható, ez a megközelítés nem lehet jó megoldás.
- Automatikus méretezése szabályok eltávolítása egy virtuálisgép-méretezési csoport egy példányát, ha azok nem automatikusan eltávolítása az adott csomópont Tudásbázis társított Service Fabric-fürt kivéve, ha a csomópont a tartóssági szint ezüst vagy arany van. Automatikus méretezése szabályok működik, a méretezési készletben szint (és nem a Service Fabric szinten), mert automatikus méretezése szabályok is távolítsa el a Service Fabric-csomópont őket szabályosan leállítása nélkül. Goromba csomópont eltávolítása fog hagy "szellemrekord" a Service Fabric-csomópont állapota méretezési a műveletek után. Egy adott (vagy egy szolgáltatás) kell rendszeresen törölnie a Service Fabric-fürtöt az eltávolított csomópont állapota.
- Arany vagy ezüst tartóssági szint csomóponttípus automatikusan megtisztítja az eltávolított csomópontokat, ezért semmilyen további karbantartás nincs szükség.
- Bár vannak [sok metrikák](../monitoring-and-diagnostics/insights-autoscale-common-metrics.md) automatikus méretezése szabályok által támogatott, akkor még mindig korlátozott. Ha a forgatókönyv nem vonatkozik a készletben lévő egyes metrika alapján méretezéshez, majd automatikus méretezése szabályok nem lehet jó választás.

Hogyan meg kell megközelíti a Service Fabric skálázás attól függ, hogy a forgatókönyvéhez. Ha skálázás ritka, csomópontok hozzáadásához és eltávolításához manuálisan nem elegendő. Az összetettebb forgatókönyveket automatikus méretezése szabályok és SDK-k teszi ki a szolgáltatás szoftveres kínál az hatékony alternatívák.

Az Azure API-k léteznek, amelyek lehetővé teszik a virtuális gép programozott módon használható alkalmazások méretezési készletek és a Service Fabric-fürtök. Meglévő automatikus méretezése lehetőségek nem működnek az adott esetben, ha ezen API-k lehetővé teszik egyéni méretezési logika végrehajtásához. 

Egy megközelítést a "otthoni végrehajtott" az automatikus skálázás funkció végrehajtására egy új állapotmentes szolgáltatások hozzáadása a Service Fabric-alkalmazás skálázási műveleteinek a felügyeletét. A legmagasabb szintű vezérlő és az alkalmazás keresztül testreszabhatóság miatt a skálázás viselkedés létrehozása a saját szolgáltatás skálázás biztosít. Ez lehet hasznos, ha pontosan meghatározhatja hogyan vagy ha egy alkalmazás méretezi-e a bejövő vagy kimenő igénylő forgatókönyvek. Azonban ez a vezérlő tartalmaz a kompromisszummal jár, kód összetettségét. Ezzel a megközelítéssel azt jelenti, hogy saját méretezési kódot, amely nem triviális kell. A szolgáltatáson belül `RunAsync` módszer, eseményindítók készlete is megállapítja, hogy skálázás szükség (többek között a paraméterek maximális fürt például ellenőrzése és a méretezés cooldowns).   

A virtuális gép méretezési készlet kapcsolati (mindkettő ellenőrizze a virtuálisgép-példányok jelenlegi száma és módosítható) használja az API a [Folyékonyan beszél Azure felügyeleti számítási könyvtár](https://www.nuget.org/packages/Microsoft.Azure.Management.Compute.Fluent/). A Folyékonyan beszél számítási kódtár biztosít egy könnyen használható API-t használják a virtuálisgép-méretezési készlet.  Segítségével kommunikál a Service Fabric-fürt magát, [System.Fabric.FabricClient](/dotnet/api/system.fabric.fabricclient).

A méretezési kód szolgáltatásként a fürt méretezhető, de nem szükséges. Mindkét `IAzure` és `FabricClient` kapcsolódhatnak a kapcsolódó Azure-erőforrások távolról, így a méretezési szolgáltatás könnyen lehet egy konzolalkalmazás vagy a Windows-szolgáltatás fut a kívül a Service Fabric-alkalmazás.

E tényezők alapján, érdemes lehet [megvalósítása több testreszabott automatikus méretezési modellek](service-fabric-cluster-programmatic-scaling.md).


## <a name="scaling-a-standalone-cluster-in-or-out"></a>Egy önálló fürtön bejövő vagy kimenő skálázás
Önálló lehetővé teszi, hogy telepítse a Service Fabric fürt a helyszíni vagy az Ön által választott felhő szolgáltató.  Csomóponttípusok, fizikai gépek vagy virtuális gépek, attól függően, hogy a központi telepítés állnak. Azure-ban futó fürtökhöz képest, a folyamatot egy önálló fürtön skálázás egy kicsit bonyolultabb.  Meg kell manuálisan a a fürtben található csomópontok számának megváltoztatására, és futtassa a konfigurációs Fürtfrissítés.

Csomópontok eltávolítása több frissítés kezdeményezhet. Egyes csomópontok lesznek megjelölve `IsSeedNode=”true”` címkét, és a fürt lekérdezésével azonosítható manifest használatával [Get-ServiceFabricClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest). Ilyen csomópontok eltávolítása is tovább tarthat a többinél mivel helyezi át az ilyen helyzetekben a kezdőérték csomópontok kell. A fürt legalább három elsődleges típusú csomópontok kell fenntartani.

Ha egy önálló fürtön skálázást, tartsa szem előtt az alábbi irányelveket:
- A cseréje az elsődleges csomópont helyett eltávolítása és a kötegek egymás után kell elvégezni egy csomópont.
- Mielőtt eltávolítaná a csomóponttípus, ellenőrizheti, ha vannak-e azokat a csomópontokat hivatkozzon a csomóponttípus. A megfelelő típusú csomópont eltávolítása előtt távolítsa el ezeket a csomópontokat. Minden megfelelő csomópontokat törlődik, miután a NodeType távolítsa el a fürtkonfiguráció, és egy konfigurációs megkezdéséhez használatával frissítse [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade).

További információkért lásd: [önálló fürt méretezése](service-fabric-cluster-windows-server-add-remove-nodes.md).

## <a name="scaling-an-azure-cluster-up-or-down"></a>Egy Azure-fürttel skálázás feljebb vagy lejjebb
Virtuálisgép-méretezési csoportok olyan Azure számítási erőforrás, melyekkel telepíthetnek és kezelhetnek olyan virtuális gépek gyűjteménye. Minden csomópont-típus egy Azure-fürttel definiált [beállítani egy külön méretezési](service-fabric-cluster-nodetypes.md). Az egyes csomóponttípusok kezelheti külön-külön.  Skálázás csomóponttípus felfelé vagy lefelé magában foglalja a méretezési csoportban lévő virtuálisgép-példánya Termékváltozata módosítása. 

> [!WARNING]
> Azt javasoljuk, hogy nem módosítja a virtuális gép Termékváltozat méretezési készlet/csomópont típusú kivéve, ha fut a [tartóssági Silver vagy nagyobb](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster). Virtuális gép SKU méretének módosítása egy olyan adatok felülíró helyszíni infrastruktúra művelet. Nem néhány rendelkező késleltetés vagy a figyelheti ezt a módosítást lehetséges, hogy a művelet okozhat az állapotalapú szolgáltatások, vagy más váratlan működési problémákkal rendelkeznek, még a állapot nélküli munkaterheléseket miatt. 
>

Amikor skálázás egy Azure-fürttel, tartsa szem előtt az alábbi eszközöket:
- Ha skálázás le egy elsődleges csomóponttípushoz, meg kell soha nem csökkentheti azt több mint mi a [megbízhatósági szint](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) lehetővé teszi.

A csomóponttípus méretezés felfelé vagy lefelé a folyamat eltér attól függően, hogy azt nem elsődleges vagy elsődleges csomóponttípus.

### <a name="scaling-non-primary-node-types"></a>Nem elsődleges csomóponttípusok skálázás
Hozzon létre egy új típusú csomópont a szükséges erőforrások.  Frissítse a szolgáltatást az új csomóponttípus futó egy elhelyezési korlátozás.  Fokozatos (egyenként), csökkentheti a példányok száma, a régi csomópont típusú példányok száma nulla, hogy ez nem befolyásolja a fürt megbízhatóságát.  Szolgáltatások ezzel az új csomóponttípus fokozatosan telepíti át, mert a régi típusú csomópont decommisioned.

### <a name="scaling-the-primary-node-type"></a>Az elsődleges csomóponttípusok skálázás
Azt javasoljuk, hogy a virtuális gép az elsődleges csomóponttípusok Termékváltozata nem módosítja. Ha több fürt kapacitás van szüksége, javasoljuk, több példány hozzáadása. 

Ha, hogy nem lehetséges, létrehozhat egy új fürt és [alkalmazásállapot visszaállítása](service-fabric-reliable-services-backup-restore.md) (ha van ilyen) a régi fürtről. Nem kell minden rendszer szolgáltatás állapotának visszaállítására, akkor jönnek létre újból az új fürt számára az alkalmazások központi telepítésekor. Ha csak állapot nélküli alkalmazásokban a fürtben futó, akkor kell a alkalmazásokat az új fürtre, a rendszer nincs semmi visszaállítása. Ha úgy dönt, nyissa meg a nem támogatott útvonal és a virtuális gép SKU módosítani kívánja, majd végezni a módosításokat a virtuálisgép-méretezési Model definition tükrözzék az új SKU állítsa be. Ha a fürt csak egy csomópont fájltípusú, majd győződjön meg arról, hogy válaszol-e az állapotalapú alkalmazások az összes [replika életciklus-események szolgáltatás](service-fabric-reliable-services-lifecycle.md) (ilyen például a build replika Beragadt) egy időben, valamint a szolgáltatás a replika újbóli létrehozása időtartam értéke kisebb, mint öt percet (ezüst tartóssági szint). 

## <a name="next-steps"></a>További lépések
* További tudnivalók [alkalmazás méretezhetőség](service-fabric-concepts-scalability.md).
* [Egy Azure-fürttel bejövő vagy kimenő méretezése](service-fabric-tutorial-scale-cluster.md).
* [Egy Azure-fürt méretezése programozott módon](service-fabric-cluster-programmatic-scaling.md) SDK használatával a Folyékonyan beszél Azure számítási.
* [Bejövő vagy kimenő standaone fürt méretezése](service-fabric-cluster-windows-server-add-remove-nodes.md).

