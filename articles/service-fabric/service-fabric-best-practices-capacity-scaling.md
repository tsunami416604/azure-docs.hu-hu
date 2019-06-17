---
title: Kapacitástervezés és skálázás az Azure Service Fabrichez |} A Microsoft Docs
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
ms.date: 04/25/2019
ms.author: pepogors
ms.openlocfilehash: 58c50eac60f1a8a47aac9a88125bc3e0132ec3db
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67059159"
---
# <a name="capacity-planning-and-scaling-for-azure-service-fabric"></a>Kapacitástervezés és skálázás az Azure Service Fabrichez

Mielőtt bármely Azure Service Fabric-fürtöt hoz létre, vagy méretezni a számítási erőforrásokat, amelyek a fürt, fontos a kapacitástervezés. A kapacitás tervezésével kapcsolatos további információkért lásd: [tervezése a Service Fabric-fürt kapacitása](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity). További ajánlott eljárásokat fürtök méretezhetősége, olvassa [Service Fabric méretezési szempontok](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric#scalability-considerations).

A mérlegeli csomópont típusa és a fürt jellemzőit, mellett számíthat a méretezési műveletek, mint egy óra, éles környezet esetében hosszabb időt vesz igénybe. A figyelmet a adja hozzá virtuális gépek számától függetlenül.

## <a name="autoscaling"></a>Automatikus skálázás
Kezelje az ajánlott eljárás, mert végre kell hajtania keresztül az Azure Resource Manager-sablonok, a méretezési műveletek [erőforrás-konfigurációk kódként]( https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code). 

A virtual machine scale sets – automatikus skálázás használatával fogja elérhetővé tenni a rendszerverzióval ellátott Resource Manager-sablon forrásprogramja határozza meg a virtuálisgép-méretezési csoportokhoz tartozó példányok számát. Pontatlan definíció növeli annak kockázatát, hogy később üzembe helyezések okozhat-e a nem kívánt méretezési műveletek. Általánosságban elmondható Ha az automatikus skálázás kell használnia:

* A Resource Manager-sablonok üzembe helyezése a deklarált megfelelő kapacitással nem támogatja a használati eset.
     
   Manuális skálázás mellett beállíthatja egy [folyamatos integrációt és teljesítést folyamatot az Azure DevOps-szolgáltatásokkal az Azure erőforráscsoport-telepítési projekt](https://docs.microsoft.com/azure/vs-azure-tools-resource-groups-ci-in-vsts). Ez a folyamat általában váltja ki egy logikai alkalmazást, amely a virtuális gép teljesítmény-mérőszámok onnan lekérdezett használja a [Azure Monitor REST API](https://docs.microsoft.com/azure/azure-monitor/platform/rest-api-walkthrough). A folyamat gyakorlatilag automatikus skálázást alkalmat alapú bármilyen metrikákra vonatkozóan szeretne, miközben optimalizálja a Resource Manager-sablonok.
* Horizontális skálázása egyszerre csak egy virtuális gép méretezési készlet csomópont kell.
   
   A horizontális felskálázáshoz három vagy több csomópontja egyidejűleg kell [egy virtuálisgép-méretezési csoportot adja hozzá a Service Fabric-fürt horizontális](virtual-machine-scale-set-scale-node-type-scale-out.md). Méretezése a legbiztonságosabb, és virtuálisgép-méretezési csoport horizontális állítja vízszintesen, egyszerre egy csomóponton.
* Silver szintű megbízhatóság rendelkezik, vagy magasabb, a Service Fabric-fürtöt, és a Silver szintű tartósságot, vagy bármilyen méret esetén az automatikus skálázási szabályok konfigurálására szolgáló újabb.
  
   Az automatikus skálázási szabályok kapacitásérték-minimumot öt virtuálisgép-példányok nagyobbnak vagy azzal egyenlőnek kell lennie. A megbízhatósági szint legalább az elsődleges csomóponttípus nagyobbnak vagy azzal egyenlőnek kell lennie.

> [!NOTE]
> A Service Fabric állapotalapú service fabric: / rendszer/InfastructureService/< NODE_TYPE_NAME > futtat minden Silver és magasabb szintű tartósságot rendelkező csomópont típusa. Legyen az egyetlen rendszer szolgáltatás futtatásához az Azure-ban a fürtök csomóponttípusok valamelyik támogatott.

## <a name="vertical-scaling-considerations"></a>Függőleges méretezési szempontok

[Vertikális skálázás](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out) az Azure Service Fabric-csomóponttípus szükséges lépéseit és szempontjait számos. Példa:

* A fürt kifogástalan állapotban kell lennie a skálázás előtt. Ellenkező esetben meg kell történő leállítása instabillá a fürt további.
* Silver szintű tartóssági szint vagy nagyobb szükség az összes Service Fabric fürtcsomópont-típusokat, amelyek az állapotalapú szolgáltatások üzemeltetésére.

> [!NOTE]
> Az elsődleges csomóponttípushoz, amelyen az állapotalapú Service Fabric-rendszerszolgáltatások kell lennie a Silver szintű tartóssági szint vagy nagyobb. Miután engedélyezte a Silver szintű tartósságot, például frissítések, a fürt működését hozzáadása vagy eltávolítása, csomópontok, és így tovább lassabb lesz, mert a rendszer az adatok biztonsága optimalizálja a műveletek sebessége keresztül.

Vertikális skálázás virtuálisgép-méretezési csoportot egy destruktív művelet. Ehelyett horizontális skálázása a fürt egy új méretezési csoportot ezzel a kívánt Termékváltozat hozzáadásával. A kívánt Termékváltozat egy biztonságos függőleges méretezési művelet befejeződik, majd át a szolgáltatásokat. A virtuális gép méretezési csoport erőforrás Termékváltozat módosítása azért egy destruktív művelet, a gazdagépek, mely eltávolítja az összes helyi megőrzött állapot feldolgozóméret.

A fürt használja a Service Fabric [csomópont-tulajdonságok és elhelyezési korlátozások](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-cluster-description#node-properties-and-placement-constraints) arról dönteni, hogy az alkalmazás szolgáltatásokat. Amikor, még függőleges skálázás az elsődleges csomóponttípushoz, deklarálja azonos tulajdonság értékei `"nodeTypeRef"`. A virtual machine scale sets, ezeket az értékeket is található a Service Fabric-bővítménnyel. 

Az alábbi kódrészlet egy Resource Manager-sablon fogja deklarálja tulajdonságait mutatja. Rendelkezik ugyanazt az értéket az újonnan létrehozott méretezési csoportok, amelyek a méretezést használ, és csak a fürt ideiglenes állapot-nyilvántartó szolgáltatásként is támogatott.

```json
"settings": {
   "nodeTypeRef": ["[parameters('primaryNodetypeName')]"]
}
```

> [!NOTE]
> Ne hagyja a fürtöt, amely ugyanaz, mint több méretezési csoportokkal `nodeTypeRef` tulajdonság értéke hosszabb, mint egy sikeres függőleges skálázási műveletek végrehajtásához szükséges.
>
> A tesztelési környezetek operations mindig ellenőrizze, mielőtt megkísérli a módosításokat az éles környezetbe. Alapértelmezés szerint a Service Fabric-fürt rendszerszolgáltatások rendelkeznek egy elhelyezési korlátozás csak a cél elsődleges csomóponttípus.

A csomópont tulajdonságait és a bejelentett elhelyezési korlátozások hajtsa végre a következő lépéseket egy Virtuálisgép-példány egyszerre. Ez lehetővé teszi a rendszerszolgáltatások (és az állapotalapú szolgáltatások) lehet szabályosan leállítani a Virtuálisgép-példány új replika létrehozásakor máshol szeretne eltávolítani.

1. A PowerShellben futtassa a `Disable-ServiceFabricNode` szándékával `RemoveNode` letiltása a csomópont eltávolítása fog. Távolítsa el, amely rendelkezik a legtöbb typ uzlu. Például ha egy hat csomópontból álló fürtöt, távolítsa el a "MyNodeType_5" virtuálisgép-példány.
2. Futtatás `Get-ServiceFabricNode` , győződjön meg arról, hogy a csomópont átváltott le van tiltva. Ha nem, akkor várjon, amíg a csomópont le van tiltva. Ez előfordulhat, hogy az egyes csomópontok néhány órát is igénybe vehet. Nem folytatható, amíg a csomópont átváltott le van tiltva.
3. Egy adott csomópont típusa a virtuális gépek számának csökkentéséhez. A legmagasabb szintű Virtuálisgép-példány már törlődni fog.
4. Ismételje meg az 1 – 3 igény szerint, de az elsődleges csomóponttípusok kevesebb, mint a megbízhatósági szint kódhiba a példányok száma soha nem méretezhető. Lásd: [tervezése a Service Fabric-fürt kapacitása](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) ajánlott példányok listáját.

### <a name="example-scenario"></a>Példaforgatókönyv
A függőleges skálázási művelet végrehajtása egy támogatott forgatókönyv: a Service Fabric-fürt és az alkalmazás nem felügyelt lemezt az áttelepítendő alkalmazás állásidő nélkül felügyelt lemezekre. 

Üzembe helyezése egy új felügyelt lemezekkel rendelkező virtuális gép méretezési csoportot, és hajtson végre alkalmazásfrissítést a kiosztott kapacitást célzó elhelyezési korlátozások. A Service Fabric-fürt majd ütemezheti a számítási feladatok üzembe helyezett fürtöt csomópont kapacitás, amely jelennek meg a frissítési tartomány alkalmazás üzemkimaradás nélkül. 

Háttérkészlet-végpontjai az [Azure Load Balancer alapszintű Termékváltozatát](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview#skus) egy egyetlen rendelkezésre állási csoportban, vagy egy virtuálisgép-méretezési csoportban lévő virtuális gépek lehetnek. Ez azt jelenti, hogy egy alapszintű Termékváltozatú terheléselosztó nem használható, ha systems Service Fabric-alkalmazásokat a méretezési csoportok, anélkül, hogy ez a Service Fabric-fürt felügyeleti végpontja az ideiglenes inaccessibility között helyezi át. Ez igaz, annak ellenére, hogy a fürt és az alkalmazás továbbra is futnak.

Egy virtuális IP-címnek (VIP) tárolóhelye közötti csere alapszintű Termékváltozatú terheléselosztó és a Standard Termékváltozatú terheléselosztó erőforrásainak végrehajtása során a felhasználók gyakran Standard Termékváltozatú terheléselosztó üzembe helyezése. Ez a módszer minden jövőbeli inaccessibility körülbelül 30 másodperc szükséges virtuális IP-CÍMEK felcserélése korlátozza.

## <a name="horizontal-scaling"></a>Vízszintes méretezés

Végezhet a horizontális skálázást vagy [manuálisan](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down) vagy [programozott módon](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-programmatic-scaling).

> [!NOTE]
> Ha egy csomópont típusa, amely rendelkezik a Silver vagy Gold tartóssági méretezéssel lassú méretezés lesz.

### <a name="scaling-out"></a>Méretezés

Service Fabric-fürt méretezése növelje a példányszámot adott virtuális gép méretezési csoport esetében. Ki lehet terjeszteni programozott módon használatával `AzureClient` és a kívánt méretezési csoport kapacitásának növelése azonosítója.

```c#
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
```

A horizontális felskálázáshoz manuálisan módosítsa a kapacitást a kívánt SKU tulajdonságát [virtuálisgép-méretezési csoport](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) erőforrás.

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
* Az állapotalapú szolgáltatások szüksége lesz egy bizonyos számú csomópontot, amely mindig naprakész rendelkezésre állását és a szolgáltatás állapotának megőrzéséhez. Minimális szüksége lesz a csomópontok számát a partíció vagy a szolgáltatás a cél replika set száma egyenlő.

A manuális skálázása kövesse az alábbi lépéseket:

1. A PowerShellben futtassa a `Disable-ServiceFabricNode` szándékával `RemoveNode` letiltása a csomópont eltávolítása fog. Távolítsa el, amely rendelkezik a legtöbb typ uzlu. Például ha egy hat csomópontból álló fürtöt, távolítsa el a "MyNodeType_5" virtuálisgép-példány.
2. Futtatás `Get-ServiceFabricNode` , győződjön meg arról, hogy a csomópont átváltott le van tiltva. Ha nem, akkor várjon, amíg a csomópont le van tiltva. Ez előfordulhat, hogy az egyes csomópontok néhány órát is igénybe vehet. Nem folytatható, amíg a csomópont átváltott le van tiltva.
3. Egy adott csomópont típusa a virtuális gépek számának csökkentéséhez. A legmagasabb szintű Virtuálisgép-példány már törlődni fog.
4. Ismételje meg az 1-3, amíg a kapacitást üzembe helyezésekor. Az elsődleges csomóponttípusok kisebb, mint a megbízhatósági szint indokol, a példányok nem méretezhető. Lásd: [tervezése a Service Fabric-fürt kapacitása](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) ajánlott példányok listáját.

Manuális méretezése a, módosítsa a kapacitást a kívánt SKU tulajdonságát [virtuálisgép-méretezési csoport](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) erőforrás.

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

A csomópont a Leállítás méretezése a programozott módon elő kell készítenie. Keresse meg a csomópontot, eltávolítja (a legmagasabb szintű példány csomópont). Példa:

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

Inaktiválás és eltávolíthatja a csomópontot azonos használatával `FabricClient` példány (`client` ebben az esetben) és a csomópont-példány (`instanceIdString` ebben az esetben) az előző kód a használt:

```c#
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
> Egy fürt leskálázás, amikor megjelenik a eltávolított csomópontot vagy Virtuálisgép-példány a Service Fabric Explorerben a nem megfelelő állapot jelenik meg. Ezt a viselkedést egy ismertetése: [jelenhet meg a Service Fabric Explorerben viselkedések](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down#behaviors-you-may-observe-in-service-fabric-explorer). A következőket teheti:
> * Hívja a [Remove-ServiceFabricNodeState parancs](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) csomópontok megfelelő névvel.
> * Üzembe helyezése a [Service Fabric automatikus méretezési segédlet az alkalmazás](https://github.com/Azure/service-fabric-autoscale-helper/) a fürtön. Ez az alkalmazás biztosítja, hogy a horizontálisan le-csomópontok törlődik a Service Fabric Explorerből.

## <a name="reliability-levels"></a>Megbízhatósági szintek

A [megbízhatósági szint](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) a Service Fabric-fürt erőforrás tulajdonsága. Nem konfigurálható eltérő az egyes csomóponttípusok. Azt szabályozza, a replikációs tényező a rendszerszolgáltatások a fürt számára, és a egy beállítás a fürt resource szintjén. 

A megbízhatósági szint határozza meg, hogy az adott elsődleges csomóponttípus csomópontok minimális száma. A megbízhatósági szint hajthatja végre a következő értékeket:

* Platinum: Futtatja a helyrendszeri szolgáltatások hét és kilenc magcsomópontok cél replika set számával együtt.
* Gold: Futtatja a helyrendszeri szolgáltatások hét és hét magcsomópontok cél replika set számával együtt.
* Ezüst: Futtatja a helyrendszeri szolgáltatások öt és az öt magcsomópontok cél replika set számával együtt.
* Bronz: A rendszer szolgáltatást futtat három és három magcsomópontok cél replika set számával együtt.

A minimális ajánlott megbízhatósági szint Silver.

A megbízhatósági szint van beállítva, a Tulajdonságok szakaszában a [Microsoft.ServiceFabric/clusters erőforrás](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters), ehhez hasonló:

```json
"properties":{
    "reliabilityLevel": "Silver"
}
```

## <a name="durability-levels"></a>Tartóssági szint

> [!WARNING]
> A csomóponttípusok a bronz tartóssági futó beszerzése _jogosultság nélküli_. Infrastruktúra-feladatok, amelyek befolyásolják az állapot nélküli számítási feladatokat fogja nem leállítható vagy késleltetett, amely hatással lehet a számítási feladatokat. 
>
> Bronz tartóssági csak állapot nélküli számítási feladatokat futtató csomóponttípusok használja. A termelési számítási feladatokhoz futtassa a Silver és magasabb szintű állapot konzisztencia biztosításához. Válassza ki a megfelelő megbízhatóságát, az útmutató a [kapacitástervezési dokumentációjában](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity).

A tartóssági szint kell állítani a két erőforrás. A bővítmény profilja a [virtuálisgép-méretezési csoport erőforrás](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile):

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

A többi erőforrás alatt áll `nodeTypes` a a [Microsoft.ServiceFabric/clusters erőforrás](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters): 

```json
"nodeTypes": [
    {
        "name": "[variables('vmNodeType0Name')]",
        "durabilityLevel": "Bronze"
    }
]
```

## <a name="next-steps"></a>További lépések

* Fürt létrehozása a virtuális gépek vagy a Windows Server rendszert futtató számítógépeken: [A Service Fabric-fürt létrehozása a Windows Server](service-fabric-cluster-creation-for-windows-server.md).
* Fürt létrehozása a virtuális gépek vagy a Linux operációs rendszert futtató számítógépeken: [Linux-fürt létrehozása](service-fabric-cluster-creation-via-portal.md).
* Ismerje meg [Service Fabric támogatási lehetőségeinek](service-fabric-support.md).

[Image1]: ./media/service-fabric-best-practices/generate-common-name-cert-portal.png
