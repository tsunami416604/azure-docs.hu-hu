---
title: Az Azure CLI-Példaszkript – egy Azure-alkalmazás konfigurációja Store exportálás |} A Microsoft Docs
description: Információk és a minta parancsprogramok biztosít az alkalmazások konfigurálása az Azure áruházból exportálása
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
ms.openlocfilehash: 480e21994846f5b2adb85bcf846df2a7cdda4bda
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57444464"
---
# <a name="export-from-an-azure-app-configuration-store"></a>Alkalmazások konfigurálása az Azure áruházban exportálása

Ez a példaszkript egy Azure-alkalmazások konfigurálása áruházból kulcs-értékeket exportálja.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure CLI 2.0-s vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket.

Az Azure App konfigurációs CLI-bővítmény a következő parancs végrehajtásával először telepítenie kell:

        az extension add -n appconfig

## <a name="sample-script"></a>Példaszkript

```azurecli-interactive
#!/bin/bash

# Export all key-values
az appconfig kv export --name myTestAppConfigStore --file ~/Export.json
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja egy alkalmazás a konfigurációs adattároló exportálása. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az appconfig export](/cli/azure/ext/appconfig/appconfig) | Export-alkalmazások konfigurálása az erőforrás tárolásához. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További App konfigurációs CLI-példaszkripteket megtalálható a [alkalmazások konfigurálása az Azure dokumentációs](../cli-samples.md).
