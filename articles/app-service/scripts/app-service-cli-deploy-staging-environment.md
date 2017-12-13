---
title: "Az Azure CLI parancsfájl minta - webalkalmazás létrehozása és kód telepítése az átmeneti környezetben való használatra |} Microsoft Docs"
description: "Az Azure CLI parancsfájl minta - webalkalmazás létrehozása és kód telepítése az átmeneti környezetben való használatra"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 2b995dcd-e471-4355-9fda-00babcdb156e
ms.service: app-service-web
ms.workload: web
ms.devlang: azurecli
ms.tgt_pltfrm: na
ms.topic: sample
ms.date: 12/11/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: ccba0127904102e0956bc65eb682171aa8f22cb0
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2017
---
# <a name="create-a-web-app-and-deploy-code-to-a-staging-environment"></a>Webalkalmazás létrehozása, majd kód telepítése az átmeneti környezetben való használatra

Ez a parancsfájlpélda hoz létre egy webalkalmazást az App Service egy további üzembe helyezési pont "tesztelés" nevű, és ezután telepíti egy mintaalkalmazást az "előkészítési" pont.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha telepítése és a parancssori felület helyileg használata mellett dönt, az Azure parancssori felület verzió szüksége 2.0-s vagy újabb. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Mintaparancsfájl

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/deploy-deployment-slot/deploy-deployment-slot.sh "Create a web app and deploy code to a staging environment")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Parancsfájl ismertetése

A parancsfájl a következő parancsokat. Minden egyes parancsa a tábla-parancs adott dokumentációjára mutató hivatkozásokat.

| Parancs | Megjegyzések |
|---|---|
| [`az group create`](/cli/azure/group?view=azure-cli-latest#az_group_create) | Az összes erőforrás tároló erőforrás csoportot hoz létre. |
| [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az_appservice_plan_create) | App Service-csomag létrehozása. |
| [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create) | Létrehoz egy Azure-webalkalmazásban. |
| [`az webapp deployment slot create`](/cli/azure/webapp/deployment/slot?view=azure-cli-latest#az_webapp_deployment_slot_create) | Egy üzembe helyezési tárhely létrehozása. |
| [`az webapp deployment source config`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az_webapp_deployment_source_config) | Azure-webalkalmazás társít egy Mercurial vagy a Git-tárházat. |
| [`az webapp deployment slot swap`](/cli/azure/webapp/deployment/slot?view=azure-cli-latest#az_webapp_deployment_slot_swap) | A megadott üzembe helyezési pont felcserélése éles környezetben. |

## <a name="next-steps"></a>Következő lépések

További információ az Azure parancssori felület: [Azure CLI dokumentáció](https://docs.microsoft.com/cli/azure/overview).

További App Service CLI parancsfájl minták megtalálhatók a [Azure App Service-dokumentáció](../app-service-cli-samples.md).
