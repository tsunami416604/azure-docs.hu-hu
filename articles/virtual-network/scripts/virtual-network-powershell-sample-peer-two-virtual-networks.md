---
title: Társ két virtuális hálózat – Azure PowerShell szkript minta
description: Azure PowerShell-példaszkript – Társviszony létesítése két virtuális hálózat között
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/20/2018
ms.author: kumud
ms.openlocfilehash: 6efc96565714021b15eb0a44cb69f71fe78e817a
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87068729"
---
# <a name="peer-two-virtual-networks-script-sample"></a>Példaszkript társviszony létesítéséhez két virtuális hálózat között

Ez a példaszkript két virtuális hálózatot hoz létre és csatlakoztat ugyanabban a régióban az Azure-hálózaton keresztül. A szkript futtatása után társviszonyt fog létesíteni a két virtuális hálózat között.

A szkriptet az Azure [Cloud Shellben](https://shell.azure.com/powershell) vagy egy helyi PowerShell-telepítésből futtathatja. Ha a PowerShellt helyileg használja, a parancsfájlhoz az az PowerShell-modul 5.4.1-es vagy újabb verziójára lesz szükség. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/virtual-network/peer-two-virtual-networks/peer-two-virtual-networks.ps1 "Peer two networks")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

Az alábbi paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás:

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja egy erőforráscsoport, egy virtuális gép és minden kapcsolódó erőforrás létrehozásához. Az alábbi táblában lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [Új – AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)| Létrehoz egy Azure-beli virtuális hálózatot és alhálózatot. |
| [Add-AzVirtualNetworkPeering](/powershell/module/az.network/add-azvirtualnetworkpeering) | Társviszonyt létesít két virtuális hálózat között.  |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](/powershell/azure/).

A [Virtuális hálózatokra vonatkozó PowerShell-minták](../powershell-samples.md) között további, a virtuális hálózatokra vonatkozó PowerShell-példaszkripteket talál.