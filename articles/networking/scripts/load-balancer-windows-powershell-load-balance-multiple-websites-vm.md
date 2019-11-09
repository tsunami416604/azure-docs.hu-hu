---
title: Azure PowerShell parancsfájl-minta – több webhely terheléselosztása a Azure PowerShellkal | Microsoft Docs
description: Azure PowerShell parancsfájl-minta – több webhely terheléselosztása ugyanarra a virtuális gépre
services: load-balancer
documentationcenter: load-balancer
author: asudbring
manager: KumudD
ms.service: load-balancer
ms.devlang: powershell
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 05/16/2017
ms.author: allensu
ms.openlocfilehash: 0b266f5fcad0ef709e29a125f1b9b5ddba7fec90
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/09/2019
ms.locfileid: "73890292"
---
# <a name="load-balance-multiple-websites"></a>Több webhely terheléselosztása

Ez a szkript létrehoz egy virtuális hálózatot két virtuális géppel (VM), amely egy rendelkezésre állási csoport tagjai. A terheléselosztó két különböző IP-cím forgalmát irányítja a két virtuális gépre. A szkript futtatása után telepíthet a webkiszolgáló szoftvert a virtuális gépekre, és több webhelyet is üzemeltethet, mindegyiket a saját IP-címével.

Szükség esetén telepítse az Azure PowerShellt az [Azure PowerShell útmutatójának](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) utasításait követve, majd a `Connect-AzAccount` futtatásával hozza létre a kapcsolatot az Azure-ral.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/load-balancer/load-balance-multiple-web-sites-vm/load-balance-multiple-web-sites-vm.ps1  "Load balance multiple web sites")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

Az alábbi paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja egy erőforráscsoport, virtuális hálózat, terheléselosztó és minden kapcsolódó erőforrás létrehozására. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [Új – AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset) | Azure rendelkezésre állási készletet hoz létre magas rendelkezésre állás biztosításához. |
| [Új – AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Alhálózati konfigurációt hoz létre. Ez a konfiguráció a virtuális hálózat létrehozására szolgál. |
| [Új – AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Virtuális hálózatot hoz létre. |
| [Új – AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Egy nyilvános IP-címet hoz létre. |
| [Új – AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) | Előtérbeli IP-konfigurációt hoz létre egy terheléselosztóhoz. |
| [Új – AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) | Létrehozza egy terheléselosztó háttércímkészlet-konfigurációját. |
| [Új – AzLoadBalancerProbeConfig](/powershell/module/az.network/new-azloadbalancerprobeconfig) | Létrehoz egy NLB-vizsgálatot. Az NLB-vizsgálat az NLB-készletbe tartozó egyes virtuális gépeket monitorozza. Ha valamelyik virtuális gép elérhetetlenné válik, a terheléselosztó nem irányít rá forgalmat. |
| [Új – AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerruleconfig) | Létrehoz egy NLB-szabályt. Ebben a példában egy, a 80-es portra vonatkozó szabály jön létre. A hálózati terheléselosztóra érkező HTTP-forgalom az NLB-csoportba tartozó egyik virtuális gép 80-as portjára lesz irányítva. |
| [Új – AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) | Létrehoz egy terheléselosztót. |
| [Új – AzNetworkInterfaceIpConfig](/powershell/module/az.network/new-aznetworkinterfaceipconfig) | Speciális funkciókat határoz meg egy virtuális hálózati adapterhez. |
| [Új – AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) | Hálózati adaptert hoz létre. |
| [Új – AzVMConfig](/powershell/module/az.compute/new-azvmconfig) | Egy virtuálisgép-konfigurációt hoz létre. Ebben a konfigurációban olyan információk szerepelnek, mint a virtuális gép neve, az operációs rendszer és a rendszergazdai hitelesítő adatok. A rendszer a virtuális gépek létrehozása során használja ezt a konfigurációt. |
| [New-AzVM](/powershell/module/az.compute/new-azvm) | Virtuális gépet hoz létre. |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Eltávolít egy erőforráscsoportot és az összes abban található erőforrást. |

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](https://docs.microsoft.com/powershell/azure/overview).

További hálózatkezelési PowerShell-példaszkripteket az [Azure-hálózatkezelés áttekintő dokumentációjában](../powershell-samples.md?toc=%2fazure%2fnetworking%2ftoc.json) találhat.
