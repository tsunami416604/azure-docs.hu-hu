---
title: Fürt telepítése a rendelkezésre állási zónák között
description: Ismerje meg, hogyan hozhat létre egy Azure Service Fabric-fürtöt a rendelkezésre állási zónák között.
author: peterpogorski
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: pepogors
ms.openlocfilehash: 6da9517f822c9c157d26a1bda8dab2c694b08b12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75609978"
---
# <a name="deploy-an-azure-service-fabric-cluster-across-availability-zones"></a>Azure Service Fabric-fürt üzembe helyezése a rendelkezésre állási zónák között
Az Azure rendelkezésre állási zónái egy magas rendelkezésre állási ajánlat, amely megvédi az alkalmazásokat és az adatokat az adatközponti hibáktól. A rendelkezésre állási zóna egy egyedi fizikai hely, amely független áramellátással, hűtéssel és hálózatépítéssel rendelkezik egy Azure-régióban.

A Service Fabric támogatja a rendelkezésre állási zónák között átnyúló fürtöket az adott zónákhoz rögzített csomóponttípusok üzembe helyezésével. Ez biztosítja az alkalmazások magas rendelkezésre állását. Az Azure rendelkezésre állási zónái csak bizonyos régiókban érhetők el. További információ: [Azure availability zones overview](https://docs.microsoft.com/azure/availability-zones/az-overview).

Mintasablonok érhetők el: [Service Fabric kereszt-rendelkezésre állási zóna sablon](https://github.com/Azure-Samples/service-fabric-cluster-templates)

## <a name="recommended-topology-for-primary-node-type-of-azure-service-fabric-clusters-spanning-across-availability-zones"></a>Ajánlott topológia az Azure Service Fabric-fürtök elsődleges csomóponttípusához, amely a rendelkezésre állási zónák között terjed
A rendelkezésre állási zónák között elosztott Service Fabric-fürt biztosítja a fürtállapot magas rendelkezésre állását. A Service Fabric-fürt zónák közötti átfogására létre kell hoznia egy elsődleges csomóponttípust a régió által támogatott minden rendelkezésre állási zónában. Ez egyenletesen osztja el a magcsomópontokat az elsődleges csomóponttípusok között.

Az elsődleges csomóponttípus ajánlott topológiájához az alábbiakban ismertetett erőforrások szükségesek:

* A fürt megbízhatósági szintje Platina.
* Három csomóponttípus elsődlegesként megjelölve.
    * Minden csomóponttípust le kell képeznie a különböző zónákban található saját virtuálisgép-méretezési készlethez.
    * Minden virtuálisgép-méretezési csoportnak legalább öt csomópontnak kell lennie (Ezüst tartósság).
* Egyetlen nyilvános IP-erőforrás szabványos termékváltozat használatával.
* Egyetlen terheléselosztó erőforrás standard termékváltozat használatával.
* Az alhálózat által hivatkozott NSG, amelyben a virtuálisgép-méretezési készletek üzembe helyezése.

>[!NOTE]
> A virtuális gép méretezési készletének egyetlen elhelyezési csoport tulajdonságát igaz értékre kell állítani, mivel a Service Fabric nem támogatja a zónákra kiterjedő egyetlen virtuálisgép-méretezési csoportot.

 ![Az Azure Service Fabric rendelkezésre állási zóna architektúrája][sf-architecture]

## <a name="networking-requirements"></a>Hálózati követelmények
### <a name="public-ip-and-load-balancer-resource"></a>Nyilvános IP- és terheléselosztó-erőforrás
Ahhoz, hogy a zones tulajdonság egy virtuálisgép-méretezési készlet erőforrás, a terheléselosztó és az *Standard* IP-erőforrás által hivatkozott, hogy a virtuálisgép-méretezési csoport egyaránt szabványos termékváltozatot kell használnia. Ha egy terheléselosztót vagy IP-erőforrást hoz létre a Termékváltozat tulajdonság nélkül, akkor hozzon létre egy alapszintű termékváltozatot, amely nem támogatja a rendelkezésre állási zónákat. A szabványos termékváltozat terheléselosztó alapértelmezés szerint blokkolja az összes külső forgalmat; a külső forgalom engedélyezéséhez nsg-t kell telepíteni az alhálózatra.

```json
{
    "apiVersion": "2018-11-01",
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[concat('LB','-', parameters('clusterName')]",
    "location": "[parameters('computeLocation')]",
    "sku": {
        "name": "Standard"
    }
}
```

```json
{
    "apiVersion": "2018-11-01",
    "type": "Microsoft.Network/loadBalancers",
    "name": "[concat('LB','-', parameters('clusterName')]", 
    "location": "[parameters('computeLocation')]",
    "dependsOn": [
        "[concat('Microsoft.Network/networkSecurityGroups/', concat('nsg', parameters('subnet0Name')))]"
    ],
    "properties": {
        "addressSpace": {
            "addressPrefixes": [
                "[parameters('addressPrefix')]"
            ]
        },
        "subnets": [
        {
            "name": "[parameters('subnet0Name')]",
            "properties": {
                "addressPrefix": "[parameters('subnet0Prefix')]",
                "networkSecurityGroup": {
                "id": "[resourceId('Microsoft.Network/networkSecurityGroups', concat('nsg', parameters('subnet0Name')))]"
              }
            }
          }
        ]
    },
    "sku": {
        "name": "Standard"
    }
}
```

>[!NOTE]
> A nyilvános IP-és terheléselosztó-erőforrásokon nem lehet helyben módosítani a termékváltozatot. Ha olyan meglévő erőforrásokból, amelyek alapszintű termékváltozatokkal rendelkeznek, tekintse meg a jelen cikk áttelepítési szakaszát.

### <a name="virtual-machine-scale-set-nat-rules"></a>Virtuálisgép-méretezési készlet NAT-szabályok
A bejövő NAT-szabályok terheléselosztó szabályainak meg kell egyezniük a virtuálisgép-méretezési csoport NAT-készletéivel. Minden virtuálisgép-méretezési készletnek egyedi bejövő NAT-készletpel kell rendelkeznie.

```json
{
"inboundNatPools": [
    {
        "name": "LoadBalancerBEAddressNatPool0",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "50999",
            "frontendPortRangeStart": "50000",
            "protocol": "tcp"
        }
    },
    {
        "name": "LoadBalancerBEAddressNatPool1",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "51999",
            "frontendPortRangeStart": "51000",
            "protocol": "tcp"
        }
    },
    {
        "name": "LoadBalancerBEAddressNatPool2",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "52999",
            "frontendPortRangeStart": "52000",
            "protocol": "tcp"
        }
    }
    ]
}
```

### <a name="standard-sku-load-balancer-outbound-rules"></a>Szabványos termékváltozat terheléselosztó kimenő szabályai
A standard terheléselosztó és a szabványos nyilvános IP-cím új képességeket és különböző viselkedéseket vezet be a kimenő kapcsolatokhoz az alapszintű termékkészletek használatával összehasonlítva. Ha azt szeretné, kimenő kapcsolat, amikor a standard skus-ok, explicit módon meg kell határoznia azt vagy a standard nyilvános IP-címek vagy standard nyilvános terheléselosztó. További információ: [Kimenő kapcsolatok](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#snatexhaust) és [az Azure Standard Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview).

>[!NOTE]
> A standard sablon egy NSG-re hivatkozik, amely alapértelmezés szerint lehetővé teszi az összes kimenő forgalmat. A bejövő forgalom a Service Fabric-kezelési műveletekhez szükséges portokra korlátozódik. Az NSG-szabályok az Ön igényeinek megfelelően módosíthatók.

### <a name="enabling-zones-on-a-virtual-machine-scale-set"></a>Zónák engedélyezése virtuálisgép-méretezési csoportban
Zóna engedélyezéséhez egy virtuálisgép-méretezési csoportban a következő három értéket kell tartalmaznia a virtuálisgép méretezési csoport erőforrásában.

* Az első érték a **zones** tulajdonság, amely meghatározza, hogy a virtuálisgép-méretezési csoport melyik rendelkezésre állási zónába lesz telepítve.
* A második érték a "singlePlacementGroup" tulajdonság, amelyet igaz értékre kell állítani.
* A harmadik érték a Service Fabric virtuálisgép-méretezési csoport bővítményében található "faultDomainOverride" tulajdonság. A tulajdonság értékének tartalmaznia kell azt a régiót és zónát, amelyben ez a virtuálisgép-méretezési készlet kerül. Példa: "faultDomainOverride": "eastus/az1" Minden virtuálisgép-méretezési csoport erőforrásait ugyanabban a régióban kell elhelyezni, mert az Azure Service Fabric-fürtök nem rendelkeznek régióközi támogatással.

```json
{
    "apiVersion": "2018-10-01",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType1Name')]",
    "location": "[parameters('computeLocation')]",
    "zones": ["1"],
    "properties": {
        "singlePlacementGroup": "true",
    },
    "virtualMachineProfile": {
    "extensionProfile": {
    "extensions": [
    {
    "name": "[concat(parameters('vmNodeType1Name'),'_ServiceFabricNode')]",
    "properties": {
        "type": "ServiceFabricNode",
        "autoUpgradeMinorVersion": false,
        "publisher": "Microsoft.Azure.ServiceFabric.Test",
        "settings": {
            "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
            "nodeTypeRef": "[parameters('vmNodeType1Name')]",
            "dataPath": "D:\\\\SvcFab",
            "durabilityLevel": "Silver",
            "certificate": {
                "thumbprint": "[parameters('certificateThumbprint')]",
                "x509StoreName": "[parameters('certificateStoreValue')]"
            },
            "systemLogUploadSettings": {
                "Enabled": true
            },
            "faultDomainOverride": "eastus/az1"
        },
        "typeHandlerVersion": "1.0"
    }
}
```

### <a name="enabling-multiple-primary-node-types-in-the-service-fabric-cluster-resource"></a>Több elsődleges csomóponttípus engedélyezése a Service Fabric fürterőforrásában
Ha egy vagy több csomóponttípust elsődlegesként szeretne beállítani egy fürterőforrásban, állítsa az "isPrimary" tulajdonságot "true"-ra. Service Fabric-fürt üzembe helyezésekor a rendelkezésre állási zónák között, három csomóponttípust kell rendelkeznie különböző zónákban.

```json
{
    "reliabilityLevel": "Platinum",
    "nodeTypes": [
    {
        "name": "[parameters('vmNodeType0Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt0applicationEndPort')]",
            "startPort": "[parameters('nt0applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt0fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt0ephemeralEndPort')]",
            "startPort": "[parameters('nt0ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt0fabricHttpGatewayPort')]",
        "isPrimary": true,
        "vmInstanceCount": "[parameters('nt0InstanceCount')]"
    },
    {
        "name": "[parameters('vmNodeType1Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt1applicationEndPort')]",
            "startPort": "[parameters('nt1applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt1fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt1ephemeralEndPort')]",
            "startPort": "[parameters('nt1ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt1fabricHttpGatewayPort')]",
        "isPrimary": true,
        "vmInstanceCount": "[parameters('nt1InstanceCount')]"
    },
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
        "isPrimary": true,
        "vmInstanceCount": "[parameters('nt2InstanceCount')]"
    }
    ],
}
```

## <a name="migrate-to-using-availability-zones-from-a-cluster-using-a-basic-sku-load-balancer-and-a-basic-sku-ip"></a>Áttérés a rendelkezésre állási zónák egy fürtből egy egyszerű termékváltozat terheléselosztó és egy egyszerű termékváltozat IP
Egy fürt áttelepítéséhez, amely egy terheléselosztót és ip-címet használt egy alapvető termékváltozattal, először létre kell hoznia egy teljesen új terheléselosztót és IP-erőforrást a szabványos termékváltozat használatával. Ezek az erőforrások nem frissíthetők helyben.

Az új LB és IP kell hivatkozni az új kereszt-rendelkezésre állási zóna csomópont típusok, amelyek et szeretne használni. A fenti példában három új virtuálisgép-méretezési készlet erőforrás tadott hozzá az 1,2 és 3 zónákban. Ezek a virtuálisgép-méretezési csoportok az újonnan létrehozott LB és IP-címre hivatkoznak, és elsődleges csomóponttípusokként vannak megjelölve a Service Fabric fürterőforrásban.

Először is hozzá kell adnia az új erőforrásokat a meglévő Erőforrás-kezelő sablonhoz. Ezek a források a következők:
* Nyilvános IP-erőforrás standard termékváltozat használatával.
* A terheléselosztó erőforrás szabványos termékváltozat használatával.
* Az alhálózat által hivatkozott NSG, amelyben a virtuálisgép-méretezési készletek üzembe helyezése.
* Három csomóponttípus elsődlegesként megjelölve.
    * Minden csomóponttípust le kell képeznie a különböző zónákban található saját virtuálisgép-méretezési készlethez.
    * Minden virtuálisgép-méretezési csoportnak legalább öt csomópontnak kell lennie (Ezüst tartósság).

Ezekre az erőforrásokra példa a [mintasablonban](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/10-VM-Ubuntu-2-NodeType-Secure)található.

```powershell
New-AzureRmResourceGroupDeployment `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $Template `
    -TemplateParameterFile $Parameters
```

Miután az erőforrások telepítése befejeződött, megkezdheti az elsődleges csomóponttípus csomópontjainak letiltását az eredeti fürtből. A csomópontok letiltása után a rendszerszolgáltatások áttelepülnek a fenti lépésben üzembe helyezett új elsődleges csomóponttípusra.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint $ClusterName `
    -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My 

Write-Host "Connected to cluster"

$nodeNames = @("_nt0_0", "_nt0_1", "_nt0_2", "_nt0_3", "_nt0_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames) {
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}
```

Miután a csomópontok mind le vannak tiltva, a rendszerszolgáltatások az elsődleges csomóponttípuson fognak futni, amely zónák között van elosztva. Ezután eltávolíthatja a letiltott csomópontokat a fürtből. A csomópontok eltávolítása után eltávolíthatja az eredeti IP-, terheléselosztó és virtuálisgép-méretezési készlet erőforrásait.

```powershell
foreach($name in $nodeNames){
    # Remove the node from the cluster
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}

$scaleSetName="nt0"
Remove-AzureRmVmss -ResourceGroupName $groupname -VMScaleSetName $scaleSetName -Force

$lbname="LB-cluster-nt0"
$oldPublicIpName="LBIP-cluster-0"
$newPublicIpName="LBIP-cluster-1"

Remove-AzureRmLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force
Remove-AzureRmPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force
```

Ezután távolítsa el az ezekre az erőforrásokra mutató hivatkozásokat a telepített Erőforrás-kezelő sablonból.

Az utolsó lépés a DNS-név és a nyilvános IP frissítése.

```powershell
$oldprimaryPublicIP = Get-AzureRmPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $groupname
$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel
$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn

Remove-AzureRmLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force
Remove-AzureRmPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force

$PublicIP = Get-AzureRmPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $groupname
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzureRmPublicIpAddress -PublicIpAddress $PublicIP

```

[sf-architecture]: ./media/service-fabric-cross-availability-zones/sf-cross-az-topology.png
