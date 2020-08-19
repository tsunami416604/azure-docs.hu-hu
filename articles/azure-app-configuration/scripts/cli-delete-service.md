---
title: Azure CLI-parancsfájl minta – Azure-alkalmazás konfigurációs tárolójának törlése
titleSuffix: Azure App Configuration
description: Azure-alkalmazás konfigurációs tárolójának törlése egy minta Azure CLI-parancsfájl használatával. Lásd a parancsfájlban használt parancsokra mutató hivatkozásokat.
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/19/2020
ms.author: lcozzens
ms.custom: devx-track-azurecli
ms.openlocfilehash: e107ba9628eca384f518f7a241be5c00c278ef0a
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88588302"
---
# <a name="delete-an-azure-app-configuration-store"></a>Azure-alkalmazás konfigurációs tárolójának törlése

Ez a minta parancsfájl törli az Azure-alkalmazások konfigurációjának egy példányát.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure CLI 2.0-s vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket.

## <a name="sample-script"></a>Példaszkript

```azurecli-interactive
#/bin/bash

# Delete an App Configuration store named myTestAppConfigStore from the Resource Group myResourceGroup
az appconfig delete --name myTestAppConfigStore --resource-group myResourceGroup
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja az alkalmazás konfigurációs tárolójának törléséhez. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [az appconfig delete](/cli/azure/appconfig#az-appconfig-delete) | Egy alkalmazás-konfigurációs tároló erőforrásának törlése. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

Az alkalmazások konfigurációjának további parancssori felületi mintái az [Azure app CONFIGURATION CLI-mintákban](../cli-samples.md)találhatók.
