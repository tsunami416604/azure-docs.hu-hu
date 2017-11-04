---
title: "Az Azure CLI-parancsfájlt minták – Windows Server 2016 virtuális gép létrehozása az IIS-kiszolgálón a DSC használata |} Microsoft Docs"
description: "Az Azure CLI-parancsfájlt minták – Windows Server 2016 virtuális gép létrehozása az IIS-kiszolgálón a DSC használata"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: rickstercdn
manager: timlt
editor: tysonn
tags: 
ms.assetid: 
ms.service: virtual-machines-Windows
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/23/2017
ms.author: rclaus
ms.custom: mvc
ms.openlocfilehash: 96516cb374841e663f831957ccbc6690e801154d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-vm-with-iis-using-dsc"></a>Virtuális gép létrehozása az IIS-kiszolgálón a DSC használata

Ez a parancsfájl létrehoz egy virtuális gépet, és az Azure virtuális gépek DSC egyéni parancsprogramok futtatására szolgáló bővítmény segítségével IIS telepítése és konfigurálása. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Mintaparancsfájl

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-windows-iis-using-dsc/create-windows-iis-using-dsc.sh "Quick Create VM")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

A következő parancsot az erőforráscsoport, virtuális gép és az összes kapcsolódó erőforrások eltávolítása.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>Parancsfájl ismertetése

A parancsfájl a következő parancsokat egy erőforráscsoport, virtuális gép és minden kapcsolódó erőforrás létrehozásához. Minden egyes parancsa a tábla-parancs adott dokumentációjára mutató hivatkozásokat.

| Parancs | Megjegyzések |
|---|---|
| [az csoport létrehozása](https://docs.microsoft.com/cli/azure/group#az_group_create) | Az összes erőforrás tároló erőforrás csoportot hoz létre. |
| [az virtuális gép létrehozása](https://docs.microsoft.com/cli/azure/vm#az_vm_create) | A virtuális gépet hoz létre, és csatlakozik a hálózati kártya, virtuális hálózatot, alhálózatot és NSG. Ez a parancs is meghatározza a használandó virtuálisgép-lemezkép, és rendszergazdai hitelesítő adatait.  |
| [az a virtuális gép bővítmény csoportjának](https://docs.microsoft.com/cli/azure/vm#az_vm_create) | Az egyéni parancsprogramok futtatására szolgáló bővítmény felvétele a virtuális gépet, mely hív meg telepíteni az IIS-parancsfájlt. |
| [az vm-port megnyitása](https://docs.microsoft.com/cli/azure/vm#az_vm_open_port) | Bejövő adatforgalom engedélyezésére a hálózati biztonsági csoport szabályt hoz létre. Ez a példa a 80-as port a HTTP-forgalom van megnyitva. |
| [az csoport törlése](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | Egy olyan erőforráscsoport, beleértve az összes beágyazott erőforrások törlése. |

## <a name="next-steps"></a>Következő lépések

További információ az Azure parancssori felület: [Azure CLI dokumentáció](https://docs.microsoft.com/cli/azure/overview).

További virtuális gép CLI parancsfájl minták megtalálhatók a [Azure Windows virtuális dokumentációját](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
