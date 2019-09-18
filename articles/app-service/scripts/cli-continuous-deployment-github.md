---
title: Azure CLI-szkript minta – alkalmazás létrehozása a GitHubról történő folyamatos üzembe helyezéssel | Microsoft Docs
description: Azure CLI-szkript mintája – alkalmazás létrehozása és folyamatos üzembe helyezése a GitHubról
services: app-service\web
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: 0205c991-0989-4ca3-bb41-237dcc964460
ms.service: app-service-web
ms.workload: web
ms.devlang: azurecli
ms.tgt_pltfrm: na
ms.topic: sample
ms.date: 09/02/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 02853186a1f28d8c6db6b5421c6ebba0640461cb
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/03/2019
ms.locfileid: "71057890"
---
# <a name="create-an-app-service-app-with-continuous-deployment-from-github-using-cli"></a>App Service-alkalmazás létrehozása a GitHubról történő folyamatos üzembe helyezéssel a parancssori felület használatával

Ez a példa egy alkalmazást hoz létre App Service a kapcsolódó erőforrásokkal együtt, majd beállítja a folyamatos üzembe helyezést egy GitHub-adattárból. Ahhoz, hogy a GitHub üzembe helyezése folyamatos üzembe helyezés nélkül történjen, tekintse meg [az alkalmazás létrehozása és kód üzembe helyezése a githubról](cli-deploy-github.md) A példához a következők szükségesek:

* Egy GitHub-adattár az alkalmazáskóddal, amelyhez rendszergazdai jogosultság szükséges. Az automatikus buildek beszerzéséhez strukturálja a tárházat az [adattár előkészítése](../deploy-continuous-deployment.md#prepare-your-repository) táblázat alapján.
* Egy [személyes hozzáférési jogkivonat (PAT)](https://help.github.com/articles/creating-an-access-token-for-command-line-use) a GitHub-fiókjához.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, az Azure CLI 2.0-s vagy újabb verziójára lesz szükség. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését]( /cli/azure/install-azure-cli) ismertető cikket.

## <a name="sample-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/deploy-github-continuous/deploy-github-continuous.sh?highlight=3-4 "Create an app with continuous deployment from GitHub")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) | Létrehoz egy App Service-csomagot. |
| [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) | Létrehoz egy App Service alkalmazást. |
| [`az webapp deployment source config`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config) | Egy App Service alkalmazást társít egy git-vagy Mercurial-tárházhoz. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure).

További App Service CLI-példaszkripteket az [Azure App Service dokumentációjában](../samples-cli.md) találhat.
