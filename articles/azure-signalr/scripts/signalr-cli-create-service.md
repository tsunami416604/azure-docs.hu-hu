---
title: Azure CLI parancsfájl-minta – szignáló szolgáltatás létrehozása
description: Az új Azure Signaler-szolgáltatás új erőforráscsoportbeli, véletlenszerű névvel történő létrehozásához kövesse a minta parancsfájlt.
author: sffamily
ms.service: signalr
ms.devlang: azurecli
ms.topic: sample
ms.date: 11/13/2018
ms.author: zhshang
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: ab9183171230e1811f2d974917fc23ee53480996
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87494978"
---
# <a name="create-a-signalr-service"></a>SignalR szolgáltatás létrehozása 

Ez a példaszkript létrehoz egy véletlenszerűen elnevezett új Azure SignalR szolgáltatási erőforrást egy új erőforráscsoportban.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure CLI 2.0-s vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését]( /cli/azure/install-azure-cli) ismertető cikket. 

## <a name="sample-script"></a>Példaszkript

Ez a szkript létrehoz egy új SignalR szolgáltatási erőforrást és egy új erőforráscsoportot. 

```azurecli-interactive
#!/bin/bash

# Generate a unique suffix for the service name
let randomNum=$RANDOM*$RANDOM

# Generate a unique service and group name with the suffix
SignalRName=SignalRTestSvc$randomNum
#resource name must be lowercase
mySignalRSvcName=${SignalRName,,}
myResourceGroupName=$SignalRName"Group"

# Create resource group 
az group create --name $myResourceGroupName --location eastus

# Create the Azure SignalR Service resource
az signalr create \
  --name $mySignalRSvcName \
  --resource-group $myResourceGroupName \
  --sku Standard_S1 \
  --unit-count 1 \
  --service-mode Default

# Get the SignalR primary connection string 
primaryConnectionString=$(az signalr key list --name $mySignalRSvcName \
  --resource-group $myResourceGroupName --query primaryConnectionString -o tsv)

echo "$primaryConnectionString"
```

Jegyezze fel az új erőforráscsoporthoz létrehozott tényleges nevet. Ezt az erőforráscsoport-nevet fogja használni, amikor törölni szeretné a csoport összes erőforrását.

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Szkript ismertetése

A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik. Ez a szkript a következő parancsokat használja:

| Parancs | Jegyzetek |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az signalr create](/cli/azure/signalr#az-signalr-create) | Létrehoz egy Azure SignalR szolgáltatási erőforrást. |
| [az signalr key list](/cli/azure/signalr/key#az-signalr-key-list) | Felsorolja az alkalmazás által a tartalomfrissítések SignalRrel történő valós idejű leküldése során használt kulcsokat. |


## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További Azure SignalR szolgáltatási CLI-példaszkripteket az [Azure SignalR szolgáltatás dokumentációjában](../signalr-reference-cli.md) találhat.
