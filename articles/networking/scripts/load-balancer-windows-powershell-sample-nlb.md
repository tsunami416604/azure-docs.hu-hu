---
title: Az Azure PowerShell-parancsfájl minta - betöltési oszthatja el a forgalmat a virtuális gépek magas rendelkezésre állásra |} Microsoft Docs
description: Az Azure PowerShell-parancsfájl minta - betöltési oszthatja el a forgalmat a virtuális gépek magas rendelkezésre álláshoz
services: load-balancer
documentationcenter: load-balancer
author: georgewallace
manager: timlt
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: load-balancer
ms.devlang: powershell
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 05/16/2017
ms.author: gwallace
ms.openlocfilehash: 61d65cbdcf7867cc7a2a225648dc8b1ab7137bcb
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
---
# <a name="load-balance-traffic-to-vms-for-high-availability"></a>Betöltési oszthatja el a forgalmat a virtuális gépek magas rendelkezésre álláshoz

Ez a parancsfájl-minta létrehoz minden szükséges a magas rendelkezésre állású konfigurált több Windows virtuális gépek futtatásához és az elosztott terhelésű konfiguráció betöltése. A szkript futtatása után három virtuális géppel rendelkezik majd, amelyek egy Azure-beli rendelkezésre állási csoporthoz vannak csatlakoztatva, és egy Azure Load Balanceren keresztül érhetők el.

Szükség esetén telepítse az Azure PowerShellt az [Azure PowerShell útmutatójának](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) utasításait követve, majd a `Connect-AzureRmAccount` futtatásával hozza létre a kapcsolatot az Azure-ral.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-nlb/create-vm-nlb.ps1 "Quick Create VM")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

Az alábbi paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja egy erőforráscsoport, egy virtuális gép, egy rendelkezésre állási csoport, egy terheléselosztó és minden kapcsolódó erőforrás létrehozásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) | Alhálózati konfigurációt hoz létre. Ez a konfiguráció a virtuális hálózat létrehozására szolgál. |
| [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) | Létrehoz egy Azure-beli virtuális hálózatot és alhálózatot. |
| [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress)  | Létrehoz egy nyilvános IP-címet egy statikus IP-címmel és egy hozzárendelt DNS-névvel. |
| [New-AzureRmLoadBalancer](/powershell/module/azurerm.network/new-azurermloadbalancer)  | Egy Azure terheléselosztót hoz létre. |
| [Új AzureRmLoadBalancerProbeConfig](/powershell/module/azurerm.network/new-azurermloadbalancerprobeconfig) | Terheléselosztói mintavétel létrehozása. Terheléselosztói mintavétel egyes virtuális gépek, a betöltés terheléselosztó-készlet figyelésére használható. Ha valamelyik virtuális gép elérhetetlenné válik, a terheléselosztó nem irányít rá forgalmat. |
| [Új AzureRmLoadBalancerRuleConfig](/powershell/module/azurerm.network/new-azurermloadbalancerruleconfig) | Létrehoz egy terheléselosztó szabályhoz. Ebben a példában egy, a 80-es portra vonatkozó szabály jön létre. HTTP-forgalom érkezik a terheléselosztót, mert a 80-as port továbbításuk a terhelés terheléselosztó-készlet a virtuális gépek közül. |
| [Új AzureRmLoadBalancerInboundNatRuleConfig](/powershell/module/azurerm.network/new-azurermloadbalancerinboundnatruleconfig) | Létrehoz egy terheléselosztó hálózati címfordítás (NAT) szabály.  NAT-szabályok leképezése egy portot a virtuális gép egy terheléselosztó-portot. Ez a példa egy NAT-szabály jön létre a terhelés terheléselosztó-készlet minden egyes virtuális gépek SSH forgalmát.  |
| [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup) | Létrehoz egy hálózati biztonsági csoportot (NSG), amely biztonsági határként szolgál az internet és a virtuális gép között. |
| [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig) | Létrehoz egy NSG-szabályt a befelé irányuló forgalom engedélyezésére. Ebben a példában a 22-es portot nyitjuk meg az SSH-forgalom számára. |
| [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) | Létrehoz egy virtuális hálózati kártyát, és csatlakoztatja a virtuális hálózathoz, az alhálózathoz és az NSG-hez. |
| [Új AzureRmAvailabilitySet](/powershell/module/azurerm.compute/new-azurermavailabilityset) | Létrehoz egy rendelkezésre állási csoportot. A rendelkezésre állási csoportok az alkalmazások rendelkezésre állását biztosítják a virtuális gépek fizikai erőforrások közötti elosztásával, hogy az esetlegesen fellépő hibák ne érintsék a teljes készletet. |
| [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) | Egy virtuálisgép-konfigurációt hoz létre. Ebben a konfigurációban olyan információk szerepelnek, mint a virtuális gép neve, az operációs rendszer és a rendszergazdai hitelesítő adatok. A rendszer a virtuális gépek létrehozása során használja ezt a konfigurációt. |
| [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm)  | Létrehozza a virtuális gépet, és csatlakoztatja a hálózati kártyához, a virtuális hálózathoz, az alhálózathoz és az NSG-hez. A parancs megadja továbbá a használandó virtuálisgép-rendszerképet és a rendszergazdai jelszavakat.  |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](https://docs.microsoft.com/powershell/azure/overview).

További hálózati PowerShell parancsfájl-példák találhatók a [Azure hálózati áttekintés dokumentáció](../powershell-samples.md?toc=%2fazure%2fnetworking%2ftoc.json).
