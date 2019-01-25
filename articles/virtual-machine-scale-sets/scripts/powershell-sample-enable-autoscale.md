---
title: Azure PowerShell-minták – Gazdagépalapú automatikus méretezés engedélyezése | Microsoft Docs
description: Azure PowerShell-minták
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
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: ee5103434ed1ad7e123b6ce4ed67809ac11fd2b0
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54882786"
---
# <a name="automatically-scale-a-virtual-machine-scale-set-with-powershell"></a>Virtuálisgép-méretezési csoport automatikus méretezése a PowerShell-lel
Ez a szkript egy Windows Server 2016 rendszert futtató virtuálisgép-méretezési csoportot hoz létre, illetve gazdagépalapú mérőszámok használatával automatikusan elvégzi a méretezést, amely a CPU terhelésének változásaihoz igazodik.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript
[!code-powershell[main](../../../powershell_scripts/virtual-machine-scale-sets/auto-scale-host-metrics/auto-scale-host-metrics.ps1 "Automatically scale a virtual machine scale set")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása
Az alábbi paranccsal eltávolítható az erőforráscsoport, a méretezési csoport és az összes kapcsolódó erőforrás.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Szkript ismertetése
A szkript a következő parancsokat használja az üzemelő példány létrehozásához. A táblázatban lévő összes elem a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss) | Létrehozza a virtuálisgép-méretezési csoportot és az összes támogató erőforrást, beleértve a virtuális hálózatot, a terheléselosztót és a NAT-szabályokat. |
| [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss) | Lekéri a virtuálisgép-méretezési csoport adatait. |
| [Add-AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension) | Egy virtuálisgép-bővítményt ad hozzá az egyéni szkriptekhez egy alapszintű webalkalmazás telepítéséhez. |
| [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss) | Frissíti a virtuálisgép-méretezési csoport modelljét a virtuálisgép-bővítmény alkalmazására. |
| [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) | Lekéri a terheléselosztó által a felhasználóhoz hozzárendelt nyilvános IP-cím adatait. |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Eltávolít egy erőforráscsoportot és az összes abban található erőforrást. |

## <a name="next-steps"></a>További lépések
Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/overview).

A virtuálisgép-méretezési csoportokhoz használható további PowerShell-példaszkripteket az [Azure virtuálisgép-méretezési csoportokra vonatkozó dokumentációjában](../powershell-samples.md) találhat.