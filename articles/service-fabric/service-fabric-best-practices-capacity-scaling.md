---
title: Az Azure Service Fabric kapacitásának tervezése és méretezése | Microsoft Docs
description: Ajánlott eljárások Service Fabric fürtök és alkalmazások tervezéséhez és méretezéséhez.
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
ms.date: 04/25/2019
ms.author: pepogors
ms.openlocfilehash: d4daa7ae9c7e58c1949dfbe4427a154c389100d4
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68348371"
---
# <a name="capacity-planning-and-scaling-for-azure-service-fabric"></a>Kapacitás megtervezése és méretezése az Azure Service Fabric

Mielőtt bármilyen Azure Service Fabric-fürtöt hozna létre, vagy a fürtöt futtató számítási erőforrásokat méretezheti, fontos a kapacitás megtervezése. További információ a kapacitás megtervezéséről: [a Service Fabric-fürt kapacitásának](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)megtervezése. A fürtök méretezhetőségével kapcsolatos további gyakorlati útmutatásért lásd: [Service Fabric skálázhatósági megfontolások](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric#scalability-considerations).

A csomópont típusának és a fürt jellemzőinek figyelembevétele mellett a skálázási műveletek várhatóan hosszabb időt vesznek igénybe, amíg az éles környezetben is elvégezhető. Ez a megfontolás a hozzáadott virtuális gépek számától függetlenül igaz.

## <a name="autoscaling"></a>Automatikus skálázás
A skálázási műveleteket Azure Resource Manager sablonokon keresztül kell végrehajtania, mivel ez az ajánlott eljárás az [erőforrás-konfigurációk kódként]( https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code)való kezelésére. 

A virtuálisgép-méretezési csoportokon keresztüli automatikus skálázással a verziószámmal ellátott Resource Manager-sablon pontatlanul definiálja a példányok számát a virtuálisgép-méretezési csoportokhoz. A pontatlan definíció növeli annak kockázatát, hogy a jövőbeli központi telepítések nem kívánt skálázási műveleteket okozzák. Általában az automatikus skálázást kell használnia, ha:

* A megfelelő kapacitással rendelkező Resource Manager-sablonok üzembe helyezése nem támogatja a használati esetet.
     
   A manuális skálázás mellett az Azure DevOps-szolgáltatásokban is konfigurálhat [folyamatos integrációs és kézbesítési folyamatot az Azure erőforráscsoport-telepítési projektjeivel](https://docs.microsoft.com/azure/vs-azure-tools-resource-groups-ci-in-vsts). Ezt a folyamatot általában egy olyan logikai alkalmazás indítja el, amely a [Azure Monitor Rest APIból](https://docs.microsoft.com/azure/azure-monitor/platform/rest-api-walkthrough)lekérdezett virtuális gépek teljesítmény-metrikáit használja. A folyamat hatékonyan kibővíti a kívánt mérőszámok alapján, a Resource Manager-sablonok optimalizálásával.
* Egyszerre csak egy virtuálisgép-méretezési csoport csomópontot kell horizontálisan méreteznie.
   
   Ha egyszerre három vagy több csomópontot szeretne felskálázásra, egy [virtuálisgép-méretezési csoport hozzáadásával Service Fabric](virtual-machine-scale-set-scale-node-type-scale-out.md)-fürtöt kell felskálázása. A virtuálisgép-méretezési csoportok horizontálisan, egyszerre egy csomóponton méretezhetők és méretezhetők.
* A Service Fabric-fürthöz Silver megbízhatósági vagy magasabb szintű, az automatikus skálázási szabályokat konfiguráló méreteken pedig ezüst tartósság vagy magasabb.
  
   Az automatikus skálázási szabályok minimális kapacitásának legalább öt virtuálisgép-példánynak kell lennie. Az elsődleges csomópont típusának legalább a megbízhatósági szintjénél nagyobbnak kell lennie.

> [!NOTE]
> A Service Fabric állapot-nyilvántartó Service Fabric:/System/InfastructureService/< NODE_TYPE_NAME > minden olyan csomópont-típuson fut, amely ezüst vagy magasabb tartóssággal rendelkezik. Ez az egyetlen olyan rendszerszolgáltatás, amely az Azure-ban a fürtök bármelyik csomópont-típusán futtatható.

## <a name="vertical-scaling-considerations"></a>Vertikális skálázási megfontolások

Az Azure Service Fabric egy csomópont-típus [vertikális skálázása](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out) számos lépést és szempontot igényel. Példa:

* A skálázás előtt a fürtnek kifogástalan állapotban kell lennie. Ellenkező esetben a fürt destabilizálása is megtörténik.
* Az állapot-nyilvántartó szolgáltatásokat futtató összes Service Fabric esetében az ezüst tartóssági szintnek vagy nagyobbnak kell lennie.

> [!NOTE]
> Az állapot-nyilvántartó Service Fabric rendszerszolgáltatásokat futtató elsődleges csomópont típusa csak ezüst tartóssági vagy magasabb szintű lehet. Miután engedélyezte az ezüst tartósságot, a fürt műveletei, például a frissítések, a csomópontok hozzáadása vagy eltávolítása, és így tovább lassabbak lesznek, mivel a rendszer az adatbiztonságot a műveletek sebessége alapján optimalizálja.

A virtuálisgép-méretezési csoport vertikális skálázása romboló művelet. Ehelyett horizontálisan méretezheti a fürtöt egy új méretezési csoport hozzáadásával a kívánt SKU-val. Ezután telepítse át a szolgáltatásokat a kívánt SKU-ba egy biztonságos vertikális skálázási művelet elvégzéséhez. A virtuálisgép-méretezési csoport erőforrás-SKU-jának módosítása roncsolásos művelet, mert alaphelyzetbe állítja a gazdagépeket, ami eltávolítja az összes helyileg megőrzött állapotot.

A fürt Service Fabric [csomópont-tulajdonságokat és elhelyezési korlátozásokat](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-cluster-description#node-properties-and-placement-constraints) használ, hogy eldöntse, hová szeretné üzemeltetni az alkalmazás szolgáltatásait. Az elsődleges csomópont típusának vertikális skálázásakor deklarálja az azonos tulajdonságértékek értékét a következőhöz `"nodeTypeRef"`:. Ezeket az értékeket a virtuálisgép-méretezési csoportokhoz tartozó Service Fabric-bővítményben tekintheti meg. 

A Resource Manager-sablonok következő kódrészlete a deklarált tulajdonságokat jeleníti meg. Ez az érték megegyezik az újonnan kiosztott méretezési csoportokkal, amelyekhez méretezést végez, és csak a fürt ideiglenes állapot-nyilvántartó szolgáltatásaként támogatott.

```json
"settings": {
   "nodeTypeRef": ["[parameters('primaryNodetypeName')]"]
}
```

> [!NOTE]
> Ne hagyja, hogy a fürt több méretezési csoporttal fusson `nodeTypeRef` , amelyek ugyanazt a tulajdonságot használják, mint amennyi a sikeres vertikális skálázási művelet befejezéséhez szükséges.
>
> Mindig ellenőrizze a tesztelési környezetekben lévő műveleteket, mielőtt megkísérli a módosításokat az éles környezetben. Alapértelmezés szerint Service Fabric a fürt rendszerszolgáltatásainak elhelyezési megkötése csak a célként megadott elsődleges csomópont típusára vonatkozik.

A csomópont tulajdonságai és az elhelyezési megkötések deklarálása esetén a következő lépésekkel egyszerre egy virtuálisgép-példányt hajthat végre. Ez lehetővé teszi, hogy a rendszerszolgáltatások (és az állapot-nyilvántartó szolgáltatások) szabályosan legyenek leállítva az eltávolítani kívánt virtuálisgép-példányon, mivel az új replikák máshol jönnek létre.

1. A powershellből futtassa `Disable-ServiceFabricNode` `RemoveNode` a parancsot, hogy letiltsa az eltávolítani kívánt csomópontot. Távolítsa el a legmagasabb számú csomópont-típust. Ha például hat csomópontos fürttel rendelkezik, távolítsa el a "MyNodeType_5" virtuálisgép-példányt.
2. A `Get-ServiceFabricNode` futtatásával győződjön meg arról, hogy a csomópont le van-e tiltva. Ha nem, akkor várjon, amíg a csomópont le van tiltva. Ez eltarthat egy pár órát az egyes csomópontokhoz. Ne folytassa, amíg a csomópont nem lett letiltva.
3. Csökkentse a virtuális gépek számát egy adott csomópont-típusban. A legmagasabb virtuálisgép-példány most el lesz távolítva.
4. Ismételje meg az 1 – 3 igény szerint, de az elsődleges csomóponttípusok kevesebb, mint a megbízhatósági szint kódhiba a példányok száma soha nem méretezhető. Lásd: [a Service Fabric-fürt kapacitásának](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) megtervezése az ajánlott példányok listájához.
5. Ha minden virtuális gép el lett mentve ("Down"-ként jelenik meg), a Fabric:/System/InfrastructureService/[NODE name] hibaüzenetet jelenít meg. Ezután frissítheti a fürterőforrás-t a csomópont típusának eltávolításához. Használhatja az ARM-sablon üzembe helyezését, vagy szerkesztheti a fürterőforrás-t az [Azure Resource Managerrel](https://resources.azure.com). Ezzel elindítja a fürt frissítését, amely a hibás állapotú háló:/System/InfrastructureService/[NODE type] szolgáltatást fogja eltávolítani.
 6. Ezután törölheti is a VMScaleSet, ha a csomópontokat a Service Fabric Explorer nézetből "lefelé" fogja látni. Az utolsó lépés az, hogy megtisztítsa őket `Remove-ServiceFabricNodeState` a paranccsal.

## <a name="horizontal-scaling"></a>Vízszintes méretezés

A horizontális skálázást [manuálisan](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down) vagy [programozott](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-programmatic-scaling)módon is elvégezheti.

> [!NOTE]
> Ha olyan csomópont-típust használ, amely ezüst vagy arany tartósságot tartalmaz, a skálázás lassú lesz.

### <a name="scaling-out"></a>Méretezés

Service Fabric-fürt felskálázása egy adott virtuálisgép-méretezési csoport példányszámának növelésével. A kapacitás növeléséhez programozott módon, a és `AzureClient` a kívánt méretezési csoport azonosítójának használatával is méretezhető.

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
```

A manuális méretezéshez frissítse a kapacitást a kívánt [virtuálisgép-méretezési készlet](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) erőforrás SKU tulajdonságában.

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

### <a name="scaling-in"></a>Skálázás folyamatban

A-ben a méretezés több szempontot igényel, mint a horizontális felskálázás. Példa:

* Service Fabric rendszerszolgáltatások a fürt elsődleges csomópont-típusa alatt futnak. Soha ne állítsa le vagy méretezze le az adott csomópont típusú példányok számát, hogy kevesebb példánya legyen, mint a megbízhatósági szinten. 
* Állapot-nyilvántartó szolgáltatás esetén bizonyos számú csomópontra van szükség, amely mindig a rendelkezésre állás fenntartása és a szolgáltatás állapotának megőrzése érdekében szükséges. Legalább egy olyan csomópontra van szükség, amely megegyezik a partíció vagy szolgáltatás célként megadott replikáinak számával.

A manuális méretezéshez kövesse az alábbi lépéseket:

1. A powershellből futtassa `Disable-ServiceFabricNode` `RemoveNode` a parancsot, hogy letiltsa az eltávolítani kívánt csomópontot. Távolítsa el a legmagasabb számú csomópont-típust. Ha például hat csomópontos fürttel rendelkezik, távolítsa el a "MyNodeType_5" virtuálisgép-példányt.
2. A `Get-ServiceFabricNode` futtatásával győződjön meg arról, hogy a csomópont le van-e tiltva. Ha nem, akkor várjon, amíg a csomópont le van tiltva. Ez eltarthat egy pár órát az egyes csomópontokhoz. Ne folytassa, amíg a csomópont nem lett letiltva.
3. Csökkentse a virtuális gépek számát egy adott csomópont-típusban. A legmagasabb virtuálisgép-példány most el lesz távolítva.
4. A kívánt kapacitás kiépítése előtt ismételje meg az 1 – 3. lépést. Ne méretezze le az elsődleges csomópontokban lévő példányok számát a megbízhatósági szinteknél kisebb értékre. Lásd: [a Service Fabric-fürt kapacitásának](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) megtervezése az ajánlott példányok listájához.

Ha manuálisan szeretné méretezni a méretezést, frissítse a kapacitást a virtuálisgép- [méretezési csoport](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) kívánt erőforrás SKU tulajdonságában.

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

A csomópont programozott módon történő skálázásához elő kell készítenie a csomópontot a Leállítás érdekében. Keresse meg az eltávolítandó csomópontot (a legmagasabb példányú csomópont). Példa:

```csharp
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

Inaktiválja és távolítsa el a csomópontot ugyanazzal `FabricClient` a példánnyal`client` (ebben az esetben) és a csomópont`instanceIdString` -példánnyal (ebben az esetben), amelyet az előző kódban használt:

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);

// Remove the node from the Service Fabric cluster
ServiceEventSource.Current.ServiceMessage(Context, $"Disabling node {mostRecentLiveNode.NodeName}");
await client.ClusterManager.DeactivateNodeAsync(mostRecentLiveNode.NodeName, NodeDeactivationIntent.RemoveNode);

// Wait (up to a timeout) for the node to gracefully shut down
var timeout = TimeSpan.FromMinutes(5);
var waitStart = DateTime.Now;
while ((mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Up || mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Disabling) &&
        DateTime.Now - waitStart < timeout)
{
    mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync()).FirstOrDefault(n => n.NodeName == mostRecentLiveNode.NodeName);
    await Task.Delay(10 * 1000);
}

// Decrement virtual machine scale set capacity
var newCapacity = (int)Math.Max(MinimumNodeCount, scaleSet.Capacity - 1); // Check min count 

scaleSet.Update().WithCapacity(newCapacity).Apply();
```

> [!NOTE]
> Amikor lekicsinyít egy fürtöt, az eltávolított csomópont-vagy virtuálisgép-példány nem kifogástalan állapotban jelenik meg Service Fabric Explorerban. Ennek a viselkedésnek a magyarázata: [Service Fabric Explorerban](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down#behaviors-you-may-observe-in-service-fabric-explorer)megfigyelhető viselkedések. A következőket teheti:
> * Hívja meg a [Remove-ServiceFabricNodeState parancsot](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) a megfelelő csomópont-névvel.
> * Telepítse az [Service Fabric autoscale Helper alkalmazást](https://github.com/Azure/service-fabric-autoscale-helper/) a fürtön. Ez az alkalmazás biztosítja, hogy a méretezett csomópontok törlődnek Service Fabric Explorerból.

## <a name="reliability-levels"></a>Megbízhatósági szintek

A [megbízhatósági szint](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) a Service Fabric fürterőforrás egyik tulajdonsága. Az egyes csomópont-típusok esetében nem konfigurálható másképpen. A fürt rendszerszolgáltatásainak replikációs tényezőjét vezérli, és a fürt erőforrás-szintjének beállítása. 

A megbízhatósági szint határozza meg, hogy az elsődleges csomópont típusa hány csomópontot tartalmazhat. A megbízhatósági szintek a következő értékeket vehetik igénybe:

* Platina Futtatja a rendszerszolgáltatásokat egy célként megadott replika-készlet hét és kilenc mag csomópontjának számával.
* Arany Futtatja a rendszerszolgáltatásokat egy célként megadott másodpéldány-készletben hét és hét mag csomópont között.
* Ezüst Futtatja a rendszerszolgáltatásokat egy célként megadott replika öt és öt magot tartalmazó csomópontjának számával.
* Bronz Futtatja a rendszerszolgáltatásokat a célként megadott replika három és három vetőmag-csomópontjának számával.

A minimálisan ajánlott megbízhatósági szint Silver.

A megbízhatósági szint a [Microsoft. ServiceFabric/Clusters erőforrás](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters)tulajdonságok részében van beállítva, a következőhöz hasonlóan:

```json
"properties":{
    "reliabilityLevel": "Silver"
}
```

## <a name="durability-levels"></a>Tartóssági szintek

> [!WARNING]
> A bronz tartóssággal futó csomópont-típusok _nem_kapnak jogosultságot. Az állapot nélküli munkaterheléseket befolyásoló infrastrukturális feladatok nem lesznek leállítva vagy késleltetve, ami hatással lehet a számítási feladatokra. 
>
> A bronz tartósságot csak olyan csomópont-típusok esetében használja, amelyek állapot nélküli munkaterheléseket futtatnak. Éles számítási feladatokhoz futtassa az Silver vagy a Higher parancsot az állapot konzisztenciájának biztosítása érdekében. Válassza ki a megfelelő megbízhatóságot a [kapacitás](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)megtervezése dokumentációjában található útmutatás alapján.

A tartóssági szintet két erőforrásban kell megadni. Az egyik a virtuálisgép-méretezési [csoport erőforrásának](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile)bővítmény-profilja:

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

A másik erőforrás a `nodeTypes` [Microsoft. ServiceFabric/Clusters erőforrás](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters)alatt található: 

```json
"nodeTypes": [
    {
        "name": "[variables('vmNodeType0Name')]",
        "durabilityLevel": "Bronze"
    }
]
```

## <a name="next-steps"></a>További lépések

* Hozzon létre egy fürtöt a virtuális gépeken vagy a Windows Servert futtató számítógépeken: [Service Fabric fürt létrehozása a Windows Server](service-fabric-cluster-creation-for-windows-server.md)rendszerhez.
* Fürt létrehozása virtuális gépeken vagy Linuxon futó számítógépeken: [Hozzon létre egy Linux](service-fabric-cluster-creation-via-portal.md)-fürtöt.
* További információ a [Service Fabric támogatási lehetőségeiről](service-fabric-support.md).

[Image1]: ./media/service-fabric-best-practices/generate-common-name-cert-portal.png
