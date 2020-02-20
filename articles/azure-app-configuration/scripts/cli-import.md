---
title: Azure CLI-parancsfájl minta – importálás alkalmazás-konfigurációs tárolóba
titleSuffix: Azure App Configuration
description: Információt és példákat tartalmazó parancsfájlokat tartalmaz az Azure app Configuration Store-ba való importáláshoz
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: balans
editor: ''
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: azure-app-configuration
ms.date: 02/24/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: b0704c6262451cef615355e871ce7f26005b3132
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/19/2020
ms.locfileid: "77467669"
---
# <a name="import-to-an-azure-app-configuration-store"></a>Importálás Azure-alkalmazás konfigurációs tárolójába

Ez a minta parancsfájl a kulcs-érték beállításokat egy Azure-alkalmazás konfigurációs tárolójába importálja.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha az Azure CLI helyi telepítését és használatát választja, akkor ehhez a cikkhez az Azure CLI 2,0-es vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. A telepítéséhez vagy frissítéséhez tekintse meg [Az Azure CLI telepítését](/cli/azure/install-azure-cli)ismertető témakört.

## <a name="sample-script"></a>Példaszkript

```azurecli-interactive
#!/bin/bash

# Import key-values from a file
az appconfig kv import --name myTestAppConfigStore --source file --path ~/Import.json
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja egy alkalmazás-konfigurációs tárolóba való importáláshoz. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az appconfig kV import](/cli/azure/appconfig/kv#az-appconfig-kv-import) | Importálás az alkalmazás konfigurációs tárolójának erőforrásaiba. |

## <a name="next-steps"></a>Következő lépések

Az Azure CLI-vel kapcsolatos további információkért tekintse meg az [Azure CLI dokumentációját](/cli/azure).

Az alkalmazások konfigurációjának további parancssori felületi mintái az [Azure app CONFIGURATION CLI-mintákban](../cli-samples.md)találhatók.
