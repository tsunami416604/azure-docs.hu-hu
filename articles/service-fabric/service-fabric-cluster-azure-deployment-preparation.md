---
title: Azure Service Fabric-fürt üzembe helyezésének megtervezése | Microsoft Docs
description: Ismerje meg, hogyan tervezheti meg és készítse elő az Azure-beli üzemi Service Fabric-fürtök üzembe helyezését.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/20/2019
ms.author: atsenthi
ms.openlocfilehash: a130e9bc8859360704c9be1c0a7fe066d2ed4567
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68600004"
---
# <a name="plan-and-prepare-for-a-cluster-deployment"></a>A fürt üzembe helyezésének megtervezése és előkészítése

Éles fürtök üzembe helyezésének tervezése és előkészítése nagyon fontos.  Számos szempontot figyelembe kell venni.  Ez a cikk végigvezeti a fürt üzembe helyezésének előkészítésének lépésein.

## <a name="read-the-best-practices-information"></a>Az ajánlott eljárásokat ismertető információk
Az Azure Service Fabric-alkalmazások és-fürtök sikeres kezeléséhez olyan műveletekre van szükség, amelyeket kifejezetten ajánlott elvégezni az éles környezet megbízhatóságának optimalizálása érdekében.  További információért olvassa el [Service Fabric alkalmazás-és fürt ajánlott eljárásait](service-fabric-best-practices-overview.md).

## <a name="select-the-os-for-the-cluster"></a>Válassza ki a fürt operációs rendszerét
Service Fabric lehetővé teszi Service Fabric-fürtök létrehozását a Windows Servert vagy Linuxot futtató virtuális gépeken vagy számítógépeken.  A fürt üzembe helyezése előtt ki kell választania az operációs rendszert:  Windows vagy Linux.  A fürt minden csomópontja (virtuális gép) ugyanazt az operációs rendszert futtatja, nem keverheti össze a Windows-és Linux-alapú virtuális gépeket ugyanabban a fürtben.

## <a name="capacity-planning"></a>Kapacitástervezés
Az éles üzembe helyezéshez a kapacitás megtervezése fontos lépés. Az alábbiakban néhány dolog láthat, amelyet érdemes a folyamat részeként figyelembe vennie.

* A fürthöz tartozó csomópont-típusok kezdeti száma 
* A csomópontok típusának tulajdonságai (méret, példányok száma, elsődleges, internetre irányuló, virtuális gépek száma stb.)
* A fürt megbízhatóságra és tartósságra vonatkozó jellemzői.

### <a name="select-the-initial-number-of-node-types"></a>Válassza ki a csomópontok típusának kezdeti számát
Először is meg kell állapítania, hogy a létrehozandó fürt milyen módon lesz használatban. Milyen típusú alkalmazásokat kíván telepíteni a fürtbe? Az alkalmazása több szolgáltatással rendelkezik, és ezek közül bármelyiknek nyilvánosnak vagy internetkapcsolatnak kell lennie? Különböző infrastrukturális igényeket (például nagyobb RAM-ot vagy magasabb CPU-ciklusokat) igényelnek a szolgáltatásai (az alkalmazást alkotó alkalmazások)? Egy Service Fabric fürt több csomópontból állhat: egy elsődleges csomópont-típusból és egy vagy több nem elsődleges csomópont típusból. Mindegyik csomópont-típus egy virtuálisgép-méretezési csoportra van leképezve. Ezután mindegyik csomóponttípus egymástól függetlenül skálázható vertikálisan le vagy fel, eltérő nyitott portokkal rendelkezhet, és eltérő kapacitásmetrikái lehetnek. A [csomópont-tulajdonságok és elhelyezési][placementconstraints] megkötések beállítható úgy, hogy bizonyos szolgáltatásokat bizonyos csomópont-típusokra korlátozzanak.  További információért olvassa el [a fürthöz szükséges csomópontok számát](service-fabric-cluster-capacity.md#the-number-of-node-types-your-cluster-needs-to-start-out-with).

### <a name="select-node-properties-for-each-node-type"></a>Csomópont-tulajdonságok kiválasztása minden csomópont-típushoz
A csomópont-típusok a társított méretezési csoportba tartozó virtuális gépek SKU-azonosítóját, számát és tulajdonságait határozzák meg.

Az egyes csomópont-típusokhoz tartozó virtuális gépek minimális méretét a csomópont típusához választott [tartóssági szint][durability] határozza meg.

Az elsődleges csomópont típusához tartozó virtuális gépek minimális számát a választott megbízhatósági [szint][reliability] határozza meg.

Tekintse meg az [elsődleges csomópontok típusaira](service-fabric-cluster-capacity.md#primary-node-type---capacity-guidance)vonatkozó minimális javaslatokat, a [nem elsődleges csomópont-típusokra vonatkozó állapot-nyilvántartó](service-fabric-cluster-capacity.md#non-primary-node-type---capacity-guidance-for-stateful-workloads)munkaterheléseket, valamint az [állapot nélküli munkaterheléseket a nem elsődleges csomópontok típusainál](service-fabric-cluster-capacity.md#non-primary-node-type---capacity-guidance-for-stateless-workloads). 

A csomópontok minimális számánál nagyobbnak kell lennie az ebben a csomópont-típusban futtatni kívánt alkalmazás/szolgáltatások replikáinak száma alapján.  [Service Fabric alkalmazások kapacitásának](service-fabric-capacity-planning.md) megtervezése segít megbecsülni az alkalmazások futtatásához szükséges erőforrásokat. Az alkalmazások számítási feladatainak módosításához a későbbiekben akár később, akár lejjebb is méretezheti a fürtöt. 

### <a name="select-the-durability-and-reliability-levels-for-the-cluster"></a>A fürt tartóssági és megbízhatósági szintjeinek kiválasztása
A tartóssági szint azt jelzi, hogy a rendszer a virtuális gépek által a mögöttes Azure-infrastruktúrához tartozó jogosultságokat használja. Az elsődleges csomópont típusában ez a jogosultság lehetővé teszi, hogy a Service Fabric szüneteltetni lehessen a rendszerszolgáltatások és az állapot-nyilvántartó szolgáltatások Kvórumának követelményeit érintő virtuálisgép-szintű infrastruktúra-kérelmeket (például a virtuális gépek újraindítását, a virtuális gépek rendszerképét vagy a virtuális gépek áttelepítését). A nem elsődleges csomópontok típusainál ez a jogosultság lehetővé teszi, hogy a Service Fabric szüneteltetni lehessen a virtuálisgép-szintű infrastruktúra-kérelmeket (például a virtuális gépek újraindítását, a virtuális gép rendszerképét és a virtuális gépek áttelepítését), amelyek hatással vannak az állapot-nyilvántartó szolgáltatásokra  A különböző szintek és javaslatok előnyeit, amelyeken a használni kívánt szintet és a-t használja, tekintse meg a [fürt tartóssági jellemzőit][durability].

A megbízhatósági szint azon rendszerszolgáltatások replikáinak a megadására szolgál, amelyeket ebben a fürtben szeretne futtatni az elsődleges csomópont típusán. Minél több replikát, annál megbízhatóbb a rendszerszolgáltatások a fürtben.  A különböző szintek és javaslatok előnyeit, amelyeken a használni kívánt szintet és a-t használja, tekintse meg a [fürt megbízhatósági jellemzőit][reliability]. 

## <a name="enable-reverse-proxy-andor-dns"></a>Fordított proxy és/vagy DNS engedélyezése
A fürtön belül egymáshoz csatlakozó szolgáltatások általában közvetlenül hozzáférhetnek más szolgáltatások végpontjait, mert a fürt csomópontjai ugyanazon a helyi hálózaton találhatók. Ahhoz, hogy könnyebb legyen csatlakozni a szolgáltatások között, Service Fabric további szolgáltatásokat nyújt: Egy [DNS-szolgáltatás](service-fabric-dnsservice.md) és egy [fordított proxy szolgáltatás](service-fabric-reverseproxy.md).  A fürt telepítésekor mindkét szolgáltatás engedélyezhető.

Mivel számos szolgáltatás – különösen a tároló-szolgáltatások – rendelkezhet egy meglévő URL-névvel, amely képes a szabványos DNS protokoll használatával (a elnevezési szolgáltatás protokoll helyett), különösen az alkalmazás "lift and SHIFT" forgatókönyvekben való feloldására. Pontosan ez a DNS-szolgáltatás. Lehetővé teszi a DNS-nevek hozzárendelését a szolgáltatás neveként, így a végponti IP-címek feloldását.

A fordított proxy a fürtben a HTTP-végpontokat (a HTTPS-t is beleértve) elérhetővé teszi. A fordított proxy nagy mértékben leegyszerűsíti az egyéb szolgáltatások meghívását egy adott URI-formátum megadásával.  A fordított proxy az egyes szolgáltatásokhoz szükséges feloldási, csatlakozási és újrapróbálkozási lépéseket is kezeli.

## <a name="prepare-for-disaster-recovery"></a>Felkészülés vészhelyreállításra
A magas rendelkezésre állás megvalósításának kritikus része annak biztosítása, hogy a szolgáltatások képesek legyenek túlélni az összes különböző típusú hibát. Ez különösen fontos a nem tervezett és a vezérlőn kívüli hibák esetén. [A](service-fabric-disaster-recovery.md) vész-helyreállítási előkészületek olyan gyakori meghibásodási módokat ismertetnek, amelyek a modellezés és a kezelés nem megfelelő kezelése esetén lehetnek katasztrófák. Emellett azt is ismerteti, hogy milyen kockázatcsökkentő és műveletek történnek, ha a katasztrófa amúgy is történt.

## <a name="production-readiness-checklist"></a>Termelési készenlét ellenőrzőlistája
Készen áll az alkalmazás és a fürt a termelési forgalom elvégzésére? Mielőtt üzembe helyezné a fürtöt az éles környezetben, futtassa az [éles készültségi ellenőrzőlistát](service-fabric-production-readiness-checklist.md). Az alkalmazás és a fürt zökkenőmentesen működik az ellenőrzőlista elemeinek használatával. Erősen ajánlott az összes ilyen elemet kijelölni az éles környezetben való üzembe helyezés előtt.

## <a name="next-steps"></a>További lépések
* [Windows rendszerű Service Fabric-fürt létrehozása](service-fabric-best-practices-overview.md)
* [Linux rendszerű Service Fabric-fürt létrehozása](service-fabric-tutorial-create-vnet-and-linux-cluster.md)

[placementconstraints]: service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints
[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[reliability]: service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster