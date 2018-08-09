---
title: Vagy a Service Fabric-fürt skálázása |} A Microsoft Docs
description: Vagy Service Fabric-fürt méretezése igény szerint megfelelően minden egyes csomópont típusa/virtuálisgép-méretezési az automatikus skálázási szabályok beállításával. Adja hozzá, vagy távolíthat el csomópontokat a Service Fabric-fürt
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: aeb76f63-7303-4753-9c64-46146340b83d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/22/2017
ms.author: aljo
ms.openlocfilehash: 1869b25756693a4a3626d713b6bd2adab035cea6
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2018
ms.locfileid: "39716905"
---
# <a name="scale-a-service-fabric-cluster-in-or-out-using-auto-scale-rules-or-manually"></a>Méretezheti a Service Fabric-fürt a- vagy leskálázása automatikus skálázási szabályok használatával vagy manuálisan
Virtuálisgép-méretezési csoportok olyan számítási Azure-erőforrások üzembe helyezése és kezelése a virtuális gépek csoportként gyűjtemény használható. Meghatározott Service Fabric-fürtök minden csomóponttípusa egy külön virtuálisgép-méretezési csoportban, van beállítva. Mindegyik csomóponttípus majd leskálázható vagy el egymástól függetlenül, más-más részhalmazához nyitott portokkal rendelkezik, és eltérő kapacitásmetrikái. További információ ezekről a a [Service Fabric NodeType](service-fabric-cluster-nodetypes.md) dokumentumot. Mivel a Service Fabric-csomóponttípusok a fürtben, a háttérbeli virtuális gép méretezési épülnek, kell minden egyes csomópont típusa/virtuálisgép-méretezési automatikus méretezési szabályokat.

> [!NOTE]
> Az előfizetés hozzáadása az új virtuális gépeket, a fürtöt alkotó elég mag kell rendelkeznie. Nem történik modell ellenőrzés jelenleg, így üzembe helyezési idő hibák, ha bármelyik kvótát az újrapróbálkozásig.
> 
> 

## <a name="choose-the-node-typevirtual-machine-scale-set-to-scale"></a>Csomópont típusa/virtuális gép méretezése méretezési kiválasztása
Jelenleg nem képes az automatikus skálázási szabályok virtuálisgép-méretezési csoportok a portál használatával adja meg, ezért várjuk a csomóponttípusok a listában, majd adja hozzá automatikus skálázási szabályok azokat használhatja az Azure Powershellt (1.0 +).

A fürtöt alkotó virtuálisgép-méretezési listájának lekéréséhez futtassa a következő parancsmagokat:

```powershell
Get-AzureRmResource -ResourceGroupName <RGname> -ResourceType Microsoft.Compute/VirtualMachineScaleSets

Get-AzureRmVmss -ResourceGroupName <RGname> -VMScaleSetName <Virtual Machine scale set name>
```

## <a name="set-auto-scale-rules-for-the-node-typevirtual-machine-scale-set"></a>Csomópont típusa/virtuális virtuálisgép-méretezési csoportot az automatikus skálázási szabályok beállítása
Ha a fürtben több csomóponttípus, majd ismételje meg ezt minden egyes csomópont típusok/virtuális virtuálisgép-méretezési beállítja, hogy szeretné-e (bejövő vagy kimenő) méretezése. Vegye figyelembe a szükséges csomópontok számát, mielőtt beállítja az automatikus skálázást. Az elsődleges csomóponttípushoz megadott minimálisan szükséges csomópontszámot a kiválasztott megbízhatósági szint határozza meg. Tudjon meg többet [megbízhatósági szintek](service-fabric-cluster-capacity.md).

> [!NOTE]
> Az elsődleges csomópont leskálázást kisebb, mint a minimális számú ügyeljen, írja be a fürt nem stabil, vagy leállásához azt. Az alkalmazások és a rendszer szolgáltatásokra az adatvesztést eredményezhet.
> 
> 

Az automatikus méretezési funkció jelenleg nem határozzák meg, hogy az alkalmazások a előfordulhat, hogy a Service Fabric jelentéskészítés terhelés. Így az automatikus skálázás kap tisztán határozzák meg a teljesítményszámlálók, amelyek mindegyike a virtuális gép által kibocsátott vannak jelenleg méretezési csoport példányaihoz.  

Az alábbi lépéseket követve [állíthat be minden egyes virtuális gép méretezési csoport automatikus méretezési](../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview.md).

> [!NOTE]
> Forgatókönyv le egy méretezési csoportban, kivéve, ha a csomópont típusa rendelkezik egy arany és ezüst tartóssági szintjének meg kell hívnia a [Remove-ServiceFabricNodeState parancsmag](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate) csomópontok megfelelő névvel. A bronz tartóssági esetében nem ajánlott egyszerre egynél több csomópont csökkentheti.
> 
> 

## <a name="manually-add-vms-to-a-node-typevirtual-machine-scale-set"></a>Csomópont típusa/virtuális virtuálisgép-méretezési csoportot a virtuális gépek manuális hozzáadása
Minta/utasításait a [gyors üzembe helyezési sablonkatalógus](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) egyes NodeType csomóponttípusban virtuális gépek számának módosításához. 

> [!NOTE]
> Virtuális gépek hozzáadása időt vesz igénybe, így nem lehet azonnali kiegészítése várható. Így jól az ideje, hogy több mint 10 percet, mielőtt a Virtuálisgép-kapacitás érhető el a replikákat a kapacitásunkat, / service-példányok elhelyezett tervezze.
> 
> 

## <a name="manually-remove-vms-from-the-primary-node-typevirtual-machine-scale-set"></a>Manuálisan távolítsa el a virtuális gépek az elsődleges csomópont típusa/virtuális virtuálisgép-méretezési csoportot
> [!NOTE]
> A service fabric-rendszerszolgáltatások futtassa az elsődleges csomóponttípushoz, a fürtben. Ezért soha ne állítsa le vagy vertikális leskálázás, hogy a csomóponttípusok a példányok száma kisebb, mint a megbízhatósági szint szintje. Tekintse meg [részleteit itt a megbízhatósági szint](service-fabric-cluster-capacity.md). 
> 
> 

Hajtsa végre a következő lépéseket egy Virtuálisgép-példány egyszerre kell. Ez lehetővé teszi a rendszerszolgáltatások (és az állapotalapú szolgáltatások) lehet szabályosan leállítani a Virtuálisgép-példányt távolítja el és a többi csomóponton létrehozott új replikákat.

1. Futtatás [Disable-ServiceFabricNode](https://docs.microsoft.com/powershell/module/servicefabric/disable-servicefabricnode?view=azureservicefabricps) "RemoveNode" szándékát tiltsa le a csomópontot a fogjuk eltávolítani (a csomópont típusa a legmagasabb szintű példány).
2. Futtatás [Get-ServiceFabricNode](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) , győződjön meg arról, hogy a csomópont valóban átváltott le van tiltva. Ha nem, akkor várjon, amíg a csomópont le van tiltva. Ez a lépés nem hurry.
3. Minta/utasításait a [gyors üzembe helyezési sablonkatalógus](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) által egy adott csomópont típusa a virtuális gépek számának módosításához. A példány, eltávolítva a legmagasabb szintű Virtuálisgép-példány. 
4. Ismételje meg az 1 – 3 igény szerint, de az elsődleges csomóponttípusok kevesebb, mint a megbízhatósági szint kódhiba a példányok száma soha nem méretezhető. Tekintse meg [részleteit itt a megbízhatósági szint](service-fabric-cluster-capacity.md). 

## <a name="manually-remove-vms-from-the-non-primary-node-typevirtual-machine-scale-set"></a>Manuálisan távolítsa el virtuális gépek nem elsődleges csomópontot típusa/virtuális virtuálisgép-méretezési csoportot
> [!NOTE]
> Az állapotalapú szolgáltatások szüksége lesz egy bizonyos számú csomópont mindig ki lehet a rendelkezésre állás fenntartása, és a szolgáltatás állapotának megőrzése. Jelenleg a nagyon minimális szüksége lesz a csomópontok számát a partíció szolgáltatás a cél replika set száma egyenlő. 
> 
> 

Hajtsa végre a következő lépéseket egy Virtuálisgép-példány van szüksége egy időben. Ez lehetővé teszi a rendszerszolgáltatások (és az állapotalapú szolgáltatások) lehet szabályosan leállítani a Virtuálisgép-példány távolítja el, és új replikák létre más helyét.

1. Futtatás [Disable-ServiceFabricNode](https://docs.microsoft.com/powershell/module/servicefabric/disable-servicefabricnode?view=azureservicefabricps) "RemoveNode" szándékát tiltsa le a csomópontot a fogjuk eltávolítani (a csomópont típusa a legmagasabb szintű példány).
2. Futtatás [Get-ServiceFabricNode](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) , győződjön meg arról, hogy a csomópont valóban átváltott le van tiltva. Ha nem, akkor várjon, amíg a csomópont le van tiltva. Ez a lépés nem hurry.
3. Minta/utasításait a [gyors üzembe helyezési sablonkatalógus](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) által egy adott csomópont típusa a virtuális gépek számának módosításához. Ezzel eltávolítja a legmagasabb szintű Virtuálisgép-példányhoz. 
4. Ismételje meg az 1 – 3 igény szerint, de az elsődleges csomóponttípusok kevesebb, mint a megbízhatósági szint kódhiba a példányok száma soha nem méretezhető. Tekintse meg [részleteit itt a megbízhatósági szint](service-fabric-cluster-capacity.md).

## <a name="behaviors-you-may-observe-in-service-fabric-explorer"></a>Azt is megfigyelheti a Service Fabric Explorerben viselkedések
Amikor a fürt vertikális a Service Fabric Explorert a csomópontok (virtuális gép méretezési csoport példányaihoz) a fürt részét képező fogja tartalmazni.  Horizontális a fürt működik, megjelenik a nem megfelelő állapot jelenik meg, ha meghívja a eltávolított csomópontra/Virtuálisgép-példány [Remove-ServiceFabricNodeState cmd](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate) csomópontok megfelelő névvel.   

Itt van ez a viselkedés a magyarázata.

A Service Fabric Explorert a felsorolt csomópontokat tartalmazza milyen a Service Fabric-rendszerszolgáltatások tükre (FM kifejezetten) a fürt kellett/rendelkezik csomópontok ismer. Állítsa a virtuálisgép-méretezési csoport horizontális, a virtuális gép törölve lett, de FM rendszerszolgáltatás fenyegetésként észlel továbbra is, hogy a csomópont (amely a virtuális gép, amely törölve lett lett leképezve) fog érkezni. Ezért a Service Fabric Explorer továbbra is megjeleníti a csomóponton (bár az állapot vagy hiba ismeretlen is lehet).

Annak érdekében, hogy, hogy amikor eltávolít egy virtuális Gépet eltávolít egy csomópontot, két lehetősége van:

1) Válassza ki a fürthöz, így Ön az infrastruktúra integrációs csomóponttípus, arany és ezüst tartóssági szintet. Amely majd automatikusan eltávolítja a csomópontot a rendszerállapot-szolgáltatások (FM) a horizontális.
Tekintse meg [részleteit itt tartóssági szint](service-fabric-cluster-capacity.md)

2) Miután a Virtuálisgép-példány mérete, meg kell hívnia a [Remove-ServiceFabricNodeState parancsmag](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate).

> [!NOTE]
> Service Fabric-fürtök bizonyos mennyiségű csomópontokat folyamatosan rendelkezésre állását és állapot - nevezik "a kvórum fenntartása." megőrzése érdekében megkövetelte a szükséges Így is a fürtben lévő összes gép leállítása, kivéve, ha először végrehajtása után általában nem biztonságos egy [teljes biztonsági mentést az állapot](service-fabric-reliable-services-backup-restore.md).
> 
> 

## <a name="next-steps"></a>További lépések
Olvassa el a következő fürtkapacitás tervezése, a fürt frissítése, és a szolgáltatások particionálása is ismerteti:

* [A fürt kapacitásának megtervezése](service-fabric-cluster-capacity.md)
* [Fürt frissítése](service-fabric-cluster-upgrade.md)
* [Állapotalapú szolgáltatások partíció a maximális méret](service-fabric-concepts-partitioning.md)

<!--Image references-->
[BrowseServiceFabricClusterResource]: ./media/service-fabric-cluster-scale-up-down/BrowseServiceFabricClusterResource.png
[ClusterResources]: ./media/service-fabric-cluster-scale-up-down/ClusterResources.png
