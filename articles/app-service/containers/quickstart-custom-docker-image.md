---
title: "Egyéni Docker központi lemezkép futtatható Azure Web App az tárolókat |} Microsoft Docs"
description: "Hogyan használható az egyéni Docker-lemezkép az Azure Web App az tárolókat."
keywords: "az Azure app service, a webes alkalmazás, a linux, a docker, a tároló"
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
ms.openlocfilehash: 8e7afd89def170ce756aae9e76daf91d78cc20e0
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
---
# <a name="run-a-custom-docker-hub-image-in-azure-web-app-for-containers"></a>Egyéni Docker központi lemezkép futtassa az tárolókat Azure Web App alkalmazásban

App Service biztosít előre definiált alkalmazás verem Linux-támogatással rendelkező különböző verziókat, például a PHP 7.0 és a Node.js 4.5. Egy egyéni Docker-lemezkép segítségével futtassa a webalkalmazás már nincs definiálva az Azure alkalmazás veremben. A gyors üzembe helyezés jeleníti meg a webalkalmazás létrehozása és telepítése a [hivatalos Nginx Docker kép](https://hub.docker.com/r/_/nginx/) rá. A webes alkalmazás használata a [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).

![Az Azure-ban futó mintaalkalmazás](media/quickstart-custom-docker-image/hello-world-in-browser.png)

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app-for-container"></a>A tároló-webalkalmazás létrehozása

Az [ az webapp create](/cli/azure/webapp#create) paranccsal hozzon létre egy [webalkalmazást](../app-service-web-overview.md) a `myAppServicePlan` App Service-csomagban. Ne felejtse el lecserélni `<app name>` egy egyedi alkalmazásnévvel rendelkező.

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app name> --deployment-container-image-name nginx
```

Az előző parancsban szereplő `--deployment-container-image-name` a nyilvános Docker Hub képre mutató [https://hub.docker.com/r/_/nginx/](https://hub.docker.com/r/_/nginx/).

Ha a webalkalmazás létrejött, az Azure parancssori felület kimenetét mutatja be az alábbi példához hasonló:

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

Tallózással keresse meg a következő URL-címet a webböngésző használatával.

```bash
http://<app_name>.azurewebsites.net
```

![Az Azure-ban futó mintaalkalmazás](media/quickstart-custom-docker-image/hello-world-in-browser.png)

**Gratulálunk!** A tárolók a webes alkalmazás telepítése után egy egyéni Docker-lemezképet.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Egyéni Docker kép használata](tutorial-custom-docker-image.md)
