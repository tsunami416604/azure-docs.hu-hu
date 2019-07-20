---
title: Azure CLI-szkript mintája – alkalmazás létrehozása és folyamatos üzembe helyezése az Azure Reposből | Microsoft Docs
description: Azure CLI parancsfájl-minta – alkalmazás létrehozása folyamatos üzembe helyezéssel az Azure Reposből
services: app-service\web
documentationcenter: ''
author: msangapu-msft
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: 389d3bd3-cd8e-4715-a3a1-031ec061d385
ms.service: app-service-web
ms.workload: web
ms.devlang: azurecli
ms.tgt_pltfrm: na
ms.topic: sample
ms.date: 12/11/2017
ms.author: msangapu-msft
ms.custom: seodec18
ms.openlocfilehash: 502b2fabfe1bb90862e673d6bd88a5397065d5d7
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359942"
---
# <a name="create-an-app-service-app-with-continuous-deployment-using-azure-cli"></a>App Service-alkalmazás létrehozása folyamatos üzembe helyezéssel az Azure CLI használatával

Ez a példa egy alkalmazást hoz létre App Service a kapcsolódó erőforrásokkal együtt, majd beállítja a folyamatos üzembe helyezést egy Azure DevOps adattárból. A példához a következők szükségesek:

* Egy Azure DevOps-tárház, amelynek az alkalmazás kódját rendszergazdai jogosultságokkal rendelkezik.
* Egy [személyes hozzáférési jogkivonat (PAT)](https://docs.microsoft.com/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate?view=vsts) az Azure DevOps-szervezethez.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, az Azure CLI 2.0-s vagy újabb verziójára lesz szükség. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését]( /cli/azure/install-azure-cli) ismertető cikket.

## <a name="sample-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/deploy-vsts-continuous/deploy-vsts-continuous.sh?highlight=3-4 "Create an app with continuous deployment from Azure DevOps")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) | App Service-csomag létrehozása. |
| [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) | Létrehoz egy App Service alkalmazást. |
| [`az webapp deployment source config`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config) | Egy App Service alkalmazást társít egy git-vagy Mercurial-tárházhoz. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure).

További App Service CLI-példaszkripteket az [Azure App Service dokumentációjában](../samples-cli.md) találhat.
