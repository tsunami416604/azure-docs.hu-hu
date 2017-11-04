---
title: "Az Azure CLI Script Sample – a virtuális gép létrehozása a VHD-vel |} Microsoft Docs"
description: "Az Azure CLI-parancsfájlt minta - egy virtuális merevlemez virtuális gép létrehozása."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: allclark
manager: douge
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/09/2017
ms.author: allclark
ms.custom: mvc
ms.openlocfilehash: 6234473d9f7f0eb18ea85e52273eb82a9ce04da5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-vm-with-a-virtual-hard-disk"></a>Virtuális gép létrehozása virtuális merevlemezzel

Ebben a példában egy virtuális gép virtuális merevlemez használatával hoz létre.
Létrehoz egy erőforráscsoport, a tárfiók és a tároló, majd létrehoz egy virtuális gép által a virtuális merevlemez feltölteni a tárolóba.
Azt a felváltja az ssh nyilvános kulcsát a nyilvános kulccsal úgy, hogy rendelkezik hozzáféréssel a virtuális Gépet.

Szüksége lesz egy rendszerindító virtuális Merevlemezt.
A virtuális Merevlemezt, amely használtuk letöltését https://azclisamples.blob.core.windows.net/vhds/sample.vhd vagy a saját virtuális merevlemez használatára. A parancsfájl megkeresi `~/sample.vhd`.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Mintaparancsfájl

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-vhd/create-vm-vhd.sh "Create VM using a VHD")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

A következő parancsot az erőforráscsoport, virtuális gép és az összes kapcsolódó erőforrások eltávolítása.

```azurecli-interactive 
az group delete -n az-cli-vhd
```

## <a name="script-explanation"></a>Parancsfájl ismertetése

A parancsfájl a következő parancsokat egy erőforráscsoport, virtuális gép, a rendelkezésre állási csoporthoz, terheléselosztó és minden kapcsolódó erőforrások létrehozásához. Minden egyes parancsa a tábla-parancs adott dokumentációjára mutató hivatkozásokat.

| Parancs | Megjegyzések |
|---|---|
| [az csoport létrehozása](https://docs.microsoft.com/cli/azure/group#az_group_create) | Az összes erőforrás tároló erőforrás csoportot hoz létre. |
| [az tárolási fiók listája](https://docs.microsoft.com/cli/azure/storage/account#az_storage_account_list) | Storage-fiókok listája |
| [az ellenőrzés-tárfióknév](https://docs.microsoft.com/cli/azure/storage/account#az_storage_account_check_name) | Ellenőrzi, hogy a tárfiók neve érvényes-e, és, hogy még nem létezik |
| [az tárolási fióklista kulcsok](https://docs.microsoft.com/cli/azure/storage/account/keys#az_storage_account_keys_list) | A tárfiókok kulcsait listája |
| [az a tárolási blob létezik](https://docs.microsoft.com/cli/azure/storage/blob#az_storage_blob_exists) | Ellenőrzi, hogy létezik-e a blob |
| [az a tároló létrehozása](https://docs.microsoft.com/cli/azure/storage/container#az_storage_container_create) | Egy tároló létrehoz egy tárfiókot. |
| [az tárolási blob feltöltése](https://docs.microsoft.com/cli/azure/storage/blob#az_storage_blob_upload) | A virtuális merevlemez feltöltésével hoz létre a blob a tárolóban. |
| [az vm listája](https://docs.microsoft.com/cli/azure/vm#az_vm_list) | A használt `--query` ellenőrizze, hogy a virtuális gép nevét használja. | 
| [az virtuális gép létrehozása](https://docs.microsoft.com/cli/azure/vm/availability-set#az_vm_availability_set_create) | A virtuális gépeket hoz létre. |
| [az vm access-linux-felhasználói fiók beállítása](https://docs.microsoft.com/cli/azure/vm/access#az_vm_access_set_linux_user) | Alaphelyzetbe állítja az SSH-kulcs az aktuális felhasználói hozzáférést biztosítanak a virtuális Gépet. |
| [az vm-ip-címeinek listáját](https://docs.microsoft.com/cli/azure/vm#az_vm_list-ip-addresses) | Lekérdezi a létrehozott virtuális IP-címét. |

## <a name="next-steps"></a>Következő lépések

További információ az Azure parancssori felület: [Azure CLI dokumentáció](https://docs.microsoft.com/cli/azure/overview).

További virtuális gép CLI parancsfájl minták megtalálhatók a [Azure Linux virtuális dokumentációját](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
