---
title: Horizontális felskálázás egy Azure Service Fabric-fürt hozzáadása a virtuálisgép-méretezési csoport |} A Microsoft Docs
description: Ismerje meg a Service Fabric-fürt méretezése egy virtuálisgép-méretezési csoportban hozzáadásával.
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
ms.date: 08/21/2018
ms.author: ryanwi
ms.openlocfilehash: 8f460b41cd2ce62b7a3e0138caa25f68e2fd22ad
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50156493"
---
# <a name="scale-a-service-fabric-cluster-out-by-adding-a-virtual-machine-scale-set"></a>Egy virtuálisgép-méretezési csoportban hozzáadásával ki a Service Fabric-fürt méretezése
Ez a cikk ismerteti az Azure Service Fabric-fürt méretezése egy új virtuálisgép-méretezési csoportot egy meglévő fürthöz hozzáadásával. Service Fabric-fürt, amelybe mikroszolgáltatásokat helyezhet üzembe és felügyelhet virtuális vagy fizikai gépek hálózaton keresztül csatlakozó készlete áll. Egy számítógép vagy virtuális Gépet, amely egy fürt része csomópontoknak nevezzük. Virtuálisgép-méretezési csoportok olyan számítási Azure-erőforrások üzembe helyezése és kezelése a virtuális gépek gyűjteményét készletként használt. Minden csomópont-típus egy Azure-fürtön definiált [külön méretezési csoportként](service-fabric-cluster-nodetypes.md). Mindegyik csomóponttípus kezelhetők külön-külön. Egy Service Fabric-fürt létrehozását követően méretezhetők egy fürtcsomóponttípus függőlegesen (módosíthatja a csomópontok az erőforrások), frissítse az operációs rendszer a csomópont típusú virtuális gépeket, vagy adjon hozzá egy új virtuálisgép-méretezési csoportot egy meglévő fürthöz.  Méretezheti a fürt bármikor, még akkor is, ha a számítási feladatok a fürtön futnak.  A fürt skálázható, mivel az alkalmazások automatikus méretezése is.

> [!WARNING]
> Nem indulnak el módosítani az elsődleges NodeType csomóponttípus Virtuálisgép-Termékváltozatra, ha a fürt állapota nem kifogástalan. A fürt állapota nem kifogástalan, ha meg fogja csak történő leállítása instabillá a fürt további, ha megpróbálja módosítani a virtuális gép Termékváltozata.
>
> Azt javasoljuk, hogy nem módosítja a virtuális gép Termékváltozata méretezési készlet vagy csomópont típusa, ha fut a következőn: [Silver szintű tartósságot vagy nagyobb](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster). Virtuális gép SKU-méret módosítása egy olyan adatok felülíró helyszíni infrastruktúra művelet. Késleltetés, vagy figyelheti a módosítás néhány képessége nélkül is lehet, hogy a művelet az állapotalapú szolgáltatások esetében adatvesztést is okozhat, vagy akár állapot nélküli számítási feladatok esetében más előre nem látható operatív problémákat okozhat. Ez azt jelenti, hogy az elsődleges csomópont típusa, ami állapotalapú service fabric rendszer-szolgáltatásokat futtató, vagy minden csomópont típusa, amelyen fut az állapotalapú alkalmazások munkahelyi tölti be.
>

## <a name="upgrade-the-size-and-operating-system-of-the-primary-node-type-vms"></a>A méret és az elsődleges csomóponttípushoz virtuális gépek operációs rendszerének frissítése
A virtuális gép méretét és az elsődleges csomóponttípushoz virtuális gépek operációs rendszerének frissítése során a rendszer.  A frissítés után az elsődleges csomóponttípushoz virtuális gépek, szabványos D4_V2 méretét és a futó Windows Server 2016 Datacenter tárolókkal.

> [!WARNING]
> Ez az eljárás egy üzemben lévő fürt előtt javasoljuk, hogy tanulmányozza a mintasablonokat, és ellenőrizze a folyamat tesztelési fürtön történő. A fürt ideje még nem érhető el. NEM módosíthatja parrallel; az azonos NodeType deklarálni több vmss-hez szüksége lesz, minden egyes NodeType VMSS módosítások alkalmazásához külön-külön elválasztott üzembe helyezési műveletek végrehajtásához.

1. A kezdeti kétféle csomópontot és két méretezési csoportok (egy méretezési csoportot egy csomópont típusa) fürt üzembe helyezése a minta használatával [sablon](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.json) és [paraméterek](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.parameters.json) fájlokat.  Mindkét méretezési csoportok a következők: Standard D2_V2 mérete és a futó Windows Server 2012 R2 Datacenter.  Várjon, amíg a referenciakonfiguráció frissítés végrehajtásához a fürt.   
2. Nem kötelező – a fürt üzembe helyezése egy állapotalapú mintát.
3. Miután eldöntötte, frissítse az elsődleges csomóponttípushoz virtuális gépek, adja hozzá egy új méretezési csoportot az alábbi minta használatával az elsődleges csomóponttípushoz [sablon](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.json) és [paraméterek](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.parameters.json) fájljait, így az elsődleges csomóponttípushoz most már két méretezési csoportokat.  Rendszer-szolgáltatások és a felhasználói alkalmazások képesek a két különböző méretezési csoportokban lévő virtuális gépek közötti áttelepítése.  Az új méretezési csoportot a virtuális gépek mérete szabványos D4_V2, és futtassa a Windows Server 2016 Datacenter tárolókkal.  Egy új terheléselosztót és egy nyilvános IP-címet is hozzáadott az új méretezési csoportot az.  
    Keresse meg az új méretezési csoportot a sablonban, keresse meg a "Microsoft.Compute/virtualMachineScaleSets" erőforrás neve szerint a *vmNodeType2Name* paraméter.  Az új méretezési adnak hozzá az elsődleges csomópont típusa használja a Tulajdonságok > virtualMachineProfile - > extensionProfile -> bővítmények -> Tulajdonságok -> Beállítások -> nodetyperef hivatkozással beállítás.
4. A fürt állapotának ellenőrzése, és ellenőrizze a csomópontokon kifogástalan állapotban.
5. Tiltsa le a csomópontokat az elsődleges csomópont típusú, célja, hogy távolítsa el a csomópontot a régi méretezési csoportban. Letilthatja a egyszerre, és a művelet várólistára kerül. Várjon, amíg az összes csomópont le vannak tiltva, ami hosszabb időt is igénybe vehet.  Írja be a csomópont a régebbi csomópontok le vannak tiltva, mert a rendszer szolgáltatások és a kezdőérték csomópontok telepítse át a virtuális gépeket az új méretezési az elsődleges csomóponttípushoz.
6. Távolítsa el a régebbi méretezési az elsődleges csomóponttípushoz.
7. Távolítsa el a terheléselosztó a régi méretezési csoporthoz társított. A fürt nem érhető el, amíg az új méretezési csoportot az új nyilvános IP cím és a load balancer vannak konfigurálva.  
8. A régi elsődleges csomóponthoz társított nyilvános IP-cím DNS-beállítások Store írja be a méretezési csoport egy változóban, és távolítsa el a nyilvános IP-címet.
9. Cserélje le a DNS-beállításait az új elsődleges csomópont típusa méretezési törölt nyilvános IP-cím DNS-beállításainak társított nyilvános IP-cím.  A fürt már érhető el újra.
10. A csomópont állapota a csomópontok eltávolítása a fürtből.  Ha a tartóssági szint a régi méretezési volt silver vagy gold, ebben a lépésben a rendszer automatikusan történik.
11. Ha az állapot-nyilvántartó alkalmazás az előző lépésben telepített, ellenőrizze, hogy az alkalmazás működési.

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

## <a name="adding-an-additional-scale-set-to-an-existing-cluster"></a>Meglévő fürt hozzáadása egy kiegészítő skálázási beállítása
Új NodeType virtuálisgép-méretezési ad egy meglévő fürthöz, hasonlít az elsődleges csomóponttípushoz, fent említett frissítése, kivéve az azonos nodetyperef hivatkozással; nem fog használni természetesen nem letiltja a bármely aktívan használt virtuálisgép-méretezési csoportokat, és nem fogadhatja elveszíti fürt rendelkezésre állását, ha nem frissítik az elsődleges csomóponttípushoz. 

Nodetyperef hivatkozással tulajdonság van deklarálva a virtuálisgép-méretezési csoport Service Fabric bővítménye tulajdonságok:
```json
<snip>
"publisher": "Microsoft.Azure.ServiceFabric",
     "settings": {
     "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
     "nodeTypeRef": "[parameters('vmNodeType2Name')]",
     "dataPath": "D:\\\\SvcFab",
     "durabilityLevel": "Silver",
<snip>
```

Emellett szüksége lesz a Service Fabric-fürt erőforrás ad hozzá az új csomópont típusa:

```json
<snip>
"nodeTypes": [
      {
      "name": "[parameters('vmNodeType2Name')]",
      "applicationPorts": {
                "endPort": "[parameters('nt2applicationEndPort')]",
                "startPort": "[parameters('nt2applicationStartPort')]"
      },
      "clientConnectionEndpointPort": "[parameters('nt2fabricTcpGatewayPort')]",
      "durabilityLevel": "Silver",
       "ephemeralPorts": {
                "endPort": "[parameters('nt2ephemeralEndPort')]",
                "startPort": "[parameters('nt2ephemeralStartPort')]"
      },
      "httpGatewayEndpointPort": "[parameters('nt2fabricHttpGatewayPort')]",
      "isPrimary": false,
      "vmInstanceCount": "[parameters('nt2InstanceCount')]"
},
<snip>
```

## <a name="next-steps"></a>További lépések
* Ismerje meg [alkalmazás méretezhetőségi](service-fabric-concepts-scalability.md).
* [Egy Azure-fürtön lévő vagy horizontális skálázása](service-fabric-tutorial-scale-cluster.md).
* [Egy Azure-fürtön programozott skálázása](service-fabric-cluster-programmatic-scaling.md) az fluent Azure compute SDK-t.
* [Vagy önálló fürt méretezése](service-fabric-cluster-windows-server-add-remove-nodes.md).

