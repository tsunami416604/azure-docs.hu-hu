---
title: Virtuális gép létrehozása pillanatképből (Windows) – PowerShell-minta
description: Azure PowerShell szkript minta – virtuális gép létrehozása pillanatképből
services: virtual-machines-windows
documentationcenter: virtual-machines
author: ramankumarlive
manager: kavithag
editor: ramankum
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/10/2017
ms.author: ramankum
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: ff0a94fd4b36602863281fb91e261f31d5ad0b39
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2020
ms.locfileid: "89072588"
---
# <a name="create-a-virtual-machine-from-a-snapshot-with-powershell-windows"></a>Virtuális gép létrehozása pillanatképből a PowerShell használatával (Windows)

Ez a szkript létrehoz egy virtuális gépet egy operációsrendszer-lemez pillanatképéből. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-from-snapshot/create-vm-from-snapshot.ps1 "Create VM from managed os disk")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

Az alábbi paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja a pillanatkép-tulajdonságok lekéréséhez, egy felügyelt lemez létrehozásához a pillanatképből, és létrehoz egy virtuális gépet. A táblázatban lévő összes elem a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [Get-AzSnapshot](/powershell/module/az.compute/get-azsnapshot) | A pillanatkép-név használatával beolvassa a pillanatképet. |
| [Új – AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) | Létrehoz egy lemez-konfigurációt. Ezt a konfigurációt a lemez létrehozási folyamata használja. |
| [Új – AzDisk](/powershell/module/az.compute/new-azdisk) | Létrehoz egy felügyelt lemezt. |
| [Új – AzVMConfig](/powershell/module/az.compute/new-azvmconfig) | Egy virtuálisgép-konfigurációt hoz létre. Ebben a konfigurációban olyan információk szerepelnek, mint a virtuális gép neve, az operációs rendszer és a rendszergazdai hitelesítő adatok. A rendszer a virtuális gépek létrehozása során használja ezt a konfigurációt. |
| [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk) | A felügyelt lemezt operációsrendszer-lemezként csatolja a virtuális géphez |
| [Új – AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Egy nyilvános IP-címet hoz létre. |
| [Új – AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) | Hálózati adaptert hoz létre. |
| [New-AzVM](/powershell/module/az.compute/new-azvm) | Létrehoz egy virtuális gépet. |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Eltávolít egy erőforráscsoportot és az összes abban található erőforrást. |

## <a name="next-steps"></a>Következő lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/).

A virtuális gépekhez kapcsolódó további PowerShell-példaszkripteket az [Azure Windows rendszerű virtuális gépekre vonatkozó dokumentációjában](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) találhat.
