---
title: Azure CLI-példaszkript – IIS-sel rendelkező Windows Server 2016 virtuális gép létrehozása DSC használatával | Microsoft Docs
description: Azure CLI-példaszkript – IIS-sel rendelkező Windows Server 2016 virtuális gép létrehozása DSC használatával
services: virtual-machines-windows
documentationcenter: virtual-machines
author: rickstercdn
manager: jeconnoc
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: virtual-machines-Windows
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/23/2017
ms.author: rclaus
ms.custom: mvc
ms.openlocfilehash: 6cc689471247af3782e7e733be463cfc3e21afe5
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54478433"
---
# <a name="create-a-vm-with-iis-using-dsc"></a>IIS-sel rendelkező virtuális gép létrehozása DSC használatával

Ez a szkript létrehoz egy virtuális gépet, és az Azure Virtual Machine DSC egyéni szkriptbővítménnyel telepíti és konfigurálja az IIS-t. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-windows-iis-using-dsc/create-windows-iis-using-dsc.sh "Quick Create VM")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

Az alábbi paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja egy erőforráscsoport, egy virtuális gép és minden kapcsolódó erőforrás létrehozásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm#az_vm_create) | Létrehozza a virtuális gépet, és csatlakoztatja a hálózati kártyához, a virtuális hálózathoz, az alhálózathoz és az NSG-hez. A parancs megadja továbbá a használandó virtuálisgép-rendszerképet és a rendszergazdai jelszavakat.  |
| [az vm extension set](https://docs.microsoft.com/cli/azure/vm#az_vm_create) | Hozzáadja az egyéni szkriptbővítményt a virtuális géphez, amely meghív egy szkriptet az IIS telepítésére. |
| [az vm open-port](https://docs.microsoft.com/cli/azure/vm) | Létrehoz egy hálózati biztonsági csoportra vonatkozó szabályt a befelé irányuló forgalom engedélyezésére. Ebben a példában a 80-as portot nyitjuk meg a HTTP-forgalom számára. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure).

További virtuális gép CLI-példaszkripteket az [Azure Windows virtuális gépekre vonatkozó dokumentációban](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) találhat.
