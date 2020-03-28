---
title: Azure CLI-példaszkript – Tárolók törlése előtag alapján | Microsoft Docs
description: Azure Storage-blobtárolók törlése a nevük előtagja alapján.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.devlang: cli
ms.topic: sample
ms.date: 06/22/2017
ms.author: tamram
ms.openlocfilehash: 391cc4c08b7067ef388c2130cb340fb5597c843f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "80067022"
---
# <a name="delete-containers-based-on-container-name-prefix"></a>Tárolók törlése a nevük előtagja alapján

Ez a szkript először létrehoz néhány példatárolót egy Azure-blobtárolón, majd töröl közülük néhányat a nevükben található előtag alapján.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/storage/delete-containers-by-prefix/delete-containers-by-prefix.sh?highlight=2-3 "Delete containers by prefix")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

Az alábbi paranccsal eltávolítható az erőforráscsoport, a maradék tárolók és az összes kapcsolódó erőforrás.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja a tárolók a nevük előtagja alapján történő törléséhez. A táblázatban lévő összes elem a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az group create](/cli/azure/group) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az storage account create](/cli/azure/storage/account) | Létrehoz egy Azure Storage-fiókot a megadott erőforráscsoportban. |
| [az storage container create](/cli/azure/storage/container) | Létrehoz egy tárolót az Azure Blob Storage-ban. |
| [az storage container list](/cli/azure/storage/container) | Felsorolja egy Azure Storage-fiók tárolóit. |
| [az storage container delete](/cli/azure/storage/container) | Törli egy Azure Storage-fiók tárolóit. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További Storage CLI-példaszkripteket az [Azure Storage Azure CLI-példái](../blobs/storage-samples-blobs-cli.md) között találhat.
