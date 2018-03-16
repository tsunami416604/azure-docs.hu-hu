---
title: "Azure CLI-példaszkript – Tárfiók hozzáférési kulcsainak rotálása | Microsoft Docs"
description: "Egy Azure Storage-fiók létrehozása, majd a hozzáférési kulcsainak lekérése és rotálása."
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
ms.author: tamram
ms.openlocfilehash: 160509a5a82b71b281d57d97e103bb4190605b7c
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2018
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
| [az group create](/cli/azure/group#az_group_create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az storage account create](/cli/azure/storage/account#az_storage_account_create) | Létrehoz egy Azure Storage-fiókot a megadott erőforráscsoportban. |
| [az storage account keys list](/cli/azure/storage/account/keys#az_storage_account_keys_list) | Megjeleníti a megadott tárfiók hozzáférési kulcsait. |
| [az storage account keys renew](/cli/azure/storage/account/keys#az_storage_account_keys_renew) | Újra létrehozza a tárfiók elsődleges vagy másodlagos hozzáférési kulcsát. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További Storage CLI-példaszkripteket [az Azure Blob Storage Azure CLI-példái](../blobs/storage-samples-blobs-cli.md) között találhat.
