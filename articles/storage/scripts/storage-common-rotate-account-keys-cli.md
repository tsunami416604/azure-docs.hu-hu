---
title: "Az Azure CLI parancsfájl minta - Elforgatás fiók tárelérési kulcsok |} Microsoft Docs"
description: "Hozzon létre egy Azure Storage-fiókot, majd kérje le, majd a fiók hozzáférési kulcsait elforgatása."
services: storage
documentationcenter: na
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: sample
ms.date: 06/22/2017
ms.author: marsma
ms.openlocfilehash: 743c7b01e82721e855b4c33bf2d36714ca8c6468
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/29/2017
---
# <a name="create-a-storage-account-and-rotate-its-account-access-keys"></a>Hozzon létre egy tárfiókot, és a fiók hozzáférési kulcsait elforgatása

Ezt a parancsfájlt hoz létre egy Azure Storage-fiók, az új tárfiók hozzáférési kulcsainak jeleníti meg, majd megújítja (forog) a kulcsokat.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Mintaparancsfájl

[!code-azurecli-interactive[main](../../../cli_scripts/storage/rotate-storage-account-keys/rotate-storage-account-keys.sh "Rotate storage account keys")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

A következő paranccsal távolítsa el az erőforráscsoportot, storage-fiók és minden kapcsolódó erőforrás.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Parancsfájl ismertetése

A parancsfájl a következő parancsokat a storage-fiók létrehozása és beolvasása és a hívóbetűk elforgatása. A tábla hivatkozások parancs vonatkozó dokumentációt összes elemére.

| Parancs | Megjegyzések |
|---|---|
| [az csoport létrehozása](/cli/azure/group#create) | Az összes erőforrás tároló erőforrás csoportot hoz létre. |
| [az storage-fiók létrehozása](/cli/azure/storage/account#create) | A megadott erőforráscsoport hoz létre egy Azure Storage-fiókot. |
| [az tárolási fióklista kulcsok](/cli/azure/storage/account/keys#list) | Megjeleníti a fiók tárelérési kulcsok a megadott fiók. |
| [az tárfiókkulcsok megújítása](/cli/azure/storage/account/keys#renew) | Az elsődleges vagy másodlagos tárfiók tárelérési kulcsainak újragenerálása. |

## <a name="next-steps"></a>Következő lépések

További információ az Azure parancssori felület: [Azure CLI dokumentáció](/cli/azure/overview).

További tárhely CLI parancsfájl minták megtalálhatók a [Azure Blob Storage Azure CLI minták](../blobs/storage-samples-blobs-cli.md).
