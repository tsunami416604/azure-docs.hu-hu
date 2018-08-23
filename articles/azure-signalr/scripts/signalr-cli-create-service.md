---
title: Azure CLI-példaszkript – SignalR szolgáltatás létrehozása | Microsoft Docs
description: Azure CLI-példaszkript – SignalR szolgáltatás létrehozása
services: signalr
documentationcenter: signalr
author: wesmc7777
manager: cfowler
editor: ''
tags: azure-service-management
ms.service: signalr
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: signalr
ms.date: 04/20/2018
ms.author: wesmc
ms.custom: mvc
ms.openlocfilehash: 9000531384e7aa14cb412863f99d2271cee9fb39
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2018
ms.locfileid: "41920541"
---
# <a name="create-a-signalr-service"></a>SignalR szolgáltatás létrehozása 

Ez a példaszkript létrehoz egy véletlenszerűen elnevezett új Azure SignalR szolgáltatási erőforrást egy új erőforráscsoportban.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure CLI 2.0-s vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Példaszkript

Ez a szkript az Azure CLI *signalr* bővítményét használja. A példaszkript használata előtt a következő parancsot végrehajtva telepítse a *signalr* bővítményt az Azure CLI-hez:

```azurecli-interactive
az extension add -n signalr
```

Ez a szkript létrehoz egy új SignalR szolgáltatási erőforrást és egy új erőforráscsoportot. 

[!code-azurecli-interactive[main](../../../cli_scripts/azure-signalr/create-signalr-service-and-group/create-signalr-service-and-group.sh "Creates a new Azure SignalR Service resource and resource group")]

Jegyezze fel az új erőforráscsoporthoz létrehozott tényleges nevet. Ezt az erőforráscsoport-nevet fogja használni, amikor törölni szeretné a csoport összes erőforrását.

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Szkript ismertetése

A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik. Ez a szkript a következő parancsokat használja:

| Parancs | Megjegyzések |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az signalr create](/cli/azure/group#az-group-create) | Létrehoz egy Azure SignalR szolgáltatási erőforrást. |
| [az signalr key list](/cli/azure/ext/signalr/signalr/key#ext-signalr-az-signalr-key-list) | Felsorolja az alkalmazás által a tartalomfrissítések SignalRrel történő valós idejű leküldése során használt kulcsokat. |


## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További Azure SignalR szolgáltatási CLI-példaszkripteket az [Azure SignalR szolgáltatás dokumentációjában](../signalr-cli-samples.md) találhat.
