---
title: Az Azure CLI-Példaszkript – egy Azure-alkalmazás konfigurációja Store importálás |} A Microsoft Docs
description: Importál egy Azure App a konfigurációs adattároló biztosít információkat és a minta parancsprogramok
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
ms.openlocfilehash: 65fa09b779f656654411a1d0c3602598655f50c9
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56884775"
---
# <a name="import-to-an-azure-app-configuration-store"></a>Az Azure App a konfigurációs adattároló importálása

Ez a példaszkript importál egy Azure App a konfigurációs adattároló kulcs-értékeket.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure CLI 2.0-s vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket.

Az Azure App konfigurációs CLI-bővítmény a következő parancs végrehajtásával először telepítenie kell:

        az extension add -n appconfig

## <a name="sample-script"></a>Példaszkript

```azurecli-interactive
#!/bin/bash

# Import key-values from a file
az appconfig kv import --name myTestAppConfigStore --file ~/Import.json
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja egy alkalmazás a konfigurációs adattároló importálása. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az appconfig import](/cli/azure/ext/appconfig/appconfig#ext-appconfig-az-appconfig-import) | Importálja az alkalmazás-konfigurációjához erőforrás tárolásához. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További App konfigurációs CLI-példaszkripteket megtalálható a [alkalmazások konfigurálása az Azure dokumentációs](../cli-samples.md).
