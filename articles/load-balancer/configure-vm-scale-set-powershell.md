---
title: Virtuálisgép-méretezési készlet konfigurálása meglévő Azure-terheléselosztóval – Azure PowerShell
description: Ismerje meg, hogyan konfigurálhat egy virtuálisgép-méretezési készletet egy meglévő Azure Load Balancer-rel.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: article
ms.date: 03/26/2020
ms.openlocfilehash: 0db09083a2197ce72e6d6eed2381b0308239586e
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349995"
---
# <a name="configure-a-virtual-machine-scale-set-with-an-existing-azure-load-balancer-using-azure-powershell"></a>Virtuálisgép-méretezési készlet konfigurálása meglévő Azure Load Balancer-rel az Azure PowerShell használatával

Ebben a cikkben megtudhatja, hogyan konfigurálhatja a virtuális gép méretezési készlet egy meglévő Azure Load Balancer. 

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés.
- Egy meglévő szabványos sku terheléselosztó az előfizetésben, ahol a virtuális gép méretezési csoport lesz telepítve.
- Egy Azure virtuális hálózat a virtuális gép méretezési készlet.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure-cli"></a>Bejelentkezés az Azure CLI-be

Jelentkezzen be az Azure-ba.

```azurepowershell-interactive
Connect-AzAccount
```

## <a name="deploy-a-virtual-machine-scale-set-with-existing-load-balancer"></a>Virtuálisgép-méretezési csoport üzembe helyezése meglévő terheléselosztóval

Cserélje le a zárójelben lévő értékeket a konfigurációban lévő erőforrások nevére.

```azurepowershell-interactive

$rsg = <resource-group>
$loc = <location>
$vms = <vm-scale-set-name>
$vnt = <virtual-network>
$sub = <subnet-name>
$lbn = <load-balancer-name>
$pol = <upgrade-policy-mode>

$lb = Get-AzLoadBalancer -ResourceGroupName $rsg -Name $lbn

New-AzVmss -ResourceGroupName $rsg -Location $loc -VMScaleSetName $vms -VirtualNetworkName $vnt -SubnetName $sub -LoadBalancerName $lb -UpgradePolicyMode $pol

```

Az alábbi példa egy virtuálisgép-méretezési készletet telepít a következőkkel:

- **MyVMSS** nevű virtuálisgép-méretezési készlet
- Az Azure Load Balancer nevű Azure load **balancer**
- **MyBackendPool** nevű terheléselosztó háttérkészlete
- Az Azure virtuális hálózat neve **myVnet**
- **MySubnet** nevű alhálózat
- **MyResourceGroup** nevű erőforráscsoport

```azureppowershell-interactive

$rsg = "myResourceGroup"
$loc = "East US 2"
$vms = "myVMSS"
$vnt = "myVnet"
$sub = "mySubnet"
$pol = "Automatic"
$lbn = "myLoadBalancer"

$lb = Get-AzLoadBalancer -ResourceGroupName $rsg -Name $lbn

New-AzVmss -ResourceGroupName $rsg -Location $loc -VMScaleSetName $vms -VirtualNetworkName $vnt -SubnetName $sub -LoadBalancerName $lb -UpgradePolicyMode $pol
```
> [!NOTE]
> A méretezési készlet létrehozása után a háttérport nem módosítható a terheléselosztó állapotminta által használt terheléselosztási szabályhoz. A port módosításához távolítsa el az állapotminta az Azure virtuálisgép-méretezési készlet frissítésével, frissítse a portot, majd konfigurálja újra az állapotminta.

## <a name="next-steps"></a>További lépések

Ebben a cikkben üzembe helyezett egy virtuális gép méretezési készlet egy meglévő Azure Load Balancer.  A virtuálisgép-méretezési készletekről és a terheléselosztóról a következő témakörben olvashat bővebben:

- [Mi az az Azure Load Balancer?](load-balancer-overview.md)
- [Mik a virtuálisgép-méretezési csoportok?](../virtual-machine-scale-sets/overview.md)
                                