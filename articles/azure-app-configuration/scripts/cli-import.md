---
title: Azure CLI-parancsfájlminta – Importálás alkalmazáskonfigurációs tárolóba
titleSuffix: Azure App Configuration
description: Azure CLI-parancsfájl használata – Konfiguráció importálása az Azure App konfigurációjának
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/19/2020
ms.author: lcozzens
ms.openlocfilehash: 71d6aafa82f647b9c6164ee9a06b43ed7e9a66af
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77523614"
---
# <a name="import-to-an-azure-app-configuration-store"></a>Importálás az Azure App konfigurációs tárolóba

Ez a mintaparancsfájl kulcsérték-beállításokat importál egy Azure App Configuration Store-ba.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha úgy dönt, hogy telepíti és használja az Azure CLI helyileg, ez a cikk megköveteli, hogy az Azure CLI 2.0-s vagy újabb verzióját. A verzió azonosításához futtassa a következőt: `az --version`. A telepítéshez vagy frissítéshez [olvassa el az Azure CLI telepítése](/cli/azure/install-azure-cli)című témakört.

## <a name="sample-script"></a>Példaszkript

```azurecli-interactive
#!/bin/bash

# Import key-values from a file
az appconfig kv import --name myTestAppConfigStore --source file --path ~/Import.json
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Szkript ismertetése

Ez a parancsfájl a következő parancsokat használja az alkalmazáskonfigurációs tárolóba történő importáláshoz. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az appconfig kv importálás](/cli/azure/appconfig/kv#az-appconfig-kv-import) | Importálás egy alkalmazáskonfigurációs tároló erőforrásába. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-ről az [Azure CLI dokumentációjában](/cli/azure)olvashat bővebben.

További alkalmazáskonfigurációs CLI-parancsfájlminták találhatók az [Azure App Konfigurációs CLI-mintáiban.](../cli-samples.md)
