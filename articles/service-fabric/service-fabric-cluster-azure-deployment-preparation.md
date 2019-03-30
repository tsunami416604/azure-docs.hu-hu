---
title: Az Azure Service Fabric-fürt központi telepítésének megtervezése |} A Microsoft Docs
description: Ismerje meg tervezése és előkészítése a Service Fabric fürtök üzembe helyezése az Azure-bA egy éles üzemi környezetek részei.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: aljo
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/20/2019
ms.author: aljo
ms.openlocfilehash: 0f3a9010805ec1a18490f6f530f60d7a3c763398
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58663238"
---
# <a name="plan-and-prepare-for-a-cluster-deployment"></a>Tervezés és felkészülés egy fürt üzembe helyezése

Tervezés és felkészülés egy éles fürtök üzembe helyezése nagyon fontos.  Nincsenek számos tényezőt kell figyelembe venni.  Ez a cikk végigvezeti a lépéseken, a fürt üzembe helyezés előkészítése.

## <a name="read-the-best-practices-information"></a>Az ajánlott eljárások adatok olvasása
Az Azure Service Fabric-alkalmazások és -fürtök felügyelete a sikeres, vannak, hogy kifejezetten ajánljuk a megbízhatóság az éles környezet optimalizálása hajt végre műveleteket.  További információkért olvassa el [gyakorlati tanácsok a Service Fabric-alkalmazás és fürt](service-fabric-best-practices-overview.md).

## <a name="select-the-os-for-the-cluster"></a>A fürt operációs rendszerének kiválasztása
A Service Fabric lehetővé teszi, hogy minden olyan virtuális gépeket vagy a Windows Server vagy Linux rendszerű számítógépeken a Service Fabric-fürtök létrehozásához.  A fürt üzembe helyezése előtt ki kell választania az operációs rendszer:  Windows vagy Linux.  A fürt minden csomópontján (virtuális gép) futtatja az ugyanazon operációs rendszer, nem használhatók vegyesen Windows és Linux rendszerű virtuális gépek ugyanazon fürt.

## <a name="capacity-planning"></a>Kapacitástervezés
Éles rendszerek üzembe a kapacitástervezés egy fontos lépés. Az alábbiakban néhány dolog láthat, amelyet érdemes a folyamat részeként figyelembe vennie.

* A csomóponttípusok a fürt kezdeti száma 
* Az egyes csomóponttípusok (mérete, elsődleges példányok száma, az internetre irányuló, virtuális gépek száma stb.) tulajdonságai
* A fürt megbízhatóságra és tartósságra vonatkozó jellemzői.

### <a name="select-the-initial-number-of-node-types"></a>Válassza ki a kezdeti csomóponttípusok száma
Először döntse el, mi a fürt létrehozásakor fog használni a. Milyen típusú alkalmazásokat kíván a fürt üzembe helyezése a? Az alkalmazás nem rendelkezik több szolgáltatást, és ezek közül bármelyik szükséges nyilvános vagy az internetre? Rendelkezik a szolgáltatások (alkotó az alkalmazás) infrastruktúra különböző igényeinek megfelelően, például a nagyobb RAM vagy nagyobb CPU-ciklusok? Service Fabric-fürt több csomóponttípus is állhat: egy elsődleges csomóponttípusok és a egy vagy több nem elsődleges csomóponttípusok. Mindegyik csomóponttípus van rendelve egy virtuálisgép-méretezési csoportot. Ezután mindegyik csomóponttípus egymástól függetlenül skálázható vertikálisan le vagy fel, eltérő nyitott portokkal rendelkezhet, és eltérő kapacitásmetrikái lehetnek. [Csomópont tulajdonságai és elhelyezési korlátozások] [ placementconstraints] korlátozhatja az adott szolgáltatások adott csomóponttípusok is beállítható.  További információkért olvassa el [kell kezdődnie a fürt csomóponttípusok száma](service-fabric-cluster-capacity.md#the-number-of-node-types-your-cluster-needs-to-start-out-with).

### <a name="select-node-properties-for-each-node-type"></a>Válassza ki az egyes csomóponttípusok csomópont tulajdonságai
A társított méretezési csomóponttípusok határozzák meg a Virtuálisgép-Termékváltozat, szám és a virtuális gépek tulajdonságait.

Az egyes csomóponttípusok virtuális gép minimális mérete határozza meg a [tartóssági szint] [ durability] typ uzlu ki.

A minimális számú virtuális gépet az elsődleges csomópont típusa határozza meg a [megbízhatósági szint] [ reliability] választja.

Tekintse meg a minimális javaslatok [elsődleges csomóponttípusok](service-fabric-cluster-capacity.md#primary-node-type---capacity-guidance), [nem elsődleges csomóponttípusok az állapotalapú alkalmazások és szolgáltatások](service-fabric-cluster-capacity.md#non-primary-node-type---capacity-guidance-for-stateful-workloads), és [állapot nélküli munkaterhelés nem elsődleges csomóponttípusok a](service-fabric-cluster-capacity.md#non-primary-node-type---capacity-guidance-for-stateless-workloads). 

Bármely több, mint a csomópontok minimális száma alapján kell, hogy ennek a csomóponttípusnak futtatni kívánt alkalmazás/szolgáltatás replikák száma.  [Kapacitás megtervezése a Service Fabric-alkalmazások](service-fabric-capacity-planning.md) segítséget nyújt az alkalmazások futtatásához szükséges erőforrásokat becslései szerint. Mindig a fürt vertikális felskálázás vagy lefelé később módosíthatja a változó az alkalmazás számítási feladatait. 

### <a name="select-the-durability-and-reliability-levels-for-the-cluster"></a>Válassza ki a fürt a tartósság és a megbízhatósági szintek
A tartóssági szint, amelyek jelzik, hogy a rendszer a jogosultságokat, amely a virtuális gépek rendelkeznek az alapul szolgáló Azure-infrastruktúra-szolgál. Az elsődleges csomóponttípushoz Ez a jogosultság lehetővé teszi a Service Fabric bármely virtuális gép szintű infrastruktúra kérelem (például egy virtuális gép újraindítása, a virtuális gép rendszerképét alaphelyzetbe állítani vagy a virtuális gépek migrálása), amely hatással van a kvórum követelményei a helyrendszeri szolgáltatások és az állapotalapú szolgáltatások felfüggesztését. Ez a jogosultság nem elsődleges csomóponttípusok megállítja a virtuális gép szintű infrastruktúra kérések eredményéről (például a virtuális gép újraindítása, a virtuális gép rendszerképét alaphelyzetbe állítani és a virtuális gépek migrálása), amely hatással van a kvórum követelményei az állapotalapú szolgáltatások Service Fabric lehetővé teszi.  A magánfelhőmodell előnyeit a különböző szintek és használni, és amikor, hogy milyen szintű ajánlásokat [a fürt tartóssági jellemzőit][durability].

A megbízhatósági szint segítségével állítsa be a replikákat a rendszer szolgáltatások a fürtben futó az elsődleges csomóponttípushoz kívánt számát. A további a replikák száma, a megbízhatóbb a helyrendszeri szolgáltatások vannak, a fürtben.  A magánfelhőmodell előnyeit a különböző szintek és használni, és amikor, hogy milyen szintű ajánlásokat [a fürt megbízhatósági jellemzőit][reliability]. 

## <a name="enable-reverse-proxy-andor-dns"></a>Fordított proxy és/vagy DNS engedélyezése
Szolgáltatások csatlakozik egymáshoz egy fürtben általában közvetlenül hozzáférhet a végpontok az más szolgáltatások, mert a fürt csomópontjainak azonos helyi hálózatra. Legyen a csatlakoztathatók a szolgáltatások közötti, a Service Fabric kiegészítő szolgáltatásokat nyújtja: A [DNS-szolgáltatás](service-fabric-dnsservice.md) és a egy [fordított proxy szolgáltatás](service-fabric-reverseproxy.md).  Mindkét szolgáltatás engedélyezhető, ha a fürt üzembe helyezése.

Óta számos szolgáltatás különösen tárolóalapú szolgáltatásokat is rendelkezik egy meglévő URL-címet, ezek elhárításához használatával a standard DNS protokoll (helyett az elnevezési szolgáltatásban protokoll) magától kényelmes, különösen az alkalmazások "átemelése" forgatókönyvek. Ez a pontosan, a DNS-szolgáltatás leírása. Lehetővé teszi, hogy a DNS-név leképezése a szolgáltatás nevét, és ezért a végpont IP-címek feloldása.

A fordított proxy-címek (beleértve a HTTPS) HTTP-végpontokat tesznek közzé szolgáltatások a fürtben. A fordított proxy jelentősen leegyszerűsíti a más szolgáltatások meghívása azáltal, hogy egy adott URI-formátum.  A fordított proxy kezelésére is alkalmas a feloldás, csatlakozzon, és próbálkozzon újra egy másik kommunikálni egy szolgáltatáshoz szükséges lépéseket.

## <a name="prepare-for-disaster-recovery"></a>Felkészülés vészhelyreállításra
Magas rendelkezésre állás biztosítása kritikus része annak ellenőrzése, hogy szolgáltatásokat az összes különböző típusú hibák hibatűrését. Ez különösen fontos a nem tervezett hibák és a hatáskörén kívül esik. [Felkészülés vészhelyreállításra](service-fabric-disaster-recovery.md) néhány gyakori hibaállapotra katasztrófák lehet, ha nem modellezése, és megfelelően felügyelt ismerteti. Megoldások és a teendő, ha vészhelyzet történt ennek ellenére is ismerteti.

## <a name="production-readiness-checklist"></a>Termelési készenlét ellenőrzőlistája
Az alkalmazás és a fürt készen áll az éles forgalmat is? A fürt telepítése éles környezetbe, mielőtt haladjon végig a [éles készültségi ellenőrzőlista](service-fabric-production-readiness-checklist.md). Tartsa meg az alkalmazás és a fürt feldolgozása révén a feladatlista elemeinek működőkre. Erősen ajánlott ezeket éles környezetben való elhelyezés előtt ellenőrizni kell az összes elemet.

## <a name="next-steps"></a>További lépések
* [A Windows rendszert futtató Service Fabric-fürt létrehozása](service-fabric-best-practices-overview.md)
* [Linux operációs rendszert futtató Service Fabric-fürt létrehozása](service-fabric-tutorial-create-vnet-and-linux-cluster.md)

[placementconstraints]: service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints
[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[reliability]: service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster