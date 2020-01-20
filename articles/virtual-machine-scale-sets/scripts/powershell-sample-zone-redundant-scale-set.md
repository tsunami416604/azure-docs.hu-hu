---
title: Azure PowerShell minták – zóna – redundáns méretezési csoport
description: Ez a szkript egy Windows Server 2016 rendszert futtató virtuálisgép-méretezési csoportot hoz létre több rendelkezésre állási zónában.
author: cynthn
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: sample
ms.date: 04/05/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 996701f554858a503d5b8fe8a8e2711c7f968c2e
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2020
ms.locfileid: "76276459"
---
# <a name="create-a-zone-redundant-virtual-machine-scale-set-with-powershell"></a>Zónaredundáns virtuálisgép-méretezési csoport létrehozása PowerShell segítségével
Ez a szkript egy Windows Server 2016 rendszert futtató virtuálisgép-méretezési csoportot hoz létre több rendelkezésre állási zónában. A szkript futtatása után a virtuális gépet RDP-kapcsolaton keresztül érheti el.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/virtual-machine-scale-sets/create-zone-redundant-scale-set/create-zone-redundant-scale-set.ps1 "Create zone-redundant scale set")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása
Az alábbi paranccsal eltávolítható az erőforráscsoport, a méretezési csoport és az összes kapcsolódó erőforrás.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Szkript ismertetése
A szkript a következő parancsokat használja az üzemelő példány létrehozásához. A táblázatban lévő összes elem a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [Új – AzVmss](/powershell/module/az.compute/new-azvmss) | Létrehozza a virtuálisgép-méretezési csoportot és az összes támogató erőforrást, beleértve a virtuális hálózatot, a terheléselosztót és a NAT-szabályokat. |
| [Get-AzVmss](/powershell/module/az.compute/get-azvmss) | Lekéri a virtuálisgép-méretezési csoport adatait. |
| [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) | Egy virtuálisgép-bővítményt ad hozzá az egyéni szkriptekhez egy alapszintű webalkalmazás telepítéséhez. |
| [Frissítés – AzVmss](/powershell/module/az.compute/update-azvmss) | Frissíti a virtuálisgép-méretezési csoport modelljét a virtuálisgép-bővítmény alkalmazására. |
| [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) | Lekéri a terheléselosztó által a felhasználóhoz hozzárendelt nyilvános IP-cím adatait. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Eltávolít egy erőforráscsoportot és az összes abban található erőforrást. |


## <a name="next-steps"></a>Következő lépések
Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/overview).

A virtuálisgép-méretezési csoportokhoz használható további PowerShell-példaszkripteket az [Azure virtuálisgép-méretezési csoportokra vonatkozó dokumentációjában](../powershell-samples.md) találhat.
