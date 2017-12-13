---
title: "Azure CLI parancsfájl minta - webalkalmazás létrehozása és központi telepítése FTP-vel fájlok |} Microsoft Docs"
description: "Azure CLI parancsfájl minta - webalkalmazás létrehozása és központi telepítése FTP-vel fájlok"
services: app-service\web
documentationcenter: 
author: cephalin
manager: cfowler
editor: 
tags: azure-service-management
ms.service: app-service-web
ms.workload: web
ms.devlang: azurecli
ms.tgt_pltfrm: sample
ms.topic: sample
ms.date: 12/12/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 9e71c5ac8bbf494ed60995457180efaf1a17111f
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2017
---
# <a name="create-a-web-app-and-deploy-files-with-ftp"></a>Webalkalmazás létrehozása és központi telepítése FTP-vel fájlok

Ez a parancsfájlpélda hoz létre egy webalkalmazást az App Service azok kapcsolódó erőforrásait, és a statikus HTML-lapot, FTP-használ, majd telepíti. Az FTP-feltöltési, a parancsfájl a [cURL](https://en.wikipedia.org/wiki/CURL) példaként. A fájlok feltöltéséhez bármely FTP-eszköz is használhatja.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha telepítése és a parancssori felület helyileg használata mellett dönt, az Azure parancssori felület verzió szüksége 2.0-s vagy újabb. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Mintaparancsfájl

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/deploy-ftp/deploy-ftp.sh "Create a web app and deploy files with FTP")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Parancsfájl ismertetése 

A parancsfájl a következő parancsokat. Minden egyes parancsa a tábla-parancs adott dokumentációjára mutató hivatkozásokat.

| Parancs | Megjegyzések |
|---|---|
| [`az group create`](/cli/azure/group?view=azure-cli-latest#az_group_create) | Az összes erőforrás tároló erőforrás csoportot hoz létre. |
| [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az_appservice_plan_create) | App Service-csomag létrehozása. |
| [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create) | Létrehoz egy Azure-webalkalmazásban. |
| [`az webapp deployment list-publishing-profiles`](/cli/azure/webapp/deployment?view=azure-cli-latest#az_webapp_deployment_list_publishing_profiles) | Ismerje meg a részleteket érhető el webes alkalmazás központi telepítési profil esetében. |

## <a name="next-steps"></a>Következő lépések

További információ az Azure parancssori felület: [Azure CLI dokumentáció](https://docs.microsoft.com/cli/azure/overview).

További App Service CLI parancsfájl minták megtalálhatók a [Azure App Service-dokumentáció](../app-service-cli-samples.md).
