---
title: Azure CLI-parancsfájlminta – Exportálás az Azure App konfigurációs tárolójából
titleSuffix: Azure App Configuration
description: Konfiguráció exportálása az Azure App Configuration programból az Azure CLI-parancsfájljával
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/19/2020
ms.author: lcozzens
ms.openlocfilehash: 3e014504fc1e94c3b323a49b408fa992de00d14a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77523620"
---
# <a name="export-from-an-azure-app-configuration-store"></a>Exportálás az Azure App konfigurációs tárolójából

Ez a mintaparancsfájl kulcsértékeket exportál egy Azure App Configuration Store-ból.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure CLI 2.0-s vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket.

## <a name="sample-script"></a>Példaszkript

```azurecli-interactive
#!/bin/bash

# Export all key-values
az appconfig kv export --name myTestAppConfigStore --file ~/Export.json
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Szkript ismertetése

Ez a parancsfájl a következő parancsokat használja az alkalmazáskonfigurációs tárolóból történő exportáláshoz. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az appconfig kv exportálás](/cli/azure/appconfig/kv#az-appconfig-kv-export) | Exportálás egy alkalmazáskonfigurációs tároló ból származó erőforrásból. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További alkalmazáskonfigurációs CLI-parancsfájlminták találhatók az [Azure App Konfigurációs CLI-mintáiban.](../cli-samples.md)
