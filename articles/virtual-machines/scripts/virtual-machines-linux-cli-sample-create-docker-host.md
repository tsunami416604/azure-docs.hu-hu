---
title: "Az Azure CLI-parancsfájlt minták – hozzon létre egy Docker-állomás |} Microsoft Docs"
description: "Az Azure CLI-parancsfájlt minták – hozzon létre egy Docker-állomás"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/15/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: c1e0be0e305ba4d53d8e26c55a92b63e1291171d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-vm-with-docker"></a>Hozzon létre egy virtuális gép Docker

Ezt a parancsfájlt hoz létre egy virtuális gép Docker engedélyezve van, és elindítja egy Docker-tároló NGINX futtató. A parancsfájl futtatása után az Azure virtuális gép teljes Tartományneve keresztül érheti el az NGINX-webkiszolgálón. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Mintaparancsfájl

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-docker-host/create-docker-host.sh "Docker Host")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

A következő parancsot az erőforráscsoport, virtuális gép és az összes kapcsolódó erőforrások eltávolítása.

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Parancsfájl ismertetése

A parancsfájl a következő parancsokat a központi telepítés létrehozásához. A parancs adott dokumentáció tábla mutató összes elemére.

| Parancs | Megjegyzések |
|---|---|
| [az csoport létrehozása](https://docs.microsoft.com/cli/azure/group#az_group_create) | Az összes erőforrás tároló erőforrás csoportot hoz létre. |
| [az virtuális gép létrehozása](https://docs.microsoft.com/cli/azure/vm#az_vm_create) | A virtuális gépet hoz létre, és csatlakozik a hálózati kártya, virtuális hálózatot, alhálózatot és hálózati biztonsági csoport. Ez a parancs is meghatározza a használandó virtuálisgép-lemezkép, és rendszergazdai hitelesítő adatait.  |
| [az vm-port megnyitása](https://docs.microsoft.com/cli/azure/vm#az_vm_open_port) | Bejövő adatforgalom engedélyezésére a hálózati biztonsági csoport szabályt hoz létre. Ez a példa a 80-as port a HTTP-forgalom van megnyitva. |
| [Azure virtuális gép bővítmény beállítása](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | Hozzáadja, és futtat egy virtuálisgép-bővítményt egy virtuális géphez. Ez a példa a Docker Virtuálisgép-bővítmény konfigurálására szolgál egy Docker-állomás.|
| [az csoport törlése](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | Egy erőforráscsoport, beleértve az összes beágyazott erőforrások törlése. |

## <a name="next-steps"></a>Következő lépések

További információ az Azure parancssori felület: [Azure CLI dokumentáció](https://docs.microsoft.com/cli/azure/overview).

További virtuális gép CLI parancsfájl minták megtalálhatók a [Azure Linux virtuális dokumentációját](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
