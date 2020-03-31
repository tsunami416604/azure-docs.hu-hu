---
title: Azure Service Fabric-csomóponttípus ának felskálázása
description: Ismerje meg, hogyan méretezhet egy Service Fabric-fürtet egy virtuálisgép-méretezési csoport hozzáadásával.
ms.topic: article
ms.date: 02/13/2019
ms.openlocfilehash: 33d535cb093eeb95e0ce95bdd5722bfd21150a40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75464224"
---
# <a name="scale-up-a-service-fabric-cluster-primary-node-type"></a>Service Fabric-fürt elsődleges csomóponttípusának vertikális felskálázása
Ez a cikk ismerteti, hogyan skálázhatja a Service Fabric fürt elsődleges csomóponttípus a virtuális gép erőforrásainak növelésével. A Service Fabric-fürt olyan virtuális vagy fizikai gépek hálózathoz csatlakoztatott készlete, amelybe a mikroszolgáltatások üzembe helyezése és kezelése történik. Egy gép vagy virtuális gép, amely egy fürt része, nevezzük csomópont. A virtuálisgép-méretezési csoportok egy Azure-számítási erőforrás, amely et a virtuális gépek gyűjteményének készletként történő üzembe helyezéséhez és kezeléséhez használja. Az Azure-fürtben definiált minden csomóponttípus [külön méretezési csoportként van beállítva.](service-fabric-cluster-nodetypes.md) Ezután minden csomóponttípus külön-külön kezelhető. A Service Fabric-fürt létrehozása után függőlegesen skálázhatja a fürtcsomópont típusát (módosíthatja a csomópontok erőforrásait), vagy frissítheti a csomópont típusú virtuális gépek operációs rendszerét.  A fürt bármikor méretezhető, még akkor is, ha a számítási feladatok futnak a fürtön.  A fürt méretezése, az alkalmazások automatikusan skálázható is.

> [!WARNING]
> Ne kezdje el módosítani az elsődleges csomóponttípusú VM Termékváltozatokat, ha a fürt állapota nem kifogástalan. Ha a fürt állapota nem megfelelő állapotú, csak destabilizálja a fürt tovább, ha megpróbálja módosítani a virtuális gép Termékváltozat.
>
> Azt javasoljuk, hogy ne módosítsa a virtuális gép termékváltozatának méretezési csoport/csomópont típusú, kivéve, ha [fut silver tartósság vagy nagyobb.](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) A virtuális gép termékváltozatának mérete egy adatromboló helybeni infrastruktúra-művelet. Anélkül, hogy a változás késleltethető vagy figyelhető lenne, lehetséges, hogy a művelet adatvesztést okozhat az állapotalapú szolgáltatások számára, vagy más előre nem látható működési problémákat okozhat, még állapotnélküli munkaterhelések esetén is. Ez azt jelenti, hogy az elsődleges csomópont típusa, amely állapotalapú szolgáltatásháló-rendszerszolgáltatások, vagy bármely csomópont típus, amely az állapotalapú alkalmazás munkabetölti.
>


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="upgrade-the-size-and-operating-system-of-the-primary-node-type-vms"></a>Az elsődleges csomóponttípusú virtuális gépek méretének és operációs rendszerének frissítése
Itt van a folyamat a virtuális gép méretének és az operációs rendszer az elsődleges csomópont típusú virtuális gépek.  A frissítés után az elsődleges csomópont típusú virtuális gépek mérete Standard D4_V2 és a Windows Server 2016 Datacenter tárolókkal.

> [!WARNING]
> Mielőtt ezt az eljárást egy éles fürtön kísérelte volna meg, javasoljuk, hogy tanulmányozza a mintasablonokat, és ellenőrizze a folyamatot egy tesztfürtön. A fürt egy ideig nem érhető el. Nem módosíthatja több VMSS deklarált, mint az azonos NodeType párhuzamosan; külön üzembe helyezési műveleteket kell végrehajtania ahhoz, hogy minden NodeType VMSS-re külön-külön alkalmazzon módosításokat.

1. A kezdeti fürt két csomóponttípussal és két méretezési készlettel (csomóponttípusonként egy méretezési csoport) telepíthető ezekkel a [mintasablon-](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.json) és [paraméterfájlokkal.](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.parameters.json)  Mindkét méretezési csoport szabványos D2_V2 és Windows Server 2012 R2 Datacenter rendszert futtat.  Várja meg, amíg a fürt befejezi az alapszintű frissítést.   
2. Nem kötelező– állapotalapú minta üzembe helyezése a fürtbe.
3. Miután úgy döntött, hogy frissíti az elsődleges csomópont típusú virtuális gépek, adjon hozzá egy új méretezési csoport az elsődleges csomópont típus ezekkel a [mintasablon](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.json) és [paraméterfájlok,](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.parameters.json) így az elsődleges csomópont típusa most már két méretezési készletek.  A rendszerszolgáltatások és a felhasználói alkalmazások képesek áttelepíteni a virtuális gépek között a két különböző méretezési csoportban.  Az új méretezési csoport virtuális gépei szabványos méretűek, D4_V2 és futtatják a Windows Server 2016 Datacenter tárolókkal.  Egy új terheléselosztó és nyilvános IP-cím is hozzáadódik az új méretezési csoporthoz.  
    Az új méretezési csoport a sablonban, keresse meg a "Microsoft.Compute/virtualMachineScaleSets" erőforrás neve a *vmNodeType2Name* paraméter.  Az új méretezési csoport az elsődleges csomóponttípushoz adódik a >virtualMachineProfile->extensionProfile->extensions->properties->settings->nodeTypeRef beállítással.
4. Ellenőrizze a fürt állapotát, és ellenőrizze, hogy az összes csomópont kifogástalan-e.
5. Tiltsa le az elsődleges csomóponttípus régi méretezési csoportjában lévő csomópontokat azzal a szándékkal, hogy távolítsa el a csomópontot. Egyszerre letilthatja, és a műveletek várólistára kerülnek. Várjon, amíg az összes csomópont le van tiltva, ami eltarthat egy ideig.  Mivel a csomóponttípus régebbi csomópontjai le vannak tiltva, a rendszerszolgáltatások és a magvas csomópontok az elsődleges csomóponttípusúj méretezési csoportjának virtuális gépeire vándorolnak.
6. Távolítsa el a régebbi méretezési csoport az elsődleges csomópont típusát.
7. Távolítsa el a régi méretezési készlethez társított terheléselosztót. A fürt nem érhető el, amíg az új nyilvános IP-cím és terheléselosztó az új méretezési csoporthoz van konfigurálva.  
8. Tárolja a régi elsődleges csomóponttípus-méretezési csoporthoz társított nyilvános IP-cím DNS-beállításait egy változóban, és távolítsa el azt a nyilvános IP-címet.
9. Cserélje le az új elsődleges csomóponttípus-méretezési csoporthoz társított nyilvános IP-cím DNS-beállításait a törölt nyilvános IP-cím DNS-beállításaira.  A fürt most már újra elérhető.
10. Távolítsa el a csomópontok csomópontállapotát a fürtből.  Ha a régi méretarány-készlet tartóssági szintje ezüst vagy arany volt, ezt a lépést a rendszer automatikusan végzi.
11. Ha egy előző lépésben telepítette az állapotalapú alkalmazást, ellenőrizze, hogy az alkalmazás működőképes-e.

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

## <a name="next-steps"></a>További lépések
* Információ [acsomóponttípus fürthöz való hozzáadásáról](virtual-machine-scale-set-scale-node-type-scale-out.md)
* További információ az [alkalmazások méretezhetőségéről.](service-fabric-concepts-scalability.md)
* [Azure-fürt méretezése be- és ki.](service-fabric-tutorial-scale-cluster.md)
* [Az Azure-fürtöket programozott módon méretezzük](service-fabric-cluster-programmatic-scaling.md) a gördülékeny Azure-beli SDK használatával.
* [Önálló fürt méretezése be- és kiméretezése](service-fabric-cluster-windows-server-add-remove-nodes.md).

