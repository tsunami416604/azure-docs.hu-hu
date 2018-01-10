---
title: "Függvény létrehozása a Visual Studio Team Services telepített Azure-ban |} Microsoft Docs"
description: "Függvény-alkalmazás létrehozása és központi telepítése a Visual Studio Team Services funkciókódot"
services: functions
keywords: 
author: syntaxc4
ms.author: cfowler
ms.date: 01/09/2018
ms.topic: sample
ms.service: functions
ms.custom: mvc
ms.openlocfilehash: bf9428f23e851bae3485ec3d724dfb9ccd2af4c1
ms.sourcegitcommit: 6fb44d6fbce161b26328f863479ef09c5303090f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2018
---
# <a name="create-a-function-in-azure-that-is-deployed-from-visual-studio-team-services"></a>Függvény létrehozása a Visual Studio Team Services telepített Azure-ban

Ez a témakör bemutatja, hogyan Azure Functions használatával hozzon létre egy [kiszolgáló nélküli](https://azure.microsoft.com/overview/serverless-computing/) függvény alkalmazás használata a [fogyasztás terv](../functions-scale.md#consumption-plan). A függvény alkalmazást, amely a funkciók tárolója, folyamatosan van telepítve a Visual Studio Team Services (VSTS) tárházból. Ez a témakör elvégzése kell rendelkeznie:

* Egy VSTS-tárházat, amely tartalmazza a függvény app projektet, és rendszergazdai engedélyekkel rendelkeznie.
* A [személyes hozzáférési jogkivonat (PAT)](https://docs.microsoft.com/vsts/accounts/use-personal-access-tokens-to-authenticate) a VSTS-tárház eléréséhez.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha inkább használja az Azure parancssori felület helyileg, akkor telepítse, és 2.0-s vagy újabb verzióját használja. Azt az Azure CLI-verziót, `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Mintaparancsfájl

Ez a minta egy Azure-függvény alkalmazást hoz létre, és telepíti a Visual Studio Team Services funkciókódot.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/deploy-function-app-with-function-vsts/deploy-function-app-with-function-vsts.sh?highlight=3-4 "Azure Service")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Parancsfájl ismertetése

A parancsfájl a következő parancsokat egy erőforráscsoport, a tárfiók, függvény alkalmazás és minden kapcsolódó erőforrás létrehozásához. Minden egyes parancsa a tábla-parancs adott dokumentációjára mutató hivatkozásokat.

| Parancs | Megjegyzések |
|---|---|
| [az csoport létrehozása](https://docs.microsoft.com/cli/azure/group#az_group_create) | Az összes erőforrás tároló erőforrás csoportot hoz létre. |
| [az storage-fiók létrehozása](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_create) | App Service-csomag létrehozása. |
| [az functionapp létrehozása](https://docs.microsoft.com/cli/azure/appservice/web#az_appservice_web_delete) |
| [az App Service web verziókezelő konfiguráció](https://docs.microsoft.com/cli/azure/appservice/web/source-control#az_appservice_web_source_control_config) | Egy függvény app társítja a Git vagy Mercurial tárházba. |

## <a name="next-steps"></a>További lépések

További információ az Azure parancssori felület: [Azure CLI dokumentáció](https://docs.microsoft.com/cli/azure/overview).

További Azure Functions CLI parancsfájl minták megtalálhatók a [dokumentáció az Azure Functions](../functions-cli-samples.md).
