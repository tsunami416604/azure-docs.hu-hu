---
title: Azure CLI parancsfájl-minta – importálás Azure app Configuration Store-ba | Microsoft Docs
description: Információt és példákat tartalmazó parancsfájlokat tartalmaz az Azure app Configuration Store-ba való importáláshoz
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: azure-app-configuration
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: cd1e54fc6cfbf254da010c03dfaa859a0ee8213c
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029814"
---
# <a name="import-to-an-azure-app-configuration-store"></a>Importálás Azure-alkalmazás konfigurációs tárolójába

Ez a minta parancsfájl importálja a kulcs-értékeket egy Azure-alkalmazás konfigurációs tárolójába.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure CLI 2.0-s vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket.

Először telepítenie kell az Azure app Configuration CLI bővítményt a következő parancs végrehajtásával:

        az extension add -n appconfig

## <a name="sample-script"></a>Példaszkript

```azurecli-interactive
#!/bin/bash

# Import key-values from a file
az appconfig kv import --name myTestAppConfigStore --source file --path ~/Import.json
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja egy alkalmazás-konfigurációs tároló importálásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az appconfig import](/cli/azure/ext/appconfig/appconfig/kv#ext-appconfig-az-appconfig-kv-import) | Importálás az alkalmazás konfigurációs tárolójának erőforrásaiba. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

Az alkalmazások konfigurációjának további parancssori felületi mintái az [Azure-alkalmazás konfigurációs dokumentációjában](../cli-samples.md)találhatók.
