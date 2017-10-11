---
title: "Az Azure PowerShell-parancsfájl minta - betöltési oszthatja el a forgalmat a virtuális gépek magas rendelkezésre állásra |} Microsoft Docs"
description: "Az Azure PowerShell-parancsfájl minta - betöltési oszthatja el a forgalmat a virtuális gépek magas rendelkezésre álláshoz"
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
ms.openlocfilehash: c77def8906b151f2cc6e4bbc4188be8ecbeac732
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="load-balance-traffic-to-vms-for-high-availability"></a>Betöltési oszthatja el a forgalmat a virtuális gépek magas rendelkezésre álláshoz

Ez a parancsfájl-minta létrehoz minden szükséges a magas rendelkezésre állású konfigurált több Windows virtuális gépek futtatásához és az elosztott terhelésű konfiguráció betöltése. A parancsfájl futtatása után meg kell három virtuális gépet, az Azure rendelkezésre állási csoport, és az Azure Load Balancer keresztül érhető el.

Szükség esetén telepítse az Azure PowerShell található utasítás használatával a [Azure PowerShell útmutató](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/), majd futtassa a `Login-AzureRmAccount` kapcsolat létrehozása az Azure-ral.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Mintaparancsfájl

[!code-powershell[fő](../../../powershell_scripts/virtual-machine/create-vm-nlb/create-vm-nlb.ps1 "gyors létrehozása méretű VM")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

A következő parancsot az erőforráscsoport, virtuális gép és az összes kapcsolódó erőforrások eltávolítása.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Parancsfájl ismertetése

A parancsfájl a következő parancsokat egy erőforráscsoport, virtuális gép, a rendelkezésre állási csoporthoz, terheléselosztó és minden kapcsolódó erőforrások létrehozásához. Minden egyes parancsa a tábla-parancs adott dokumentációjára mutató hivatkozásokat.

| Parancs | Megjegyzések |
|---|---|
| [Új-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Az összes erőforrás tároló erőforrás csoportot hoz létre. |
| [Új AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) | Létrehoz egy alhálózati konfigurációt. Ezt a konfigurációt használja a virtuális hálózat létrehozásának folyamatát. |
| [Új-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) | Létrehoz egy Azure-beli virtuális hálózat és az alhálózatot. |
| [Új AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress)  | Hoz létre egy nyilvános IP-cím egy statikus IP-címet és egy társított DNS-névvel. |
| [Új AzureRmLoadBalancer](/powershell/module/azurerm.network/new-azurermloadbalancer)  | Egy Azure terheléselosztót hoz létre. |
| [Új AzureRmLoadBalancerProbeConfig](/powershell/module/azurerm.network/new-azurermloadbalancerprobeconfig) | Terheléselosztói mintavétel létrehozása. Terheléselosztói mintavétel egyes virtuális gépek, a betöltés terheléselosztó-készlet figyelésére használható. Ha a virtuális gép elérhetetlenné válik, nem továbbítódik a virtuális géphez. |
| [Új AzureRmLoadBalancerRuleConfig](/powershell/module/azurerm.network/new-azurermloadbalancerruleconfig) | Létrehoz egy terheléselosztó szabályhoz. Ez a példa létrejön egy szabály 80-as porton. HTTP-forgalom érkezik a terheléselosztót, mert a 80-as port továbbításuk a terhelés terheléselosztó-készlet a virtuális gépek közül. |
| [Új AzureRmLoadBalancerInboundNatRuleConfig](/powershell/module/azurerm.network/new-azurermloadbalancerinboundnatruleconfig) | Létrehoz egy terheléselosztó hálózati címfordítás (NAT) szabály.  NAT-szabályok leképezése egy portot a virtuális gép egy terheléselosztó-portot. Ez a példa egy NAT-szabály jön létre a terhelés terheléselosztó-készlet minden egyes virtuális gépek SSH forgalmát.  |
| [Új AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup) | Hálózati biztonsági csoport (NSG), amely az internethez és a virtuális gép biztonsági határt hoz létre. |
| [Új AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig) | Létrehoz egy NSG-szabály, amely engedélyezi a bejövő forgalmat. Ez a példa 22-es portot SSH forgalom van megnyitva. |
| [Új AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) | Létrehoz egy virtuális hálózati kártya, és csatolja azt a virtuális hálózati alhálózat és NSG. |
| [Új AzureRmAvailabilitySet](/powershell/module/azurerm.compute/new-azurermavailabilityset) | Létrehoz egy rendelkezésre állási csoportot. Rendelkezésre állási készletek úgy a virtuális gépek fizikai erőforrások között úgy, hogy hiba esetén nem történik teljes alkalmazás hasznos üzemidő biztosítása. |
| [Új AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) | Létrehoz egy Virtuálisgép-konfiguráció. Ez a konfiguráció tartoznak a virtuális gép nevét, az operációs rendszer és a rendszergazdai hitelesítő adatokkal. A konfiguráció a Virtuálisgép-létrehozása során használatos. |
| [Új AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm)  | A virtuális gépet hoz létre, és csatlakozik a hálózati kártya, virtuális hálózatot, alhálózatot és NSG. Ez a parancs is meghatározza a virtuálisgép-lemezkép használt és a felügyeleti hitelesítő adatokat kell.  |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Egy olyan erőforráscsoport, beleértve az összes beágyazott erőforrások törlése. |

## <a name="next-steps"></a>Következő lépések

Az Azure PowerShell további információkért lásd: [Azure PowerShell dokumentációs](https://docs.microsoft.com/powershell/azure/overview).

További hálózati PowerShell parancsfájl-példák találhatók a [Azure hálózati áttekintés dokumentáció](../powershell-samples.md?toc=%2fazure%2fnetworking%2ftoc.json).
