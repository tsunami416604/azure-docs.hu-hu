---
title: Az Azure Service Fabric fürt méretezése |} A Microsoft Docs
description: Ismerje meg az Azure Service Fabric-fürtök a, vagy ki, és felfelé vagy lefelé méretezés.
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
ms.date: 11/13/2018
ms.author: ryanwi
ms.openlocfilehash: 527ab4ee0edaf3ac2048403d7063edef8fc58ae8
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57451977"
---
# <a name="scaling-azure-service-fabric-clusters"></a>Méretezés az Azure Service Fabric-fürtök
Service Fabric-fürt, amelybe mikroszolgáltatásokat helyezhet üzembe és felügyelhet virtuális vagy fizikai gépek hálózaton keresztül csatlakozó készlete áll. Egy számítógép vagy virtuális Gépet, amely egy fürt része csomópontoknak nevezzük. Fürtök potenciálisan több ezer csomóponton is tartalmazhat. Egy Service Fabric-fürt létrehozását követően, horizontálisan a fürt (módosíthatja a csomópontok számát), vagy függőlegesen (módosíthatja a csomópontok az erőforrások).  Méretezheti a fürt bármikor, még akkor is, ha a számítási feladatok a fürtön futnak.  A fürt skálázható, mivel az alkalmazások automatikus méretezése is.

A fürt átméretezése miért? Alkalmazások számára az idő előrehaladtával változik.  Szükség lehet növelni a fürterőforrások felel meg az alkalmazás nagyobb számítási feladatok vagy a hálózati forgalom, vagy csökkentse a fürterőforrások, ha az igény csökken.

## <a name="scaling-in-and-out-or-horizontal-scaling"></a>Skálázás be és ki, vagy a horizontális skálázás
Módosíthatja a fürtben található csomópontok számát.  Miután az új csomópontok csatlakoztatását a fürthöz, a [fürterőforrás-kezelő](service-fabric-cluster-resource-manager-introduction.md) szolgáltatások helyezi őket, ami csökkenti a meglévő csomópontok terhelése.  A csomópontok számát is csökkentheti, ha a fürt erőforrásait nem hatékonyan használ.  Csomópontok hagyja a fürt, szolgáltatások áthelyezése ki azokat a csomópontokat, és a terhelés növekedésével a többi csomóponton.  Egy Azure-ban futó fürtben található csomópontok számának csökkentése pénzt takaríthat meg, mivel után kell fizetni, a virtuális gépek száma, használata és a virtuális gépeken a munkaterhelés nem.  

- Előnyök: Korlátlan méretű elméletileg.  Ha az alkalmazás t méretezhetőségre tervezték, korlátlan növekedési további fürtcsomópontok hozzáadásával engedélyezheti.  Az eszközkészlet a felhőalapú környezetek megkönnyíti a hozzáadása vagy eltávolítása a csomópontokra, így egyszerűen módosíthatja a kapacitást, és csak kell fizetnie az erőforrások után.  
- Hátrányait: Csak azok az alkalmazások [méretezhetőséghez tervezett](service-fabric-concepts-scalability.md).  Alkalmazás-adatbázisok és az adatmegőrzés szükség lehet további architekturális munka, valamint méretezését.  [A Reliable collections](service-fabric-reliable-services-reliable-collections.md) a Service Fabric állapotalapú szolgáltatások azonban sokkal könnyebb méretezését, az alkalmazásadatok.

Virtuálisgép-méretezési csoportok olyan számítási Azure-erőforrások üzembe helyezése és kezelése a virtuális gépek csoportként gyűjtemény használható. Minden csomópont-típus egy Azure-fürtön definiált [külön méretezési csoportként](service-fabric-cluster-nodetypes.md). Mindegyik csomóponttípus majd leskálázható vagy el egymástól függetlenül, más-más részhalmazához nyitott portokkal rendelkezik, és eltérő kapacitásmetrikái. 

Ha méretezése egy Azure-fürtön, vegye figyelembe a következő irányelveket:
- éles számítási feladatokat futtató elsődleges csomóponttípusok mindig rendelkeznie kell legalább öt csomóponttal.
- állapot-nyilvántartó éles számítási feladatok nem elsődleges csomóponttípusok mindig rendelkeznie kell legalább öt csomóponttal.
- állapot nélküli számítási feladatok futtatása nem elsődleges csomóponttípusok mindig rendelkeznie kell legalább két csomóponttal.
- Minden csomópont típusú [tartóssági szint](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) , arany és ezüst mindig rendelkeznie kell legalább öt csomóponttal.
- Nem véletlenszerű Virtuálisgép-példányok/csomópontok eltávolítása a csomópont típusa, a szolgáltatás le virtuálisgép-méretezési készlet méretezési mindig használja. Véletlenszerű Virtuálisgép-példányok törlése kedvezőtlen hatással lehet a rendszerek képes megfelelően terhelésének elosztása érdekében.
- Automatikus skálázási szabályok használata esetén állítsa be a szabályokat, hogy a skálázás (eltávolítása a Virtuálisgép-példányok) az történik, egy csomópont egyszerre. Skálázás lefelé több példány egyszerre jelenleg nem biztonságos.

Mivel a Service Fabric-csomóponttípusok a fürtben épülnek fel a háttérbeli virtuálisgép-méretezési csoport beállítja, érdemes [automatikus skálázási szabályok beállítása, vagy manuálisan méretezhető,](service-fabric-cluster-scale-up-down.md) minden egyes csomópont típusa/virtuális virtuálisgép-méretezési csoportot.

### <a name="programmatic-scaling"></a>Programozott skálázása
Sok esetben [fürt méretezése manuális vagy automatikus skálázási szabályok](service-fabric-cluster-scale-up-down.md) jó megoldás. Speciális forgatókönyvekhez, azokat nem lehet a megfelelő laphoz. Ezek a módszerek a lehetséges hátrányai a következők:

- Manuális skálázás megköveteli, hogy jelentkezzen be, és igényelhetnek a méretezési műveletek. Ha a méretezési műveletek szükségesek gyakran vagy kiszámíthatatlan időpontokban, ezzel a módszerrel nem lehet nagyon jó megoldásnak.
- Automatikus méretezési szabályok egy példányt egy virtuálisgép-méretezési csoportot eltávolítja, ha azok ne automatikusan távolítsa el az adott csomópont Tudásbázis Service Fabric-fürthöz társított, ha a csomópont típusa Silver vagy Gold szintű tartósságot. Automatikus skálázási szabályok, a méretezési szint (nem a Service Fabric szintjén) működik, mert az automatikus skálázási szabályok eltávolíthatja őket szabályosan leállítása nélkül Service Fabric csomópontjaival. Goromba csomópont eltávolítása hagyja "ghost" a Service Fabric-csomópont állapota horizontális leskálázási műveletek után. Egyéni (vagy egy szolgáltatás) kellene rendszeres karbantartása eltávolított csomópont állapota a Service Fabric-fürtben.
- Egy csomópont típusa, arany és ezüst szintű tartósságot automatikusan megtisztítja eltávolított csomópontokat, ezért semmilyen további karbantartási van szükség.
- Bár vannak [számos metrikák](../azure-monitor/platform/autoscale-common-metrics.md) az automatikus skálázási szabályok támogatja, akkor ez még mindig korlátozott. Ha a forgatókönyv nem vonatkozik a készletben lévő egyes mérőszám alapján méretezését, majd automatikus skálázási szabályok esetleg nem jó választás.

A forgatókönyvtől függ, hogy hogyan kell módszerrel érdemes a Service Fabric-méretezés. Ha a skálázás nem szokványos, valószínűleg elegendő arra, hogy adja hozzá, vagy távolítsa el manuálisan a csomópontok. Az összetettebb esetekhez automatikus skálázási szabályok és az SDK-adatokhoz hozzáférést biztosító programozott módon méretezhető kínál hatékony alternatívák.

Az Azure API-k léteznek, amely lehetővé teszi alkalmazások programozott módon dolgozni a virtuálisgép-méretezési csoportokban, és a Service Fabric-fürtök. Ha a meglévő automatikus méretezési beállítások nem működnek a forgatókönyvnek, API-k lehetővé teszik egyéni méretezési logikát. 

A "házi készítésű" automatikus méretezési funkció megvalósításának egyik módszere egy új állapotmentes szolgáltatás hozzáadása a méretezési műveletek kezelése a Service Fabric-alkalmazás. Létrehozásában-szolgáltatás méretezése a legmagasabb fokú vezérlő és a testreszabhatóság keresztül az alkalmazás viselkedését a méretezést biztosít. Ez lehet hasznos, ha pontosan szabályozhatja, hogy mikorra vagy egy alkalmazás méretezhető, vagy igénylő forgatókönyvek. Azonban ez a vezérlő tartalmaz a kompromisszummal jár, kód összetettségét. Ez a megközelítés azt jelenti, hogy saját méretezési kódot, amely nem triviális kell. A szolgáltatáson belül `RunAsync` metódus, eseményindítók készletét is, hogy a skálázás szükséges (beleértve például a maximális fürt mérete paraméter ellenőrzése és cooldowns méretezése).   

A virtuális gép méretezési készlet interakciók (mind a virtuálisgép-példányok száma és a módosítása) használt API-t a [fluent Azure kezelési számítási kódtár](https://www.nuget.org/packages/Microsoft.Azure.Management.Compute.Fluent/). A fluent számítási kódtár egy könnyen használható API-t biztosít a virtual machine scale sets való interakcióhoz.  Együttműködhet a Service Fabric-fürthöz, használja a [System.Fabric.FabricClient](/dotnet/api/system.fabric.fabricclient).

A méretezési kód futtatása szolgáltatásként skálázását, azonban a fürt nem szükséges. Mindkét `IAzure` és `FabricClient` csatlakozhat a kapcsolódó Azure-erőforrásokra távolról, így a méretezési service egyszerűen lehet egy konzolalkalmazás vagy kívül a Service Fabric-alkalmazás a futó Windows-szolgáltatás.

Ezek a korlátozások alapján, érdemes [megvalósításához több egyéni automatikus skálázási modellek](service-fabric-cluster-programmatic-scaling.md).

## <a name="scaling-up-and-down-or-vertical-scaling"></a>Felfelé és lefelé skálázást, vagy a vertikális skálázás 
Az erőforrások (CPU, memória, vagy tárolási) a fürtben található csomópontok változik.
- Előnyök: Szoftver- és alkalmazás architektúra változatlan marad.
- Hátrányait: A véges méretezhető, mivel az IP-címek fenntartási, növelheti az egyes csomópontokon erőforrások korlátozva van. Állásidő, mert szüksége lesz a fizikai vagy virtuális gépek offline annak érdekében, hogy erőforrásainak hozzáadása vagy eltávolítása.

Virtuálisgép-méretezési csoportok olyan számítási Azure-erőforrások üzembe helyezése és kezelése a virtuális gépek csoportként gyűjtemény használható. Minden csomópont-típus egy Azure-fürtön definiált [külön méretezési csoportként](service-fabric-cluster-nodetypes.md). Mindegyik csomóponttípus kezelhetők külön-külön.  Typ uzlu kiterjesztése vagy szűkítése, a méretezési csoportban lévő virtuálisgép-példánya a Termékváltozat módosítása is hozzátartozik. 

> [!WARNING]
> Azt javasoljuk, hogy nem módosítja a virtuális gép Termékváltozata méretezési készlet vagy csomópont típusa, ha fut a következőn: [Silver szintű tartósságot vagy nagyobb](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster). Virtuális gép SKU-méret módosítása egy olyan adatok felülíró helyszíni infrastruktúra művelet. Késleltetés, vagy figyelheti a módosítás néhány képessége nélkül is lehet, hogy a művelet az állapotalapú szolgáltatások esetében adatvesztést is okozhat, vagy akár állapot nélküli számítási feladatok esetében más előre nem látható operatív problémákat okozhat. 
>

Ha méretezése egy Azure-fürtön, vegye figyelembe a következő arról:
- Ha egy elsődleges csomóponttípus leskálázást, soha nem horizontális azt le több, mint a [megbízhatósági szint](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) lehetővé teszi.

Typ uzlu kiterjesztése vagy szűkítése folyamata attól függően, hogy-e egy nem elsődleges vagy az elsődleges csomópont típusa eltér.

### <a name="scaling-non-primary-node-types"></a>Nem elsődleges csomóponttípusok méretezése
Hozzon létre egy új csomópont típusa a szükséges erőforrásokat.  Frissítse az elhelyezési korlátozások futó szolgáltatások közé tartozik az új csomópont típusa.  Fokozatosan (egyenként), csökkentheti a régi csomópont típusa példányszám példányainak száma nulla, hogy az nem befolyásolja a fürt megbízhatóságát.  Szolgáltatások fokozatosan fog át az új csomópont típusa, a régi csomóponttípus leszereltként van.

### <a name="scaling-the-primary-node-type"></a>Az elsődleges csomóponttípushoz méretezése
Azt javasoljuk, hogy az elsődleges csomóponttípushoz VM-Termékváltozat nem módosítja. Ha a fürt nagyobb kapacitásra van szüksége, azt javasoljuk, további példányok hozzáadása. 

Ha, hogy nem lehetséges, létrehozhat egy új fürtöt, és [alkalmazásállapot visszaállítása](service-fabric-reliable-services-backup-restore.md) (ha van ilyen) a régi fürtről. Nem kell minden olyan szolgáltatás rendszerállapot visszaállítása, akkor létrejönnek az új fürthöz az alkalmazások központi telepítésekor. Ugyanúgy, mintha állapot nélküli alkalmazások a fürtben futó, majd kell az új fürtre alkalmazások üzembe helyezése, akkor semmit nem kell visszaállítani. Ha úgy dönt, nyissa meg a nem támogatott útvonal és a VM-Termékváltozat módosítani kívánja, majd végezni a módosításokat a virtuálisgép-méretezési csoport beállítása modell definícióját, hogy tükrözzék az új Termékváltozat. Ha a fürt egyetlen csomópont típusa, majd ellenőrizze, hogy, hogy válaszol-e az állapotalapú alkalmazások az összes [replika életciklusesemények szolgáltatás](service-fabric-reliable-services-lifecycle.md) (ilyen például a replika a build beragad) időben feldolgozza, és hogy a szolgáltatás replika újraépítése időtartam (a Silver szintű tartóssági szint) kisebb, mint öt perc alatt. 

## <a name="next-steps"></a>További lépések
* Ismerje meg [alkalmazás méretezhetőségi](service-fabric-concepts-scalability.md).
* [Egy Azure-fürtön lévő vagy horizontális skálázása](service-fabric-tutorial-scale-cluster.md).
* [Egy Azure-fürtön programozott skálázása](service-fabric-cluster-programmatic-scaling.md) az fluent Azure compute SDK-t.
* [Vagy önálló fürt méretezése](service-fabric-cluster-windows-server-add-remove-nodes.md).

