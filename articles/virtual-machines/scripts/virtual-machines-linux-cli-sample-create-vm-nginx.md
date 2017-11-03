---
title: "Az Azure CLI Script Sample – a Linux virtuális gép létrehozása az NGINX |} Microsoft Docs"
description: "Az Azure CLI parancsfájl-mintában - NGINX Linux virtuális gép létrehozása"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/27/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 0a6c33d84f1fab85e6ed2933c47c041ca2e59520
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-vm-with-nginx"></a>Hozzon létre egy virtuális gép NGINX

Ez a parancsfájl létrehoz egy Azure virtuális gépet, és használja az Azure virtuális gép egyéni parancsprogramok futtatására szolgáló bővítmény NGINX telepítéséhez. A parancsfájl futtatása után hozzáférhet a nyilvános IP-címet a virtuális gép egy bemutató webhelyet.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Mintaparancsfájl

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-nginx/create-vm-nginx.sh "Quick Create VM")]

## <a name="custom-script-extension"></a>Egyéni szkriptbővítmény

Az egyéni parancsprogramok futtatására szolgáló bővítmény másolja át a virtuális gép ezt a parancsfájlt. A parancsfájlt majd telepítését és konfigurálását egy olyan NGINX-webkiszolgálón. 

```bash
#!/bin/bash

# update package source
apt-get -y update

# install NGINX
apt-get -y install nginx
```

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

A következő parancsot az erőforráscsoport, virtuális gép és az összes kapcsolódó erőforrások eltávolítása.

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Parancsfájl ismertetése

A parancsfájl a következő parancsokat egy erőforráscsoport, virtuális gép és minden kapcsolódó erőforrás létrehozásához. Minden egyes parancsa a tábla-parancs adott dokumentációjára mutató hivatkozásokat.

| Parancs | Megjegyzések |
|---|---|
| [az csoport létrehozása](https://docs.microsoft.com/cli/azure/group#az_group_create) | Az összes erőforrás tároló erőforrás csoportot hoz létre. |
| [az virtuális gép létrehozása](https://docs.microsoft.com/cli/azure/vm#az_vm_create) | A virtuális gépet hoz létre. Ez a parancs is meghatározza a használandó virtuálisgép-lemezkép, és rendszergazdai hitelesítő adatait.  |
| [az vm-port megnyitása](https://docs.microsoft.com/cli/azure/network/nsg/rule#az_network_nsg_rule_create) | Bejövő adatforgalom engedélyezésére a hálózati biztonsági csoport szabályt hoz létre. Ez a példa a 80-as port a HTTP-forgalom van megnyitva. |
| [Azure virtuális gép bővítmény beállítása](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | Hozzáadja, és futtat egy virtuálisgép-bővítményt egy virtuális géphez. Ez a példa az egyéni parancsprogramok futtatására szolgáló bővítmény segítségével NGINX telepítése.|
| [az csoport törlése](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | Egy olyan erőforráscsoport, beleértve az összes beágyazott erőforrások törlése. |

## <a name="next-steps"></a>Következő lépések

További információ az Azure parancssori felület: [Azure CLI dokumentáció](https://docs.microsoft.com/cli/azure/overview).

További virtuális gép CLI parancsfájl minták megtalálhatók a [Azure Linux virtuális dokumentációját](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
