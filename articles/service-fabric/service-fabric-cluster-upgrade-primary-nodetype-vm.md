---
title: Az Azure Service Fabric-fürt virtuális gépek SKU vagy az operációs rendszer frissítése |} Microsoft Docs
description: Ismerje meg, hogyan lehet frissíteni a virtuális gépek a Service Fabric-fürt elsődleges csomóponttípusban.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/21/2018
ms.author: ryanwi
ms.openlocfilehash: bf707bf4b1c001b5467dd9954e9c6feb55e65654
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655244"
---
# <a name="upgrade-the-primary-node-type-vms-of-a-service-fabric-cluster"></a>A Service Fabric-fürt elsődleges csomóponttípushoz virtuális gépek frissítése
A cikkből megtudhatja, hogyan lehet frissíteni az elsődleges csomópont típusú virtuális gépek az Azure-ban futó Service Fabric-fürt.  A Service Fabric-fürt olyan hálózathoz csatlakozó virtuális vagy fizikai gépek, amelybe a mikroszolgáltatások telepíteni és felügyelni. Egy számítógép vagy virtuális Gépet, amely egy fürt része egy csomópont neve. Virtuálisgép-méretezési csoportok az Azure számítási erőforrás, amelyekkel telepíthetnek és kezelhetnek olyan virtuális gépek gyűjteménye. Minden csomópont-típus egy Azure-fürttel definiált [beállítani egy külön méretezési](service-fabric-cluster-nodetypes.md). Az egyes csomóponttípusok kezelheti külön-külön. Miután létrehozta a Service Fabric-fürt, méretezheti fürt csomóponttípus függőleges (módosítsa az erőforrásokat a csomópontok) vagy frissítse az operációs rendszert, a csomópont típusú virtuális gépek.  Méretezheti a fürt bármikor, még akkor is, ha munkaterhelések fut a fürtön.  Méretezi a fürtön, mivel az alkalmazások automatikus méretezése is.

> [!WARNING]
> Azt javasoljuk, hogy nem módosítja a virtuális gép Termékváltozat méretezési készlet/csomópont típusú kivéve, ha fut a [tartóssági Silver vagy nagyobb](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster). Virtuális gép SKU méretének módosítása egy olyan adatok felülíró helyszíni infrastruktúra művelet. Nem néhány rendelkező késleltetés vagy a figyelheti ezt a módosítást lehetséges, hogy a művelet okozhat az állapotalapú szolgáltatások, vagy más váratlan működési problémákkal rendelkeznek, még a állapot nélküli munkaterheléseket miatt. 
>

## <a name="upgrade-the-size-and-operating-system-of-the-primary-node-type-vms"></a>A méret és az elsődleges csomóponttípusok virtuális gépek operációs rendszerének frissítése
Itt az a folyamat a Virtuálisgép-méretet és az elsődleges csomópont típusú virtuális gépek operációs rendszer frissítéséhez.  A frissítés után az elsődleges csomóponttípusok virtuális gépek a szabványos D4_V2 méretét és a tárolók futó Windows Server 2016 adatközpont.

> [!WARNING]
> Ezt a műveletet az éles fürt előtt javasoljuk, hogy tanulmányozza a minta-sablonok, és ellenőrizze a folyamat egy teszt fürtön hajtották. A fürt ideje még nem érhető el.

1. A kezdeti fürtök kétféle csomópontot és két méretezési csoportok (egy méretezési készletben csomópont típusonkénti) telepítése a mintát használja [sablon](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.json) és [paraméterek](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.parameters.json) fájlokat.  Mindkét méretezési készlet mérete szabványos D2_V2 és futó Windows Server 2012 R2 Datacenter.  Várja meg a fürtöt az alapterv frissítésének befejezéséhez.   
2. Nem kötelező – a fürt központi telepítése állapotalapú minta.
3. Miután az elsődleges csomóponttípusok virtuális gépek frissítése, adja hozzá ezek mintát használja az elsődleges csomópont-típust adott meg egy új méretezési [sablon](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.json) és [paraméterek](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.parameters.json) fájlok, az elsődleges csomóponttípusok most már két méretezési készlet.  Rendszerszolgáltatások és felhasználót alkalmazások képesek a két különböző méretezési készlet a virtuális gépek közötti áttelepítése.  A virtuális gépeken új méretezési szabványos D4_V2 méret, és futtassa a Windows Server 2016 Datacenter tárolók.  Egy új terheléselosztó és a nyilvános IP-cím is bekerülnek az új méretezési készlettel.  
    A sablonban beállított új skála kereséséhez keresse meg a "Microsoft.Compute/virtualMachineScaleSets" erőforrás neve szerint a *vmNodeType2Name* paraméter.  Az új méretezési hozzá lett adva az elsődleges csomópont típusát a Tulajdonságok -> virtualMachineProfile - > extensionProfile -> bővítmények -> tulajdonságai -> Beállítások -> nodeTypeRef beállítást.
4. Ellenőrizze a fürt állapotát, és ellenőrizze, a csomópontok kifogástalan.
5. Tiltsa le az elsődleges csomóponttípusok csomópont eltávolítása szándékával régi méretezési csoportban lévő csomópontok. Egyszerre letilthatja, és a művelet várólistára kerül. Várjon, amíg az összes csomópont le vannak tiltva, ami eltarthat egy ideig.  A csomóponttípus régebbi csomópontja le vannak tiltva, mert a rendszer szolgáltatásokat, mind a seed csomópontok telepítse át a virtuális gépeket az új állítsa be az elsődleges csomóponttípusok a skála.
6. Távolítsa el a régebbi állítson be úgy az elsődleges csomóponttípusok a skála.
7. Távolítsa el a régi méretezési társított terheléselosztóhoz. A fürt nem érhető el, amíg az új nyilvános IP cím és a terheléselosztó vannak konfigurálva, az új méretezési készlet.  
8. Tároló DNS-beállításokat a nyilvános IP-cím, a régi elsődleges csomópont társított írja be a méretezési csoport egy változóban, és távolítsa el a nyilvános IP-címet.
9. Cserélje le a nyilvános IP-cím, az új elsődleges csomópont típus méretezési készletben a DNS-beállítások a törölt nyilvános IP-cím a társított a DNS-beállításait.  A fürt most érhető el újra.
10. Távolítsa el a fürt a csomópontok csomópont állapotát.  Ha a tartóssági szint a régi méretezési ezüst vagy arany, ezt a lépést a rendszer automatikusan történik.
11. Ha az előző lépésben állapot-nyilvántartó alkalmazás központi telepítése, az alkalmazás működőképességének ellenőrzése.

```powershell
# Variables.
$groupname = "sfupgradetestgroup"
$clusterloc="southcentralus"  
$subscriptionID="<your subscription ID>"

# sign in to your Azure account and select your subscription
Login-AzureRmAccount -SubscriptionId $subscriptionID 

# Create a new resource group for your deployment and give it a name and a location.
New-AzureRmResourceGroup -Name $groupname -Location $clusterloc

# Deploy the two node type cluster.
New-AzureRmResourceGroupDeployment -ResourceGroupName $groupname -TemplateParameterFile "C:\temp\cluster\Deploy-2NodeTypes-2ScaleSets.parameters.json" `
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
New-AzureRmResourceGroupDeployment -ResourceGroupName $groupname -TemplateParameterFile "C:\temp\cluster\Deploy-2NodeTypes-3ScaleSets.parameters.json" `
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
Remove-AzureRmVmss -ResourceGroupName $groupname -VMScaleSetName $scaleSetName -Force
Write-Host "Removed scale set $scaleSetName"

$lbname="LB-sfupgradetest-NTvm1"
$oldPublicIpName="PublicIP-LB-FE-0"
$newPublicIpName="PublicIP-LB-FE-2"

# Store DNS settings of public IP address related to old Primary NodeType into variable 
$oldprimaryPublicIP = Get-AzureRmPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $groupname

$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel

$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn

# Remove Load Balancer related to old Primary NodeType. This will cause a brief period of downtime for the cluster
Remove-AzureRmLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force

# Remove the old public IP
Remove-AzureRmPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force

# Replace DNS settings of Public IP address related to new Primary Node Type with DNS settings of Public IP address related to old Primary Node Type
$PublicIP = Get-AzureRmPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $groupname
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzureRmPublicIpAddress -PublicIpAddress $PublicIP

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
* További tudnivalók [alkalmazás méretezhetőség](service-fabric-concepts-scalability.md).
* [Egy Azure-fürttel bejövő vagy kimenő méretezése](service-fabric-tutorial-scale-cluster.md).
* [Egy Azure-fürt méretezése programozott módon](service-fabric-cluster-programmatic-scaling.md) SDK használatával a Folyékonyan beszél Azure számítási.
* [Bejövő vagy kimenő standaone fürt méretezése](service-fabric-cluster-windows-server-add-remove-nodes.md).

