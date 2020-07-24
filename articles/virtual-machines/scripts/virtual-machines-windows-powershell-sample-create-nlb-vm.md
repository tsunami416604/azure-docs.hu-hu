---
title: Azure PowerShell-példaszkript – Windows rendszerű virtuális gépek hálózati terheléselosztásának létrehozása
description: Azure PowerShell-példaszkript – Windows rendszerű virtuális gépek hálózati terheléselosztásának létrehozása
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: b7dad64d7c3e3ab7caa56db26719996b3519b6d5
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87082409"
---
# <a name="load-balance-traffic-between-highly-available-virtual-machines"></a>Magas rendelkezésre állású virtuális gépek közötti forgalom terheléselosztása

A példaszkript minden összetevőt létrehoz, amelyre szükség van több, magas rendelkezésre állásra és terheléselosztásra konfigurált Windows Server 2016 rendszerű virtuális gép futtatásához. A szkript futtatása után három virtuális géppel rendelkezik majd, amelyek egy Azure-beli rendelkezésre állási csoporthoz vannak csatlakoztatva, és egy Azure Load Balanceren keresztül érhetők el.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-nlb/create-vm-nlb.ps1 "Create VM NLB")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

Az alábbi paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja az üzemelő példány létrehozásához. A táblázatban lévő összes elem a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [Új – AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Alhálózati konfigurációt hoz létre. Ez a konfiguráció a virtuális hálózat létrehozására szolgál. |
| [Új – AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Virtuális hálózatot hoz létre. |
| [Új – AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Egy nyilvános IP-címet hoz létre. |
| [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) | Létrehozza egy terheléselosztó előtérbeli IP-konfigurációját. |
| [Új – AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) | Háttérbeli címkészlet-konfigurációt hoz létre egy terheléselosztóhoz. |
| [Új – AzLoadBalancerProbeConfig](/powershell/module/az.network/new-azloadbalancerprobeconfig) | Létrehoz egy mintavételi konfigurációt egy terheléselosztó számára. |
| [Új – AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerruleconfig) | Létrehoz egy szabálykonfigurációt egy terheléselosztó számára. |
| [Új – AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) | Létrehoz egy bejövő NAT-szabálykonfigurációt egy terheléselosztó számára. |
| [Új – AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) | Terheléselosztót hoz létre. |
| [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) | Egy hálózati biztonsági csoport (NSG) szabálykonfigurációját hozza létre. Ez a konfiguráció az NSG-re vonatkozó szabály létrehozására szolgál az NSG létrehozásakor. |
| [Új – AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Létrehoz egy hálózati biztonsági csoportot. |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Lekéri az alhálózattal kapcsolatos információkat. Ezeket az adatokat a rendszer hálózati adapter létrehozásakor használja. |
| [Új – AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) | Hálózati adaptert hoz létre. |
| [Új – AzVMConfig](/powershell/module/az.compute/new-azvmconfig) | Egy virtuálisgép-konfigurációt hoz létre. Ebben a konfigurációban olyan információk szerepelnek, mint a virtuális gép neve, az operációs rendszer és a rendszergazdai hitelesítő adatok. A rendszer a virtuális gépek létrehozása során használja ezt a konfigurációt. |
| [New-AzVM](/powershell/module/az.compute/new-azvm) | Virtuális gépet hoz létre. |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Eltávolít egy erőforráscsoportot és az összes abban található erőforrást. |

A virtuális gépeket saját, egyéni felügyelt rendszerkép használatával is létrehozhatja. A virtuális gép konfigurációjában `Set-AzVMSourceImage` a, a, a és a paraméterek helyett használja a, a `-Id` és a `-VM` paramétereket `-PublisherName` `-Offer` `-Skus` `-Version` .

A virtuális gép konfigurációjának létrehozása például a következő lesz:

```powershell
$vmConfig = New-AzVMConfig -VMName 'myVM3' -VMSize Standard_DS1_v2 -AvailabilitySetId $as.Id | `
  Set-AzVMOperatingSystem -Windows -ComputerName 'myVM3' -Credential $cred | `
  Set-AzVMSourceImage -Id <Image.ID of the custom managed image> | Add-AzVMNetworkInterface -Id $nicVM3.Id
 ```

## <a name="next-steps"></a>További lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/).

A virtuális gépekhez kapcsolódó további PowerShell-példaszkripteket az [Azure Windows rendszerű virtuális gépekre vonatkozó dokumentációjában](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) találhat.
