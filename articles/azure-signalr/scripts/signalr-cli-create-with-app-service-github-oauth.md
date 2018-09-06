---
title: Azure CLI-példaszkript – SignalR szolgáltatást és GitHub-hitelesítést használó webalkalmazás létrehozása | Microsoft Docs
description: Azure CLI-példaszkript – SignalR szolgáltatást és GitHub-hitelesítést használó webalkalmazás létrehozása
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
ms.date: 04/22/2018
ms.author: zhshang
ms.custom: mvc
ms.openlocfilehash: 94203153b7f9a2fecb664a12ff90548787a4f158
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2018
ms.locfileid: "43664461"
---
# <a name="create-a-web-app-that-uses-signalr-service-and-github-authentication"></a>SignalR szolgáltatást és GitHub-hitelesítést használó webalkalmazás létrehozása

Ez a példaszkript létrehoz egy új Azure SignalR szolgáltatási erőforrást, amellyel valós idejű tartalomfrissítések küldhetők le az ügyfeleknek. A szkript emellett hozzáad egy új webalkalmazást és App Service-csomagot is a SignalR szolgáltatási erőforrást használó ASP.NET Core-webalkalmazás futtatására. A webalkalmazás úgy van konfigurálva, hogy csatlakozzon az új SignalR szolgáltatási erőforráshoz, és [GitHub-hitelesítést](https://developer.github.com/v3/guides/basics-of-authentication/) használjon a hitelesítéshez. A webalkalmazás úgy van konfigurálva, hogy egy helyi Git-adattárat használjon üzembehelyezési forrásként.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure CLI 2.0-s vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Példaszkript

Ez a szkript az Azure CLI *signalr* bővítményét használja. A példaszkript használata előtt a következő parancsot végrehajtva telepítse a *signalr* bővítményt az Azure CLI-hez:

```azurecli-interactive
az extension add -n signalr
```

[!code-azurecli-interactive[main](../../../cli_scripts/azure-signalr/create-signalr-with-app-service-github-oauth/create-signalr-with-app-service-github-oauth.sh "Create a new SignalR Service and Web App configured to use SignalR, GitHub OAuth, and local Git repository deployment source.")]

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
| [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) | Új alkalmazásbeállításokat ad a webalkalmazáshoz. Ezeket az alkalmazásbeállításokat használja a rendszer a SignalR kapcsolati sztringje és a GitHub OAuth titkos alkalmazáskulcsok tárolásához. |
| [az webapp deployment user set](/cli/azure/webapp/deployment/user#az-webapp-deployment-user-set) | Frissíti az üzembehelyezési hitelesítő adatokat. |
| [az webapp deployment source config-local-git](/cli/azure/webapp/deployment/source#az-webapp-deployment-source-config-local-git) | Beszerez egy URL-címet egy Git-adattárvégponthoz, amely klónozáshoz és leküldéshez használható webalkalmazás üzembe helyezésekor. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További Azure SignalR szolgáltatási CLI-példaszkripteket az [Azure SignalR szolgáltatás dokumentációjában](../signalr-cli-samples.md) találhat.
