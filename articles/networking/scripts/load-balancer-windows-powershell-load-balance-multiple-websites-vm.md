---
title: "Az Azure PowerShell-parancsfájl minta - terhelésének elosztása több webhely, az Azure PowerShell |} Microsoft Docs"
description: "Az Azure PowerShell-parancsfájl minta - terhelésének elosztása több webhely ugyanahhoz a virtuális géphez"
services: load-balancer
documentationcenter: load-balancer
author: georgewallace
manager: timlt
editor: tysonn
tags: 
ms.assetid: 
ms.service: load-balancer
ms.devlang: powershell
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 05/16/2017
ms.author: gwallace
ms.openlocfilehash: d73cdc98ff279c3ee1b93443abe4b6c7c97786a2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="load-balance-multiple-websites"></a>Terhelésének elosztása több webhely

Parancsfájl a következő példában két virtuális gépekkel (VM), amelyek egy rendelkezésre állási csoport tagjai hoz létre egy virtuális hálózatot. A terheléselosztó két különböző IP-címeket, a két virtuális gépek forgalmát irányítja. A parancsfájl futtatása után telepíthet webkiszolgáló szoftver a virtuális gépek és a gazdagép több webhely, mindegyiket a saját IP-címét.

Szükség esetén telepítse az Azure PowerShell található utasítás használatával a [Azure PowerShell útmutató](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/), majd futtassa a `Login-AzureRmAccount` kapcsolat létrehozása az Azure-ral.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Mintaparancsfájl

[!code-powershell[main](../../../powershell_scripts/load-balancer/load-balance-multiple-web-sites-vm/load-balance-multiple-web-sites-vm.ps1  "Load balance multiple web sites")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

A következő parancsot az erőforráscsoport, virtuális gép és az összes kapcsolódó erőforrások eltávolítása.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Parancsfájl ismertetése

A parancsfájl a következő parancsokat egy erőforráscsoport, a virtuális hálózati, a terheléselosztó és a minden kapcsolódó erőforrások létrehozásához. Minden egyes parancsa a tábla-parancs adott dokumentációjára mutató hivatkozásokat.

| Parancs | Megjegyzések |
|---|---|
| [Új-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Az összes erőforrás tároló erőforrás csoportot hoz létre. |
| [Új AzureRmAvailabilitySet](/powershell/module/azurerm.compute/new-azurermavailabilityset) | Az Azure rendelkezésre állási készlet magas rendelkezésre állás biztosításához hoz létre. |
| [Új AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) | Létrehoz egy alhálózati konfigurációt. Ezt a konfigurációt használja a virtuális hálózat létrehozásának folyamatát. |
| [Új-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) | Virtuális hálózat létrehozása. |
| [Új AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) | Létrehoz egy nyilvános IP-címet. |
| [Új AzureRmLoadBalancerFrontendIpConfig](/powershell/module/azurerm.network/new-azurermloadbalancerfrontendipconfig) | Létrehoz egy terheléselosztó előtér-IP config. |
| [Új AzureRmLoadBalancerBackendAddressPoolConfig](/powershell/module/azurerm.network/new-azurermloadbalancerbackendaddresspoolconfig) | Létrehoz egy háttér címkészlet terheléselosztó beállítása. |
| [Új AzureRmLoadBalancerProbeConfig](/powershell/module/azurerm.network/new-azurermloadbalancerprobeconfig) | Létrehoz egy hálózati Terheléselosztási mintavételt. Az NLB mintavételi egyes virtuális gépek, a hálózati Terheléselosztás készlet figyelésére használható. Ha a virtuális gép elérhetetlenné válik, nem továbbítódik a virtuális géphez. |
| [Új AzureRmLoadBalancerRuleConfig](/powershell/module/azurerm.network/new-azurermloadbalancerruleconfig) | Egy hálózati Terheléselosztási szabályt hoz létre. Ez a példa létrejön egy szabály 80-as porton. HTTP-forgalom érkezik a hálózati Terheléselosztás, mivel a 80-as port továbbításuk a hálózati Terheléselosztás beállítása a virtuális gépek közül. |
| [Új AzureRmLoadBalancer](/powershell/module/azurerm.network/new-azurermloadbalancer) | Létrehoz egy terheléselosztót. |
| [Új AzureRmNetworkInterfaceIpConfig](/powershell/module/azurerm.network/new-azurermnetworkinterfaceipconfig) | Határozza meg a virtuális hálózati adapter speciális szolgáltatásai. |
| [Új AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) | Létrehoz egy adott hálózati csatoló. |
| [Új AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) | Létrehoz egy Virtuálisgép-konfiguráció. Ez a konfiguráció tartoznak a virtuális gép nevét, az operációs rendszer és a rendszergazdai hitelesítő adatokkal. A konfiguráció a Virtuálisgép-létrehozása során használatos. |
| [Új AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) | Hozzon létre egy virtuális gépet. |
|[Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Eltávolítja az erőforráscsoportot és belül található összes erőforrást. |

## <a name="next-steps"></a>Következő lépések

Az Azure PowerShell további információkért lásd: [Azure PowerShell dokumentációs](https://docs.microsoft.com/powershell/azure/overview).

További hálózati PowerShell parancsfájl-példák találhatók a [Azure hálózati áttekintés dokumentáció](../powershell-samples.md?toc=%2fazure%2fnetworking%2ftoc.json).
