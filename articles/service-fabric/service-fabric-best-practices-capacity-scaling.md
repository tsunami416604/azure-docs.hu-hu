---
title: Kapacitástervezés és -méretezés az Azure Service Fabric számára
description: A Service Fabric-fürtök és -alkalmazások tervezésére és méretezése ajánlott eljárásokra vonatkozó gyakorlati tanácsok.
author: peterpogorski
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: pepogors
ms.openlocfilehash: bf228e17ca24df9833f96f0c6fd3ef232cdf7ae6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258992"
---
# <a name="capacity-planning-and-scaling-for-azure-service-fabric"></a>Kapacitástervezés és -méretezés az Azure Service Fabric számára

Mielőtt bármilyen Azure Service Fabric-fürtöt hozna létre, vagy a fürtöt üzemeltető számítási erőforrásokat skálázna, fontos megtervezni a kapacitást. A kapacitás tervezésével kapcsolatos további információkért olvassa el [a Service Fabric fürtkapacitásának megtervezése](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)című témakört. A fürt méretezhetőségére vonatkozó további gyakorlati tanácsok a [Service Fabric méretezhetőségi szempontjaiban talál.](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric#scalability-considerations)

A csomóponttípus és a fürt jellemzői mellett a skálázási műveletek egy óránál hosszabb ideig is eltarthatnak egy éles környezetben. Ez a szempont a hozzáadni kívánt virtuális gépek számától függetlenül igaz.

## <a name="autoscaling"></a>Automatikus skálázás
Méretezési műveleteket kell végrehajtania az Azure Resource Manager-sablonokon keresztül, mert ez a legjobb gyakorlat az [erőforrás-konfigurációk kódként]( https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code)való kezelésére. 

A virtuálisgép-méretezési csoportok on-t automatikus skálázási készletei segítségével a verziózott Resource Manager-sablon tévesen határozza meg a példányok számát a virtuálisgép-méretezési csoportokhoz. A pontatlan definíció növeli annak kockázatát, hogy a jövőbeli központi telepítések nem kívánt skálázási műveleteket okoznak. Általában akkor kell automatikus skálázást használnia, ha:

* Az Erőforrás-kezelő sablonjainak megfelelő kapacitással történő üzembe helyezése nem támogatja a használati esetet.
     
   A manuális skálázás mellett konfigurálhatja [a folyamatos integrációs és kézbesítési folyamatot az Azure DevOps-szolgáltatásokban az Azure erőforráscsoport-telepítési projektek használatával.](https://docs.microsoft.com/azure/vs-azure-tools-resource-groups-ci-in-vsts) Ezt a folyamatot általában egy logikai alkalmazás váltja ki, amely az [Azure Monitor REST API-ból](https://docs.microsoft.com/azure/azure-monitor/platform/rest-api-walkthrough)lekérdezett virtuálisgép-teljesítménymutatókat használja. A folyamat hatékonyan automatikus skálázások alapján bármilyen metrikákat szeretne, miközben az Erőforrás-kezelő sablonok optimalizálása.
* Egyszerre csak egy virtuálisgép-méretezési csomópontot kell vízszintesen skáláznia.
   
   Ha egyszerre három vagy több csomóponton szeretne horizontálisan skálázható, egy [virtuálisgép-méretezési csoport hozzáadásával horizontálisfelkell bővítenie egy Service Fabric-fürtöt.](virtual-machine-scale-set-scale-node-type-scale-out.md) A legbiztonságosabb, ha a virtuálisgép-méretezési csoportokat vízszintesen, egyszerre egy csomóponton skálázható és skálázható.
* Silver megbízhatósággal vagy magasabb a Service Fabric-fürt, és ezüst tartósság vagy magasabb minden olyan skálán, ahol konfigurálja az automatikus skálázási szabályok.
  
   Az automatikus skálázási szabályok minimális kapacitásának öt virtuálisgép-példánynak vagy annál nagyobbnak kell lennie. Azt is meg kell egyeznie, vagy nagyobb, mint a megbízhatósági szint minimum az elsődleges csomópont típus.

> [!NOTE]
> A Service Fabric állapotalapú szolgáltatásfabric:/System/InfastructureService/<NODE_TYPE_NAME> fut minden csomópont típus, amely ezüst vagy nagyobb tartósságú. Ez az egyetlen olyan rendszerszolgáltatás, amely az Azure-ban a fürtcsomópont-típusok bármelyikén futtatva futtathatók.

## <a name="vertical-scaling-considerations"></a>Vertikális skálázási szempontok

Az Azure Service Fabric csomóponttípusfüggőleges [skálázása](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out) számos lépést és szempontot igényel. Példa:

* A fürtnek kifogástalannak kell lennie a méretezés előtt. Ellenkező esetben tovább destabilizálja a fürtöt.
* Ezüst tartóssági szint vagy nagyobb szükséges az összes Service Fabric fürtcsomópont-típusok, amelyek állapotalapú szolgáltatások gazdagép.

> [!NOTE]
> Az elsődleges csomópont típusa, amely állapotalapú Service Fabric rendszerszolgáltatások silver tartóssági szint vagy nagyobb kell lennie. Az Ezüst tartósságának engedélyezése után a fürtműveletek, például a frissítések, a csomópontok hozzáadása vagy eltávolítása, és így tovább, lassabbak lesznek, mivel a rendszer optimalizálja az adatbiztonságot a műveletek sebességén keresztül.

A virtuálisgép-méretezési csoport vertikális méretezése destruktív művelet. Ehelyett horizontálisan méretezze át a fürtöt egy új méretezési csoport hozzáadásával a kívánt termékváltozattal. Ezután telepítse át a szolgáltatásokat a kívánt termékváltozatba egy biztonságos függőleges skálázási művelet végrehajtásához. A virtuálisgép-méretezési készlet erőforrás termékváltozatának módosítása destruktív művelet, mert újraképzi a gazdagépeket, amely eltávolítja az összes helyileg megőrzött állapotot.

A fürt a Service [Fabric-csomópont tulajdonságait és elhelyezési korlátozásokat](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-cluster-description#node-properties-and-placement-constraints) használ annak eldöntéséhez, hogy hol kell üzemeltetni az alkalmazás szolgáltatásait. Az elsődleges csomóponttípus függőleges méretezésekor deklaráljuk `"nodeTypeRef"`az azonos tulajdonságértékeket a programhoz. Ezeket az értékeket a Service Fabric bővítmény a virtuális gép méretezési készletek. 

Az Erőforrás-kezelő sablon következő kódrészlete a deklarálni kívánt tulajdonságokat mutatja. Ugyanazt az értéket az újonnan kiépített méretezési készletek, amelyek méretezése, és ez csak ideiglenes állapotalapú szolgáltatásként a fürt támogatott.

```json
"settings": {
   "nodeTypeRef": ["[parameters('primaryNodetypeName')]"]
}
```

> [!NOTE]
> Ne hagyja, hogy a fürt több méretezési `nodeTypeRef` készlettel fusson, amelyek ugyanazt a tulajdonságértéket használják a sikeres vertikális skálázási művelet végrehajtásához szükségesnél hosszabb ideig.
>
> Mindig ellenőrizze a műveleteket tesztkörnyezetben, mielőtt megpróbálna módosítani az éles környezetet. Alapértelmezés szerint a Service Fabric fürtrendszer-szolgáltatások elhelyezési megkötéssel csak a cél elsődleges csomópont típus.

A csomópont tulajdonságainak és elhelyezési megkötéseinek deklarálva, végezze el a következő lépéseket egy virtuálisgép-példány egy időben. Ez lehetővé teszi, hogy a rendszerszolgáltatások (és az állapotalapú szolgáltatások) szabályosan lekell állítani a virtuálisgép-példány eltávolítása, mint az új replikák jönnek létre máshol.

1. A PowerShell-ből `Disable-ServiceFabricNode` `RemoveNode` futtassa szándékkal az eltávolítani kívánt csomópont letiltásához. Távolítsa el a legmagasabb számot tartalmazó csomóponttípust. Ha például hatcsomópontos fürttel rendelkezik, távolítsa el a "MyNodeType_5" virtuálisgép-példányt.
2. Futtassa, `Get-ServiceFabricNode` és győződjön meg arról, hogy a csomópont levan tiltva. Ha nem, várja meg, amíg a csomópont le van tiltva. Ez minden csomópont esetében eltarthat néhány órát. Ne folytassa, amíg a csomópont le van tiltva.
3. Csökkentse a virtuális gépek számát egy ilyen csomóponttípusban. A legmagasabb virtuálisgép-példány most antól el lesz távolítva.
4. Ismételje meg az 1–3. Lásd: [A Service Fabric fürtkapacitásának megtervezése](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) az ajánlott példányok listájáról.
5. Miután az összes virtuális gép eltűnt ("Le" néven jelenik meg), a háló:/System/InfrastructureService/[csomópont neve] hibaállapotot jelenít meg. Ezután frissítheti a fürterőforrást a csomóponttípus eltávolításához. Használhatja az ARM sablon központi telepítését, vagy szerkesztheti a fürterőforrást az [Azure erőforrás-kezelőn](https://resources.azure.com)keresztül. Ez elindítja a fürt frissítését, amely eltávolítja a hibás állapotú fabric:/System/InfrastructureService/[csomópont típus] szolgáltatást.
 6. Ezt követően tetszés szerint törölheti a VMScaleSet, továbbra is látni fogja a csomópontokat a Service Fabric Explorer nézetben mégis. Az utolsó lépés az lenne, `Remove-ServiceFabricNodeState` hogy tisztítsák meg őket a parancsot.

## <a name="horizontal-scaling"></a>Vízszintes méretezés

A vízszintes méretezést [manuálisan](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down) vagy [programozott módon](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-programmatic-scaling)is elvégezheti.

> [!NOTE]
> Ha ezüst vagy arany tartósságú csomóponttípust skáláz, a méretezés lassú lesz.

### <a name="scaling-out"></a>Méretezés

Egy Service Fabric-fürt horizontális felskálázása egy adott virtuálisgép-méretezési csoport példányszámának növelésével. A kapacitás növeléséhez programozott `AzureClient` módon skálázhatja a kívánt méretezési szint azonosítójának használatával.

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
```

Horizontális felskálázás manuálisan, frissítse a kapacitást a Termékváltozat tulajdonság a kívánt [virtuálisgép-méretezési készlet](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) erőforrás.

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

### <a name="scaling-in"></a>Méretezés a

A skálázás több figyelmet igényel, mint a horizontális felskálázás. Például:

* A Service Fabric rendszerszolgáltatásai a fürt elsődleges csomóponttípusán futnak. Soha ne állítsa le, vagy csökkentse az adott csomóponttípus példányainak számát, így kevesebb példánya van, mint amit a megbízhatósági szint indokol. 
* Egy állapotalapú szolgáltatás, szüksége van egy bizonyos számú csomópont, amely mindig a rendelkezésre állás fenntartásához és a szolgáltatás állapotának megőrzéséhez. Legalább a partíció vagy szolgáltatás cél replikakészletének számával megegyező számú csomópontra van szükség.

A manuális méretezéshez hajtsa végre az alábbi lépéseket:

1. A PowerShell-ből `Disable-ServiceFabricNode` `RemoveNode` futtassa szándékkal az eltávolítani kívánt csomópont letiltásához. Távolítsa el a legmagasabb számot tartalmazó csomóponttípust. Ha például hatcsomópontos fürttel rendelkezik, távolítsa el a "MyNodeType_5" virtuálisgép-példányt.
2. Futtassa, `Get-ServiceFabricNode` és győződjön meg arról, hogy a csomópont levan tiltva. Ha nem, várja meg, amíg a csomópont le van tiltva. Ez minden csomópont esetében eltarthat néhány órát. Ne folytassa, amíg a csomópont le van tiltva.
3. Csökkentse a virtuális gépek számát egy ilyen csomóponttípusban. A legmagasabb virtuálisgép-példány most antól el lesz távolítva.
4. Ismételje meg az 1-3. Ne csökkentse a példányok számát az elsődleges csomóponttípusokban, hogy kevesebb, mint amit a megbízhatósági szint indokol. Lásd: [A Service Fabric fürtkapacitásának megtervezése](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) az ajánlott példányok listájáról.

A manuális méretezéshez frissítse a kívánt [virtuálisgép-méretezési csoport](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) erőforrás termékváltozat-tulajdonságának kapacitását.

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

A csomópontot a leállításra, hogy programozott módon méretezhessen. Keresse meg az eltávolítandó csomópontot (a legmagasabb példánycsomópontot). Példa:

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

Kapcsolja ki és távolítsa el a `FabricClient` csomópontot az előző kódban használt példány (ebben`client` az esetben) és csomópontpéldány (ebben`instanceIdString` az esetben) használatával:

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
> Fürt leskálázásakor megjelenik az eltávolított csomópont/virtuálisgép-példány sérült állapotban a Service Fabric Explorer ben. A viselkedés magyarázatát a [Service Fabric Intézőben megfigyelhető viselkedések című témakörben olvassa el.](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down#behaviors-you-may-observe-in-service-fabric-explorer) A következőket teheti:
> * Hívja meg az [Remove-ServiceFabricNodeState parancsot](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) a megfelelő csomópontnévvel.
> * Telepítse a [Service Fabric automatikus skálázású segítő alkalmazást](https://github.com/Azure/service-fabric-autoscale-helper/) a fürtön. Ez az alkalmazás biztosítja, hogy a lekicsinyített csomópontok törlődnek a Service Fabric Explorer.

## <a name="reliability-levels"></a>Megbízhatósági szintek

A [megbízhatósági szint](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) a Service Fabric-fürt erőforrás egyik tulajdonsága. Az egyes csomóponttípusokhoz nem konfigurálható másképp. Ez szabályozza a fürt rendszerszolgáltatásainak replikációs tényezőjét, és a fürt erőforrás szintjén lévő beállítás. 

A megbízhatósági szint határozza meg az elsődleges csomóponttípus minimális számát. A megbízhatósági szint a következő értékeket veheti fel:

* Platina: A rendszerszolgáltatások futtatása a cél replika készlet száma hét és kilenc magcsomópontok.
* Arany: A rendszerszolgáltatások futtatása hét és hét magcsomópont célreplikakészletszámmal.
* Ezüst: A rendszerszolgáltatások öt és öt magcsomópontok célreplikakészlet-számmal fut.
* Bronz: A rendszerszolgáltatások három és három magcsomópontok célreplika készletszáma fut.

A minimálisajánlott megbízhatósági szint ezüst.

A megbízhatósági szint a [Microsoft.ServiceFabric/clusters erőforrás](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters)tulajdonságszakaszában van beállítva, így:

```json
"properties":{
    "reliabilityLevel": "Silver"
}
```

## <a name="durability-levels"></a>Tartóssági szintek

> [!WARNING]
> A Bronz tartóssággal futó csomóponttípusok _nem kapnak jogosultságokat._ Az állapotnélküli számítási feladatokat érintő infrastruktúra-feladatok nem lesznek leállítva vagy késve, ami hatással lehet a számítási feladatokra. 
>
> A Bronz tartósságcsak állapotnélküli számítási feladatokat futtató csomóponttípusokhoz használja. Éles számítási feladatok esetén futtassa a Silver vagy újabb futtatást az állapotkonzisztencia biztosítása érdekében. Válassza ki a megfelelő megbízhatóságot a [kapacitástervezési dokumentációban](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)található útmutatás alapján.

A tartóssági szintet két erőforrásban kell beállítani. Az egyik a [virtuálisgép-méretezési csoport erőforrásának](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile)bővítményprofilja:

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

A másik erőforrás `nodeTypes` a [Microsoft.ServiceFabric/clusters erőforrás](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters)alatt található: 

```json
"nodeTypes": [
    {
        "name": "[variables('vmNodeType0Name')]",
        "durabilityLevel": "Bronze"
    }
]
```

## <a name="next-steps"></a>További lépések

* Fürt létrehozása virtuális gépeken vagy Windows Server rendszert futtató számítógépeken: [Service Fabric fürt létrehozása Windows Server hez](service-fabric-cluster-creation-for-windows-server.md).
* Fürt létrehozása virtuális gépeken vagy Linuxot futtató számítógépeken: [Linux-fürt létrehozása](service-fabric-cluster-creation-via-portal.md).
* További információ a [Service Fabric támogatási lehetőségeiről.](service-fabric-support.md)

[Image1]: ./media/service-fabric-best-practices/generate-common-name-cert-portal.png
