---
title: Azure CLI-példaszkript – SignalR szolgáltatási erőforrás létrehozása App Service-szel | Microsoft Docs
description: Azure CLI-példaszkript – SignalR szolgáltatási erőforrás létrehozása App Service-szel
services: signalr
documentationcenter: signalr
author: sffamily
manager: cfowler
editor: ''
tags: azure-service-management
ms.service: signalr
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: signalr
ms.date: 04/20/2018
ms.author: zhshang
ms.custom: mvc
ms.openlocfilehash: 971c618300c1aedcdfb26bfaa79660a92eada475
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46978726"
---
# <a name="create-a-signalr-service-with-an-app-service"></a>SignalR szolgáltatási erőforrás létrehozása App Service-szel

Ez a példaszkript létrehoz egy új Azure SignalR szolgáltatási erőforrást, amellyel valós idejű tartalomfrissítések küldhetők le az ügyfeleknek. A szkript emellett hozzáad egy új webalkalmazást és App Service-csomagot is a SignalR szolgáltatási erőforrást használó ASP.NET Core-webalkalmazás futtatására. A webalkalmazás egy *AzureSignalRConnectionString* nevű alkalmazásbeállítással lesz konfigurálva az új SignalR szolgáltatási erőforráshoz való csatlakozáshoz.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure CLI 2.0-s vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését]( /cli/azure/install-azure-cli) ismertető cikket. 

## <a name="sample-script"></a>Példaszkript

Ez a szkript az Azure CLI *signalr* bővítményét használja. A példaszkript használata előtt a következő parancsot végrehajtva telepítse a *signalr* bővítményt az Azure CLI-hez:

```azurecli-interactive
az extension add -n signalr
```

[!code-azurecli-interactive[main](../../../cli_scripts/azure-signalr/create-signalr-with-app-service/create-signalr-with-app-service.sh "Create a new Azure SignalR Service and Web App")]

Jegyezze fel az új erőforráscsoporthoz létrehozott tényleges nevet. Ez a kimenetben látható. Ezt az erőforráscsoport-nevet fogja használni, amikor törölni szeretné a csoport összes erőforrását.

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Szkript ismertetése

A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik. Ez a szkript a következő parancsokat használja:

| Parancs | Megjegyzések |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az signalr create](/cli/azure/ext/signalr/signalr#ext-signalr-az-signalr-create) | Létrehoz egy Azure SignalR szolgáltatási erőforrást. |
| [az signalr key list](/cli/azure/ext/signalr/signalr/key#ext-signalr-az-signalr-key-list) | Felsorolja az alkalmazás által a tartalomfrissítések SignalRrel történő valós idejű leküldése során használt kulcsokat. |
| [az appservice plan create](/cli/azure/appservice/plan#az-appservice-plan-create) | Létrehoz egy webalkalmazások futtatására szolgáló Azure App Service-csomagot. |
| [az webapp create](/cli/azure/webapp#az-webapp-create) | Létrehoz egy Azure-webalkalmazást az App Service futtatási csomaggal. |
| [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) | Hozzáad egy új alkalmazásbeállítást a webalkalmazáshoz. Ez az alkalmazásbeállítás a SignalR kapcsolati sztring tárolására szolgál. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További Azure SignalR szolgáltatási CLI-példaszkripteket az [Azure SignalR szolgáltatás dokumentációjában](../signalr-cli-samples.md) találhat.
