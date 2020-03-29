---
title: Hozzon létre egy Azure-beli privát kapcsolati szolgáltatást az Azure PowerShell használatával| Microsoft dokumentumok
description: Ismerje meg, hogyan hozhat létre azure-beli privát kapcsolati szolgáltatást az Azure PowerShell használatával
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 225ae9d07cc6df2fa809e250083ee6007ab2f945
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76932082"
---
# <a name="create-a-private-link-service-using-azure-powershell"></a>Privát kapcsolatszolgáltatás létrehozása az Azure PowerShell használatával
Ez a cikk bemutatja, hogyan hozhat létre egy privát kapcsolatszolgáltatás az Azure-ban az Azure PowerShell használatával.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha úgy dönt, hogy helyileg telepíti és használja a PowerShellt, ez a cikk a legújabb Azure PowerShell-modulverziót igényli. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

A privát hivatkozás létrehozása előtt létre kell hoznia egy erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)segítségével. A következő példa létrehoz egy *myResourceGroup* nevű erőforráscsoportot a *WestCentralUS* helyen:

```azurepowershell
$location = "westcentralus"
$rgName = "myResourceGroup"
New-AzResourceGroup `
  -ResourceGroupName $rgName `
  -Location $location
```
## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása
Hozzon létre egy virtuális hálózatot a privát kapcsolatot [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). A következő példa létrehoz egy virtuális hálózat nevű *myvnet* alhálózat előtérrel (*előtérSubnet),* háttérrendszer (*backendSubnet),* privát kapcsolat *(otherSubnet*):

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
## <a name="create-internal-load-balancer"></a>Belső terheléselosztó létrehozása
Hozzon létre egy belső standard terheléselosztót a [New-AzLoadBalancer segítségével.](/powershell/module/az.network/new-azloadbalancer) A következő példa létrehoz egy belső standard terheléselosztót az előző lépésekben létrehozott előtér-IP-konfiguráció, mintavétel, szabály és háttérkészlet használatával:

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
## <a name="create-a-private-link-service"></a>Privát kapcsolatszolgáltatás létrehozása
Hozzon létre egy privát kapcsolatszolgáltatást a [New-AzPrivateLinkService szolgáltatással.](/powershell/module/az.network/new-azloadbalancer)  Ez a példa létrehoz egy *myPLS* nevű privát kapcsolatszolgáltatást a standard terheléselosztó használatával a *myResourceGroup*nevű erőforráscsoportban. 
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

### <a name="get-private-link-service"></a>Privát hivatkozásszolgáltatás beszereznie
A [Get-AzPrivateLinkService](/powershell/module/az.network/get-azprivatelinkservice) szolgáltatással kapcsolatos részletek az alábbiak szerint:

```azurepowershell
$pls = Get-AzPrivateLinkService -Name $plsName -ResourceGroupName $rgName 
```

Ebben a szakaszban a Privát kapcsolat szolgáltatás sikeresen létrejön, és készen áll a forgalom fogadására. Vegye figyelembe, hogy a fenti példa csak a Privát kapcsolat szolgáltatás létrehozása a PowerShell használatával.  Még nem konfigurálta a terheléselosztó háttérkészletek vagy bármely alkalmazás a háttérkészletek a forgalom figyelésére. Ha azt szeretné, hogy a végpontok közötti forgalom folyamatok, javasoljuk, hogy konfigurálja az alkalmazást a standard terheléselosztó mögött. 

Ezután bemutatjuk, hogyan képezze le ezt a szolgáltatást egy privát végponthoz a PowerShell használatával különböző virtuális hálózatban. A példa ismét a privát végpont létrehozására és a fent létrehozott privát kapcsolatszolgáltatáshoz való csatlakozásra korlátozódik. Virtuális gépek a virtuális hálózatban a forgalom küldése/fogadása a privát végpont a forgatókönyv létrehozásához. 

## <a name="create-a-private-endpoint"></a>Privát végpont létrehozása
### <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása
Hozzon létre egy virtuális hálózatot a saját végponthoz a [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)segítségével. Ez a példa egy *vnetPE* nevű virtuális hálózatot hoz létre *a myResourceGroup*nevű erőforráscsoportban:
 
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
Hozzon létre egy privát végpontot a virtuális hálózatban a fent létrehozott privát kapcsolatszolgáltatás elhasználódásához:
 
```azurepowershell
 
$plsConnection= New-AzPrivateLinkServiceConnection `
-Name plsConnection `
-PrivateLinkServiceId  $privateLinkService.Id  

$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $rgName -Name $peName -Location $location -Subnet $vnetPE.subnets[0] -PrivateLinkServiceConnection $plsConnection -ByManualRequest 
```
 
### <a name="get-private-endpoint"></a>Privát végpont beszereznie
A privát végpont IP-címének `Get-AzPrivateEndpoint` beszerezése az alábbiak szerint:

```azurepowershell
# Get Private Endpoint and its IP Address 
$pe =  Get-AzPrivateEndpoint `
-Name $peName `
-ResourceGroupName $rgName  `
-ExpandResource networkinterfaces

$pe.NetworkInterfaces[0].IpConfigurations[0].PrivateIpAddress 

```

### <a name="approve-the-private-endpoint-connection"></a>A privát végpontkapcsolat jóváhagyása
Hagyja jóvá a privát végpontkapcsolatot a privát kapcsolatszolgáltatással a "Approve-AzPrivateEndpointConnection" segítségével.

```azurepowershell   

$pls = Get-AzPrivateLinkService `
-Name $plsName `
-ResourceGroupName $rgName 

Approve-AzPrivateEndpointConnection -ResourceId $pls.PrivateEndpointConnections[0].Id -Description "Approved" 

``` 

## <a name="next-steps"></a>További lépések
- További információ az [Azure privát hivatkozásáról](private-link-overview.md)
 
