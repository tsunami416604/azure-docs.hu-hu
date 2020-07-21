---
title: Fürt üzembe helyezése Availability Zoneson keresztül
description: Ismerje meg, hogyan hozhat létre Azure Service Fabric-fürtöt a Availability Zones között.
author: peterpogorski
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: pepogors
ms.openlocfilehash: d763511032ebff9116702b1f649751a4b7b52afd
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86518996"
---
# <a name="deploy-an-azure-service-fabric-cluster-across-availability-zones"></a>Azure Service Fabric-fürt üzembe helyezése Availability Zones
Az Azure-beli Availability Zones magas rendelkezésre állású ajánlat, amely védelmet nyújt alkalmazásai és adatai számára az adatközpont hibáiból. A rendelkezésre állási zónák egy Azure-régión belüli, független energiaellátással, hűtéssel és hálózatkezeléssel ellátott egyedi fizikai helyek.

Service Fabric támogatja a különböző Availability Zonesokra kiterjedő fürtöket, ha olyan csomópont-típusokat telepít, amelyek meghatározott zónákra vannak rögzítve. Ez biztosítja az alkalmazások magas rendelkezésre állását. Azure Availability Zones csak a kiválasztott régiókban érhetők el. További információ: [Azure Availability Zones Overview (áttekintés](../availability-zones/az-overview.md)).

A sablonok elérhetők: [Service Fabric több rendelkezésre állási zóna sablonja](https://github.com/Azure-Samples/service-fabric-cluster-templates)

## <a name="recommended-topology-for-primary-node-type-of-azure-service-fabric-clusters-spanning-across-availability-zones"></a>Ajánlott topológia az Azure-Service Fabric fürtök elsődleges csomópont-típusához Availability Zones
A Availability Zones között elosztott Service Fabric-fürtök biztosítják a fürt állapotának magas rendelkezésre állását. Ha Service Fabric fürtöt a zónák között, a régió által támogatott összes rendelkezésre állási zónában létre kell hoznia egy elsődleges csomópont-típust. Ez egyenletesen osztja el a magok csomópontjait az egyes elsődleges csomópontok típusai között.

Az elsődleges csomópont típusához javasolt topológiához az alábbi erőforrások szükségesek:

* A fürt megbízhatósági szintje Platinum értékre van állítva.
* Három csomópontos típus van megjelölve elsődlegesként.
    * Minden csomópont-típust hozzá kell rendelni a saját virtuálisgép-méretezési csoporthoz, amely különböző zónákban található.
    * Minden virtuálisgép-méretezési csoportnak legalább öt csomóponttal kell rendelkeznie (ezüst tartósság).
* Egyetlen nyilvános IP-erőforrás szabványos SKU használatával.
* Egyetlen Load Balancer erőforrás szabványos SKU használatával.
* Az alhálózat által hivatkozott NSG, amelyben üzembe helyezi a virtuálisgép-méretezési csoportokat.

>[!NOTE]
> A virtuálisgép-méretezési csoport egyhelyes csoportok tulajdonságát igaz értékre kell állítani, mivel Service Fabric nem támogatja egyetlen virtuálisgép-méretezési csoportot, amely zónákra terjed ki.

 ![Azure Service Fabric rendelkezésre állási zóna architektúrája][sf-architecture]

## <a name="networking-requirements"></a>Hálózati követelmények
### <a name="public-ip-and-load-balancer-resource"></a>Nyilvános IP-cím és Load Balancer erőforrás
Ha engedélyezni szeretné a zónák tulajdonságot egy virtuálisgép-méretezési csoport erőforrásán, a terheléselosztó és az adott virtuálisgép-méretezési csoport által hivatkozott IP-erőforrásnak is *szabványos* SKU-t kell használnia. Egy terheléselosztó vagy IP-erőforrás az SKU tulajdonság nélkül való létrehozása egy alapszintű SKU-t hoz létre, amely nem támogatja a Availability Zones. A standard SKU Load Balancer alapértelmezés szerint letiltja a kívülről érkező összes forgalmat. a külső forgalom engedélyezéséhez telepíteni kell egy NSG az alhálózatra.

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
> A nyilvános IP-címek és a terheléselosztó erőforrásainak helybeni módosítása nem lehetséges. Ha egy alapszintű SKU-val rendelkező meglévő erőforrásról végez áttelepítést, tekintse meg a jelen cikk áttelepítés című szakaszát.

### <a name="virtual-machine-scale-set-nat-rules"></a>Virtuálisgép-méretezési csoport NAT-szabályai
A terheléselosztó bejövő NAT-szabályainak meg kell egyezniük a virtuálisgép-méretezési csoport NAT-készletével. Minden virtuálisgép-méretezési csoportnak rendelkeznie kell egy egyedi bejövő NAT-készlettel.

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

### <a name="standard-sku-load-balancer-outbound-rules"></a>Szabványos SKU Load Balancer kimenő szabályok
A standard Load Balancer és a standard nyilvános IP-címek új képességeket és különböző viselkedéseket vezetnek be a kimenő kapcsolatokhoz az alapszintű SKU-hoz képest. Ha standard SKU-kal dolgozik a kimenő kapcsolaton, explicit módon meg kell határoznia a standard nyilvános IP-címeket vagy a standard nyilvános Load Balancer. További információ: [Kimenő kapcsolatok](../load-balancer/load-balancer-outbound-connections.md) és [Azure standard Load Balancer](../load-balancer/load-balancer-overview.md).

>[!NOTE]
> A standard sablon egy olyan NSG hivatkozik, amely alapértelmezés szerint engedélyezi az összes kimenő forgalmat. A bejövő forgalom a Service Fabric felügyeleti műveletekhez szükséges portokra korlátozódik. A NSG-szabályok módosíthatók a követelmények teljesítése érdekében.

>[!NOTE]
> A standard SKU-SLB használó Service Fabric fürtöknek biztosítaniuk kell, hogy minden csomópont-típushoz olyan szabály legyen, amely engedélyezi a kimenő forgalmat az 443-as porton. Ez a fürt beállításának befejezéséhez szükséges, és az ilyen szabályok nélküli központi telepítések sikertelenek lesznek.


### <a name="enabling-zones-on-a-virtual-machine-scale-set"></a>Zónák engedélyezése virtuálisgép-méretezési csoportokban
Ha egy zónát szeretne engedélyezni egy virtuálisgép-méretezési csoporton, a következő három értéket kell tartalmaznia a virtuálisgép-méretezési csoport erőforrásaiban.

* Az első érték a **Zones** tulajdonság, amely megadja, hogy melyik rendelkezésre állási zónát telepíti a rendszer a virtuálisgép-méretezési csoport számára.
* A második érték a "singlePlacementGroup" tulajdonság, amelyet igaz értékre kell beállítani.
* A harmadik érték a Service Fabric virtuálisgép-méretezési csoport bővítményének "faultDomainOverride" tulajdonsága. Ennek a tulajdonságnak az értékének tartalmaznia kell azt a régiót és zónát, amelyben a virtuálisgép-méretezési csoport el lesz helyezve. Példa: "faultDomainOverride": "eastus/az1" a virtuálisgép-méretezési csoport összes erőforrását ugyanabba a régióba kell helyezni, mert az Azure Service Fabric-fürtök nem rendelkeznek több régiós támogatással.

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
        "publisher": "Microsoft.Azure.ServiceFabric",
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

### <a name="enabling-multiple-primary-node-types-in-the-service-fabric-cluster-resource"></a>Több elsődleges csomópont típusának engedélyezése a Service Fabric fürterőforrás
Ha egy vagy több csomópontot szeretne elsődlegesként beállítani egy fürt erőforrásában, állítsa a "isPrimary" tulajdonságot "true" értékre. Service Fabric-fürt Availability Zones-beli üzembe helyezése során három csomópont-típust kell tartalmaznia különálló zónákban.

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

## <a name="migrate-to-using-availability-zones-from-a-cluster-using-a-basic-sku-load-balancer-and-a-basic-sku-ip"></a>Migrálás a fürt Availability Zones használatára egy alapszintű SKU Load Balancer és egy alapszintű SKU IP-cím használatával
Ha olyan fürtöt szeretne áttelepíteni, amely egy alapszintű SKU-val Load Balancer és IP-címet használ, először létre kell hoznia egy teljesen új Load Balancer és IP-erőforrást a szabványos SKU használatával. Ezeket az erőforrásokat helyben nem lehet frissíteni.

Az új LB-t és IP-címet a használni kívánt új rendelkezésre állási zónák csomópont-típusaira kell hivatkozni. A fenti példában három új virtuálisgép-méretezési csoport erőforrásai lettek hozzáadva az 1., 2. és 3. zónához. Ezek a virtuálisgép-méretezési csoportok az újonnan létrehozott LB-re és IP-re hivatkoznak, és elsődleges csomópont-típusként vannak megjelölve a Service Fabric fürterőforrás számára.

A kezdéshez hozzá kell adnia az új erőforrásokat a meglévő Resource Manager-sablonhoz. Ezek az erőforrások a következők:
* A standard SKU-t használó nyilvános IP-erőforrás.
* A standard SKU-t használó Load Balancer erőforrás.
* Az alhálózat által hivatkozott NSG, amelyben üzembe helyezi a virtuálisgép-méretezési csoportokat.
* Három csomópontos típus van megjelölve elsődlegesként.
    * Minden csomópont-típust hozzá kell rendelni a saját virtuálisgép-méretezési csoporthoz, amely különböző zónákban található.
    * Minden virtuálisgép-méretezési csoportnak legalább öt csomóponttal kell rendelkeznie (ezüst tartósság).

Ilyen erőforrások például a [minta sablonban](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/10-VM-Ubuntu-2-NodeType-Secure)találhatók.

```powershell
New-AzureRmResourceGroupDeployment `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $Template `
    -TemplateParameterFile $Parameters
```

Az erőforrások telepítésének befejezése után megkezdheti a csomópontok letiltását az elsődleges csomópont típusa alapján az eredeti fürtből. Mivel a csomópontok le vannak tiltva, a rendszerszolgáltatások a fenti lépésben üzembe helyezett új elsődleges csomópont-típusra lesznek áttelepítve.

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

Ha a csomópontok le vannak tiltva, a rendszerszolgáltatások az elsődleges csomópont-típuson futnak, amely a zónák között oszlik meg. Ezután eltávolíthatja a letiltott csomópontokat a fürtből. A csomópontok eltávolítása után eltávolíthatja az eredeti IP-címet, a Load Balancer és a virtuálisgép-méretezési csoport erőforrásait.

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

Ezután el kell távolítania az erőforrásokra mutató hivatkozásokat a telepített Resource Manager-sablonból.

Az utolsó lépés a DNS-név és a nyilvános IP-cím frissítését is magában foglalja.

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
