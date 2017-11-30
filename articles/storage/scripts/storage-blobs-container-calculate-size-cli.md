---
title: "Az Azure CLI-parancsfájlt minta - kiszámításához blob-tároló mérete |} Microsoft Docs"
description: "Az Azure Blob storage-tároló méretének kiszámításához a tárolóban lévő blobok méretének összegzésével."
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
ms.date: 06/28/2017
ms.author: marsma
ms.openlocfilehash: ddaa656df4ee003dde44fe0e76c33eef8a996830
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/29/2017
---
# <a name="calculate-the-size-of-a-blob-storage-container"></a>A Blob storage-tároló méretének kiszámítása

Ez a parancsfájl az Azure Blob storage-tároló mérete a tárolóban lévő blobok méretének összegzésével számítja ki.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> A parancssori felület parancsfájl egy becsült mérete biztosít a tárolóhoz, és számlázási számítások nem használható.

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
