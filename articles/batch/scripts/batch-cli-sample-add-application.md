---
title: "Azure CLI-példaszkript – Alkalmazás hozzáadása a Batch szolgáltatásban | Microsoft Docs"
description: "Azure CLI-példaszkript – Alkalmazás hozzáadása a Batch szolgáltatásban"
services: batch
documentationcenter: 
author: dlepow
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: batch
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/29/2018
ms.author: danlep
ms.openlocfilehash: 348e94e745350173196aeb64df3a814a05dd9144
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
---
# <a name="cli-example-add-an-application-to-an-azure-batch-account"></a>Parancssori felületi példa: Alkalmazás hozzáadása egy Azure Batch-fiókhoz

Ez a szkript bemutatja, hogyan lehet hozzáadni egy alkalmazást az Azure Batch-készlettel vagy feladattal való használatra. Amikor előkészít egy alkalmazást a Batch-fiókjához való hozzáadáshoz, csomagolja be a végrehajtható fájlt – valamennyi függőségével együtt – egy zip-fájlba. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure CLI 2.0.20-as vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli). 

## <a name="example-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/batch/add-application/add-application.sh "Add Application")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A következő paranccsal távolítható el az erőforráscsoport és az ahhoz kapcsolódó összes erőforrás.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja.
A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az storage account create](/cli/azure/storage/account#az_storage_account_create) | Létrehoz egy tárfiókot. |
| [az batch account create](/cli/azure/batch/account#az_batch_account_create) | Létrehoz egy Batch-fiókot. |
| [az batch account login](/cli/azure/batch/account#az_batch_account_login) | Hitelesíti a megadott Batch-fiókot további parancssori felületi interakcióhoz.  |
| [az batch application create](/cli/azure/batch/application#az_batch_application_create) | Létrehoz egy alkalmazást.  |
| [az batch application package create](/cli/azure/batch/application/package#az_batch_application_package_create) | Hozzáad egy alkalmazáscsomagot a megadott alkalmazáshoz.  |
| [az batch application set](/cli/azure/batch/application#az_batch_application_set) | Frissíti egy alkalmazás tulajdonságait.  |
| [az group delete](/cli/azure/group#az_group_delete) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure/overview).
