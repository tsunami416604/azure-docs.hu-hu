---
title: Az Azure PowerShell-Példaszkript – forgalom terheléselosztása virtuális gépek magas rendelkezésre álláshoz |} A Microsoft Docs
description: Az Azure PowerShell-Példaszkript – forgalom terheléselosztása virtuális gépek magas rendelkezésre állás érdekében
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
ms.openlocfilehash: 16d4c92642fb6bd0191c24ebcf57ee092301971f
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2019
ms.locfileid: "56651940"
---
# <a name="load-balance-traffic-to-vms-for-high-availability"></a>Bejövő forgalmának terheléselosztása virtuális gépek magas rendelkezésre állás érdekében

Ez a példaszkript minden összetevőt létrehoz, a magas rendelkezésre állású konfigurált számos Windows virtuális gépek futtatásához, és terheléselosztásra szükséges. A szkript futtatása után három virtuális géppel rendelkezik majd, amelyek egy Azure-beli rendelkezésre állási csoporthoz vannak csatlakoztatva, és egy Azure Load Balanceren keresztül érhetők el.

Szükség esetén telepítse az Azure PowerShellt az [Azure PowerShell útmutatójának](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) utasításait követve, majd a `Connect-AzAccount` futtatásával hozza létre a kapcsolatot az Azure-ral.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-nlb/create-vm-nlb.ps1 "Quick Create VM")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

Az alábbi paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja egy erőforráscsoport, egy virtuális gép, egy rendelkezésre állási csoport, egy terheléselosztó és minden kapcsolódó erőforrás létrehozásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Alhálózati konfigurációt hoz létre. Ez a konfiguráció a virtuális hálózat létrehozására szolgál. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Létrehoz egy Azure-beli virtuális hálózatot és alhálózatot. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)  | Létrehoz egy nyilvános IP-címet egy statikus IP-címmel és egy hozzárendelt DNS-névvel. |
| [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer)  | Azure-terheléselosztót hoz létre. |
| [New-AzLoadBalancerProbeConfig](/powershell/module/az.network/new-azloadbalancerprobeconfig) | Terheléselosztói mintavételt hoz létre. A terheléselosztói mintavétel a terheléselosztó-készlet egyes virtuális gépeinek figyelésére használható. Ha valamelyik virtuális gép elérhetetlenné válik, a terheléselosztó nem irányít rá forgalmat. |
| [New-AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerruleconfig) | Terheléselosztó-szabályt hoz létre. Ebben a példában egy, a 80-es portra vonatkozó szabály jön létre. A hálózati terheléselosztóra érkező HTTP-forgalom a terheléselosztó csoporthoz tartozó egyik virtuális gép 80-as portjára lesz irányítva. |
| [New-AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) | Létrehoz egy terheléselosztó hálózati címfordítási (Network Address Translation, NAT) szabályt.  A NAT-szabályok a terheléselosztó egyik portját hozzárendelik egy virtuális gép valamelyik portjához. Ebben a példában egy NAT-szabályt hozunk létre az SSH-forgalomnak a terheléselosztó csoportba tartozó egyes virtuális gépekre történő irányítására.  |
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Létrehoz egy hálózati biztonsági csoportot (NSG), amely biztonsági határként szolgál az internet és a virtuális gép között. |
| [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) | Létrehoz egy NSG-szabályt a befelé irányuló forgalom engedélyezésére. Ebben a példában a 22-es portot nyitjuk meg az SSH-forgalom számára. |
| [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) | Létrehoz egy virtuális hálózati kártyát, és csatlakoztatja a virtuális hálózathoz, az alhálózathoz és az NSG-hez. |
| [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset) | Létrehoz egy rendelkezésre állási csoportot. A rendelkezésre állási csoportok az alkalmazások rendelkezésre állását biztosítják a virtuális gépek fizikai erőforrások közötti elosztásával, hogy az esetlegesen fellépő hibák ne érintsék a teljes készletet. |
| [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig) | Egy virtuálisgép-konfigurációt hoz létre. Ebben a konfigurációban olyan információk szerepelnek, mint a virtuális gép neve, az operációs rendszer és a rendszergazdai hitelesítő adatok. A rendszer a virtuális gépek létrehozása során használja ezt a konfigurációt. |
| [New-AzVM](/powershell/module/az.compute/new-azvm)  | Létrehozza a virtuális gépet, és csatlakoztatja a hálózati kártyához, a virtuális hálózathoz, az alhálózathoz és az NSG-hez. A parancs megadja továbbá a használandó virtuálisgép-rendszerképet és a rendszergazdai jelszavakat.  |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](https://docs.microsoft.com/powershell/azure/overview).

További hálózatkezelési PowerShell-példaszkripteket az [Azure-hálózatkezelés áttekintő dokumentációjában](../powershell-samples.md?toc=%2fazure%2fnetworking%2ftoc.json) találhat.
