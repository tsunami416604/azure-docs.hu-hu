---
title: "Az Azure CLI-parancsfájlt minták – az ASP.NET Core webalkalmazás létrehozása az Azure-tároló beállításjegyzékből egy Docker-tároló |} Microsoft Docs"
description: "Az Azure CLI-parancsfájlt minták – az ASP.NET Core webalkalmazás létrehozása az Azure-tároló beállításjegyzékből egy Docker-tároló"
services: appservice
documentationcenter: appservice
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 3a2d1983-ff7b-476a-ac44-49ec2aabb31a
ms.service: app-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 12/11/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: 9412b7175cc8e6879779bc923aa6b40d01125df7
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2017
---
# <a name="create-an-aspnet-core-web-app-in-a-docker-container-from-azure-container-registry"></a>Az ASP.NET Core webalkalmazás létrehozása az Azure-tároló beállításjegyzékből egy Docker-tároló

Ez a parancsfájlpélda hoz létre egy erőforráscsoportot, egy Linux App Service-csomag és egy webalkalmazást. Majd egy Docker-tároló használata az Azure-tároló regisztrációs kérelmet az ASP.NET Core központilag telepíti.


[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha telepítése és a parancssori felület helyileg használata mellett dönt, az Azure parancssori felület verzió szüksége 2.0-s vagy újabb. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Mintaparancsfájl

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/deploy-linux-acr/deploy-linux-acr.sh?highlight=6-9 "Linux Azure Container Registry")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Parancsfájl ismertetése

A parancsfájl a következő parancsokat egy erőforráscsoport, a web app és az összes kapcsolódó erőforrások létrehozásához. Minden egyes parancsa a tábla-parancs adott dokumentációjára mutató hivatkozásokat.

| Parancs | Megjegyzések |
|---|---|
| [`az group create`](/cli/azure/group?view=azure-cli-latest#az_group_create) | Az összes erőforrás tároló erőforrás csoportot hoz létre. |
| [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az_appservice_plan_create) | App Service-csomag létrehozása. |
| [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create) | Létrehoz egy Azure-webalkalmazásban. |
| [`az webapp config container set`](/cli/azure/webapp/config/container?view=azure-cli-latest#az_webapp_config_container_set) | Beállítja a Docker-tároló az Azure webalkalmazás számára. |

## <a name="next-steps"></a>Következő lépések

További információ az Azure parancssori felület: [Azure CLI dokumentáció](https://docs.microsoft.com/cli/azure/overview).

További App Service CLI parancsfájl minták megtalálhatók a [Azure App Service-dokumentáció](../app-service-cli-samples.md).
