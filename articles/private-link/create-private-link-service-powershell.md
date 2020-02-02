---
title: Azure Private link szolgáltatás létrehozása a Azure PowerShell használatával | Microsoft Docs
description: Ismerje meg, hogyan hozhat létre Azure Private link Service-t a Azure PowerShell használatával
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 225ae9d07cc6df2fa809e250083ee6007ab2f945
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2020
ms.locfileid: "76932082"
---
# <a name="create-a-private-link-service-using-azure-powershell"></a>Privát kapcsolati szolgáltatás létrehozása Azure PowerShell használatával
Ez a cikk bemutatja, hogyan hozhat létre egy privát link szolgáltatást az Azure-ban Azure PowerShell használatával.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, ez a cikk a legújabb Azure PowerShell modul verzióját igényli. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

A privát hivatkozás létrehozása előtt létre kell hoznia egy erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)használatával. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot a *WestCentralUS* helyen:

```azurepowershell
$location = "westcentralus"
$rgName = "myResourceGroup"
New-AzResourceGroup `
  -ResourceGroupName $rgName `
  -Location $location
```
## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása
Hozzon létre egy virtuális hálózatot a privát kapcsolathoz a [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). A következő példában létrehozunk egy *myvnet* nevű virtuális hálózatot a frontend (*frontendSubnet*), a háttér (*backendsubnet változóhoz*), a Private link (*otherSubnet*) alhálózattal:

```azurepowershell
$virtualNetworkName = "myvnet"


# Create subnet config

$frontendSubnet = New-AzVirtualNetworkSubnetConfig `
-Name frontendSubnet `
-AddressPrefix "10.0.1.0/24"  

$backendSubnet = New-AzVirtualNetworkSubnetConfig `
-Name backendSubnet `
-AddressPrefix "10.0.2.0/24"  

$otherSubnet = New-AzVirtualNetworkSubnetConfig `
-Name otherSubnet `
-AddressPrefix "10.0.3.0/24" `
-PrivateLinkServiceNetworkPolicies "Disabled" 

# Create the virtual network
$vnet = New-AzVirtualNetwork `
-Name $virtualNetworkName `
-ResourceGroupName $rgName `
-Location $location `
-AddressPrefix "10.0.0.0/16" `
-Subnet $frontendSubnet,$backendSubnet,$otherSubnet 
```
## <a name="create-internal-load-balancer"></a>Belső Load Balancer létrehozása
Hozzon létre egy belső standard Load Balancer a [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer). Az alábbi példa belső standard Load Balancer hoz létre az előző lépésekben létrehozott előtéri IP-konfiguráció, mintavétel, szabály és háttér-készlet használatával:

```azurepowershell

$lbBackendName = "LB-backend" 
$lbFrontName = "LB-frontend" 
$lbName = "lb"
 
#Create Internal Load Balancer
$frontendIP = New-AzLoadBalancerFrontendIpConfig -Name $lbFrontName -PrivateIpAddress 10.0.1.5 -SubnetId $vnet.subnets[0].Id 
$beaddresspool= New-AzLoadBalancerBackendAddressPoolConfig -Name $lbBackendName 
$probe = New-AzLoadBalancerProbeConfig -Name 'myHealthProbe' -Protocol Http -Port 80 `
  -RequestPath / -IntervalInSeconds 360 -ProbeCount 5
$rule = New-AzLoadBalancerRuleConfig -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool  $beaddresspool -Probe $probe -Protocol Tcp -FrontendPort 80 -BackendPort 80
$NRPLB = New-AzLoadBalancer -ResourceGroupName $rgName -Name $lbName -Location $location -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $probe -LoadBalancingRule $rule -Sku Standard 
```
## <a name="create-a-private-link-service"></a>Privát kapcsolati szolgáltatás létrehozása
Hozzon létre egy privát link Service-t a [New-AzPrivateLinkService](/powershell/module/az.network/new-azloadbalancer).  Ez a példa létrehoz egy *myPLS* nevű privát hivatkozás szolgáltatást a *myResourceGroup*nevű erőforráscsoport standard Load Balancer használatával. 
```azurepowershell

$plsIpConfigName = "PLS-ipconfig" 
$plsName = "pls"
$peName = "pe" 
  
$IPConfig = New-AzPrivateLinkServiceIpConfig `
-Name $plsIpConfigName `
-Subnet $vnet.subnets[2] `
-PrivateIpAddress 10.0.3.5 

$fe = Get-AzLoadBalancer -Name $lbName | Get-AzLoadBalancerFrontendIpConfig 

$privateLinkService = New-AzPrivateLinkService `
-ServiceName $plsName `
-ResourceGroupName $rgName `
-Location $location `
-LoadBalancerFrontendIpConfiguration $frontendIP `
-IpConfiguration $IPConfig 
```

### <a name="get-private-link-service"></a>Privát link szolgáltatás beolvasása
A [Get-AzPrivateLinkService által](/powershell/module/az.network/get-azprivatelinkservice) a Private link Service szolgáltatással kapcsolatos részletek a következőképpen olvashatók:

```azurepowershell
$pls = Get-AzPrivateLinkService -Name $plsName -ResourceGroupName $rgName 
```

Ebben a szakaszban a privát kapcsolati szolgáltatás sikeresen létrejött, és készen áll a forgalom fogadására. Vegye figyelembe, hogy a fenti példa csak a Private link Service PowerShell használatával történő létrehozását mutatja be.  Nem konfiguráltuk a terheléselosztó-háttér készleteit vagy bármely alkalmazást a háttér-készleteken a forgalom figyelésére. Ha meg szeretné tekinteni a végpontok közötti forgalmat, javasoljuk, hogy az alkalmazást a standard Load Balancer mögött konfigurálja. 

A következő lépésben bemutatjuk, hogyan képezhető le a szolgáltatás a különböző VNet lévő privát végpontokra a PowerShell használatával. Ez a példa a privát végpont létrehozására és a fent létrehozott privát kapcsolati szolgáltatáshoz való csatlakozásra korlátozódik. Létrehozhat Virtual Machineseket a Virtual Network a forgatókönyv létrehozásához a privát végpont felé irányuló adatforgalom küldéséhez/fogadásához. 

## <a name="create-a-private-endpoint"></a>Privát végpont létrehozása
### <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása
Hozzon létre egy virtuális hálózatot a privát végponthoz a [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Ez a példa létrehoz egy *vnetPE* nevű virtuális hálózatot az erőforráscsoport nevű *myResourceGroup*:
 
```azurepowershell
$virtualNetworkNamePE = "vnetPE"
 
# Create VNet for private endpoint
$peSubnet = New-AzVirtualNetworkSubnetConfig `
-Name peSubnet `
-AddressPrefix "11.0.1.0/24" `
-PrivateEndpointNetworkPolicies "Disabled" 

$vnetPE = New-AzVirtualNetwork `
-Name $virtualNetworkNamePE `
-ResourceGroupName $rgName `
-Location $location `
-AddressPrefix "11.0.0.0/16" `
-Subnet $peSubnet 
```

### <a name="create-a-private-endpoint"></a>Privát végpont létrehozása
Hozzon létre egy privát végpontot a virtuális hálózaton fent létrehozott privát kapcsolati szolgáltatás fogyasztásához:
 
```azurepowershell
 
$plsConnection= New-AzPrivateLinkServiceConnection `
-Name plsConnection `
-PrivateLinkServiceId  $privateLinkService.Id  

$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $rgName -Name $peName -Location $location -Subnet $vnetPE.subnets[0] -PrivateLinkServiceConnection $plsConnection -ByManualRequest 
```
 
### <a name="get-private-endpoint"></a>Privát végpont beszerzése
Szerezze be a privát végpont IP-címét `Get-AzPrivateEndpoint` a következőképpen:

```azurepowershell
# Get Private Endpoint and its IP Address 
$pe =  Get-AzPrivateEndpoint `
-Name $peName `
-ResourceGroupName $rgName  `
-ExpandResource networkinterfaces

$pe.NetworkInterfaces[0].IpConfigurations[0].PrivateIpAddress 

```

### <a name="approve-the-private-endpoint-connection"></a>A magánhálózati végponti kapcsolatok jóváhagyása
Hagyja jóvá a privát végponti kapcsolatot a Private link Service-szel a "jóváhagyás – AzPrivateEndpointConnection" értékkel.

```azurepowershell   

$pls = Get-AzPrivateLinkService `
-Name $plsName `
-ResourceGroupName $rgName 

Approve-AzPrivateEndpointConnection -ResourceId $pls.PrivateEndpointConnections[0].Id -Description "Approved" 

``` 

## <a name="next-steps"></a>Következő lépések
- További információ az [Azure Private linkről](private-link-overview.md)
 
