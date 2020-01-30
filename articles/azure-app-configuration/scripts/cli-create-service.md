---
title: Azure CLI-parancsfájl minta – Azure-alkalmazás konfigurációs tárolójának létrehozása
titleSuffix: Azure App Configuration
description: Azure CLI-parancsfájl minta – Azure-alkalmazás konfigurációs tárolójának létrehozása
services: azure-app-configuration
author: jpconnock
ms.service: azure-app-configuration
ms.topic: sample
ms.date: 01/24/2020
ms.author: jeconnoc
ms.openlocfilehash: 44c381da8648fea74059c9110438cfeb4c366116
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2020
ms.locfileid: "76766493"
---
# <a name="create-an-azure-app-configuration-store"></a>Azure-alkalmazás konfigurációs tárolójának létrehozása

Ez a példa az Azure-alkalmazások konfigurációjának új példányát hozza létre egy új erőforráscsoport véletlenszerű nevével.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure CLI 2.0-s vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket.

Először telepítenie kell az Azure app Configuration CLI bővítményt a következő parancs végrehajtásával:

        az extension add -n appconfig

## <a name="sample-script"></a>Példaszkript

```azurecli-interactive
#!/bin/bash

appConfigName=myTestAppConfigStore
#resource name must be lowercase
myAppConfigStoreName=${appConfigName,,}
myResourceGroupName=$appConfigName"Group"

# Create resource group 
az group create --name $myResourceGroupName --location eastus

# Create the Azure AppConfig Service resource and query the hostName
appConfigHostname=$(az appconfig create \
  --name $myAppConfigStoreName \
  --location eastus \
  --resource-group $myResourceGroupName \
  --query hostName \
  -o tsv)

# Get the AppConfig connection string 
appConfigConnectionString=$(az appconfig credential list \
--resource-group $myResourceGroupName \
--name $myAppConfigStoreName \
--query "[?name=='Secondary Read Only'] .connectionString" -o tsv)

# Echo the connection string for use in your application
echo "$appConfigConnectionString"
```

Jegyezze fel az új erőforráscsoporthoz létrehozott tényleges nevet. Ezt az erőforráscsoport-nevet fogja használni, amikor törölni szeretné a csoport összes erőforrását.

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja egy új erőforráscsoport és egy alkalmazás-konfigurációs tároló létrehozásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az appconfig Create](/cli/azure/ext/appconfig/appconfig#ext-appconfig-az-appconfig-create) | Létrehoz egy alkalmazás-konfigurációs tároló erőforrást. |
| [az appconfig hitelesítőadat-lista](/cli/azure/ext/appconfig/appconfig/credential?view=azure-cli-latest) | Az alkalmazás konfigurációs tárolójának hozzáférési kulcsainak listázása. |

## <a name="next-steps"></a>Következő lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

Az alkalmazások konfigurációjának további parancssori felületi mintái az [Azure app CONFIGURATION CLI-mintákban](../cli-samples.md)találhatók.
