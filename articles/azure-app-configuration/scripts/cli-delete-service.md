---
title: Azure CLI-parancsfájlminta – Az Azure App konfigurációs tárolójának törlése
titleSuffix: Azure App Configuration
description: Az Azure CLI Script használatával törölheti az Azure App Configuration Store-t
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/19/2020
ms.author: lcozzens
ms.openlocfilehash: 7f73de459d8ce9f74e3925789af630b7c804d605
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77523572"
---
# <a name="delete-an-azure-app-configuration-store"></a>Azure-alkalmazáskonfigurációs tároló törlése

Ez a mintaparancsfájl törli az Azure App-konfiguráció egy példányát.

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

Ez a parancsfájl a következő parancsokat használja egy alkalmazáskonfigurációs tároló törléséhez. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az appconfig törlés](/cli/azure/appconfig#az-appconfig-delete) | Egy alkalmazáskonfigurációs tároló erőforrás törlése. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További alkalmazáskonfigurációs CLI-parancsfájlminták találhatók az [Azure App Konfigurációs CLI-mintáiban.](../cli-samples.md)
