---
title: Rendelkezésre állási zónák között egy Azure Service Fabric-fürt üzembe helyezése |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre az Azure Service Fabric-fürt rendelkezésre állási zónák között.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/25/2019
ms.author: pepogors
ms.openlocfilehash: b664c3d655ab45c89a65a0aea31622f57ddc8d9e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65077455"
---
# <a name="deploy-an-azure-service-fabric-cluster-across-availability-zones"></a>Rendelkezésre állási zónák között egy Azure Service Fabric-fürt üzembe helyezése
Az Azure-beli rendelkezésre állási zónák a magas rendelkezésre állást kínál, amely megvédi alkalmazásait és adatait az adatközpontok meghibásodásai. A rendelkezésre állási zónában az egy egyedi fizikai hely, független áramellátással, felszerelt, hűtés és hálózati egy Azure-régióban.

A Service Fabric-fürtök rendelkezésre állási zónák között a zónához rögzített csomóponttípusok üzembe helyezésével span támogatja. Ez biztosítja a magas rendelkezésre állás az alkalmazások. Azure-beli rendelkezésre állási zónák csak érhetők el minden régióban. További információkért lásd: [Azure rendelkezésre állási zónák áttekintésének](https://docs.microsoft.com/azure/availability-zones/az-overview).

Mintasablonok érhetők el: [A Service Fabric adatbázisközi rendelkezésre állási zóna sablon](https://github.com/Azure-Samples/service-fabric-cluster-templates)

## <a name="recommended-topology-for-primary-node-type-of-azure-service-fabric-clusters-spanning-across-availability-zones"></a>Ajánlott az elsődleges csomópont típusú rendelkezésre állási zónák között átfedés Azure Service Fabric-fürtök topológiája
Service Fabric-fürt rendelkezésre állási zónák között elosztott biztosítja, hogy a fürt állapotának magas rendelkezésre állás. Service Fabric-fürt span zónák között, létre kell hoznia egy elsődleges csomóponttípus minden rendelkezésre állási zónában az a régió támogatja. Ez lesz magcsomópontok egyenletes elosztása az elsődleges csomóponttípusok száma mindegyik.

Az elsődleges csomóponttípushoz a javasolt topológiát az alábbi erőforrásokat igényel:

* A fürt megbízhatósági szintje Platinum.
* Három csomóponttípusok megjelölni elsődlegesként.
    * Mindegyik csomóponttípus hozzá kell rendelni a saját virtuálisgép-méretezési eltérő zónában található.
    * Minden egyes virtuálisgép-méretezési csoportot kell rendelkeznie legalább öt csomópont (Silver szintű tartósságot).
* Egyetlen nyilvános IP-erőforrást használ, a Standard Termékváltozat.
* Egyetlen terheléselosztó erőforrást használ, a Standard Termékváltozat.
* Egy NSG-t az alhálózatot, amelyben a virtuálisgép-méretezési csoportok üzembe hivatkozik.

>[!NOTE]
> A virtuálisgép-méretezési egyetlen elhelyezési csoport tulajdonság igaz értékre, mivel a Service Fabric nem támogatja a zónák kiterjedő egyetlen virtuális gép méretezési értékre kell állítani.

 ![Az Azure Service Fabric rendelkezésre állási zónában architektúra][sf-architecture]

## <a name="networking-requirements"></a>Hálózati követelmények
### <a name="public-ip-and-load-balancer-resource"></a>Nyilvános IP-cím és a terheléselosztó-erőforráshoz
Ahhoz, hogy a virtuálisgép-méretezési csoportot a tulajdonsága erőforrást, a terheléselosztó és az IP-erőforrás hivatkozik rá, hogy a virtuálisgép-méretezési zónák is kell használnia egy *Standard* Termékváltozat. Egy terheléselosztó vagy IP-erőforrást az SKU tulajdonság nélkül létrehozása hoz létre egy alapszintű Termékváltozat, amely nem támogatja a rendelkezésre állási zónák. Standard Termékváltozatú terheléselosztó kívülről származó összes forgalmat blokkolja a alapértelmezett; Ahhoz, hogy a forgalom kívülről, telepíteni kell egy NSG-t az alhálózathoz.

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
> Nem alkalmas a nyilvános IP-cím és a load balancer-erőforrások egy helyi módosítása termékváltozat tennie. A meglévő erőforrások, amelyek rendelkeznek egy alapszintű Termékváltozat telepít át, ha ez a cikk a migrálás című szakaszában talál.

### <a name="virtual-machine-scale-set-nat-rules"></a>Virtuálisgép-méretezési NAT-szabályok
A terheléselosztó bejövő NAT-szabályok egyeznie kell a NAT-készletek a virtuálisgép-méretezési csoportban. Minden egyes virtuálisgép-méretezési rendelkeznie kell egy egyedi bejövő NAT-készletet.

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

### <a name="standard-sku-load-balancer-outbound-rules"></a>Standard Termékváltozatú terheléselosztó kimenő szabályok
Standard Load Balancer és a Standard nyilvános IP-cím bevezetni új funkciókat vehetnek igénybe, és különböző beállításokat a kimenő kapcsolatot, ha alapszintű SKU-k használatához képest. Kimenő kapcsolat azt szeretné, ha Standard termékváltozatok dolgozik, ha explicit módon definiálnia kell azt a Standard nyilvános IP-címeket vagy nyilvános terheléselosztót. További információkért lásd: [kimenő kapcsolatok](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#snatexhaust) és [Azure Standard Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview).

>[!NOTE]
> A standard sablon hivatkozik egy NSG-t, amely lehetővé teszi minden kimenő forgalom alapértelmezés szerint. Bejövő forgalom a Service Fabric-management műveletekhez szükséges portokról korlátozódik. Az NSG-szabályok az igényeknek is módosítható.

### <a name="enabling-zones-on-a-virtual-machine-scale-set"></a>Állítsa be a virtuálisgép-méretezési csoportot zónák engedélyezése
A zóna engedélyezéséhez állítsa be, a virtuálisgép-méretezési csoportot tartalmaznia kell a következő három érték található a virtuálisgép-méretezési készlet erőforrás.

* Az első érték a **zónák** tulajdonság, amely meghatározza, melyik rendelkezésre állási zónában, a virtuális gép méretezési telepítve lesz.
* A második érték a "singlePlacementGroup" tulajdonság, amely állítsa igaz értékre.
* A harmadik értéke a Service Fabric virtuálisgép-méretezési csoport bővítményének "faultDomainOverride" tulajdonsága. Ez a tulajdonság értéke tartalmaznia kell a régió és zóna, amelyben a virtuálisgép-méretezési kerülnek. Példa: "faultDomainOverride": "eastus/az1" minden virtuális gép méretezési erőforrások ugyanabban a régióban kell elhelyezni, mert nem rendelkezik Azure Service Fabric-fürtök platformfüggetlen régió támogatása.

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

### <a name="enabling-multiple-primary-node-types-in-the-service-fabric-cluster-resource"></a>A Service Fabric-fürt erőforrás több elsődleges csomóponttípusok engedélyezése
Egy vagy több csomópont típus beállítása a fürt erőforrásai elsődlegesként, állítsa be a "isPrimary" tulajdonságot "true". A Service Fabric-fürt üzembe helyezése a rendelkezésre állási zónák között, amikor különböző zónák három csomóponttípusok kell rendelkeznie.

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

## <a name="migrate-to-using-availability-zones-from-a-cluster-using-a-basic-sku-load-balancer-and-a-basic-sku-ip"></a>A rendelkezésre állási zónák használatára a fürthöz egy alapszintű Termékváltozatú terheléselosztó és a egy alapszintű Termékváltozat IP-cím használata
A fürt, amely használta egy terheléselosztó és az IP egy alapszintű termékváltozatú, először létre kell hoznia egy teljesen új terheléselosztó és az IP-cím erőforrás a standard Termékváltozat használatával. Nem alkalmas ezen erőforrások helyben frissíteni.

Az új LB és IP-az új közötti rendelkezésre állási zónában csomópont típusa, amely a használni kívánt lehet hivatkozni. A példában a fenti három új virtuálisgép-méretezési csoport erőforrásainak zónákban 1,2 és 3 bővült. Ezen virtuálisgép-méretezési csoport állítja be a hivatkozást az újonnan létrehozott LB és IP- és a Service Fabric fürterőforrás az elsődleges csomóponttípusok vannak megjelölve.

Ha szeretné elkezdeni, szüksége lesz az új erőforrások hozzáadása a meglévő Resource Manager-sablon. Ilyen erőforrások többek között:
* Nyilvános IP-erőforrást használ, a Standard Termékváltozat.
* Használatával a Standard Termékváltozatú terheléselosztó erőforrást.
* Egy NSG-t az alhálózatot, amelyben a virtuálisgép-méretezési csoportok üzembe hivatkozik.
* Három csomóponttípusok megjelölni elsődlegesként.
    * Mindegyik csomóponttípus hozzá kell rendelni a saját virtuálisgép-méretezési eltérő zónában található.
    * Minden egyes virtuálisgép-méretezési csoportot kell rendelkeznie legalább öt csomópont (Silver szintű tartósságot).

Ezeket az erőforrásokat egy példát találhat a [mintasablon](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/10-VM-Ubuntu-2-NodeType-Secure).

```powershell
New-AzureRmResourceGroupDeployment `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $Template `
    -TemplateParameterFile $Parameters
```

Miután az erőforrások végzett üzembe helyezéséhez, tiltsa le az elsődleges csomóponttípushoz, az eredeti fürtben lévő csomópontok megkezdéséhez. A csomópontok le vannak tiltva, mert a helyrendszeri szolgáltatások az új elsődleges csomóponttípushoz, amely korábban telepítve lett az előző lépés során migrálja.

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

Miután a csomópontok minden letiltva, a rendszer szolgáltatások futni fog az elsődleges csomóponttípushoz, amely zónák között megoszlik. Ezt követően eltávolíthatja a letiltott csomópontot a fürtből. Miután a csomópontok el lesznek távolítva, eltávolíthatja az eredeti IP, a terheléselosztó, és virtuális gép méretezési erőforrások.

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

Ezután távolítsa el ezeket az erőforrásokat a Resource Manager-sablonnal üzembe helyezett kellett a hivatkozásokat.

Az utolsó lépés magában foglalja a DNS-nevet és a nyilvános IP-cím frissítése.

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
