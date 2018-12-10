---
title: Standard Load Balancer létrehozása az Azure PowerShell-lel zónaredundáns frontend
titlesuffix: Azure Load Balancer
description: A PowerShell-lel zónaredundáns nyilvános IP-cím cím előtérbeli nyilvános Load Balancer Standard létrehozása
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/23/2018
ms.author: kumud
ms.openlocfilehash: b6730b624cc0416354fecfdbe747f643540f9e89
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53138745"
---
#  <a name="create-a-standard-balancer-with-zone-redundant-frontend-using-azure-powershell"></a>Hozzon létre egy standard szintű terheléselosztó zónaredundáns előtérbeli Azure PowerShell-lel

Ez a cikk végigvezeti egy nyilvános létrehozása [Load Balancer Standard](https://aka.ms/azureloadbalancerstandard) és a egy zónaredundáns frontend-IP szabványos nyilvános cím segítségével.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

> [!NOTE]
 A rendelkezésre állási zónák támogatása az Azure-erőforrásokhoz, és a régiók és a virtuális gép méretcsaládjai érhető el. További információ az első lépésekről, és melyik Azure-erőforrások, régiók, és próbálja meg a rendelkezésre állási zónák a virtuális gép méretcsaládjai, lásd: [a rendelkezésre állási zónákat áttekintő](https://docs.microsoft.com/azure/availability-zones/az-overview). Ha támogatásra van szüksége, keresse fel a [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) fórumot, vagy [nyisson meg egy Azure támogatási jegyet](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure-előfizetésbe a `Connect-AzureRmAccount` paranccsal, és kövesse a képernyőn megjelenő útmutatásokat.

```powershell
Connect-AzureRmAccount
```

## <a name="create-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot, a következő paranccsal:

```powershell
New-AzureRmResourceGroup -Name myResourceGroup -Location westeurope
```

## <a name="create-a-public-ip-standard"></a>Hozzon létre egy nyilvános IP Standard 
Hozzon létre egy nyilvános IP Standard a következő paranccsal:

```powershell
$publicIp = New-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup -Name 'myPublicIP' `
  -Location westeurope -AllocationMethod Static -Sku Standard 
```

## <a name="create-a-front-end-ip-configuration-for-the-website"></a>Hozzon létre egy előtérbeli IP-konfigurációt a webhelyhez

Hozzon létre egy előtérbeli IP-konfiguráció a következő paranccsal:

```powershell
$feip = New-AzureRmLoadBalancerFrontendIpConfig -Name 'myFrontEndPool' -PublicIpAddress $publicIp
```

## <a name="create-the-back-end-address-pool"></a>A háttér-címkészlet létrehozása

Hozzon létre egy háttércímkészletet az alábbi paranccsal:

```powershell
$bepool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name 'myBackEndPool'
```

## <a name="create-a-load-balancer-probe-on-port-80"></a>Terheléselosztói mintavételezők létrehozásához a 80-as porton

Hozzon létre egy állapotmintát a 80-as portot a load balancer használatával a következő parancsot:

```powershell
$probe = New-AzureRmLoadBalancerProbeConfig -Name 'myHealthProbe' -Protocol Http -Port 80 `
  -RequestPath / -IntervalInSeconds 360 -ProbeCount 5
```

## <a name="create-a-load-balancer-rule"></a>Terheléselosztási szabály létrehozása
 Hozzon létre egy terheléselosztó-szabályt a következő paranccsal:

```powershell
   $rule = New-AzureRmLoadBalancerRuleConfig -Name HTTP -FrontendIpConfiguration $feip -BackendAddressPool  $bepool -Probe $probe -Protocol Tcp -FrontendPort 80 -BackendPort 80
```

## <a name="create-a-load-balancer"></a>Load Balancer létrehozása
Hozzon létre egy Load Balancer Standard a következő paranccsal:

```powershell
$lb = New-AzureRmLoadBalancer -ResourceGroupName myResourceGroup -Name 'MyLoadBalancer' -Location westeurope `
  -FrontendIpConfiguration $feip -BackendAddressPool $bepool `
  -Probe $probe -LoadBalancingRule $rule -Sku Standard
```

## <a name="next-steps"></a>További lépések
- Tudjon meg többet [Standard Load Balancer és rendelkezésre állási zónák](load-balancer-standard-availability-zones.md).



