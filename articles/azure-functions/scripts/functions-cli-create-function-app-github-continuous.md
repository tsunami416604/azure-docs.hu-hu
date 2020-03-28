---
title: Függvényalkalmazás létrehozása a GitHub központi telepítésével – Azure CLI
description: Egy függvényalkalmazás létrehozása és függvénykód üzembe helyezése egy GitHub-adattárból az Azure Functions használatával.
ms.date: 07/03/2018
ms.topic: sample
ms.custom: mvc
ms.openlocfilehash: e424737b67be58ac0c3880ad2454c87fab803a15
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75922737"
---
# <a name="create-a-function-app-in-azure-that-is-deployed-from-github"></a>GitHubról üzembe helyezett függvényalkalmazás létrehozása az Azure-ban

Ez az Azure Functions mintaparancsfájl létrehoz egy függvényalkalmazást a [felhasználási terv](../functions-scale.md#consumption-plan)használatával, a kapcsolódó erőforrásokkal együtt. Ezután egy GitHub-adattárból konfigurálja a függvénykódot folyamatos üzembe helyezéshez. 

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

| Parancs | Megjegyzések |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Létrehozza a tárfiókot, amely a függvényalkalmazáshoz szükséges. |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | Létrehoz egy függvényalkalmazást a kiszolgáló nélküli [felhasználási tervben,](../functions-scale.md#consumption-plan) és hozzárendeli egy Git- vagy Mercurial-tárházhoz. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További Azure Functions CLI-példaszkripteket az [Azure Functions dokumentációjában](../functions-cli-samples.md) találhat.
