---
title: "Az Azure CLI Script Sample – a Linux virtuális gép titkosítása |} Microsoft Docs"
description: "Az Azure CLI Script Sample – a Linux virtuális gépek titkosítása"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/15/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 36bbce089ef7626a3e4c433eb9fb2165e7fdcdd1
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/16/2017
---
# <a name="encrypt-a-linux-virtual-machine-in-azure"></a>Az Azure-ban egy Linux virtuális gép titkosítása

Ez a parancsfájl létrehoz egy biztonságos Azure Key Vault, a titkosítási kulcsokat, az Azure Active Directory szolgáltatás egyszerű és a Linux virtuális gépek (VM). A virtuális Gépet a rendszer ezután titkosítja a Key Vault és a szolgáltatás egyszerű hitelesítő adatait a titkosítási kulcs használatával.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Mintaparancsfájl

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/encrypt-disks/encrypt_vm.sh "Encrypt VM disks")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

A következő parancsot az erőforráscsoport, virtuális gép és az összes kapcsolódó erőforrások eltávolítása.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Parancsfájl ismertetése

A parancsfájl a következő parancsokat egy erőforráscsoport, az Azure Key Vault, egyszerű szolgáltatásnév, virtuális gép és minden kapcsolódó erőforrás létrehozásához. Minden egyes parancsa a tábla-parancs adott dokumentációjára mutató hivatkozásokat.

| Parancs | Megjegyzések |
|---|---|
| [az csoport létrehozása](https://docs.microsoft.com/cli/azure/group#az_group_create) | Az összes erőforrás tároló erőforrás csoportot hoz létre. |
| [az keyvault létrehozása](https://docs.microsoft.com/cli/azure/keyvault#az_keyvault_create) | Létrehoz egy Azure Key Vault, például a titkosítási kulcsok biztonságos adatok tárolására. |
| [az keyvault kulcs létrehozása](https://docs.microsoft.com/cli/azure/keyvault/key#az_keyvault_key_create) | A Key Vault egy titkosítási kulcsot hoz létre. |
| [az ad sp létrehozása-az-rbac](https://docs.microsoft.com/cli/azure/ad/sp#az_ad_sp_create_for_rbac) | Létrehoz egy Azure Active Directory szolgáltatás egyszerű biztonságosan hitelesítéséhez és titkosítási kulcsok való hozzáférés szabályozása. |
| [az keyvault-szabály beállítása](https://docs.microsoft.com/cli/azure/keyvault#az_keyvault_set_policy) | A szolgáltatás egyszerű hozzáférést biztosít a titkosítási kulcsokat a Key Vault engedélyeinek beállítása. |
| [az virtuális gép létrehozása](https://docs.microsoft.com/cli/azure/vm#az_vm_create) | A virtuális gépet hoz létre, és csatlakozik a hálózati kártya, virtuális hálózatot, alhálózatot és NSG. Ez a parancs is meghatározza a használandó virtuálisgép-lemezkép, és rendszergazdai hitelesítő adatait.  |
| [az vm-titkosítás engedélyezése](https://docs.microsoft.com/cli/azure/vm/encryption#az_vm_encryption_enable) | Engedélyezi a titkosítás használatát a virtuális gépek, a szolgáltatás egyszerű hitelesítő adatait és a titkosítási kulcs segítségével. |
| [az vm titkosítási megjelenítése](https://docs.microsoft.com/cli/azure/vm/encryption#az_vm_encryption_show) | A virtuális gép titkosítási folyamat állapotát jeleníti meg. |
| [az csoport törlése](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | Egy olyan erőforráscsoport, beleértve az összes beágyazott erőforrások törlése. |

## <a name="next-steps"></a>Következő lépések

További információ az Azure parancssori felület: [Azure CLI dokumentáció](https://docs.microsoft.com/cli/azure/overview).

További virtuális gép CLI parancsfájl minták megtalálhatók a [Azure Linux virtuális dokumentációját](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
