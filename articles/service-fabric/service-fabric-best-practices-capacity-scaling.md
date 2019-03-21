---
title: Az Azure Service Fabric-kapacitás tervezése és skálázása – ajánlott eljárások |} A Microsoft Docs
description: Ajánlott eljárások, tervezési és a Service Fabric-fürtök és alkalmazások skálázása.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: 425154958e4c60902b56f320f714a011b9095830
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57997343"
---
# <a name="capacity-planning-and-scaling"></a>Kapacitástervezés és skálázás

Mielőtt bármely Azure Service Fabric-fürtöt hoz létre, vagy a fürtöt a számítási erőforrások méretezése, fontos a kapacitástervezés. A kapacitás tervezésével kapcsolatos további információkért lásd: [tervezése a Service Fabric-fürt kapacitása](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity). Mellett a mérlegeli Nodetype és a fürt jellemzőit, tervezze meg a méretezési műveletek, mint a végrehajtásához a hozzáadni kívánt virtuális gépek számától függetlenül éles környezet egy óránál több időt vesz igénybe.

## <a name="auto-scaling"></a>Automatikus méretezés
Méretezési műveleteket kell elvégezni keresztül az Azure-erőforrások sablon üzembe helyezésének, kezelje az ajánlott eljárás, mert [erőforrás-konfigurációk kódként]( https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code), és a virtuális gép méretezési automatikus skálázást eredményez a rendszerverzióval ellátott forrásprogramja meghatározása a virtuálisgép-méretezési csoport Resource Manager-sablon beállítása a példányok számát; Növelje a későbbiekben okozó nem kívánt méretezési műveletek, valamint az általános kockázati kell használnia az automatikus méretezést, ha:

* A Resource Manager-sablonok üzembe helyezése a deklarált megfelelő kapacitással nem támogatja a használati eset.
  * Manuális skálázás mellett beállíthatja egy [folyamatos integrációs és teljesítési folyamat az Azure DevOps szolgáltatás használatával Azure erőforráscsoport-telepítési projektek]( https://docs.microsoft.com/azure/vs-azure-tools-resource-groups-ci-in-vsts), amelyek gyakran váltja ki egy logikai alkalmazást, amely virtuális gép teljesítmény-mérőszámok onnan lekérdezett [Azure Monitor REST API](https://docs.microsoft.com/azure/azure-monitor/platform/rest-api-walkthrough); hatékonyan az automatikus skálázás bármilyen metrikák alapján szeretne, optimalizálhatja az Azure Resource Manager hozzáadott értéket.
* Csak ki kell horizontális skálázása a virtuális gép méretezési készlet 1 csomópontos egyszerre.
  * Horizontális felskálázása 3 vagy több csomópontja egyidejűleg, érdemes [Service Fabric-fürt kétirányú méretezése hozzáadásával egy virtuálisgép-méretezési csoportban](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out), és méretezése a legbiztonságosabb és virtuálisgép-méretezési csoport ki állítja vízszintesen 1 csomópontos egyszerre.
* Silver szintű megbízhatóság, vagy a Service Fabric-fürt és a Silver szintű tartósságot magasabb vagy újabb bármilyen méret esetén konfigurálja az automatikus skálázási szabályok beállítása rendelkezik.
  * Az automatikus skálázási szabályok kapacitás [minimális] 5 virtuálisgép-példányok nagyobbnak vagy azzal egyenlőnek kell lennie, és a megbízhatósági szint legalább az elsődleges csomóponttípus nagyobbnak vagy azzal egyenlőnek kell lennie.

> [!NOTE]
> Az Azure Service Fabric állapotalapú service fabric: / rendszer/InfastructureService/< NODE_TYPE_NAME >, fut minden csomópont típusa, amely rendelkezik a Silver vagy nagyobb tartósságot, amely az egyetlen rendszer szolgáltatás futtatásához az Azure-ban a fürtök csomóponttípusok valamelyik támogatott . 

## <a name="vertical-scaling-considerations"></a>Függőleges méretezési szempontok

[Vertikális skálázás](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out) az Azure Service Fabric-csomóponttípus szükséges lépéseit és szempontjait számos. Példa:
* A fürt kifogástalan állapotban kell lennie a skálázás előtt. Ellenkező esetben csak fog megingatására további fürt.
* **Ezüst szintű vagy nagyobb tartósságot** az összes Service Fabric Cluster NodeType állapotalapú szolgáltatások üzemeltető megadása kötelező.

> [!NOTE]
> Az elsődleges NodeType csomóponttípus, amelyen az állapotalapú Service Fabric-rendszerszolgáltatások kell lennie a Silver szintű tartóssági szint vagy nagyobb. Miután engedélyezte a Silver szintű tartósságot, például frissítések, a fürt működését hozzáadása vagy eltávolítása, csomópontok, és így tovább lassabb lesz, mert a rendszer az adatok biztonsága optimalizálja a műveletek sebessége keresztül.

Függőleges méretezés a Virtual Machine Scale Set egy destruktív művelet van. Ehelyett horizontálisan egy új méretezési csoportot a kívánt termékváltozatú hozzáadásával a fürtök skálázásának, és át a szolgáltatásokat a kívánt Termékváltozat egy biztonságos függőleges skálázási művelet végrehajtásához. Egy virtuálisgép-méretezési erőforrás Termékváltozat módosítása egy destruktív művelet, mert azt a gazdagépet, amely eltávolítja az összes helyi megőrzött állapot feldolgozóméret.

A Service Fabric [csomópont-tulajdonságok és elhelyezési korlátozások](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-cluster-description#node-properties-and-placement-constraints) arról dönteni, hogy az alkalmazások szolgáltatásokat a fürt által használt. Ha az elsődleges csomópont típusát a függőleges skálázás deklarálja azonos tulajdonság értékei `"nodeTypeRef"`, amely megtalálható a virtuálisgép-méretezési csoport Service Fabric bővítménye. A Resource Manager-sablon a következő kódrészlet azt deklarálja, ugyanazzal az értékkel, az új kiépítése méretezési csoportok vannak méretezési lehetőségek érhetők el, és csak a támogatott egy ideiglenes állapotalapú, a fürt tulajdonságait mutatja:

```json
"settings": {
   "nodeTypeRef": ["[parameters('primaryNodetypeName')]"]
}
```

> [!NOTE]
> Ne hagyja több méretezési, amely ugyanaz, mint a fürtöt `nodeTypeRef` tulajdonság értéke hosszabb, mint egy sikeres függőleges skálázási műveletek végrehajtásához szükséges.
> Mindig ellenőrizze a tesztelési környezetek operations éles környezetben módosítások megkísérlése előtt. Alapértelmezés szerint a Service Fabric-fürt rendszer szolgáltatások rendelkezik, amelyekre az elsődleges NodeType csomóponttípus csak egy elhelyezési korlátozás.

A csomópont tulajdonságait és a bejelentett elhelyezési korlátozások hajtsa végre a következő lépéseket egy Virtuálisgép-példány egyszerre. Ez lehetővé teszi a rendszerszolgáltatások (és az állapotalapú szolgáltatások) lehet szabályosan leállítani az új replika létrehozásakor máshol távolítja el a Virtuálisgép-példányokon.
1. A PowerShellben futtassa a `Disable-ServiceFabricNode` szándékával "RemoveNode" tiltsa le a csomópontot, hogy távolítsa el lesz. Távolítsa el, amely rendelkezik a legtöbb typ uzlu. Például ha egy hat csomópontot tartalmazó fürtben, távolítsa el a "MyNodeType_5" virtuálisgép-példány.
2. Futtatás `Get-ServiceFabricNode` , győződjön meg arról, hogy a csomópont átváltott le van tiltva. Ha nem, akkor várjon, amíg a csomópont le van tiltva. Ez minden egyes csomópont esetében néhány órát is igénybe vehet. Nem folytatható, amíg a csomópont átváltott le van tiltva.
3. Egy adott csomópont típusa a virtuális gépek számának csökkentéséhez. A legmagasabb szintű Virtuálisgép-példány már törlődni fog.
4. Ismételje meg az 1 – 3 igény szerint, de az elsődleges csomóponttípusok kevesebb, mint a megbízhatósági szint kódhiba a példányok száma soha nem méretezhető. Lásd: [tervezése a Service Fabric-fürt kapacitása](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) ajánlott példányok listáját.

## <a name="horizontal-scaling"></a>Vízszintes méretezés

Vízszintes méretezés a Service Fabric teheti meg, vagy [manuálisan](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down) vagy [programozott módon](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-programmatic-scaling).

> [!NOTE]
> Ha egy csomópont típusa, amely rendelkezik a silver vagy Gold szintű tartósságot rendszer méretezés, lassú skálázás lesz.

### <a name="scaling-out"></a>Méretezés

Service Fabric-fürt méretezése növelje a példányszámot adott virtuálisgép-méretezési. Ki lehet terjeszteni programozott módon a kívánt méretezési csoport kapacitásának növelése a AzureClient és -azonosítója használatával.

```c#
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
```

A horizontális felskálázáshoz manuálisan módosítsa a kapacitást a kívánt SKU tulajdonságát [virtuálisgép-méretezési](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) erőforrás.
```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

### <a name="scaling-in"></a>A méretezés

A szükséges további szempontok, mint a felskálázás. Példa:
* Service Fabric-rendszerszolgáltatások futtassa az elsődleges csomóponttípushoz, a fürtben. Soha ne állítsa le- vagy leskálázás példányok adott csomóponttípus, így kevesebb, mint a megbízhatósági szint kódhiba példányt. 
* Az állapotalapú szolgáltatások szüksége lesz egy bizonyos számú csomópontot, amely mindig naprakész rendelkezésre állását és a szolgáltatás állapotának megőrzéséhez. Minimális szüksége lesz a csomópontok számát a partíció szolgáltatás a cél replika set száma egyenlő.

A manuális skálázása kövesse az alábbi lépéseket:

1. A PowerShellben futtassa a `Disable-ServiceFabricNode` szándékával "RemoveNode" tiltsa le a csomópontot, hogy távolítsa el lesz. Távolítsa el, amely rendelkezik a legtöbb typ uzlu. Például ha egy hat csomópontot tartalmazó fürtben, távolítsa el a "MyNodeType_5" virtuálisgép-példány.
2. Futtatás `Get-ServiceFabricNode` , győződjön meg arról, hogy a csomópont átváltott le van tiltva. Ha nem, akkor várjon, amíg a csomópont le van tiltva. Ez minden egyes csomópont esetében néhány órát is igénybe vehet. Nem folytatható, amíg a csomópont átváltott le van tiltva.
3. Egy adott csomópont típusa a virtuális gépek számának csökkentéséhez. A legmagasabb szintű Virtuálisgép-példány már törlődni fog.
4. Ismételje meg az 1 – 3 igény szerint, de az elsődleges csomóponttípusok kevesebb, mint a megbízhatósági szint kódhiba a példányok száma soha nem méretezhető. Lásd: [tervezése a Service Fabric-fürt kapacitása](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) ajánlott példányok listáját.

Manuális méretezése a, módosítsa a kapacitást a kívánt SKU tulajdonságát [virtuálisgép-méretezési](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) erőforrás.

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

1. Ismételje 1 – 3 üzembe helyezi a kapacitást. Az elsődleges csomóponttípusok kisebb, mint a megbízhatósági szint indokol, a példányok nem méretezhető. Megbízhatósági szintek és azok szükséges példányok kapcsolatos részletekért tekintse meg a [tervezése a Service Fabric-fürt kapacitása](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity).

A Leállítás, a skálázás programozott módon elő kell készítenie a csomópontot. Ebbe beletartozik a csomópontot, eltávolítja, vagyis a legnagyobb példány csomópont keresése és inaktiválása. Példa:

```c#
using (var client = new FabricClient())
{
    var mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync())
        .Where(n => n.NodeType.Equals(NodeTypeToScale, StringComparison.OrdinalIgnoreCase))
        .Where(n => n.NodeStatus == System.Fabric.Query.NodeStatus.Up)
        .OrderByDescending(n =>
        {
            var instanceIdIndex = n.NodeName.LastIndexOf("_");
            var instanceIdString = n.NodeName.Substring(instanceIdIndex + 1);
            return int.Parse(instanceIdString);
        })
        .FirstOrDefault();
```

Miután azonosította a csomópont eltávolítása, inaktiválása, és távolítsa el azt azonos `FabricClient` példány (`client` ebben az esetben) és a csomópont-példány nevét (`instanceIdString` ebben az esetben), használja a fenti kód:

```c#
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);

// Remove the node from the Service Fabric cluster
ServiceEventSource.Current.ServiceMessage(Context, $"Disabling node {mostRecentLiveNode.NodeName}");
await client.ClusterManager.DeactivateNodeAsync(mostRecentLiveNode.NodeName, NodeDeactivationIntent.RemoveNode);

// Wait (up to a timeout) for the node to gracefully shutdown
var timeout = TimeSpan.FromMinutes(5);
var waitStart = DateTime.Now;
while ((mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Up || mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Disabling) &&
        DateTime.Now - waitStart < timeout)
{
    mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync()).FirstOrDefault(n => n.NodeName == mostRecentLiveNode.NodeName);
    await Task.Delay(10 * 1000);
}

// Decrement VMSS capacity
var newCapacity = (int)Math.Max(MinimumNodeCount, scaleSet.Capacity - 1); // Check min count 

scaleSet.Update().WithCapacity(newCapacity).Apply();
```

> [!NOTE]
> Horizontális a fürt működik, megjelenik a eltávolított csomópontra/Virtuálisgép-példány a Service Fabric Explorert a nem megfelelő állapot jelenik meg. Ezt a viselkedést egy ismertetése: [jelenhet meg a Service Fabric Explorerben viselkedések](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down#behaviors-you-may-observe-in-service-fabric-explorer).
> 
> A következőket teheti:
> * Hívás [Remove-ServiceFabricNodeState cmd](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) csomópontok megfelelő névvel.
> * Üzembe helyezése [service fabric automatikus méretezési segédlet az alkalmazás](https://github.com/Azure/service-fabric-autoscale-helper/) a fürtön, amely biztosítja a méretezett csomópontok le törlődik a Service Fabric Explorerből.

## <a name="reliability-levels"></a>Megbízhatósági szintek

A [megbízhatósági szint](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) a Service Fabric-fürt erőforrás tulajdonsága, és nem konfigurálható eltérő egyéni NodeType. Azt szabályozza, a replikációs tényező a rendszerszolgáltatások a fürt számára, és a egy beállítás a fürt resource szintjén. A megbízhatósági szint határozza meg, hogy az adott elsődleges csomóponttípus csomópontok minimális száma. A megbízhatósági szint hajthatja végre a következő értékeket:
* Platinum - rendszer szolgáltatások futnak a hét és kilenc magcsomópontok cél replika set számával együtt.
* Gold - rendszer szolgáltatások futnak a hét és hét magcsomópontok cél replika set számával együtt.
* Silver szintű - öt és az öt magcsomópontok cél replika set számát a rendszer szolgáltatások futnak.
* Bronz - rendszer szolgáltatások futnak a három és három magcsomópontok cél replika set számával együtt.

A minimális ajánlott megbízhatósági szint Silver.

A megbízhatósági szint van beállítva, a Tulajdonságok szakaszában a [Microsoft.ServiceFabric/clusters erőforrás](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters), ehhez hasonló:

```json
"properties":{
    "reliabilityLevel": "Silver"
}
```

## <a name="durability-levels"></a>Tartóssági szint

> [!WARNING]
> A csomóponttípusok a bronz tartóssági futó beszerzése _jogosultság nélküli_. Ez azt jelenti, hogy infrastruktúra feladatok, amelyek hatással az állapot nélküli munkaterhelés nem kell leállt vagy késleltetett, ami hatással lehet a számítási feladatokat. Bronz tartóssági csak állapot nélküli számítási feladatokat futtató csomóponttípusok használja. A termelési számítási feladatokhoz, futtassa a Silver vagy újabb állapot konzisztencia biztosításához. Válassza ki a megfelelő megbízhatóságát, az útmutató a [kapacitástervezési dokumentációjában](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity).

A tartóssági szint kell állítani a két erőforrás. A bővítmény profiljában a [virtuálisgép-méretezési erőforrás](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile):

```json
"extensionProfile": {
    "extensions":          {
        "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
        "properties": {
            "settings": {
                "durabilityLevel": "Bronze"
            }
        }
    }
}
```

Majd a `nodeTypes` a a [Microsoft.ServiceFabric/clusters erőforrás](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters) 

```json
"nodeTypes": [
    {
        "name": "[variables('vmNodeType0Name')]",
        "durabilityLevel": "Bronze"
    }
]
```

## <a name="next-steps"></a>További lépések

* Fürt létrehozása a virtuális gépek vagy a Windows Server rendszert futtató számítógépeken: [A Service Fabric-fürt létrehozása a Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Fürt létrehozása a virtuális gépek vagy a Linux operációs rendszert futtató számítógépeken: [Linux-fürt létrehozása](service-fabric-cluster-creation-via-portal.md)
* A [Service Fabric támogatási lehetőségeinek](service-fabric-support.md) ismertetése

[Image1]: ./media/service-fabric-best-practices/generate-common-name-cert-portal.png
