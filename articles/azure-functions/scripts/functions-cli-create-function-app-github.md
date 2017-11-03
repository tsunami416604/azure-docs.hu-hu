---
title: "Függvény-alkalmazás létrehozása és központi telepítése a Githubból funkciókódot |} Microsoft Docs"
description: "Az Azure CLI parancsfájl minta - függvény-alkalmazás létrehozása és központi telepítése a Githubból funkciókódot"
services: functions
keywords: 
author: syntaxc4
ms.author: cfowler
ms.date: 04/27/2017
ms.topic: sample
ms.service: functions
ms.custom: mvc
ms.openlocfilehash: 39e26ba6c3ae0927fbf6c62af0bd3b48d16657ad
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-function-app-and-deploy-function-code-from-github"></a>Függvény-alkalmazás létrehozása és központi telepítése a Githubból funkciókódot

Ez a parancsfájlpélda hoz létre, a függvény alkalmazás használatával az [fogyasztás terv](../functions-scale.md#consumption-plan) kapcsolódó erőforrásokkal, és telepíti a funkciókódot a nyilvános GitHub-adattár (folyamatos üzembe helyezés) nélkül. A Githubból funkciókódot folyamatos kézbesítését, olvassa el a [függvény-alkalmazás létrehozása, és folyamatosan telepítése a Githubból](functions-cli-create-function-app-github-continuous.md)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure parancssori felületének 2.0-s vagy annál újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Mintaparancsfájl

Ez a minta egy Azure-függvény alkalmazás létrehozza, és telepíti a Githubról funkciókódot.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/deploy-function-app-with-function-github/deploy-function-app-with-function-github.sh?highlight=3 "Create a function app with deployment from GitHub")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Parancsfájl ismertetése

Minden egyes parancsa a tábla-parancs adott dokumentációjára mutató hivatkozásokat. Ezt a parancsfájlt az alábbi parancsokat használja:

| Parancs | Megjegyzések |
|---|---|
| [az csoport létrehozása](https://docs.microsoft.com/cli/azure/group#az_group_create) | Az összes erőforrás tároló erőforrás csoportot hoz létre. |
| [az storage-fiók létrehozása](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_create) | App Service-csomag létrehozása. |
| [az functionapp létrehozása](https://docs.microsoft.com/cli/azure/appservice/web#az_appservice_web_delete) | Létrehoz egy Azure függvény alkalmazást. |
| [az App Service web verziókezelő konfiguráció](https://docs.microsoft.com/cli/azure/appservice/web/source-control#az_appservice_web_source_control_config) | Egy függvény app társítja a Git vagy Mercurial tárházba. |

## <a name="next-steps"></a>Következő lépések

További információ az Azure parancssori felület: [Azure CLI dokumentáció](https://docs.microsoft.com/cli/azure/overview).

További Azure Functions CLI parancsfájl minták megtalálhatók a [dokumentáció az Azure Functions](../functions-cli-samples.md).
