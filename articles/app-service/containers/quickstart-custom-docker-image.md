---
title: "Egyéni Docker Hub-rendszerkép futtatása az Azure Web App for Containers szolgáltatásban | Microsoft Docs"
description: "Egyéni Docker Hub-rendszerkép használata az Azure Web App for Containers szolgáltatásban."
keywords: azure app service, web app, linux, docker, container
services: app-service
documentationcenter: 
author: cephalin
manager: cfowler
editor: 
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 11/02/2017
ms.author: cephalin;wesmc
ms.custom: mvc
ms.openlocfilehash: 7f6ed6d52bea1faec9dbed96a8d7aef020aea5d9
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
---
# <a name="run-a-custom-docker-hub-image-in-azure-web-app-for-containers"></a>Egyéni Docker Hub-rendszerkép használata az Azure Web App for Containers szolgáltatásban

Az App Service előre meghatározott, adott verziós támogatással rendelkező alkalmazáscsoportokat biztosít Linuxon, amelybe beletartozik például a PHP 7.0 vagy a Node.js 4.5. Használhat egyéni Docker rendszerképet is, hogy a webalkalmazást egy, az Azure-ban nem meghatározott alkalmazáscsoportban futtassa. Ez a gyorsútmutató webalkalmazások létrehozását és a [hivatalos Nginx Docker rendszerkép](https://hub.docker.com/r/_/nginx/) alkalmazásba telepítését mutatja be. Az [Azure CLI-vel](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) létrehozhatja a webalkalmazást.

![Az Azure-ban futó mintaalkalmazás](media/quickstart-custom-docker-image/hello-world-in-browser.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app-for-container"></a>Web App for Container létrehozása

Az [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create) paranccsal hozzon létre egy [webalkalmazást](../app-service-web-overview.md) az `myAppServicePlan` App Service-csomagban. Ne felejtse el kicserélni az `<app name>` nevet egy egyedi alkalmazásnévre.

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app name> --deployment-container-image-name nginx
```

Az előző parancsban a `--deployment-container-image-name` a nyilvános Docker Hub rendszerképre mutat ([https://hub.docker.com/r/_/nginx/](https://hub.docker.com/r/_/nginx/)).

A webalkalmazás létrehozása után az Azure CLI az alábbi példához hasonló eredményeket jelenít meg:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app name>.scm.azurewebsites.net/<app name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

## <a name="browse-to-the-app"></a>Az alkalmazás megkeresése tallózással

Tallózással keresse meg az alábbi URL-t a webböngésző használatával.

```bash
http://<app_name>.azurewebsites.net
```

![Az Azure-ban futó mintaalkalmazás](media/quickstart-custom-docker-image/hello-world-in-browser.png)

**Gratulálunk!** Üzembe helyezte az egyéni Docker-rendszerképet a Web App for Containers szolgáltatásban.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyéni Docker-rendszerkép használata](tutorial-custom-docker-image.md)
