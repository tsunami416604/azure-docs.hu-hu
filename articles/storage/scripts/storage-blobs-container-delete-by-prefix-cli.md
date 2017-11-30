---
title: "Az Azure CLI parancsfájl minta - törlés tárolókban előtag alapján |} Microsoft Docs"
description: "Azure Storage-blob tárolók egy tárolója előtagján alapuló törlése."
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
ms.openlocfilehash: dbdd5d616f92c9314a3ce64a15c351eff368a2ab
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/29/2017
---
# <a name="delete-containers-based-on-container-name-prefix"></a>Tárolója előtagján alapuló tárolók törlése

Ez a parancsfájl először néhány minta tárolók létrehozása az Azure Blob storage, majd törli az egyes a tárolókban, a tároló neve előtag alapján.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Mintaparancsfájl

[!code-azurecli-interactive[main](../../../cli_scripts/storage/delete-containers-by-prefix/delete-containers-by-prefix.sh?highlight=2-3 "Delete containers by prefix")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

Távolítsa el az erőforráscsoportot, a fennmaradó tárolók, hogy a következő parancsot, és az összes kapcsolódó erőforrásokat.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Parancsfájl ismertetése

A parancsfájl a következő parancsok tárolója előtagján alapuló tárolók törlése. A tábla hivatkozások parancs vonatkozó dokumentációt összes elemére.

| Parancs | Megjegyzések |
|---|---|
| [az csoport létrehozása](/cli/azure/group#create) | Az összes erőforrás tároló erőforrás csoportot hoz létre. |
| [az storage-fiók létrehozása](/cli/azure/storage/account#create) | A megadott erőforráscsoport hoz létre egy Azure Storage-fiókot. |
| [az a tároló létrehozása](/cli/azure/storage/container#create) | Tárolót hoz létre az Azure Blob Storage tárolóban. |
| [az tárolási tároló listája](/cli/azure/storage/container#list) | Az Azure Storage-fiók tárolók listája. |
| [az tárolási tároló törlése](/cli/azure/storage/container#delete) | A tárolók egy Azure Storage-fiók törlése. |

## <a name="next-steps"></a>Következő lépések

További információ az Azure parancssori felület: [Azure CLI dokumentáció](/cli/azure/overview).

További tárhely CLI parancsfájl minták megtalálhatók a [az Azure Storage Azure CLI minták](../blobs/storage-samples-blobs-cli.md).
