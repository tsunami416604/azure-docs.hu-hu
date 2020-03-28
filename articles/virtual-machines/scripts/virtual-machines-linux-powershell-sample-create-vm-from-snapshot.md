---
title: Virtuális gép létrehozása pillanatképből – PowerShell-minta
description: Azure PowerShell-parancsfájlminta – Virtuális gép létrehozása pillanatképből
services: virtual-machines-linux
documentationcenter: virtual-machines
author: ramankumarlive
manager: kavithag
editor: ramankum
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/10/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: 21e31df7552dfe5e6368235173622695078626b9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75463659"
---
# <a name="create-a-virtual-machine-from-a-snapshot-with-powershell"></a>Virtuális gép létrehozása pillanatképből a PowerShell használatával

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

Ez a parancsfájl a következő parancsokat használja a pillanatkép tulajdonságainak lefelvételéhez, a felügyelt lemez pillanatképből való létrehozásához és a virtuális gép létrehozásához. A táblázatban lévő összes elem a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [Get-AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/get-azsnapshot) | Pillanatkép leése a pillanatkép nevével. |
| [Új-AzDiskConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azdiskconfig) | Lemezkonfigurációt hoz létre. Ez a konfiguráció a lemezlétrehozási folyamathoz használatos. |
| [Új-Azdisk](https://docs.microsoft.com/powershell/module/az.compute/new-azdisk) | Felügyelt lemezt hoz létre. |
| [Új-AzVMConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azvmconfig) | Egy virtuálisgép-konfigurációt hoz létre. Ebben a konfigurációban olyan információk szerepelnek, mint a virtuális gép neve, az operációs rendszer és a rendszergazdai hitelesítő adatok. A rendszer a virtuális gépek létrehozása során használja ezt a konfigurációt. |
| [Set-AzVMOSDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk) | A felügyelt lemez csatlakoztatása operációs rendszerlemezként a virtuális géphez |
| [Új-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress) | Egy nyilvános IP-címet hoz létre. |
| [Új-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface) | Hálózati adaptert hoz létre. |
| [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) | Létrehoz egy virtuális gépet. |
|[Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Eltávolít egy erőforráscsoportot és az összes abban található erőforrást. |

## <a name="next-steps"></a>További lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/overview).

A virtuális gépekhez kapcsolódó további PowerShell-példaszkripteket az [Azure Linux rendszerű virtuális gépekre vonatkozó dokumentációjában](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) találhat.