---
title: Azure CLI-parancsfájlminta – Kulcsértékek használata az App Configuration Store-ban
titleSuffix: Azure App Configuration
description: Kulcsértékek létrehozása, megtekintése, frissítése és törlése az Azure CLI parancsfájljával az App Configuration Store-ból
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/19/2020
ms.author: lcozzens
ms.openlocfilehash: 1a4edabe666a554ccd01d110f0f71226221dfc67
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77523648"
---
# <a name="work-with-key-values-in-an-azure-app-configuration-store"></a>Kulcsértékek használata az Azure App configuration store-ban

Ez a mintaszkript bemutatja, hogyan:
* Új kulcs-érték pár létrehozása
* Az összes meglévő kulcs-érték pár listázása
* Újonnan létrehozott kulcs értékének frissítése
* Az új kulcs-érték pár törlése

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ez a cikk az Azure CLI 2.0-s vagy újabb verzióját igényli. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket. 

## <a name="sample-script"></a>Példaszkript

```azurecli-interactive
#!/bin/bash

appConfigName=myTestAppConfigStore
newKey="TestKey"
refKey="KeyVaultReferenceTestKey"
uri="[URL to value stored in Key Vault]"
uri2="[URL to another value stored in Key Vault]"

# Create a new key-value 
az appconfig kv set --name $appConfigName --key $newKey --value "Value 1"

# List current key-values
az appconfig kv list --name $appConfigName

# Update new key's value
az appconfig kv set --name $appConfigName --key $newKey --value "Value 2"

# List current key-values
az appconfig kv list --name $appConfigName

# Create a new key-value referencing a value stored in Azure Key Vault
az appconfig kv set --name $appConfigName --key $refKey --content-type "application/vnd.microsoft.appconfig.keyvaultref+json;charset=utf-8" --value "{\"uri\":\"$uri\"}"

# List current key-values
az appconfig kv list --name $appConfigName

# Update Key Vault reference
az appconfig kv set --name $appConfigName --key $refKey --value "{\"uri\":\"$uri2\"}"

# List current key-values
az appconfig kv list --name $appConfigName

# Delete new key
az appconfig kv delete  --name $appConfigName --key $newKey

# Delete Key Vault reference
az appconfig kv delete --name $appConfigName --key $refKey

# List current key-values
az appconfig kv list --name $appConfigName
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Szkript ismertetése

Ez a táblázat a mintaparancsfájlban használt parancsokat sorolja fel. 

| Parancs | Megjegyzések |
|---|---|
| [az appconfig kv készlet](/cli/azure/appconfig/kv#az-appconfig-kv-set) | Kulcs-érték pár létrehozása vagy frissítése. |
| [az appconfig kv lista](/cli/azure/appconfig/kv#az-appconfig-kv-list) | Listakulcs-érték párok egy App Configuration Store.List key-value pairs in a App Configuration Store. |
| [az appconfig kv törlés](/cli/azure/appconfig/kv#az-appconfig-kv-delete) | Kulcs-érték pár törlése. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További alkalmazáskonfigurációs CLI-parancsfájlminták találhatók az [Azure App Konfigurációs CLI-mintáiban.](../cli-samples.md)
