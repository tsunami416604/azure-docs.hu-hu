---
title: Docker-/ Go-alkalmazás létrehozása linuxon – az Azure App Service-ben
description: Go-alkalmazást futtató Docker-rendszerkép üzembe helyezése az Azure Web App for Containersben.
keywords: azure app service, web app, go, docker, container
services: app-service
author: msangapu
manager: jeconnoc
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.service: app-service
ms.devlang: go
ms.topic: quickstart
ms.date: 03/28/2019
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 88c9996ce3f2d89ae58881c913f6bd4e549b5814
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2019
ms.locfileid: "59547304"
---
# <a name="run-a-custom-linux-container-in-azure-app-service"></a>Futtassa egy egyéni Linux-tárolót az Azure App Service-ben

Az [App Service Linux](app-service-linux-intro.md) előre meghatározott, olyan programozási nyelvek támogatásával rendelkező alkalmazáscsoportokat biztosít Linuxon, amelybe beletartozik például a .NET, a PHP vagy a Node.js. Használhat egyéni Docker rendszerképet is, hogy a webalkalmazást egy, az Azure-ban nem meghatározott alkalmazáscsoportban futtassa. Ez a rövid útmutató webalkalmazások létrehozását és Go-rendszerképek Docker Hubból való üzembe helyezését mutatja be. Az [Azure CLI-vel](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) létrehozhatja a webalkalmazást.

![Az Azure-ban futó mintaalkalmazás](media/quickstart-docker-go/hello-world-in-browser.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app"></a>Webalkalmazás létrehozása

Az [ az webapp create](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) paranccsal hozzon létre egy [webalkalmazást](../overview.md) a `myAppServicePlan` App Service-csomagban. Ne felejtse el kicserélni az `<app name>` nevet egy globálisan egyedi alkalmazásnévre.

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app name> --deployment-container-image-name microsoft/azure-appservices-go-quickstart
```

Az előző parancsban a `--deployment-container-image-name` a nyilvános Docker Hub-rendszerképre mutat ([microsoft/azure-appservices-go-quickstart](https://hub.docker.com/r/microsoft/azure-appservices-go-quickstart/)).

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

```bash
http://<app_name>.azurewebsites.net/hello
```

![Az Azure-ban futó mintaalkalmazás](media/quickstart-docker-go/hello-world-in-browser.png)

**Gratulálunk!** Egy Go-alkalmazást futtató egyéni Docker-rendszerképet helyezett üzembe az Azure Web App for Containersben.

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Üzembe helyezés a privát tárház](tutorial-custom-docker-image.md)

> [!div class="nextstepaction"]
> [Egy egyéni tároló konfigurálása](configure-custom-container.md)

> [!div class="nextstepaction"]
> [Oktatóanyag: Többtárolós WordPress-alkalmazás](tutorial-multi-container-app.md)
