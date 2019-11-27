---
title: Load Balancer létrehozása a zóna-előtérben – Azure PowerShell
titleSuffix: Azure Load Balancer
description: Megtudhatja, hogyan hozhat létre standard Load Balancert a zóna-előtérben a Azure PowerShell használatával
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2019
ms.author: allensu
ms.openlocfilehash: 1d05b130a98ce816d070bc3ad16e25b867d7dc8a
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74215120"
---
#  <a name="create-a-standard-load-balancer-with-zonal-frontend-using-azure-powershell"></a>standard Load Balancer létrehozása a zóna-előtérben a Azure PowerShell használatával

Ez a cikk egy nyilvános IP-címet használó nyilvános [standard Load Balancer](https://aka.ms/azureloadbalancerstandard) létrehozásával foglalkozik. Annak megismeréséhez, hogy a rendelkezésre állási zónák hogyan működnek a standard Load Balancerokkal, tekintse meg a [standard Load Balancer és a rendelkezésre állás](load-balancer-standard-availability-zones.md) 

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

> [!NOTE]
> A rendelkezésre állási zónák támogatása az Azure-erőforrásokhoz, és a régiók és a virtuális gép méretcsaládjai érhető el. További információ az első lépésekről, valamint arról, hogy az Azure-erőforrások,-régiók és a virtuális gépek méretének családja milyen módon próbálhatja ki a rendelkezésre állási zónákat: [Availability Zones áttekintése](https://docs.microsoft.com/azure/availability-zones/az-overview). Ha támogatásra van szüksége, keresse fel a [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) fórumot, vagy [nyisson meg egy Azure támogatási jegyet](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure-előfizetésbe a `Connect-AzAccount` paranccsal, és kövesse a képernyőn megjelenő útmutatásokat.

```azurepowershell-interactive
Connect-AzAccount
```

## <a name="create-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot, a következő paranccsal:

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupZLB -Location westeurope
```

## <a name="create-a-public-ip-standard"></a>Hozzon létre egy nyilvános IP Standard 
Hozzon létre egy nyilvános IP Standard a következő paranccsal:

```azurepowershell-interactive
$publicIp = New-AzPublicIpAddress -ResourceGroupName myResourceGroupZLB -Name 'myPublicIPZonal' `
  -Location westeurope -AllocationMethod Static -Sku Standard -zone 1
```

## <a name="create-a-front-end-ip-configuration-for-the-website"></a>Hozzon létre egy előtérbeli IP-konfigurációt a webhelyhez

Hozzon létre egy előtérbeli IP-konfiguráció a következő paranccsal:

```azurepowershell-interactive
$feip = New-AzLoadBalancerFrontendIpConfig -Name 'myFrontEnd' -PublicIpAddress $publicIp
```

## <a name="create-the-back-end-address-pool"></a>A háttér-címkészlet létrehozása

Hozzon létre egy háttércímkészletet az alábbi paranccsal:

```azurepowershell-interactive
$bepool = New-AzLoadBalancerBackendAddressPoolConfig -Name 'myBackEndPool'
```

## <a name="create-a-load-balancer-probe-on-port-80"></a>Terheléselosztói mintavételezők létrehozásához a 80-as porton

Hozzon létre egy állapotmintát a 80-as portot a load balancer használatával a következő parancsot:

```azurepowershell-interactive
$probe = New-AzLoadBalancerProbeConfig -Name 'myHealthProbe' -Protocol Http -Port 80 `
  -RequestPath / -IntervalInSeconds 360 -ProbeCount 5
```

## <a name="create-a-load-balancer-rule"></a>Terheléselosztási szabály létrehozása
 Hozzon létre egy terheléselosztó-szabályt a következő paranccsal:

```azurepowershell-interactive
   $rule = New-AzLoadBalancerRuleConfig -Name HTTP -FrontendIpConfiguration $feip -BackendAddressPool  $bepool -Probe $probe -Protocol Tcp -FrontendPort 80 -BackendPort 80
```

## <a name="create-a-load-balancer"></a>Load Balancer létrehozása
Hozzon létre egy standard Load Balancer a következő parancs használatával:

```azurepowershell-interactive
$lb = New-AzLoadBalancer -ResourceGroupName myResourceGroupZLB -Name 'MyLoadBalancer' -Location westeurope `
  -FrontendIpConfiguration $feip -BackendAddressPool $bepool `
  -Probe $probe -LoadBalancingRule $rule -Sku Standard
```

## <a name="next-steps"></a>További lépések
- További információ a [standard Load Balancer és a rendelkezésre állási zónákról](load-balancer-standard-availability-zones.md).