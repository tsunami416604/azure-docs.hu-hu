---
title: "Függvény-alkalmazás létrehozása és központi telepítése a Visual Studio Team Services funkciókódot |} Microsoft Docs"
description: "Függvény-alkalmazás létrehozása és központi telepítése a Visual Studio Team Services funkciókódot"
services: functions
keywords: 
author: syntaxc4
ms.author: cfowler
ms.date: 04/28/2017
ms.topic: sample
ms.service: functions
ms.custom: mvc
ms.openlocfilehash: 5851b5219b6e25a5a2b005fc3d3c3b44d98ed746
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-app-service"></a>Hozzon létre egy App Service

Ebben a forgatókönyvben megtudhatja, hogyan létrehozásához, a függvény alkalmazás használatával az [fogyasztás terv](../functions-scale.md#consumption-plan) kapcsolódó erőforrásokkal, és folyamatosan telepíti a Visual Studio Team Services (VSTS) tárházból a funkciókódot. Ez a példa szüksége lesz:

* Egy VSTS-tárházat funkciók kóddal, a rendszergazdai jogosultsággal kell rendelkeznie.
* A [személyes hozzáférési jogkivonat (PAT)](https://help.github.com/articles/creating-an-access-token-for-command-line-use) a GitHub-fiókjában.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure parancssori felületének 2.0-s vagy annál újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Mintaparancsfájl

Ez a minta egy Azure-függvény alkalmazást hoz létre, és telepíti a Visual Studio Team Services funkciókódot.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/deploy-function-app-with-function-vsts/deploy-function-app-with-function-vsts.sh?highlight=3-4 "Azure Service")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Parancsfájl ismertetése

A parancsfájl a következő parancsokat egy erőforráscsoport, webalkalmazás, a documentdb és minden kapcsolódó erőforrások létrehozásához. Minden egyes parancsa a tábla-parancs adott dokumentációjára mutató hivatkozásokat.

| Parancs | Megjegyzések |
|---|---|
| [az csoport létrehozása](https://docs.microsoft.com/cli/azure/group#az_group_create) | Az összes erőforrás tároló erőforrás csoportot hoz létre. |
| [az storage-fiók létrehozása](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_create) | App Service-csomag létrehozása. |
| [az functionapp létrehozása](https://docs.microsoft.com/cli/azure/appservice/web#az_appservice_web_delete) |
| [az App Service web verziókezelő konfiguráció](https://docs.microsoft.com/cli/azure/appservice/web/source-control#az_appservice_web_source_control_config) | Egy függvény app társítja a Git vagy Mercurial tárházba. |

## <a name="next-steps"></a>Következő lépések

További információ az Azure parancssori felület: [Azure CLI dokumentáció](https://docs.microsoft.com/cli/azure/overview).

További Azure Functions CLI parancsfájl minták megtalálhatók a [dokumentáció az Azure Functions](../functions-cli-samples.md).
