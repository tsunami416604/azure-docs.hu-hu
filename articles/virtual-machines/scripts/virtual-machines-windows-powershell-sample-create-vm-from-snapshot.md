---
title: Azure PowerShell-Példaszkript – virtuális gép létrehozása pillanatképből |} A Microsoft Docs
description: Azure PowerShell-Példaszkript – virtuális gép létrehozása pillanatképből
services: virtual-machines-windows
documentationcenter: virtual-machines
author: ramankumarlive
manager: kavithag
editor: ramankum
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/10/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: c1a3edf282c20d2aec49a0ffb324ee0f9c62421c
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55244436"
---
# <a name="create-a-virtual-machine-from-a-snapshot-with-powershell"></a>Virtuális gép létrehozása pillanatképből a PowerShell-lel

Ez a szkript létrehoz egy virtuális gépet egy operációsrendszer-lemez pillanatképéből. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-from-snapshot/create-vm-from-snapshot.ps1 "Create VM from managed os disk")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

Az alábbi paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja a pillanatkép tulajdonságainak beolvasása, egy felügyelt lemez létrehozása pillanatképből, és hozzon létre egy virtuális Gépet. A táblázatban lévő összes elem a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [Get-AzureRmSnapshot](/powershell/module/azurerm.compute/get-azurermsnapshot) | Lekérdezi egy pillanatkép pillanatkép-név használatával. |
| [New-AzureRmDiskConfig](/powershell/module/azurerm.compute/new-azurermdiskconfig) | Létrehoz egy lemezkonfigurációt. Ez a konfiguráció a lemez létrehozási folyamata használnak. |
| [New-AzureRmDisk](/powershell/module/azurerm.compute/new-azurermdisk) | Egy felügyelt lemezt hoz létre. |
| [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) | Egy virtuálisgép-konfigurációt hoz létre. Ebben a konfigurációban olyan információk szerepelnek, mint a virtuális gép neve, az operációs rendszer és a rendszergazdai hitelesítő adatok. A rendszer a virtuális gépek létrehozása során használja ezt a konfigurációt. |
| [Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk) | A felügyelt lemez csatolása a virtuális gép operációsrendszer-lemezként |
| [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) | Egy nyilvános IP-címet hoz létre. |
| [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) | Hálózati adaptert hoz létre. |
| [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) | Létrehoz egy virtuális gépet. |
|[Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Eltávolít egy erőforráscsoportot és az összes abban található erőforrást. |

## <a name="next-steps"></a>További lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/overview).

A virtuális gépekhez kapcsolódó további PowerShell-példaszkripteket az [Azure Windows rendszerű virtuális gépekre vonatkozó dokumentációjában](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) találhat.