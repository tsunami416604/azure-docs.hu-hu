---
title: "Go-alkalmazás üzembe helyezése a tárolókhoz készült Azure Web App szolgáltatásban | Microsoft Docs"
description: "Megtudhatja, hogyan helyezhet üzembe Go-alkalmazást futtató rendszerképet a tárolókhoz készült Azure Web Apps szolgáltatásban."
keywords: azure app service, web app, go, docker, container
services: app-service
author: sptramer
manager: cfowler
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.service: app-service
ms.devlang: go
ms.topic: quickstart
ms.date: 01/17/2018
ms.author: sttramer
ms.custom: mvc
ms.openlocfilehash: dbe4d7bc6f5f1d83a079993c9616206fd82a1b9d
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2018
---
# <a name="deploy-a-go-app-in-azure-web-app-for-containers"></a>Go-alkalmazás üzembe helyezése a tárolókhoz készült Azure Web App szolgáltatásban

Az App Service előre meghatározott, adott verziós támogatással rendelkező alkalmazáscsoportokat biztosít Linuxon, amelybe beletartozik például a PHP 7.0 vagy a Node.js 4.5. Használhat egyéni Docker rendszerképet is, hogy a webalkalmazást egy, az Azure-ban nem meghatározott alkalmazáscsoportban futtassa. Ez a rövid útmutató bemutatja, hogyan futtathat egy Go-alkalmazást futtató meglévő Docker-tárolót az Azure App Service szolgáltatásban. Az [Azure CLI-vel](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) létrehozhatja a webalkalmazást.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app-for-the-container"></a>Web App létrehozása a tárolóban

Az [ az webapp create](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create) paranccsal hozzon létre egy [webalkalmazást](../app-service-web-overview.md) a `myAppServicePlan` App Service-csomagban. Ne felejtse el kicserélni az `<app name>` nevet egy globálisan egyedi alkalmazásnévre.

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app name> --deployment-container-image-name microsoft/appservice-go-quickstart
```

Az előző parancsban a `--deployment-container-image-name` a nyilvános Docker Hub rendszerképre mutat ([microsoft/appservice-go-quickstart](https://hub.docker.com/r/microsoft/appservice-go-quickstart)).

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

## <a name="get-data-from-the-apps-endpoint"></a>Adatok lekérése az alkalmazás végpontjáról

A `curl` paranccsal kapcsolódjon a tároló alkalmazása által feltárt REST API-végponthoz.

```bash
curl -X GET http://<app_name>.azurewebsites.net:8080/hello
```

```output
Hello world!
```

**Gratulálunk!** Egy Go-alkalmazást futtató egyéni rendszerképet helyezett üzembe a tárolókhoz készült Web Apps szolgáltatásban.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyéni Docker-rendszerkép használata](tutorial-custom-docker-image.md)
