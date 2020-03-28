---
title: 'CLI: Alkalmazás telepítése a GitHubról'
description: Ismerje meg, hogyan használhatja az Azure CLI-t az App Service-alkalmazás üzembe helyezésének és felügyeletének automatizálására. Ez a minta bemutatja, hogyan telepíthet egy alkalmazást a GitHubról.
author: msangapu-msft
tags: azure-service-management
ms.assetid: 0205c991-0989-4ca3-bb41-237dcc964460
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/11/2017
ms.author: msangapu
ms.custom: mvc, seodec18
ms.openlocfilehash: 368a5d6810de5628ab98a7bef43cfceab750f95f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "80057844"
---
# <a name="create-an-app-service-app-with-deployment-from-github-using-azure-cli"></a>Hozzon létre egy App Service-alkalmazást a GitHubról az Azure CLI használatával történő telepítéssel

Ez a mintaparancsfájl létrehoz egy alkalmazást az App Service-ben a kapcsolódó erőforrásokkal. Ezután telepíti az alkalmazáskódot egy nyilvános GitHub-tárházból (folyamatos üzembe helyezés nélkül). A GitHub folyamatos üzembe helyezését a Folyamatos üzembe helyezéssel című alkalmazás létrehozása folyamatos üzembe helyezéssel a [GitHubról című](cli-continuous-deployment-github.md)témakörben talál.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, az Azure CLI 2.0-s vagy újabb verziójára lesz szükség. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését]( /cli/azure/install-azure-cli) ismertető cikket.

## <a name="sample-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/deploy-github/deploy-github.sh?highlight=3 "Create an app with deployment from GitHub")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Szkript ismertetése 

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) | Létrehoz egy App Service-csomagot. |
| [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) | Létrehoz egy App Service-alkalmazást. |
| [`az webapp deployment source config`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config) | Egy App Service-alkalmazást git- vagy Mercurial-tárházhoz társít. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure).

További App Service CLI-példaszkripteket az [Azure App Service dokumentációjában](../samples-cli.md) találhat.
