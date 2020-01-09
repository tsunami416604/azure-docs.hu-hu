---
title: Azure PowerShell minták – teljes virtuálisgép-méretezési csoport létrehozása
description: Ez a szkript egy Windows Server 2016 rendszert futtató virtuálisgép-méretezési készletet hoz létre, ahol az egyes erőforrások konfigurálása és létrehozása történik.
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/29/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: b116a6cb4266b5ec05abd2b3210ebc772bac9868
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75350936"
---
# <a name="create-a-complete-virtual-machine-scale-set-with-powershell"></a>Teljes virtuálisgép-méretezési csoport létrehozása PowerShell segítségével

Ez a szkript létrehoz egy Windows Server 2016 rendszert futtató virtuálisgép-méretezési csoportot. Az egyes erőforrások konfigurálása és létrehozása nem a [beépített erőforrás-létrehozási lehetőségek használata a New-AzVmss-ben itt elérhető](powershell-sample-create-simple-scale-set.md). A szkript futtatása után a virtuálisgép-példányokat RDP-kapcsolaton keresztül érheti el.


[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/virtual-machine-scale-sets/complete-scale-set/complete-scale-set.ps1 "Create a complete virtual machine scale set")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása
Az alábbi paranccsal eltávolítható az erőforráscsoport, a méretezési csoport és az összes kapcsolódó erőforrás.

```powershell
Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="script-explanation"></a>Szkript ismertetése
A szkript a következő parancsokat használja az üzemelő példány létrehozásához. A táblázatban lévő összes elem a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [Új – AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Alhálózati konfigurációt hoz létre. Ez a konfiguráció a virtuális hálózat létrehozására szolgál. |
| [Új – AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Virtuális hálózatot hoz létre. |
| [Új – AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Egy nyilvános IP-címet hoz létre. |
| [Új – AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) | Létrehozza egy terheléselosztó előtérbeli IP-konfigurációját. |
| [Új – AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) | Háttérbeli címkészlet-konfigurációt hoz létre egy terheléselosztóhoz. |
| [Új – AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) | Létrehoz egy bejövő NAT-szabálykonfigurációt egy terheléselosztó számára. |
| [Új – AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) | Terheléselosztót hoz létre. |
| [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/new-azloadbalancerprobeconfig) | Létrehoz egy mintavételi konfigurációt egy terheléselosztó számára. |
| [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerruleconfig) | Létrehoz egy szabálykonfigurációt egy terheléselosztó számára. |
| [Set-AzLoadBalancer](/powershell/module/az.Network/Set-azLoadBalancer) | Frissíti a terheléselosztót a megadott információkkal. |
| [Új – AzVmssIpConfig](/powershell/module/az.Compute/New-azVmssIpConfig) | IP-konfigurációt hoz létre a méretezési csoport virtuálisgép-példányaihoz. A virtuálisgép-példányok a terheléselosztó háttérkészletéhez, a NAT-készlethez és a virtuális hálózat alhálózatához csatlakoznak. |
| [Új – AzVmssConfig](/powershell/module/az.Compute/New-azVmssConfig) | Méretezésicsoport-konfigurációt hoz létre. Ebbe a konfigurációba olyan információk tartoznak, mint a létrehozni kívánt virtuálisgép-példányok száma, a virtuális gép SKU (méret) és a frissítési szabályzat módja. A konfiguráció további parancsmagokkal van hozzáadva, és a méretezési csoport létrehozása során használatos. |
| [Set-AzVmssStorageProfile](/powershell/module/az.Compute/Set-azVmssStorageProfile) | Meghatározza a virtuálisgép-példányokhoz használni kívánt rendszerképet, és hozzáadja azt a méretezési csoport konfigurációjához. |
| [Set-AzVmssOsProfile](/powershell/module/az.Compute/Set-azVmssStorageProfile) | Megadja a rendszergazdai felhasználónevet és jelszót, valamint a virtuális gép elnevezési előtagját. Ezeket az értékeket hozzáadja a méretezési csoport konfigurációjához. |
| [Add-AzVmssNetworkInterfaceConfiguration](/powershell/module/az.Compute/Add-azVmssNetworkInterfaceConfiguration) | Virtuális hálózati adaptert ad hozzá a virtuálisgép-példányokhoz az IP-konfiguráció alapján. Ezeket az értékeket hozzáadja a méretezési csoport konfigurációjához. |
| [Új – AzVmss](/powershell/module/az.Compute/New-azVmss) | Létrehozza a méretezési csoportot a méretezési csoport konfigurációjában megadott információk alapján. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Eltávolít egy erőforráscsoportot és az összes abban található erőforrást. |

## <a name="next-steps"></a>Következő lépések
Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/overview).

A virtuálisgép-méretezési csoportokhoz használható további PowerShell-példaszkripteket az [Azure virtuálisgép-méretezési csoportokra vonatkozó dokumentációjában](../powershell-samples.md) találhat.
