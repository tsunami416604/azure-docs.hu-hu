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
ms.openlocfilehash: 8fa73479c5bbe73f0cb6c828a0552bb8dca80d3f
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75894504"
---
#  <a name="create-a-standard-load-balancer-with-zonal-frontend-using-azure-powershell"></a>standard Load Balancer létrehozása a zóna-előtérben a Azure PowerShell használatával

Ez a cikk egy nyilvános IP-címet használó nyilvános [standard Load Balancer](https://aka.ms/azureloadbalancerstandard) létrehozásával foglalkozik. Annak megismeréséhez, hogy a rendelkezésre állási zónák hogyan működnek a standard Load Balancerokkal, tekintse meg a [standard Load Balancer és a rendelkezésre állás](load-balancer-standard-availability-zones.md) 

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

> [!NOTE]
> A Availability Zones támogatása elérhető az Azure-erőforrások és-régiók kiválasztásához, valamint a virtuális gépek méretének megadásához. További információ az első lépésekről, valamint arról, hogy az Azure-erőforrások,-régiók és a virtuális gépek méretének családja milyen módon próbálhatja ki a rendelkezésre állási zónákat: [Availability Zones áttekintése](https://docs.microsoft.com/azure/availability-zones/az-overview). Ha támogatásra van szüksége, keresse fel a [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) fórumot, vagy [nyisson meg egy Azure támogatási jegyet](../azure-portal/supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure-előfizetésbe a `Connect-AzAccount` paranccsal, és kövesse a képernyőn megjelenő útmutatásokat.

```azurepowershell-interactive
Connect-AzAccount
```

## <a name="create-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot a következő parancs használatával:

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupZLB -Location westeurope
```

## <a name="create-a-public-ip-standard"></a>Nyilvános IP-szabvány létrehozása 
Hozzon létre egy nyilvános IP-címet a következő parancs használatával:

```azurepowershell-interactive
$publicIp = New-AzPublicIpAddress -ResourceGroupName myResourceGroupZLB -Name 'myPublicIPZonal' `
  -Location westeurope -AllocationMethod Static -Sku Standard -zone 1
```

## <a name="create-a-front-end-ip-configuration-for-the-website"></a>Előtér-IP-konfiguráció létrehozása a webhelyhez

Hozzon létre egy előtérbeli IP-konfigurációt a következő parancs használatával:

```azurepowershell-interactive
$feip = New-AzLoadBalancerFrontendIpConfig -Name 'myFrontEnd' -PublicIpAddress $publicIp
```

## <a name="create-the-back-end-address-pool"></a>A háttérbeli címkészlet létrehozása

Hozzon létre egy háttér-címkészletet a következő parancs használatával:

```azurepowershell-interactive
$bepool = New-AzLoadBalancerBackendAddressPoolConfig -Name 'myBackEndPool'
```

## <a name="create-a-load-balancer-probe-on-port-80"></a>Terheléselosztó-mintavétel létrehozása a 80-as porton

Hozzon létre egy állapot-mintavételt a terheléselosztó 80-as portján a következő parancs használatával:

```azurepowershell-interactive
$probe = New-AzLoadBalancerProbeConfig -Name 'myHealthProbe' -Protocol Http -Port 80 `
  -RequestPath / -IntervalInSeconds 360 -ProbeCount 5
```

## <a name="create-a-load-balancer-rule"></a>Terheléselosztási szabály létrehozása
 Hozzon létre egy terheléselosztó-szabályt a következő parancs használatával:

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

## <a name="next-steps"></a>Következő lépések
- További információ a [standard Load Balancer és a rendelkezésre állási zónákról](load-balancer-standard-availability-zones.md).