---
title: Függvényalkalmazás létrehozása a DevOps-telepítéssel – Azure CLI
description: Függvényalkalmazás létrehozása és függvénykód üzembe helyezése az Azure DevOpsból
ms.date: 07/03/2018
ms.topic: sample
ms.custom: mvc
ms.openlocfilehash: d10c3987aef6e0a32081acd17425517f5109c4bb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75922641"
---
# <a name="create-a-function-in-azure-that-is-deployed-from-azure-devops"></a>Hozzon létre egy azure-beli függvényt, amely az Azure DevOps-ból van telepítve

Ez a témakör bemutatja, hogyan hozhat létre az Azure Functions segítségével kiszolgáló [nélküli](https://azure.microsoft.com/solutions/serverless/) függvényalkalmazást a [Felhasználási terv](../functions-scale.md#consumption-plan)használatával. A függvényalkalmazás, amely a függvények tárolója, folyamatosan üzembe kerül egy Azure DevOps-tárházból. 

A témakör teljesítéséhez a következőkre lesz szüksége:

* Egy Azure DevOps-adattárra, amely tartalmazza a függvényalkalmazás projektjét, és amelyhez Ön rendszergazdai engedélyekkel rendelkezik.
* Egy [személyes hozzáférési jogkivonatra (PAT)](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate) az Azure DevOps-adattár eléréséhez.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha inkább a helyi Azure CLI-t használja, akkor az Azure CLI 2.0-s vagy újabb verzióját kell telepítenie és futtatnia. Az Azure CLI verziójának megállapításához futtassa az `az --version` parancsot. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését]( /cli/azure/install-azure-cli) ismertető cikket. 

## <a name="sample-script"></a>Példaszkript

Ez a példa létrehoz egy Azure-függvényalkalmazást, és üzembe helyezi a függvény kódját az Azure DevOpsból.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/deploy-function-app-with-function-vsts/deploy-function-app-with-function-vsts.sh?highlight=3-4 "Azure Service")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja egy erőforráscsoport, tárfiók, függvényalkalmazás és minden kapcsolódó erőforrás létrehozásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Létrehozza a tárfiókot, amely a függvényalkalmazáshoz szükséges. |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | Létrehoz egy függvényalkalmazást a kiszolgáló nélküli [felhasználási tervben.](../functions-scale.md#consumption-plan) |
| [az functionapp deployment source config](/cli/azure/functionapp/deployment/source#az-functionapp-deployment-source-config) | Társít egy függvényalkalmazást egy Git- vagy Mercurial-adattárhoz. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További Azure Functions CLI-példaszkripteket az [Azure Functions dokumentációjában](../functions-cli-samples.md) találhat.
