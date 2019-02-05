---
title: Azure CLI-példaszkript – Tárfiók hozzáférési kulcsainak rotálása | Microsoft Docs
description: Egy Azure Storage-fiók létrehozása, majd a hozzáférési kulcsainak lekérése és rotálása.
services: storage
documentationcenter: na
author: tamram
manager: timlt
editor: tysonn
ms.assetid: ''
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: sample
ms.date: 06/22/2017
ms.author: tamram
ms.openlocfilehash: ac58886225221677aa003833167ff58cd578255d
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2019
ms.locfileid: "55693921"
---
# <a name="create-a-storage-account-and-rotate-its-account-access-keys"></a>Tárfiók létrehozása és a hozzáférési kulcsainak rotálása

Ez a példaszkript létrehoz egy Azure Storage-fiókot, megjeleníti az új tárfiók hozzáférési kulcsait, majd megújítja (rotálja) a kulcsokat.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/storage/rotate-storage-account-keys/rotate-storage-account-keys.sh "Rotate storage account keys")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

Az alábbi paranccsal eltávolítható az erőforráscsoport, a tárfiók és az összes kapcsolódó erőforrás.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja a tárfiók létrehozásához, illetve a hozzáférési kulcsok lekéréséhez és rotálásához. A táblázatban lévő összes elem a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az group create](/cli/azure/group) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az storage account create](/cli/azure/storage/account) | Létrehoz egy Azure Storage-fiókot a megadott erőforráscsoportban. |
| [az storage account keys list](/cli/azure/storage/account/keys) | Megjeleníti a megadott tárfiók hozzáférési kulcsait. |
| [az storage account keys renew](/cli/azure/storage/account/keys) | Újra létrehozza a tárfiók elsődleges vagy másodlagos hozzáférési kulcsát. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További Storage CLI-példaszkripteket [az Azure Blob Storage Azure CLI-példái](../blobs/storage-samples-blobs-cli.md) között találhat.
