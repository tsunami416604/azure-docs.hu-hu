---
title: "A Service Fabric-fürt bejövő vagy kimenő méretezése |} Microsoft Docs"
description: "A Service Fabric-fürt bejövő vagy kimenő méretezhető igény szerint úgy, hogy minden csomópont típus vagy virtuális gép méretezési automatikus méretezése szabályainak megfelelően. A Service Fabric-fürt a csomópontok hozzáadásához és eltávolításához"
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: 
ms.assetid: aeb76f63-7303-4753-9c64-46146340b83d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/22/2017
ms.author: chackdan
ms.openlocfilehash: d26a97ee0e5416fb1fe38ef0fb18fa4eb0e2963d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="scale-a-service-fabric-cluster-in-or-out-using-auto-scale-rules"></a>Bejövő vagy kimenő automatikus méretezése szabályok használatával a Service Fabric-fürt méretezése
Virtuálisgép-méretezési csoportok olyan Azure számítási erőforrás, melyekkel telepíthetnek és kezelhetnek olyan virtuális gépek gyűjteménye. Minden csomópont-típus, a Service Fabric-fürt definiált egy külön virtuálisgép-méretezési csoport lett beállítva. Az egyes csomóponttípusok majd méretezhetők a kimenő portok nyitva különböző tulajdonságkészletekkel rendelkező egymástól függetlenül, illetve különböző teljesítmény-mérőszámait rendelkezhet. Azt a további információk a [Service Fabric NodeType tulajdonságok értéke](service-fabric-cluster-nodetypes.md) dokumentum. Mivel a Service Fabric csomóponttípusok a fürt virtuálisgép-méretezési csoportok: a háttér épülnek, kell minden egyes csomópont típus vagy virtuális gép méretezési automatikus méretezése szabályokat.

> [!NOTE]
> Az előfizetés hozzáadása az új virtuális gépek, a fürtöt alkotó elég magok kell rendelkeznie. Jelenleg nem modell ellenőrzi, hogy telepítési idő hiba, hogy a kvótakorlát bármelyikét érte.
> 
> 

## <a name="choose-the-node-typevirtual-machine-scale-set-to-scale"></a>Válassza ki a csomópont típus vagy virtuális gép méretezési méretezési készletben
Ön jelenleg nem képes az automatikus méretezése szabályok megadása a virtuálisgép-méretezési csoportok a portál használatával, úgy írja meg nekünk a csomóponttípusok listában, majd automatikus méretezése szabályok hozzá őket a Azure PowerShell (1.0 +) használatával.

Ahhoz, hogy a fürtöt alkotó virtuálisgép-méretezési csoport listája, a következő parancsmagok futtatásához:

```powershell
Get-AzureRmResource -ResourceGroupName <RGname> -ResourceType Microsoft.Compute/VirtualMachineScaleSets

Get-AzureRmVmss -ResourceGroupName <RGname> -VMScaleSetName <Virtual Machine scale set name>
```

## <a name="set-auto-scale-rules-for-the-node-typevirtual-machine-scale-set"></a>A csomópont típus vagy virtuális gép méretezési automatikus méretezése szabályainak beállítása
Ha a fürt több csomóponttípusok, majd ismételje meg ezt minden csomópont típusok/virtuális gépek méretezési beállítja, hogy szeretné-e a méretezési (bejövő vagy kimenő). Vegye figyelembe a csomópontok számát, amelyekkel rendelkeznie automatikus skálázás beállítása előtt. A megbízhatósági szint választott célja a rendelkeznie kell az elsődleges csomóponttípusok a csomópontok minimális száma. Tudjon meg többet az [megbízhatóságának](service-fabric-cluster-capacity.md).

> [!NOTE]
> Skálázás le az elsődleges csomópont típus kisebb, mint a minimális számú ellenőrizze a fürt instabil vagy érdekében, hogy. Ez az alkalmazások és a rendszer szolgáltatások adatvesztés vezethet.
> 
> 

Az automatikus méretezése a szolgáltatás jelenleg nem hajtja a terhelés, az alkalmazások előfordulhat, hogy jelentéskészítési, hogy a Service Fabric. Így az automatikus méretezése kap tisztán célja a teljesítményszámlálót mutat be, a virtuális gép által kibocsátott jelenleg méretezési példányok.  

A következő lépések követésével [beállítása az egyes virtuálisgép-méretezési csoport automatikus méretezése](../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview.md).

> [!NOTE]
> A forgatókönyv a vertikális, kivéve, ha a csomóponttípus rendelkezik egy arany vagy ezüst tartóssági szintjének meg kell hívnia a [Remove-ServiceFabricNodeState parancsmag](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate) a megfelelő csomópont névvel.
> 
> 

## <a name="manually-add-vms-to-a-node-typevirtual-machine-scale-set"></a>Manuálisan a virtuális gépek felvétele egy csomópont típus vagy virtuális gép méretezési
Minta/utasításait a [gyors üzembe helyezési sablon gyűjtemény](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) módosításához a virtuális gépek minden egyes csomóponttípusban számát. 

> [!NOTE]
> A virtuális gépek hozzáadása időt vesz igénybe, így nem várható kell azonnali kiegészítése. Adja hozzá a kapacitás és az időt, lehetővé teszik a több mint 10 perc múlva a Virtuálisgép-kapacitást a replikák / elhelyezett, hogy a szolgáltatás, ezért tervezzen.
> 
> 

## <a name="manually-remove-vms-from-the-primary-node-typevirtual-machine-scale-set"></a>Manuálisan távolítsa el az elsődleges csomópont típus/virtuális gép méretezési csoport virtuális gépek
> [!NOTE]
> A service fabric rendszer szolgáltatások futtatása az elsődleges csomóponttípusok a fürtön. Ezért soha ne állítsa le vagy csökkentheti a csomóponttípusok található példányok száma kisebb, mint a megbízhatósági szint indokol. Tekintse meg [a részleteket itt megbízhatóság rétegek](service-fabric-cluster-capacity.md). 
> 
> 

Hajtsa végre a következő lépéseket egy Virtuálisgép-példány egyszerre kell. Ez lehetővé teszi a rendszerszolgáltatások (és az állapotalapú szolgáltatások) kell leállítása a Virtuálisgép-példány eltávolítása és a többi csomóponton létrehozott új replikákat.

1. Futtatás [Disable-ServiceFabricNode](https://msdn.microsoft.com/library/mt125852.aspx) letiltása a csomópont "RemoveNode" biztonsági mentés fog eltávolítása (az adott típusú csomópont legmagasabb példány).
2. Futtatás [Get-ServiceFabricNode](https://msdn.microsoft.com/library/mt125856.aspx) győződjön meg arról, hogy a csomópont valóban átváltott le van tiltva. Ha nem, akkor várjon, amíg a csomópont le van tiltva. Ez a lépés nem hurry.
3. A minta/utasításait a [gyors üzembe helyezési sablon gyűjtemény](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) módosításához a virtuális gépek számát egy adott csomóponttípusban. Az eltávolított-példány a legmagasabb Virtuálisgép-példány. 
4. Ismételje meg az 1 – 3 igény szerint, de soha nem csökkentheti az elsődleges csomóponttípusok kisebb, mint a megbízhatósági szint indokol található példányok száma. Tekintse meg [a részleteket itt megbízhatóság rétegek](service-fabric-cluster-capacity.md). 

## <a name="manually-remove-vms-from-the-non-primary-node-typevirtual-machine-scale-set"></a>Manuálisan távolítsa el virtuális gépek nem elsődleges csomópontot típus vagy virtuális gép méretezési csoport
> [!NOTE]
> Egy állapotalapú szolgáltatás kell bizonyos számú csomópontok mindig ki kell fenntartani a rendelkezésre állási, a szolgáltatás állapotának megőrzéséhez. : A nagyon minimális van szüksége a csomópontok számát a partíció szolgáltatás a cél replika set számánál. 
> 
> 

Hajtsa végre a következő lépéseket egy Virtuálisgép-példány egyszerre kell. Ez lehetővé teszi a rendszerszolgáltatások (és az állapotalapú szolgáltatások) kell leállítása a távolítja el a Virtuálisgép-példány, és új replikák létrehozott más helyét.

1. Futtatás [Disable-ServiceFabricNode](https://msdn.microsoft.com/library/mt125852.aspx) letiltása a csomópont "RemoveNode" biztonsági mentés fog eltávolítása (az adott típusú csomópont legmagasabb példány).
2. Futtatás [Get-ServiceFabricNode](https://msdn.microsoft.com/library/mt125856.aspx) győződjön meg arról, hogy a csomópont valóban átváltott le van tiltva. Ha nem várja meg, amíg a csomópont le van tiltva. Ez a lépés nem hurry.
3. A minta/utasításait a [gyors üzembe helyezési sablon gyűjtemény](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) módosításához a virtuális gépek számát egy adott csomóponttípusban. Ezzel eltávolítja a legmagasabb Virtuálisgép-példány. 
4. Ismételje meg az 1 – 3 igény szerint, de soha nem csökkentheti az elsődleges csomóponttípusok kisebb, mint a megbízhatósági szint indokol található példányok száma. Tekintse meg [a részleteket itt megbízhatóság rétegek](service-fabric-cluster-capacity.md).

## <a name="behaviors-you-may-observe-in-service-fabric-explorer"></a>Viselkedéshez jelenhet meg a Service Fabric Explorerben
Amikor a fürt méretezni a Service Fabric Explorer (virtuálisgép-méretezési készlet példányok) a fürt részét képező csomópontok száma fogja tartalmazni.  Akkor a fürt működik, megjelenik a eltávolított csomópontot vagy Virtuálisgép-példány nem megfelelő állapot jelenik meg, kivéve, ha meghívja a [Remove-ServiceFabricNodeState cmd](https://msdn.microsoft.com/library/mt125993.aspx) a megfelelő csomópont névvel.   

Ez a magyarázat az ilyen viselkedést.

A felsorolt a Service Fabric Explorerben csomópontja a Service Fabric rendszer szolgáltatásoktól tükre (FM kifejezetten) a fürt kellett/rendelkezik csomópontok száma ismer. Akkor állítsa a virtuálisgép-méretezési, a virtuális gép törölve lett, azonban a FM rendszerszolgáltatás úgy továbbra is értelmezi, vissza határozza meg, hogy a csomópont (a virtuális géphez, amely törölve lett leképezve). Így továbbra is a Service Fabric Explorer (bár a állapota hibás vagy ismeretlen is lehet) a csomóponton megjelenítendő.

Győződjön meg arról, hogy a csomópont eltávolítása, a virtuális gép eltávolításakor, hogy két lehetőség közül választhat:

1) Adja meg a tartóssági szint arany vagy ezüst a csomópont esetében a fürtöt, amely az infrastruktúra-integráció lehetővé teszi. Amely majd automatikusan eltávolítja a csomópontok a rendszerállapot-szolgáltatások (FM) akkor.
Tekintse meg [a részleteket itt tartóssági szint](service-fabric-cluster-capacity.md)

2) Miután a virtuális gép példánya csökkentették, meg kell hívnia a [Remove-ServiceFabricNodeState parancsmag](https://msdn.microsoft.com/library/mt125993.aspx).

> [!NOTE]
> Service Fabric-fürtök csomópontokat a folyamatosan rendelkezésre álljon, és állapot - néven "kvórum fenntartása." megőrzése érdekében legyen egy bizonyos számú kell Igen, az általában nem biztonságos, kivéve, ha először elvégezte a fürt összes gép leállítása a [teljes biztonsági mentés a állapot](service-fabric-reliable-services-backup-restore.md).
> 
> 

## <a name="next-steps"></a>Következő lépések
Olvassa el a fürt kapacitás megtervezésének, fürt frissítése és szolgáltatások particionálás is olvashat a következő:

* [A fürt kapacitásának megtervezése](service-fabric-cluster-capacity.md)
* [Fürt frissítése](service-fabric-cluster-upgrade.md)
* [A maximális skálája partíció állapotalapú szolgáltatások](service-fabric-concepts-partitioning.md)

<!--Image references-->
[BrowseServiceFabricClusterResource]: ./media/service-fabric-cluster-scale-up-down/BrowseServiceFabricClusterResource.png
[ClusterResources]: ./media/service-fabric-cluster-scale-up-down/ClusterResources.png
