---
title: "Az Azure PowerShell-parancsfájl a minta - OMS |} Microsoft Docs"
description: "Az Azure PowerShell-parancsfájl a minta - OMS"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/12/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 2f9303568838113335343a420913b8dcb84cb49c
ms.sourcegitcommit: d247d29b70bdb3044bff6a78443f275c4a943b11
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2017
---
# <a name="create-an-operations-management-suite-monitored-vm-with-powershell"></a>Hozzon létre egy Operations Management Suite figyelt VM a PowerShell használatával

Ezt a parancsfájlt hoz létre egy Azure virtuális gépet, telepíti az Operations Management Suite (OMS) ügynök, és regisztrálja a rendszer az OMS-munkaterület. Miután a parancsfájl lefutott, a virtuális gép lesz látható az OMS-konzolon. Emellett szeretné frissíteni az OMS-munkaterület azonosítója és a munkaterület kulcsot.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Mintaparancsfájl

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-monitor-oms/create-windows-vm-detailed-oms.ps1 "Create VM OMS")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

A következő parancsot az erőforráscsoport, virtuális gép és az összes kapcsolódó erőforrások eltávolítása.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Parancsfájl ismertetése

A parancsfájl a következő parancsokat a központi telepítés létrehozásához. A parancs adott dokumentáció tábla mutató összes elemére.

| Parancs | Megjegyzések |
|---|---|
| [Új-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Az összes erőforrás tároló erőforrás csoportot hoz létre. |
| [Új AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) | A virtuális gépet hoz létre, és csatlakozik a hálózati kártya, virtuális hálózatot, alhálózatot és hálózati biztonsági csoport. Ez a parancs is 80-as portot nyitja meg, és beállítja a rendszergazdai hitelesítő adatokkal. |
| [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) | A virtuális gépet egy Virtuálisgép-bővítmény hozzáadása. Ebben az esetben az Operations Management Suite ügynök kiterjesztést az OMS-ügynököt telepíteni és regisztrálni az OMS-munkaterület virtuális gép használja. |
|[Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Eltávolítja az erőforráscsoportot és belül található összes erőforrást. |

## <a name="next-steps"></a>Következő lépések

Az Azure PowerShell modul további információkért lásd: [Azure PowerShell dokumentációs](/powershell/azure/overview).

További virtuális gép PowerShell-parancsfájl példák találhatók a [Azure Windows virtuális dokumentációját](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
