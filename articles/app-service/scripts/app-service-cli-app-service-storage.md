---
title: "Az Azure CLI-parancsfájlt minta - tárfiókba webes alkalmazás csatlakoztatása |} Microsoft Docs"
description: "Az Azure CLI-parancsfájlt minta - tárfiókba webes alkalmazás csatlakoztatása"
services: appservice
documentationcenter: appservice
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: bc8345b2-8487-40c6-a91f-77414e8688e6
ms.service: app-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 12/11/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: 0f60c4cd03a5728dc81cb43e02c8486b15221851
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2017
---
# <a name="connect-a-web-app-to-a-storage-account"></a>Webes alkalmazás csatlakoztatása a tárolási fiók

Ez a parancsfájlpélda hoz létre egy Azure storage-fiókot és egy Azure-webalkalmazásban. A web app alkalmazást beállításokkal majd a tárfiók hivatkozik.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha telepítése és a parancssori felület helyileg használata mellett dönt, az Azure parancssori felület verzió szüksége 2.0-s vagy újabb. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli).


## <a name="sample-script"></a>Mintaparancsfájl

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/connect-to-storage/connect-to-storage.sh "Azure Storage")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Parancsfájl ismertetése

A parancsfájl a következő parancsokat egy erőforráscsoport, webalkalmazás, storage-fiók és minden kapcsolódó erőforrás létrehozásához. Minden egyes parancsa a tábla-parancs adott dokumentációjára mutató hivatkozásokat.

| Parancs | Megjegyzések |
|---|---|
| [`az group create`](/cli/azure/group?view=azure-cli-latest#az_group_create) | Az összes erőforrás tároló erőforrás csoportot hoz létre. |
| [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az_appservice_plan_create) | App Service-csomag létrehozása. |
| [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create) | Létrehoz egy Azure-webalkalmazásban. |
| [`az storage account create`](/cli/azure/storage/account?view=azure-cli-latest#az_storage_account_create) | Tárfiók létrehozása. |
| [`az storage account show-connection-string`](/cli/azure/storage/account?view=azure-cli-latest#az_storage_account_show_connection_string) | A kapcsolati karakterlánc beolvasása a storage-fiók. |
| [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set) | Létrehozza vagy frissíti az Azure-webalkalmazás Alkalmazásbeállítás. Alkalmazásbeállítások az alkalmazások környezeti változóként érhetők el. |

## <a name="next-steps"></a>Következő lépések

További információ az Azure parancssori felület: [Azure CLI dokumentáció](https://docs.microsoft.com/cli/azure/overview).

További App Service CLI parancsfájl minták megtalálhatók a [Azure App Service-dokumentáció](../app-service-cli-samples.md).
