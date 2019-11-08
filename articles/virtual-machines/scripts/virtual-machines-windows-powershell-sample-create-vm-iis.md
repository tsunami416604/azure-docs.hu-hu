---
title: Azure PowerShell-példaszkript – IIS | Microsoft Docs
description: Azure PowerShell-példaszkript – IIS
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/12/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 0c8893336182ef3446bc654315396fb6dfa8ca13
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748682"
---
# <a name="create-an-iis-vm-with-powershell"></a>IIS-es virtuális gép létrehozása a PowerShell-lel

Ez a szkript egy Windows Server 2016-ot futtató Azure-beli virtuális gépet hoz létre, majd az Azure Virtual Machine egyéni szkriptbővítménnyel telepíti az IIS-t. A szkript futtatása után az alapértelmezett IIS-webhelyet a virtuális gép nyilvános IP-címén érheti el.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-iis/create-windows-vm-iis.ps1 "Create VM IIS")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

Az alábbi paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja az üzemelő példány létrehozásához. A táblázatban lévő összes elem a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) | Létrehozza a virtuális gépet, és csatlakoztatja a hálózati kártyához, virtuális hálózathoz, alhálózathoz és hálózati biztonsági csoporthoz. A parancs emellett megnyitja a 80-as portot, és beállítja a felügyeleti hitelesítő adatokat. |
| [Set-AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension) | Egy virtuálisgép-bővítményt ad hozzá a virtuális géphez. Ebben a példában az IIS telepítésére az egyéni szkriptbővítmény használatával kerül sor. |
|[Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Eltávolít egy erőforráscsoportot és az összes abban található erőforrást. |

## <a name="next-steps"></a>További lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/overview).

A virtuális gépekhez kapcsolódó további PowerShell-példaszkripteket az [Azure Windows rendszerű virtuális gépekre vonatkozó dokumentációjában](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) találhat.
