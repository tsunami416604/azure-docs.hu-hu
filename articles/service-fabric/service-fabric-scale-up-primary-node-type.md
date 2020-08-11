---
title: Azure Service Fabric elsődleges csomópont-típus vertikális felskálázása
description: Megtudhatja, hogyan méretezheti Service Fabric fürtöt egy csomópont-típus hozzáadásával.
ms.topic: article
ms.date: 08/06/2020
ms.author: pepogors
ms.openlocfilehash: eecf398359470f6e5e151c53eb63b3cb56efbe39
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/10/2020
ms.locfileid: "88056754"
---
# <a name="scale-up-a-service-fabric-cluster-primary-node-type-by-adding-a-node-type"></a>Service Fabric-fürt elsődleges csomópont-típusának vertikális felskálázása csomópont-típus hozzáadásával
Ez a cikk azt ismerteti, hogyan méretezhető egy Service Fabric-fürt elsődleges csomópontjának típusa egy további csomópont-típusnak a fürthöz való hozzáadásával. A Service Fabric-fürt olyan virtuális vagy fizikai gépek hálózathoz csatlakoztatott készlete, amelybe a rendszer üzembe helyezi és kezeli a szolgáltatásait. Egy fürt részét képező gépet vagy virtuális gépet csomópontnak nevezzük. A virtuálisgép-méretezési csoportok egy Azure-beli számítási erőforrás, amely készletként telepíti és felügyeli a virtuális gépek gyűjteményét. Az Azure-fürtben definiált összes csomópont-típus [külön méretezési csoportként van beállítva](service-fabric-cluster-nodetypes.md). Ezután mindegyik csomópont-típust külön lehet kezelni.

A következő oktatóanyagban található minta sablonok itt találhatók: [Service Fabric elsődleges csomópont típusú méretezési minták](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Primary-NodeType-Scaling-Sample)

> [!WARNING]
> Ha a fürt állapota nem kifogástalan, ne próbálkozzon az elsődleges csomópont típusú vertikális Felskálázási eljárással, mivel ez a művelet csak a fürt destabilizálására szolgál.
>

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="process-to-upgrade-the-size-and-operating-system-of-the-primary-node-type"></a>Az elsődleges csomópont típusú méret és operációs rendszer frissítésének folyamata
A következő eljárással frissítheti a virtuális gépek méretét és operációs rendszerét az elsődleges csomópont típusaként.  A frissítés után az elsődleges csomópont típusú virtuális gépek szabványos D4_V2 és a Windows Server 2019 Datacenter tárolókkal futnak.

> [!WARNING]
> Az eljárás üzemi fürtön való megkísérlése előtt javasoljuk, hogy tanulmányozza a minta sablonokat, és ellenőrizze a folyamatot egy tesztelési fürtön. Előfordulhat, hogy a fürt rövid ideig nem érhető el. 

### <a name="deploy-the-initial-service-fabric-cluster"></a>A kezdeti Service Fabric fürt üzembe helyezése 
Ha követni szeretné a mintát, telepítse a kezdeti fürtöt egyetlen elsődleges csomópont-típussal, és egyetlen méretezési készletet [Service Fabric-kezdeti fürtöt](https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/Primary-NodeType-Scaling-Sample/AzureDeploy-1.json). Ezt a lépést kihagyhatja, ha már telepítve van egy meglévő Service Fabric-fürt. 

1. Jelentkezzen be az Azure-fiókjába. 
```powershell
# sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId "<your subscription ID>"
```
2. Új erőforráscsoport létrehozása. 
```powershell
# create a resource group for your cluster deployment
$resourceGroupName = "myResourceGroup"
$location = "WestUS"

New-AzResourceGroup `
    -Name $resourceGroupName
    -Location $location
```
3. Adja meg a sablonfájlok paramétereinek értékét. 
4. Telepítse a fürtöt a 2. lépésben létrehozott erőforráscsoporthoz. 
```powershell
# deploy the template files to the resource group created above
$templateFilePath = "C:\AzureDeploy-1.json"
$parameterFilePath = "C:\AzureDeploy.Parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
```

### <a name="add-a-new-primary-node-type-to-the-cluster"></a>Új elsődleges csomópont-típus hozzáadása a fürthöz
> [!Note]
> A skálázási művelet befejezése után a következő lépésekben létrehozott erőforrások lesznek a fürt új elsődleges csomópont-típusa. Győződjön meg arról, hogy a kezdeti alhálózat, a nyilvános IP-cím, a Load Balancer, a virtuálisgép-méretezési csoport és a csomópont típusa alapján egyedi neveket használ. 

A következő lépések mindegyikével megtalálhatja a sablont: [Service Fabric – új csomópont típusú fürt](https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/Primary-NodeType-Scaling-Sample/AzureDeploy-2.json). Az alábbi lépések olyan részleges erőforrás-kódrészleteket tartalmaznak, amelyek kiemelik az új erőforrások változásait.  

1. Hozzon létre egy új alhálózatot a meglévő Virtual Networkban.
```json
{
    "name": "[variables('subnet1Name')]",
    "properties": {
        "addressPrefix": "[variables('subnet1Prefix')]"
    }
}
```
2. Hozzon létre egy új nyilvános IP-erőforrást egy egyedi Domainnamelabel értékkel. 
```json
{
    "apiVersion": "[variables('publicIPApiVersion')]",
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[concat(variables('lbIPName'),'-',variables('vmNodeType1Name'))]",
    "location": "[variables('computeLocation')]",
    "properties": {
    "dnsSettings": {
        "domainNameLabel": "[concat(variables('dnsName'),'-','nt2')]"
    },
    "publicIPAllocationMethod": "Dynamic"
    },
    "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
    }
}
```
3. Hozzon létre egy új Load Balancer-erőforrást, amely a fent létrehozott nyilvános IP-címektől függ. 
```json
"dependsOn": [
    "[concat('Microsoft.Network/publicIPAddresses/',concat(variables('lbIPName'),'-',variables('vmNodeType1Name')))]"
]
```
4. Hozzon létre egy új virtuálisgép-méretezési készletet, amely az új virtuális gép SKU-t és az operációs rendszer SKU-át szeretné méretezni. 

Csomópont típusa ref 
```json
"nodeTypeRef": "[variables('vmNodeType1Name')]"
```

Virtuális gép termékváltozata
```json
"sku": {
    "name": "[parameters('vmNodeType1Size')]",
    "capacity": "[parameters('nt1InstanceCount')]",
    "tier": "Standard"
}
```

OPERÁCIÓS RENDSZER SKU 
```json
"imageReference": {
    "publisher": "[parameters('vmImagePublisher1')]",
    "offer": "[parameters('vmImageOffer1')]",
    "sku": "[parameters('vmImageSku1')]",
    "version": "[parameters('vmImageVersion1')]"
}
```
5. Adjon hozzá egy új csomópont-típust a fürthöz, amely a fent létrehozott virtuálisgép-méretezési csoportra hivatkozik. A csomópont **isPrimary** tulajdonságát True értékre kell állítani. 
```json
"name": "[variables('vmNodeType1Name')]",
"applicationPorts": {
    "endPort": "[variables('nt0applicationEndPort')]",
    "startPort": "[variables('nt0applicationStartPort')]"
},
"clientConnectionEndpointPort": "[variables('nt0fabricTcpGatewayPort')]",
"durabilityLevel": "Bronze",
"ephemeralPorts": {
    "endPort": "[variables('nt0ephemeralEndPort')]",
    "startPort": "[variables('nt0ephemeralStartPort')]"
},
"httpGatewayEndpointPort": "[variables('nt0fabricHttpGatewayPort')]",
"isPrimary": true,
"reverseProxyEndpointPort": "[variables('nt0reverseProxyEndpointPort')]",
"vmInstanceCount": "[parameters('nt1InstanceCount')]"
```
6. Telepítse a frissített ARM-sablont. 
```powershell
# deploy the updated template files to the existing resource group
$templateFilePath = "C:\AzureDeploy-2.json"
$parameterFilePath = "C:\AzureDeploy.Parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
```

A Service Fabric-fürt most két csomópont-típussal fog rendelkezni, amikor a telepítés befejeződött. 

### <a name="remove-the-existing-node-type"></a>A meglévő csomópont típusának eltávolítása 
Ha az erőforrások üzembe helyezése befejeződött, megkezdheti a csomópontok letiltását az eredeti csomópont-típusban. Mivel a csomópontok le vannak tiltva, a rendszerszolgáltatások a fenti lépésben üzembe helyezett új elsődleges csomópont-típusra lesznek áttelepítve.

1. Állítsa hamis értékre a Service Fabric fürterőforrás elsődleges csomópont-típus tulajdonságát. 
```json
{
    "name": "[variables('vmNodeType0Name')]",
    "applicationPorts": {
        "endPort": "[variables('nt0applicationEndPort')]",
        "startPort": "[variables('nt0applicationStartPort')]"
    },
    "clientConnectionEndpointPort": "[variables('nt0fabricTcpGatewayPort')]",
    "durabilityLevel": "Bronze",
    "ephemeralPorts": {
        "endPort": "[variables('nt0ephemeralEndPort')]",
        "startPort": "[variables('nt0ephemeralStartPort')]"
    },
    "httpGatewayEndpointPort": "[variables('nt0fabricHttpGatewayPort')]",
    "isPrimary": false,
    "reverseProxyEndpointPort": "[variables('nt0reverseProxyEndpointPort')]",
    "vmInstanceCount": "[parameters('nt0InstanceCount')]"
}
```
2. Telepítse a sablont a frissített isPrimary tulajdonsággal az eredeti csomópont-típuson. Az elsődleges jelzővel rendelkező sablon hamis értékre van állítva az eredeti csomópont-típusnál: [Service Fabric – elsődleges csomópont típusa false (hamis](https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/Primary-NodeType-Scaling-Sample/AzureDeploy-3.json)).

```powershell
# deploy the updated template files to the existing resource group
$templateFilePath = "C:\AzureDeploy-3.json"
$parameterFilePath = "C:\AzureDeploy.Parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
```

3. Tiltsa le a csomópontokat a 0. típusú csomópontban. 
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint $ClusterConnectionEndpoint `
    -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My 

Write-Host "Connected to cluster"


$nodeType = "nt1vm" # specify the name of node type
$nodes = Get-ServiceFabricNode 

Write-Host "Disabling nodes..."
foreach($node in $nodes)
{
  if ($node.NodeType -eq $nodeType)
  {
    $node.NodeName

    Disable-ServiceFabricNode -Intent RemoveNode -NodeName $node.NodeName -Force
  }
}
```
* A bronz tartósság érdekében várjon, amíg az összes csomópont letiltott állapotba kerül.
* Az ezüst és az arany tartóssága érdekében egyes csomópontok le lesznek tiltva, a REST pedig letiltási állapotba kerül. Ellenőrizze, hogy a csomópontok részletek lapján letiltott állapotban van-e, ha mind beragadt az infrastruktúra-szolgáltatási partíciók Kvórumának biztosítására, akkor biztonságos a folytatás.

> [!Note]
> Ez a lépés hosszabb időt is igénybe vehet. 

4. Állítsa le az adattípust a 0. csomóponton. 
```powershell
foreach($node in $nodes)
{
  if ($node.NodeType -eq $nodeType)
  {
    $node.NodeName

    Start-ServiceFabricNodeTransition -Stop -OperationId (New-Guid) -NodeInstanceId $node.NodeInstanceId -NodeName $node.NodeName -StopDurationInSeconds 10000
  }
}
```
5. Csomópontok felszabadítása az eredeti virtuálisgép-méretezési csoportból 
```powershell
$scaleSetName="nt1vm"
$scaleSetResourceType="Microsoft.Compute/virtualMachineScaleSets"

Remove-AzResource -ResourceName $scaleSetName -ResourceType $scaleSetResourceType -ResourceGroupName $resourceGroupName -Force
```
> [!Note]
> A 6. és 7. lépés megadása nem kötelező, ha már használ szabványos SKU nyilvános IP-címet és standard SKU Load balancert. Ebben az esetben előfordulhat, hogy több virtuálisgép-méretezési csoport vagy csomópont-típus is van ugyanazon a terheléselosztó alatt. 

6. Most már törölheti az eredeti IP-címet, és Load Balancer erőforrásokat is. Ebben a lépésben a DNS-nevet is frissíti. 

```powershell
$lbname="LB-cluster-name-nt1vm"
$lbResourceType="Microsoft.Network/loadBalancers"
$ipResourceType="Microsoft.Network/publicIPAddresses"
$oldPublicIpName="PublicIP-LB-FE-nt1vm"
$newPublicIpName="PublicIP-LB-FE-nt2vm"

$oldprimaryPublicIP = Get-AzPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $resourceGroupName
$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel
$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn

Remove-AzResource -ResourceName $lbname -ResourceType $lbResourceType -ResourceGroupName $resourceGroupName -Force
Remove-AzResource -ResourceName $oldPublicIpName -ResourceType $ipResourceType -ResourceGroupName $resourceGroupName -Force

$PublicIP = Get-AzPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $resourceGroupName
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzPublicIpAddress -PublicIpAddress $PublicIP
``` 

7. Frissítse a fürt felügyeleti végpontját az új IP-címhez való hivatkozáshoz. 
```json
  "managementEndpoint": "[concat('https://',reference(concat(variables('lbIPName'),'-',variables('vmNodeType1Name'))).dnsSettings.fqdn,':',variables('nt0fabricHttpGatewayPort'))]",
```
8. Csomópont-állapot eltávolítása a 0. típusú csomópontból
```powershell
foreach($node in $nodes)
{
  if ($node.NodeType -eq $nodeType)
  {
    $node.NodeName

    Remove-ServiceFabricNodeState -NodeName $node.NodeName -Force
  }
}
```
9. Távolítsa el az eredeti csomópont típusú hivatkozást az ARM-sablon Service Fabric erőforrásáról. 
```json
"name": "[variables('vmNodeType0Name')]",
"applicationPorts": {
    "endPort": "[variables('nt0applicationEndPort')]",
    "startPort": "[variables('nt0applicationStartPort')]"
},
"clientConnectionEndpointPort": "[variables('nt0fabricTcpGatewayPort')]",
"durabilityLevel": "Bronze",
"ephemeralPorts": {
    "endPort": "[variables('nt0ephemeralEndPort')]",
    "startPort": "[variables('nt0ephemeralStartPort')]"
},
"httpGatewayEndpointPort": "[variables('nt0fabricHttpGatewayPort')]",
"isPrimary": true,
"reverseProxyEndpointPort": "[variables('nt0reverseProxyEndpointPort')]",
"vmInstanceCount": "[parameters('nt0InstanceCount')]"
```
Csak ezüst és magasabb tartósságú fürtök esetén frissítse a sablonban szereplő fürterőforrás-erőforrást, és konfigurálja az állapotfigyelő házirendeket a háló:/rendszeralkalmazási állapot mellőzéséhez, ha az alább megadott módon applicationDeltaHealthPolicies-t ad hozzá a fürterőforrás-tulajdonságok területen. Az alábbi házirend figyelmen kívül hagyja a meglévő hibákat, de nem engedélyezi az új állapotú hibákat.
```json
"upgradeDescription":  
{ 
 "forceRestart": false, 
 "upgradeReplicaSetCheckTimeout": "10675199.02:48:05.4775807", 
 "healthCheckWaitDuration": "00:05:00", 
 "healthCheckStableDuration": "00:05:00", 
 "healthCheckRetryTimeout": "00:45:00", 
 "upgradeTimeout": "12:00:00", 
 "upgradeDomainTimeout": "02:00:00", 
 "healthPolicy": { 
   "maxPercentUnhealthyNodes": 100, 
   "maxPercentUnhealthyApplications": 100 
 }, 
 "deltaHealthPolicy":  
 { 
   "maxPercentDeltaUnhealthyNodes": 0, 
   "maxPercentUpgradeDomainDeltaUnhealthyNodes": 0, 
   "maxPercentDeltaUnhealthyApplications": 0, 
   "applicationDeltaHealthPolicies":  
   { 
       "fabric:/System":  
       { 
           "defaultServiceTypeDeltaHealthPolicy":  
           { 
                   "maxPercentDeltaUnhealthyServices": 0 
           } 
       } 
   } 
 } 
}
```
10. Távolítsa el az ARM-sablon eredeti csomópont-típusához kapcsolódó összes többi erőforrást. Lásd: [Service Fabric – új csomópont típusú fürt](https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/Primary-NodeType-Scaling-Sample/AzureDeploy-4.json) egy sablonhoz az összes ilyen eredeti erőforrás eltávolításával.

11. Telepítse a módosított Azure Resource Manager sablont. * * Ez a lépés eltarthat egy ideig, általában akár két óráig is. Ez a frissítés megváltoztatja a beállításokat a InfrastructureService, ezért szükség van a csomópontok újraindítására. Ebben az esetben a rendszer figyelmen kívül hagyja a forceRestart. A upgradeReplicaSetCheckTimeout paraméter határozza meg azt a maximális időtartamot, ameddig Service Fabric a partíció biztonságos állapotba kerül, ha még nem biztonságos állapotban van. Miután a biztonsági ellenőrzés egy csomóponton lévő összes partícióra kiterjed, Service Fabric folytatja a frissítést a csomóponton. A upgradeTimeout paraméter értéke 6 órára csökkenthető, de a maximális biztonság érdekében 12 órát kell használni.
Ezután ellenőrizze, hogy a portálon a Service Fabric erőforrás készként jelenik-e meg. 

```powershell
# deploy the updated template files to the existing resource group
$templateFilePath = "C:\AzureDeploy-4.json"
$parameterFilePath = "C:\AzureDeploy.Parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
```

A fürt elsődleges csomópontjának típusa már frissítve lett. Ellenőrizze, hogy a telepített alkalmazások megfelelően működnek-e, és hogy a fürt állapota rendben van-e.

## <a name="next-steps"></a>További lépések
* Megtudhatja, hogyan [adhat hozzá csomópont-típust fürthöz](virtual-machine-scale-set-scale-node-type-scale-out.md)
* Az [alkalmazások méretezhetőségének](service-fabric-concepts-scalability.md)megismerése.
* [Azure-fürt méretezése vagy](service-fabric-tutorial-scale-cluster.md)kibontása.
* Az [Azure-fürtöket programozott módon méretezheti](service-fabric-cluster-programmatic-scaling.md) a Fluent Azure számítási SDK használatával.
* [Önálló fürt méretezése vagy](service-fabric-cluster-windows-server-add-remove-nodes.md)kibontása.
