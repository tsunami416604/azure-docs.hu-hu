---
title: "Az Azure CLI parancsfájl minta - alkalmazás hozzáadása a kötegben |} Microsoft Docs"
description: "Az Azure CLI parancsfájl minta - kötegben alkalmazás hozzáadása"
services: batch
documentationcenter: 
author: annatisch
manager: daryls
editor: tysonn
ms.assetid: 
ms.service: batch
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/02/2017
ms.author: antisch
ms.openlocfilehash: cbfe8ab565ecf7f298a9a6c0f0c8298c675f178c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="adding-applications-to-azure-batch-with-azure-cli"></a>Az Azure CLI Azure Batch-alkalmazások hozzáadása

Ez a parancsfájl bemutatja, hogyan állíthatja be az Azure Batch-készlet vagy a feladat használható alkalmazás. Alkalmazás beállítása a csomag a végrehajtható fájlt, és függőségeit, .zip fájlba. Ebben a példában a végrehajtható fájl zip-fájl neve "saját-alkalmazás-exe.zip".

## <a name="prerequisites"></a>Előfeltételek

- Telepítse az Azure parancssori felület használatával a utasításokat a [Azure parancssori felület telepítési útmutató](https://docs.microsoft.com/cli/azure/install-azure-cli), ha még nem tette meg.
- Batch-fiók létrehozása, ha még nem rendelkezik. Lásd: [Batch-fiók létrehozása az Azure parancssori felülettel](https://docs.microsoft.com/azure/batch/scripts/batch-cli-sample-create-account) számára egy fiókot hoz létre parancsfájlt.

## <a name="sample-script"></a>Mintaparancsfájl

[!code-azurecli[main](../../../cli_scripts/batch/add-application/add-application.sh "Add Application")]

## <a name="clean-up-application"></a>Alkalmazás törlése

Miután lefuttatta a fenti minta parancsfájlt, a következő parancsokat az alkalmazások és a feltöltött alkalmazáscsomagok eltávolítása.

```azurecli
az batch application package delete -g myresourcegroup -n mybatchaccount --application-id myapp --version 1.0 --yes
az batch application delete -g myresourcegroup -n mybatchaccount --application-id myapp --yes
```

## <a name="script-explanation"></a>Parancsfájl ismertetése

A parancsfájl a következő parancsok töltsön fel egy alkalmazáscsomagot, és hozzon létre egy alkalmazást.
Minden egyes parancsa a tábla-parancs-specifikus dokumentációjára mutató hivatkozásokat.

| Parancs | Megjegyzések |
|---|---|
| [az kötegelt alkalmazás létrehozása](https://docs.microsoft.com/cli/azure/batch/application#az_batch_application_create) | Alkalmazást hoz létre.  |
| [az kötegelt alkalmazás beállítása](https://docs.microsoft.com/cli/azure/batch/application#az_batch_application_set) | Alkalmazás tulajdonságainak frissítése.  |
| [az kötegelt alkalmazáscsomag létrehozása](https://docs.microsoft.com/cli/azure/batch/application/package#az_batch_application_package_create) | A megadott alkalmazás ad hozzá egy alkalmazáscsomagot.  |

## <a name="next-steps"></a>Következő lépések

További információ az Azure parancssori felület: [Azure CLI dokumentáció](https://docs.microsoft.com/cli/azure/overview).

További kötegelt CLI parancsfájl minták megtalálhatók a [Azure Batch CLI dokumentáció](../batch-cli-samples.md).
