---
title: "Egyéni Docker központi lemezkép futtassa a Web App az tárolókat |} Microsoft Docs"
description: "Hogyan használható az egyéni Docker-lemezkép webalkalmazás az tárolókat."
keywords: "az Azure app service, a webes alkalmazás, a linux, a docker, a tároló"
services: app-service
documentationcenter: 
author: naziml
manager: cfowler
editor: 
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 09/05/2017
ms.author: wesmc
ms.custom: mvc
ms.openlocfilehash: c85f79cc14cdcecd2a05fc0ff91c4864b9fba277
ms.sourcegitcommit: 3e3a5e01a5629e017de2289a6abebbb798cec736
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2017
---
# <a name="run-a-custom-docker-hub-image-in-web-app-for-containers"></a>A Web App az tárolókat egyéni Docker központi lemezkép futtatása

App Service biztosít előre definiált alkalmazás verem Linux-támogatással rendelkező különböző verziókat, például a PHP 7.0 és a Node.js 4.5. Egyéni Docker-lemezkép használatával a webalkalmazás telepítése az egy alkalmazás verem nem definiált már az Azure-ban. A gyors üzembe helyezés bemutatja, hogyan webalkalmazás létrehozása és központi telepítése egy Python-alapú Docker lemezképet. A webes alkalmazás használata a [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app-for-container"></a>A tároló-webalkalmazás létrehozása

Az [ az webapp create](/cli/azure/webapp#create) paranccsal hozzon létre egy [webalkalmazást](../app-service-web-overview.md) a `myAppServicePlan` App Service-csomagban. Ne felejtse el lecserélni `<app name>` egy egyedi alkalmazásnévvel rendelkező.

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app name> --deployment-container-image-name elnably/dockerimagetest
```

Az előző parancsban szereplő `--deployment-container-image-name` a nyilvános Docker Hub képre mutató [https://hub.docker.com/r/elnably/dockerimagetest/](https://hub.docker.com/r/elnably/dockerimagetest/). Vizsgálhatja meg a tartalmat [https://github.com/ahmedelnably/dockerimagetest](https://github.com/ahmedelnably/dockerimagetest).

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
> [Az Azure-ban Docker Python és PostgreSQL webalkalmazás létrehozása](tutorial-docker-python-postgresql-app.md)
