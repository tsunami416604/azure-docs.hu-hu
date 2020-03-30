---
title: Service Fabric-fürt méretezése be- és kihelyezéshez
description: A Service Fabric-fürtök et az igényeknek megfelelően méretezhet, hogy minden csomóponttípushoz/virtuálisgép-méretezési csoporthoz automatikus méretezési szabályokat állítson be. Csomópontok hozzáadása vagy eltávolítása szolgáltatásháló-fürthöz
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: 26ef13f38d525e4e493ad933bfb906dd36ed0070
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258732"
---
# <a name="scale-a-cluster-in-or-out"></a>Fürt horizontális fel- és leskálázása

> [!WARNING]
> A méretezés előtt olvassa el ezt a szakaszt

Számítási erőforrások méretezése az alkalmazás munkaterhelésének forrásához szándékos tervezést igényel, szinte mindig több mint egy órát vesz igénybe egy éles környezetben, és megköveteli a számítási feladatok és az üzleti környezet megértéséhez; valójában, ha még soha nem végzett ezt a tevékenységet, javasoljuk, hogy kezdje el olvasni és megérteni [a Service Fabric fürt kapacitástervezési szempontokat,](service-fabric-cluster-capacity.md)mielőtt folytatná a dokumentum hátralévő részét. Ez a javaslat a nem kívánt LiveSite-problémák elkerülése, és azt is javasoljuk, hogy sikeresen tesztelje a műveleteket úgy dönt, hogy végre egy nem éles környezetben. Bármikor [jelentheti a termelési problémákat, vagy fizetős támogatást kérhet az Azure-hoz.](service-fabric-support.md#report-production-issues-or-request-paid-support-for-azure) A megfelelő környezettel rendelkező műveletek végrehajtásához kiosztott mérnökök számára ez a cikk leírhatja a skálázási műveleteket, de el kell döntenie és meg kell értenie, hogy mely műveletek megfelelőek a használati esethez; például milyen erőforrásokat kell skálázni (CPU, Storage, Memória), milyen irányban kell skálázni (függőlegesen vagy vízszintesen), és milyen műveleteket kell végrehajtani (Erőforrássablon üzembe helyezése, Portál, PowerShell/CLI).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scale-a-service-fabric-cluster-in-or-out-using-auto-scale-rules-or-manually"></a>Service Fabric-fürt méretezése automatikus skálázási szabályok használatával vagy manuálisan
A virtuálisgép-méretezési csoportok egy Azure-számítási erőforrás, amely segítségével üzembe helyezheti és kezelheti a virtuális gépek gyűjteménye egy készlet. A Service Fabric-fürtben definiált minden csomóponttípus külön virtuálisgép-méretezési csoportként van beállítva. Ezután minden csomóponttípus egymástól függetlenül méretezhető, különböző portkészleteket nyithat meg, és különböző kapacitásmetrikákat is létrehozhat. Erről a [Service Fabric-csomóponttípusok](service-fabric-cluster-nodetypes.md) dokumentumban olvashat bővebben. Mivel a fürtben lévő Service Fabric-csomóponttípusok a háttérrendszerben lévő virtuálisgép-méretezési készletekből készülnek, minden csomóponttípushoz/virtuálisgép-méretezési csoporthoz automatikus méretezési szabályokat kell beállítania.

> [!NOTE]
> Az előfizetésnek elegendő maggal kell rendelkeznie a fürtöt felvevő új virtuális gépek hozzáadásához. Jelenleg nincs modellérvényesítés, így a telepítési idő sikertelen, ha a kvótakorlátok bármelyikét eléri. Egyetlen csomóponttípus sem haladhatja meg egyszerűen a 100 csomópontot VMSS-enként. Előfordulhat, hogy hozzá kell adnia a VMSS-eket a célzott skálázás eléréséhez, és az automatikus méretezés nem tud automatikusan hozzáadni a VMSS-eket. A VMSS helybeni hozzáadása egy élő fürthöz kihívást jelentő feladat, és ez általában azt eredményezi, hogy a felhasználók új fürtöket létesítenek a létrehozáskor kiépített megfelelő csomóponttípusokkal; [a fürt kapacitásának megfelelő megtervezése.](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) 
> 
> 

## <a name="choose-the-node-typevirtual-machine-scale-set-to-scale"></a>Válassza ki a csomópont típusát/a virtuálisgép méretezési készletét a méretezéshez
Jelenleg nem tudja megadni az automatikus méretezési szabályok at virtuálisgép-méretezési csoportok a portál on egy Service Fabric-fürt létrehozása, ezért használja az Azure PowerShell (1.0+) a csomóponttípusok, majd adja hozzá az automatikus méretezési szabályok at őket.

A fürtöt felállító virtuálisgép-méretezési csoport listájának lekért, futtassa a következő parancsmagokat:

```powershell
Get-AzResource -ResourceGroupName <RGname> -ResourceType Microsoft.Compute/VirtualMachineScaleSets

Get-AzVmss -ResourceGroupName <RGname> -VMScaleSetName <virtual machine scale set name>
```

## <a name="set-auto-scale-rules-for-the-node-typevirtual-machine-scale-set"></a>Automatikus méretezési szabályok beállítása a csomóponttípushoz/virtuálisgép-méretezési csoporthoz
Ha a fürt több csomóponttípussal rendelkezik, ismételje meg ezt minden olyan csomóponttípusnál/virtuálisgép-méretezési csoportnál, amelyet (be vagy ki) méretezni szeretne. Vegye figyelembe a szükséges csomópontok számát, mielőtt beállítja az automatikus skálázást. Az elsődleges csomóponttípushoz megadott minimálisan szükséges csomópontszámot a kiválasztott megbízhatósági szint határozza meg. További információ a [megbízhatósági szintekről.](service-fabric-cluster-capacity.md)

> [!NOTE]
> Az elsődleges csomóponttípus leskálázása a minimális számnál kisebbre instabillá teszi a fürtöt, vagy lecsökkenti azt. Ez adatvesztést okozhat az alkalmazások és a rendszerszolgáltatások számára.
> 
> 

Jelenleg az automatikus méretezési funkció nem hajtja a terhelések, amelyek az alkalmazások jelentési Service Fabric. Így ebben az időben az automatikus skálázás kapsz pusztán hajtja a teljesítmény számlálók, amelyek által kibocsátott virtuálisgép méretezési készlet példányok.  

Kövesse ezeket az utasításokat [az automatikus méretezés beállításához minden virtuálisgép-méretezési csoporthoz.](../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview.md)

> [!NOTE]
> Horizontális felskálázási forgatókönyv esetén, kivéve, ha a csomópont típusa arany vagy ezüst [tartóssági szinttel][durability] rendelkezik, meg kell hívnia az [Remove-ServiceFabricNodeState parancsmagát](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate) a megfelelő csomópontnévvel. A Bronz tartóssága érdekében nem ajánlott egyszerre egy csomópontnál több csomópontot csökkenteni.
> 
> 

## <a name="manually-add-vms-to-a-node-typevirtual-machine-scale-set"></a>Virtuális gépek manuális hozzáadása csomóponttípushoz/virtuálisgép-méretezési csoporthoz

A horizontális felskálázás során további virtuálisgép-példányokat ad a méretezési csoporthoz. Ezek a példányok lesznek a Service Fabric által használt csomópontok. A Service Fabric tudja, ha a méretezési csoport új példányokkal bővül (felskálázással), és automatikusan ennek megfelelően jár el. 

> [!NOTE]
> Virtuális gépek hozzáadása időt vesz igénybe, ezért ne várják, hogy a kiegészítések azonnali. Ezért tervezze meg, hogy a kapacitás hozzáadása jó előre, hogy több mint 10 percig, mielőtt a virtuális gép kapacitása elérhető a replikák/szolgáltatáspéldányok get placed.
> 

### <a name="add-vms-using-a-template"></a>Virtuális gépek hozzáadása sablon használatával
Kövesse a minta/utasítások a [rövid útmutató sablon gyűjteményben](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) a virtuális gépek száma az egyes csomóponttípusokban. 

### <a name="add-vms-using-powershell-or-cli-commands"></a>Virtuális gépek hozzáadása PowerShell- vagy CLI-parancsokkal
A következő kód név alapján megkeres egy méretezési csoportot, és 1-gyel növeli a **kapacitását**.

```powershell
$scaleset = Get-AzVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm
$scaleset.Sku.Capacity += 1

Update-AzVmss -ResourceGroupName $scaleset.ResourceGroupName -VMScaleSetName $scaleset.Name -VirtualMachineScaleSet $scaleset
```

Ez a kód 6-ra állítja a kapacitást.

```azurecli
# Get the name of the node with
az vmss list-instances -n nt1vm -g sfclustertutorialgroup --query [*].name

# Use the name to scale
az vmss scale -g sfclustertutorialgroup -n nt1vm --new-capacity 6
```

## <a name="manually-remove-vms-from-a-node-typevirtual-machine-scale-set"></a>Virtuális gépek manuális eltávolítása csomóponttípusból/virtuálisgép-méretezési csoportból
Ha egy csomópont típus, eltávolítja a virtuálisgép-példányok a méretezési csoportból. Ha a csomópont típusa bronz tartóssági szint, a Service Fabric nem tudja, mi történt, és jelenti, hogy egy csomópont eltűnt. A Service Fabric ekkor a fürt hibás állapotát jelzi. A rossz állapot elkerülése érdekében explicit módon el kell távolítania a csomópontot a fürtből, és el kell távolítania a csomópont állapotát.

A szolgáltatásháló rendszerszolgáltatásai a fürt elsődleges csomóponttípusán futnak. Az elsődleges csomóponttípus leskálázásakor soha ne csökkentse a példányok számát a [megbízhatósági szint](service-fabric-cluster-capacity.md) által igényeltnél kisebbre. 
 
Egy állapotalapú szolgáltatás, szüksége van egy bizonyos számú csomópont, hogy mindig akár a rendelkezésre állás fenntartása és a szolgáltatás állapotának megőrzése. Legalább a partíció/szolgáltatás cél replikakészletének megfelelő csomópontok számát kell megvásárolnia.

### <a name="remove-the-service-fabric-node"></a>A Service Fabric-csomópont eltávolítása

A csomópontállapot manuális eltávolításának lépései csak a *Bronz* tartóssági szinttel rendelkező csomóponttípusokra vonatkoznak.  *Az Ezüst* és *arany* tartóssági szint esetében ezeket a lépéseket a platform automatikusan elvégezheti. A tartóssággal kapcsolatos további információ: [Service Fabric-fürtök kapacitástervezése][durability].

Annak érdekében, hogy a fürt csomópontjainak megoszlása egyenletes legyen a frissítési és tartalék tartományokban, illetve ezáltal egyenletesen legyen a kihasználtságuk, elsőként a legutóbb létrehozott csomópontot kell eltávolítani. Más szóval a csomópontokat létrehozásuk fordított sorrendjében kell eltávolítani. A legutóbb létrehozott csomópont rendelkezik a legnagyobb `virtual machine scale set InstanceId` tulajdonságértékkel. Az alábbi kódpéldák a legutóbb létrehozott csomópontot adják vissza.

```powershell
Get-ServiceFabricNode | Sort-Object NodeInstanceId -Descending | Select-Object -First 1
```

```shell
sfctl node list --query "sort_by(items[*], &name)[-1]"
```

A Service Fabric-fürtnek értesülnie kell róla, hogy ez a csomópont el lesz távolítva. Három lépést kell végrehajtania:

1. Tiltsa le a csomópontot, hogy ezentúl ne replikálja az adatokat.  
PowerShell: `Disable-ServiceFabricNode`  
sfctl: `sfctl node disable`

2. Állítsa le a csomópontot, hogy a Service Fabric-futtatókörnyezet szabályszerűen álljon le, és az alkalmazás kapjon egy megszakítási kérelmet.  
PowerShell: `Start-ServiceFabricNodeTransition -Stop`  
sfctl: `sfctl node transition --node-transition-type Stop`

2. Távolítsa el a csomópontot a fürtről.  
PowerShell: `Remove-ServiceFabricNodeState`  
sfctl: `sfctl node remove-state`

A fenti három lépés alkalmazása után a csomópont eltávolítható a méretezési csoportból. A [Bronzon][durability] kívül bármely más tartóssági szint használata esetén a rendszer automatikusan végrehajtja ezeket a lépéseket a méretezési csoport adott példányának eltávolításakor.

A következő kódrészlet lekérdezi az utolsó létrehozott csomópontot, majd letiltja, leállítja, és eltávolítja a fürtről.

```powershell
#### After you've connected.....
# Get the node that was created last
$node = Get-ServiceFabricNode | Sort-Object { $_.NodeName.Substring($_.NodeName.LastIndexOf('_') + 1) } -Descending | Select-Object -First 1

# Node details for the disable/stop process
$nodename = $node.NodeName
$nodeid = $node.NodeInstanceId

$loopTimeout = 10

# Run disable logic
Disable-ServiceFabricNode -NodeName $nodename -Intent RemoveNode -TimeoutSec 300 -Force

$state = Get-ServiceFabricNode | Where-Object NodeName -eq $nodename | Select-Object -ExpandProperty NodeStatus

while (($state -ne [System.Fabric.Query.NodeStatus]::Disabled) -and ($loopTimeout -ne 0))
{
    Start-Sleep 5
    $loopTimeout -= 1
    $state = Get-ServiceFabricNode | Where-Object NodeName -eq $nodename | Select-Object -ExpandProperty NodeStatus
    Write-Host "Checking state... $state found"
}

# Exit if the node was unable to be disabled
if ($state -ne [System.Fabric.Query.NodeStatus]::Disabled)
{
    Write-Error "Disable failed with state $state"
}
else
{
    # Stop node
    $stopid = New-Guid
    Start-ServiceFabricNodeTransition -Stop -OperationId $stopid -NodeName $nodename -NodeInstanceId $nodeid -StopDurationInSeconds 300

    $state = (Get-ServiceFabricNodeTransitionProgress -OperationId $stopid).State
    $loopTimeout = 10

    # Watch the transaction
    while (($state -eq [System.Fabric.TestCommandProgressState]::Running) -and ($loopTimeout -ne 0))
    {
        Start-Sleep 5
        $state = (Get-ServiceFabricNodeTransitionProgress -OperationId $stopid).State
        Write-Host "Checking state... $state found"
    }

    if ($state -ne [System.Fabric.TestCommandProgressState]::Completed)
    {
        Write-Error "Stop transaction failed with $state"
    }
    else
    {
        # Remove the node from the cluster
        Remove-ServiceFabricNodeState -NodeName $nodename -TimeoutSec 300 -Force
    }
}
```

Az alábbi **sfctl** kódban a következő parancs kéri le a legutóbb létrehozott csomópont **node-name** értékét: `sfctl node list --query "sort_by(items[*], &name)[-1].name"`

```shell
# Inform the node that it is going to be removed
sfctl node disable --node-name _nt1vm_5 --deactivation-intent 4 -t 300

# Stop the node using a random guid as our operation id
sfctl node transition --node-instance-id 131541348482680775 --node-name _nt1vm_5 --node-transition-type Stop --operation-id c17bb4c5-9f6c-4eef-950f-3d03e1fef6fc --stop-duration-in-seconds 14400 -t 300

# Remove the node from the cluster
sfctl node remove-state --node-name _nt1vm_5
```

> [!TIP]
> A következő **sfctl**-lekérdezésekkel ellenőrizheti az egyes lépések állapotát
>
> **Inaktiválási állapot ellenőrzése**
> `sfctl node list --query "sort_by(items[*], &name)[-1].nodeDeactivationInfo"`
>
> **Leállítás állapotának ellenőrzése**
> `sfctl node list --query "sort_by(items[*], &name)[-1].isStopped"`
>

### <a name="scale-in-the-scale-set"></a>A méretezési csoport horizontális leskálázása

Most, hogy a Service Fabric-csomópont el lett távolítva a fürtből, a virtuálisgép-méretezési csoport leskálázható. Az alábbi példa 1-gyel csökkenti a méretezési csoport kapacitását.

```powershell
$scaleset = Get-AzVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm
$scaleset.Sku.Capacity -= 1

Update-AzVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm -VirtualMachineScaleSet $scaleset
```

Ez a kód a kapacitást 5-re állítja.

```azurecli
# Get the name of the node with
az vmss list-instances -n nt1vm -g sfclustertutorialgroup --query [*].name

# Use the name to scale
az vmss scale -g sfclustertutorialgroup -n nt1vm --new-capacity 5
```

## <a name="behaviors-you-may-observe-in-service-fabric-explorer"></a>A Service Fabric Explorer ben megfigyelt viselkedések
Fürt méretezésekénél a Service Fabric Explorer a fürt részét használó csomópontok (virtuálisgép-méretezési csoport példányai) számát fogja tükrözni.  Azonban, ha egy fürtet lefelé skáláz, látni fogja az eltávolított csomópont/virtuálisgép-példány sérült állapotban jelenik meg, kivéve, ha meghívja [Remove-ServiceFabricNodeState cmd](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate) a megfelelő csomópont nevét.   

Itt a magyarázat erre a viselkedésre.

A Service Fabric Explorerben felsorolt csomópontok tükrözik, amit a Service Fabric rendszerszolgáltatások (FM kifejezetten) tud a fürt által/birtokolt csomópontok számát. A virtuális gép méretezése lefelé, a virtuális gép törlődött, de az FM-rendszerszolgáltatás továbbra is úgy gondolja, hogy a csomópont (amely le van képezve a virtuális gép, amely törölt) visszatér. Így a Service Fabric Explorer továbbra is megjeleníti, hogy a csomópont (bár az állapot lehet hiba vagy ismeretlen).

Annak érdekében, hogy egy csomópont eltávolítása a virtuális gép eltávolításakor, két lehetőség közül választhat:

1. Válassza ki az arany vagy ezüst tartóssági szintjét a fürt csomóponttípusaihoz, amely biztosítja az infrastruktúra integrációját. Amely ezután automatikusan eltávolítja a csomópontokat a rendszerszolgáltatások (FM) állapotából, amikor leskálázódik.
A [tartóssági szintek részleteiitt tájékatok.](service-fabric-cluster-capacity.md)

2. Miután a virtuálisgép-példány le kicsinyített, meg kell hívnia az [Remove-ServiceFabricNodeState parancsmamot.](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate)

> [!NOTE]
> A Service Fabric-fürtök nek bizonyos számú csomópontot kell mindig felkadniuk a rendelkezésre állás fenntartása és az állapot megőrzése érdekében – ezt nevezik "kvórum fenntartása". Ezért általában nem biztonságos leállítani a fürt összes gépét, kivéve, ha először teljes biztonsági másolatot végzett [az állapotról.](service-fabric-reliable-services-backup-restore.md)
> 
> 

## <a name="next-steps"></a>További lépések
Az alábbiakban megismerést, valamint a fürtkapacitás tervezését, a fürt frissítését és a particionálási szolgáltatásokat is megtudhatja:

* [A fürtkapacitás megtervezése](service-fabric-cluster-capacity.md)
* [Fürtfrissítések](service-fabric-cluster-upgrade.md)
* [Állapotalapú szolgáltatások particionálása a maximális méretezéshez](service-fabric-concepts-partitioning.md)

<!--Image references-->
[BrowseServiceFabricClusterResource]: ./media/service-fabric-cluster-scale-up-down/BrowseServiceFabricClusterResource.png
[ClusterResources]: ./media/service-fabric-cluster-scale-up-down/ClusterResources.png

[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
