---
title: Function-alkalmazás létrehozása GitHub-üzembe helyezéssel – Azure CLI
description: Egy függvényalkalmazás létrehozása és függvénykód üzembe helyezése egy GitHub-adattárból az Azure Functions használatával.
ms.date: 07/03/2018
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 7f7c272fa95d2697d41cb751e39ef72b0da86a12
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2020
ms.locfileid: "87498519"
---
# <a name="create-a-function-app-in-azure-that-is-deployed-from-github"></a>GitHubról üzembe helyezett függvényalkalmazás létrehozása az Azure-ban

Ez a Azure Functions minta szkript létrehoz egy Function alkalmazást a használati [terv](../functions-scale.md#consumption-plan)használatával, valamint a kapcsolódó erőforrásait. Ezután egy GitHub-adattárból konfigurálja a függvénykódot folyamatos üzembe helyezéshez. 

A példához a következők szükségesek:

* Egy GitHub-adattár a függvénykóddal, amelyhez rendszergazdai jogosultság szükséges.
* Egy [személyes hozzáférési jogkivonat (PAT)](https://help.github.com/articles/creating-an-access-token-for-command-line-use) a GitHub-fiókjához.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha inkább a helyi Azure CLI-t használja, akkor az Azure CLI 2.0-s vagy újabb verzióját kell telepítenie és futtatnia. Az Azure CLI verziójának megállapításához futtassa az `az --version` parancsot. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését]( /cli/azure/install-azure-cli) ismertető cikket. 

## <a name="sample-script"></a>Példaszkript

Ez a példa létrehoz egy Azure-függvényalkalmazást, és üzembe helyezi a függvény kódját a GitHubról.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/deploy-function-app-with-function-github-continuous/deploy-function-app-with-function-github-continuous.sh?highlight=3-4 "Azure Service")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Szkript ismertetése

A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik. Ez a szkript a következő parancsokat használja:

| Parancs | Jegyzetek |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Létrehozza a tárfiókot, amely a függvényalkalmazáshoz szükséges. |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | Egy Function-alkalmazást hoz létre a kiszolgáló nélküli [felhasználási tervben](../functions-scale.md#consumption-plan) , és társítja azt egy git vagy Mercurial adattárral. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További Azure Functions CLI-példaszkripteket az [Azure Functions dokumentációjában](../functions-cli-samples.md) találhat.
