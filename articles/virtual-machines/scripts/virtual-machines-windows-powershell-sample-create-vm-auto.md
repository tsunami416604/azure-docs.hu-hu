---
title: "Windows virtuális gép létrehozása új-AzVM az Azure-ban |} Microsoft Docs"
description: "Az Azure PowerShell-parancsfájl minták – Windows virtuális gép létrehozása a New-AzVM parancsmag."
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/29/2017
ms.author: cynthn
ms.openlocfilehash: 36660ea1e8609ea8a03f4fa63e6f12f765bf9721
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-fully-configured-virtual-machine-with-the-new-azvm-powershell-cmdlet-preview"></a>A teljesen konfigurált virtuális gép létrehozása a New-AzVM PowerShell-parancsmaggal (előzetes verzió)

A parancsfájl a New-AzVM parancsmag felhő rendszerhéj segítségével hozzon létre egy Azure virtuális gépen futó Windows Server 2016. A parancsfájl futtatása után a virtuális gép RDP keresztül érheti el. Felhő rendszerhéj a modult, amely tartalmazza a New-AzVM megkönnyíti, hogy tekintse meg az új funkció alapértelmezés szerint telepítve van.

Ez a minta a New-AzVM parancsmagot, amely jelenleg előzetes verzióban érhető használja. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

## <a name="sample-script"></a>Mintaparancsfájl


```azurepowershell-interactive
New-AzVM -Name myVM `
    -VirtualNetworkName myVNet `
    -Location westeurope `
    -SecurityGroupName myNSG `
    -PublicIpAddressName myPIP 
```


## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

A következő parancsot az erőforráscsoport, virtuális gép és az összes kapcsolódó erőforrások eltávolítása.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myVMResourceGroup
```


## <a name="next-steps"></a>Következő lépések

Az Azure PowerShell modul további információkért lásd: [Azure PowerShell dokumentációs](/powershell/azure/overview).

További virtuális gép PowerShell-parancsfájl példák találhatók a [Azure Windows virtuális dokumentációját](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
