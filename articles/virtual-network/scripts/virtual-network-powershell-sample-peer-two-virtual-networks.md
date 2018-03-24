---
title: Az Azure PowerShell parancsfájl minta - társ két virtuális hálózatok |} Microsoft Docs
description: Az Azure PowerShell parancsfájl minta - társ két virtuális hálózatok
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: powershell
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/20/2018
ms.author: jdial
ms.openlocfilehash: c0efdf759a0bdb87de4dc8ff9566a8e817503c5e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="peer-two-virtual-networks"></a>A két partner virtuális hálózatok

Ezt a parancsfájlt hoz létre, és két virtuális hálózat ugyanabban a régióban csatlakozik az Azure-hálózatot. A parancsfájl futtatása után létrehozhat egy társviszony-létesítés virtuális hálózatok között.

A parancsfájl az Azure-ból végrehajthat [felhő rendszerhéj](https://shell.azure.com/powershell), vagy egy helyi PowerShell telepítési. Ha a PowerShell segítségével helyileg, ezt a parancsfájlt a AzureRM PowerShell modul verziója 5.4.1 szükséges-e vagy újabb. A telepített verzió megkereséséhez futtassa `Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Login-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!code-azurepowershell-interactive[main](../../../powershell_scripts/virtual-network/peer-two-virtual-networks/peer-two-virtual-networks.ps1 "Peer two networks")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

Futtassa a következő parancs futtatásával távolítsa el az erőforráscsoportot, virtuális gép és az összes kapcsolódó erőforrások:

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja egy erőforráscsoport, egy virtuális gép és minden kapcsolódó erőforrás létrehozásához. Minden parancs az alábbi táblázatban hivatkozások parancs adott dokumentációját:

| Parancs | Megjegyzések |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. | 
| [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork)| Létrehoz egy Azure-beli virtuális hálózatot és alhálózatot. |
| [Add-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/add-azurermvirtualnetworkpeering) | Létrehozza a társviszony-létesítés virtuális hálózatok között.  |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](/powershell/azure/overview).

További virtuális hálózati PowerShell parancsfájl minták található [virtuális hálózati PowerShell-példák](../powershell-samples.md).