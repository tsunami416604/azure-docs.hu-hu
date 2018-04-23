---
title: Azure PowerShell-példaszkript – Társviszony létesítése két virtuális hálózat között | Microsoft Docs
description: Azure PowerShell-példaszkript – Társviszony létesítése két virtuális hálózat között
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/20/2018
ms.author: jdial
ms.openlocfilehash: 4fe4777b3adf585b2b6ddc862023905e756404d9
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
---
# <a name="peer-two-virtual-networks-script-sample"></a>Példaszkript társviszony létesítéséhez két virtuális hálózat között

Ez a példaszkript két virtuális hálózatot hoz létre és csatlakoztat ugyanabban a régióban az Azure-hálózaton keresztül. A szkript futtatása után társviszonyt fog létesíteni a két virtuális hálózat között.

A szkriptet az Azure [Cloud Shellben](https://shell.azure.com/powershell) vagy egy helyi PowerShell-telepítésből futtathatja. Ha helyileg használja a PowerShellt, ehhez a szkripthez az AzureRM PowerShell-modul 5.4.1-es vagy újabb verziójára lesz szükség. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!code-azurepowershell-interactive[main](../../../powershell_scripts/virtual-network/peer-two-virtual-networks/peer-two-virtual-networks.ps1 "Peer two networks")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

Az alábbi paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás:

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja egy erőforráscsoport, egy virtuális gép és minden kapcsolódó erőforrás létrehozásához. Az alábbi táblában lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. | 
| [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork)| Létrehoz egy Azure-beli virtuális hálózatot és alhálózatot. |
| [Add-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/add-azurermvirtualnetworkpeering) | Társviszonyt létesít két virtuális hálózat között.  |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](/powershell/azure/overview).

A [virtuális hálózatokra vonatkozó PowerShell-minták](../powershell-samples.md) között további, a virtuális hálózatokra vonatkozó PowerShell-példaszkripteket talál.
