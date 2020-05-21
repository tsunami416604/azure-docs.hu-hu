---
title: Azure CLI-példaszkript – Alkalmazás hozzáadása a Batch szolgáltatásban
description: Ez a minta azt mutatja be, hogyan adhat hozzá egy alkalmazást Azure Batch készlettel vagy feladattal való használatra.
ms.topic: sample
ms.date: 01/29/2018
ms.openlocfilehash: 792af55be17551d1635b6821456c6e8e53e9daea
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83681935"
---
# <a name="cli-example-add-an-application-to-an-azure-batch-account"></a>Parancssori felületi példa: Alkalmazás hozzáadása egy Azure Batch-fiókhoz

Ez a szkript bemutatja, hogyan lehet hozzáadni egy alkalmazást az Azure Batch-készlettel vagy feladattal való használatra. Amikor előkészít egy alkalmazást a Batch-fiókjához való hozzáadáshoz, csomagolja be a végrehajtható fájlt – valamennyi függőségével együtt – egy zip-fájlba. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure CLI 2.0.20-as vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli). 

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
| [az group create](/cli/azure/group#az-group-create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Létrehoz egy tárfiókot. |
| [az batch account create](/cli/azure/batch/account#az-batch-account-create) | Létrehoz egy Batch-fiókot. |
| [az batch account login](/cli/azure/batch/account#az-batch-account-login) | Hitelesíti a megadott Batch-fiókot további parancssori felületi interakcióhoz.  |
| [az batch application create](/cli/azure/batch/application#az-batch-application-create) | Létrehoz egy alkalmazást.  |
| [az batch application package create](/cli/azure/batch/application/package#az-batch-application-package-create) | Hozzáad egy alkalmazáscsomagot a megadott alkalmazáshoz.  |
| [az batch application set](/cli/azure/batch/application#az-batch-application-set) | Frissíti egy alkalmazás tulajdonságait.  |
| [az group delete](/cli/azure/group#az-group-delete) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>Következő lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure).
