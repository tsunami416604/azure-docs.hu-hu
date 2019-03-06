---
title: Az Azure CLI-Példaszkript – együttműködnek a kulcs-értékeket egy Azure-alkalmazás konfigurációja Store az |} A Microsoft Docs
description: Arról nyújt tájékoztatást, kulcs-értékeket egy Azure App a konfigurációs adattárolónál a használata
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
ms.openlocfilehash: 9288ea08da6335dd29e7a15a9bc871b76c1ce7e9
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57438431"
---
# <a name="work-with-key-values-in-an-azure-app-configuration-store"></a>Együttműködve a kulcs-értékeket egy alkalmazások konfigurálása az Azure áruházban

Ez a példaszkript létrehoz egy új kulcs-érték-alkalmazások konfigurálása az Azure áruházban, minden meglévő kulcs értékeit sorolja fel, frissíti az újonnan létrehozott kulcs értékét és végül törli őket.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure CLI 2.0-s vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket.

Az Azure App konfigurációs CLI-bővítmény a következő parancs végrehajtásával először telepítenie kell:

        az extension add -n appconfig

## <a name="sample-script"></a>Példaszkript

```azurecli-interactive
#!/bin/bash

appConfigName=myTestAppConfigStore
newKey="TestKey"

# Create a new key-value 
az appconfig kv set --name $appConfigName --key $newKey --value "Value 1"

# List current key-values
az appconfig kv list --name $appConfigName

# Update new key's value
az appconfig kv set --name $appConfigName --value "Value 2"

# List current key-values
az appconfig kv list --name $appConfigName

# Delete new key
az appconfig kv delete  --name $appConfigName --key $newKey

# List current key-values
az appconfig kv list --name $appConfigName
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja egy alkalmazás a konfigurációs adattárolónál a kulcs-értékeket a művelethez használandó. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az appconfig-kv beállítása](/cli/azure/ext/appconfig/appconfig) | Létrehozza vagy frissíti a kulcs-érték. |
| [az appconfig kv listája](/cli/azure/ext/appconfig/appconfig) | Egy alkalmazás a konfigurációs adattárolónál a kulcs-értékeit sorolja fel. |
| [az appconfig kv delete](/cli/azure/ext/appconfig/appconfig) | Töröl egy kulcs-érték. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További App konfigurációs CLI-példaszkripteket megtalálható a [alkalmazások konfigurálása az Azure dokumentációs](../cli-samples.md).
