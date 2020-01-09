---
title: Azure Service Fabric csomópont-típus vertikális felskálázása
description: Megtudhatja, hogyan méretezheti Service Fabric fürtöt egy virtuálisgép-méretezési csoport hozzáadásával.
ms.topic: article
ms.date: 02/13/2019
ms.openlocfilehash: 33d535cb093eeb95e0ce95bdd5722bfd21150a40
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75464224"
---
# <a name="scale-up-a-service-fabric-cluster-primary-node-type"></a>Service Fabric-fürt elsődleges csomópont-típusának vertikális felskálázása
Ez a cikk azt ismerteti, hogyan lehet a virtuális gépek erőforrásainak növelésével bővíteni egy Service Fabric-fürt elsődleges csomópontjának típusát. A Service Fabric-fürt olyan virtuális vagy fizikai gépek hálózathoz csatlakoztatott készlete, amelybe a rendszer üzembe helyezi és kezeli a szolgáltatásait. Egy fürt részét képező gépet vagy virtuális gépet csomópontnak nevezzük. A virtuálisgép-méretezési csoportok egy Azure-beli számítási erőforrás, amely készletként telepíti és felügyeli a virtuális gépek gyűjteményét. Az Azure-fürtben definiált összes csomópont-típus [külön méretezési csoportként van beállítva](service-fabric-cluster-nodetypes.md). Ezután mindegyik csomópont-típust külön lehet kezelni. Service Fabric-fürt létrehozása után függőlegesen méretezheti a fürt csomópontjának típusát (módosítsa a csomópontok erőforrásait), vagy frissítse a csomópont típusú virtuális gépek operációs rendszerét.  A fürtöt bármikor méretezheti, még akkor is, ha a munkaterhelések futnak a fürtön.  A fürt skálázása esetén az alkalmazások is automatikusan méretezhetők.

> [!WARNING]
> Ne kezdje el megváltoztatni az elsődleges NodeType VM SKU-t, ha a fürt állapota nem kifogástalan. Ha a fürt állapota nem megfelelő, akkor a rendszer csak a fürt állapotát fogja tovább kikényszeríteni, ha megpróbálja módosítani a virtuális gép SKU-jának változását.
>
> Azt javasoljuk, hogy ne változtassa meg a méretezési csoport/csomópont típusa virtuálisgép-SKU-jának használatát, kivéve, ha az [ezüst tartósságon vagy annál nagyobb mértékben](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster)fut. A VM SKU méretének módosítása egy adatpusztító helyi infrastruktúra-művelet. A módosítás késleltetése vagy monitorozása nélkül lehetséges, hogy a művelet adatvesztést okozhat az állapot-nyilvántartó szolgáltatások számára, vagy más, előre nem látható működési problémákat okozhat, még az állapot nélküli munkaterhelések esetében is. Ez azt jelenti, hogy az elsődleges csomópont típusa, amely állapot-nyilvántartó Service Fabric rendszerszolgáltatásokat futtat, vagy bármely olyan csomópont-típus, amely az állapot-nyilvántartó alkalmazás munkaterheléseit futtatja.
>


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="upgrade-the-size-and-operating-system-of-the-primary-node-type-vms"></a>Az elsődleges csomópont típusú virtuális gépek méretének és operációs rendszerének frissítése
Az alábbi folyamat a virtuális gépek méretének és operációs rendszerének frissítését írja le.  A frissítés után az elsődleges csomópont típusú virtuális gépek szabványos D4_V2 és a Windows Server 2016 Datacenter tárolókkal futnak.

> [!WARNING]
> Az eljárás üzemi fürtön való megkísérlése előtt javasoljuk, hogy tanulmányozza a minta sablonokat, és ellenőrizze a folyamatot egy tesztelési fürtön. A fürt egy időre sem érhető el. Párhuzamosan nem lehet módosítani több VMSS, amelyek ugyanabban a NodeType vannak deklarálva; külön üzembe helyezési műveleteket kell végrehajtania, hogy az egyes NodeType-VMSS módosításokat alkalmazzon.

1. Telepítse a kezdeti fürtöt két csomópont-típussal és két méretezési csoporttal (egy csomópont típusú méretezési csoport) a minta [sablon](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.json) és a [Paraméterek](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.parameters.json) fájljainak használatával.  Mindkét méretezési csoport mérete standard D2_V2, és a Windows Server 2012 R2 Datacenter fut.  Várjon, amíg a fürt befejezi az alapkonfiguráció frissítését.   
2. Opcionális – állapot-nyilvántartó minta üzembe helyezése a fürtön.
3. Miután eldöntötte, hogy frissítette az elsődleges csomópont típusú virtuális gépeket, adjon hozzá egy új méretezési csoportot az elsődleges csomópont-típushoz a minta [sablon](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.json) és a [Parameters](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.parameters.json) fájlok használatával, hogy az elsődleges csomópont típusa most két méretezési csoporttal rendelkezik.  A rendszerszolgáltatások és a felhasználói alkalmazások áttelepíthetők a két különböző méretezési csoportba tartozó virtuális gépek között.  Az új méretezési csoport virtuális gépei standard szintű D4_V2, és Windows Server 2016 Datacenter-t futtatnak tárolókkal.  Az új méretezési csoporttal új terheléselosztó és nyilvános IP-cím is hozzá lesz adva.  
    A sablonban található új méretezési csoport megkereséséhez keresse meg a *vmNodeType2Name* paraméter által megnevezett "Microsoft. számítási/virtualMachineScaleSets" erőforrást.  Az új méretezési csoport hozzá lesz adva az elsődleges csomópont-típushoz a Properties-> virtualMachineProfile-> extensionProfile-> Extensions-> Properties-> Settings-> nodeTypeRef-beállítás használatával.
4. Ellenőrizze a fürt állapotát, és ellenőrizze, hogy az összes csomópont kifogástalan állapotú-e.
5. Tiltsa le a csomópontokat az elsődleges csomópont típusának régi méretezési csoportjában a csomópont eltávolítására szolgáló szándékkal. Egyszerre letilthatja az összes műveletet, és a rendszer várólistára helyezi a műveleteket. Várjon, amíg a csomópontok le vannak tiltva, ami hosszabb időt is igénybe vehet.  Mivel a csomópont-típus régebbi csomópontjai le vannak tiltva, a rendszerszolgáltatások és a magok csomópontjai az elsődleges csomópont típusában lévő új méretezési csoport virtuális gépei felé lesznek áttelepítve.
6. Távolítsa el a régebbi méretezési készletet az elsődleges csomópont típusától.
7. Távolítsa el a régi méretezési csoporthoz társított terheléselosztó. A fürt nem érhető el, amíg az új nyilvános IP-cím és terheléselosztó konfigurálva van az új méretezési csoportra.  
8. Tárolja a régi elsődleges csomópont típusú méretezési csoporthoz társított nyilvános IP-cím DNS-beállításait egy változóban, és távolítsa el a nyilvános IP-címet.
9. Cserélje le az új elsődleges csomópont típusú méretezési csoporthoz társított nyilvános IP-cím DNS-beállításait a törölt nyilvános IP-cím DNS-beállításaival.  A fürt most már elérhető.
10. Távolítsa el a csomópontok csomópontjának állapotát a fürtből.  Ha a régi méretezési csoport tartóssági szintje ezüst vagy arany volt, ezt a lépést a rendszer automatikusan végrehajtja.
11. Ha egy előző lépésben telepítette az állapot-nyilvántartó alkalmazást, ellenőrizze, hogy az alkalmazás működőképes-e.

```powershell
# Variables.
$groupname = "sfupgradetestgroup"
$clusterloc="southcentralus"  
$subscriptionID="<your subscription ID>"

# sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId $subscriptionID 

# Create a new resource group for your deployment and give it a name and a location.
New-AzResourceGroup -Name $groupname -Location $clusterloc

# Deploy the two node type cluster.
New-AzResourceGroupDeployment -ResourceGroupName $groupname -TemplateParameterFile "C:\temp\cluster\Deploy-2NodeTypes-2ScaleSets.parameters.json" `
    -TemplateFile "C:\temp\cluster\Deploy-2NodeTypes-2ScaleSets.json" -Verbose

# Connect to the cluster and check the cluster health.
$ClusterName= "sfupgradetest.southcentralus.cloudapp.azure.com:19000"
$thumb="F361720F4BD5449F6F083DDE99DC51A86985B25B"

Connect-ServiceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My 

Get-ServiceFabricClusterHealth

# Deploy a new scale set into the primary node type.  Create a new load balancer and public IP address for the new scale set.
New-AzResourceGroupDeployment -ResourceGroupName $groupname -TemplateParameterFile "C:\temp\cluster\Deploy-2NodeTypes-3ScaleSets.parameters.json" `
    -TemplateFile "C:\temp\cluster\Deploy-2NodeTypes-3ScaleSets.json" -Verbose

# Check the cluster health again. All 15 nodes should be healthy.
Get-ServiceFabricClusterHealth

# Disable the nodes in the original scale set.
$nodeNames = @("_NTvm1_0","_NTvm1_1","_NTvm1_2","_NTvm1_3","_NTvm1_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames){
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}

Write-Host "Checking node status..."
foreach($name in $nodeNames){
 
    $state = Get-ServiceFabricNode -NodeName $name 

    $loopTimeout = 50

    do{
        Start-Sleep 5
        $loopTimeout -= 1
        $state = Get-ServiceFabricNode -NodeName $name
        Write-Host "$name state: " $state.NodeDeactivationInfo.Status
    }

    while (($state.NodeDeactivationInfo.Status -ne "Completed") -and ($loopTimeout -ne 0))
    

    if ($state.NodeStatus -ne [System.Fabric.Query.NodeStatus]::Disabled)
    {
        Write-Error "$name node deactivation failed with state" $state.NodeStatus
        exit
    }
}

# Remove the scale set
$scaleSetName="NTvm1"
Remove-AzVmss -ResourceGroupName $groupname -VMScaleSetName $scaleSetName -Force
Write-Host "Removed scale set $scaleSetName"

$lbname="LB-sfupgradetest-NTvm1"
$oldPublicIpName="PublicIP-LB-FE-0"
$newPublicIpName="PublicIP-LB-FE-2"

# Store DNS settings of public IP address related to old Primary NodeType into variable 
$oldprimaryPublicIP = Get-AzPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $groupname

$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel

$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn

# Remove Load Balancer related to old Primary NodeType. This will cause a brief period of downtime for the cluster
Remove-AzLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force

# Remove the old public IP
Remove-AzPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force

# Replace DNS settings of Public IP address related to new Primary Node Type with DNS settings of Public IP address related to old Primary Node Type
$PublicIP = Get-AzPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $groupname
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzPublicIpAddress -PublicIpAddress $PublicIP

# Check the cluster health
Get-ServiceFabricClusterHealth

# Remove node state for the deleted nodes.
foreach($name in $nodeNames){
    # Remove the node from the cluster
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}
```

## <a name="next-steps"></a>Következő lépések
* Megtudhatja, hogyan [adhat hozzá csomópont-típust fürthöz](virtual-machine-scale-set-scale-node-type-scale-out.md)
* Az [alkalmazások méretezhetőségének](service-fabric-concepts-scalability.md)megismerése.
* [Azure-fürt méretezése vagy](service-fabric-tutorial-scale-cluster.md)kibontása.
* Az [Azure-fürtöket programozott módon méretezheti](service-fabric-cluster-programmatic-scaling.md) a Fluent Azure számítási SDK használatával.
* [Önálló fürt méretezése vagy](service-fabric-cluster-windows-server-add-remove-nodes.md)kibontása.

