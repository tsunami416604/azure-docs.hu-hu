---
title: "Az Azure CLI-parancsfájlt minta - kiszámításához blob-tároló mérete |} Microsoft Docs"
description: "Az Azure Blob storage-tároló méretének kiszámításához a tárolóban lévő blobok méretének összegzésével."
services: storage
documentationcenter: na
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: sample
ms.date: 06/28/2017
ms.author: marsma
ms.openlocfilehash: 21d49f1ef2f0f9e93e72dcd2a1667033b234bab6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="calculate-the-size-of-a-blob-storage-container"></a>A Blob storage-tároló méretének kiszámítása

Ez a parancsfájl az Azure Blob storage-tároló mérete a tárolóban lévő blobok méretének összegzésével számítja ki.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Mintaparancsfájl

[!code-azurecli[main](../../../cli_scripts/storage/calculate-container-size/calculate-container-size.sh?highlight=2-3 "Calculate container size")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

A következő parancsot az erőforráscsoport, a tároló és az összes kapcsolódó erőforrások eltávolításához.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Parancsfájl ismertetése

A parancsfájl a következő parancsokat a Blob storage tárolót méretének kiszámításához. A tábla hivatkozások parancs vonatkozó dokumentációt összes elemére.

| Parancs | Megjegyzések |
|---|---|
| [az csoport létrehozása](/cli/azure/group#create) | Az összes erőforrás tároló erőforrás csoportot hoz létre. |
| [az tárolási blob feltöltése](/cli/azure/storage/account#create) | Helyi fájlok feltölt egy Azure Blob storage tárolók. |
| [az tárolási blob listája](/cli/azure/storage/account/keys#list) | Egy Azure Blob storage tárolóban lévő blobok sorolja fel. |

## <a name="next-steps"></a>Következő lépések

További információ az Azure parancssori felület: [Azure CLI dokumentáció](/cli/azure/overview).

További tárhely CLI parancsfájl minták megtalálhatók a [Azure Blob Storage Azure CLI minták](../blobs/storage-samples-blobs-cli.md).
