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
ms.date: 06/19/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 449e5729a15e619c43e5f4a0643915c2d3114d17
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-web-app-and-deploy-code-to-a-staging-environment"></a>Webalkalmazás létrehozása, majd kód telepítése az átmeneti környezetben való használatra

Ez a parancsfájlpélda hoz létre egy webalkalmazást az App Service egy további üzembe helyezési pont "tesztelés" nevű, és ezután telepíti egy mintaalkalmazást az "előkészítési" pont.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure parancssori felületének 2.0-s vagy annál újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Mintaparancsfájl

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/deploy-deployment-slot/deploy-deployment-slot.sh "Create a web app and deploy code to a staging environment")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Parancsfájl ismertetése

A parancsfájl a következő parancsokat. Minden egyes parancsa a tábla-parancs adott dokumentációjára mutató hivatkozásokat.

| Parancs | Megjegyzések |
|---|---|
| [az csoport létrehozása](https://docs.microsoft.com/cli/azure/group#az_group_create) | Az összes erőforrás tároló erőforrás csoportot hoz létre. |
| [az App Service-csomag létrehozása](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_create) | App Service-csomag létrehozása. |
| [az alkalmazás-kulcs létrehozása](https://docs.microsoft.com/cli/azure/webapp#az_webapp_create) | Létrehoz egy Azure-webalkalmazásban. |
| [az webalkalmazás üzembe helyezési tárhely létrehozása](https://docs.microsoft.com/cli/azure/webapp/deployment/slot#az_webapp_deployment_slot_create) | Egy üzembe helyezési tárhely létrehozása. |
| [az webalkalmazás központi telepítési forrás konfigurációs](https://docs.microsoft.com/cli/azure/webapp/deployment/source#az_webapp_deployment_source_config) | Azure-webalkalmazás társít egy Mercurial vagy a Git-tárházat. |
| [az alkalmazás kulcs Tallózás](https://docs.microsoft.com/cli/azure/webapp#az_webapp_browse) | Azure-webalkalmazás megnyitása a böngészőben. |
| [az webalkalmazás központi telepítési tárolóhelycsere](https://docs.microsoft.com/cli/azure/webapp/deployment/slot#az_webapp_deployment_slot_swap) | A megadott üzembe helyezési pont felcserélése éles környezetben. |

## <a name="next-steps"></a>Következő lépések

További információ az Azure parancssori felület: [Azure CLI dokumentáció](https://docs.microsoft.com/cli/azure/overview).

További App Service CLI parancsfájl minták megtalálhatók a [Azure App Service-dokumentáció](../app-service-cli-samples.md).
