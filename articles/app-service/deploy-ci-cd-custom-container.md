---
title: CI/CD – egyéni Linux-tárolók
description: Megtudhatja, hogyan állíthatja be a folyamatos üzembe helyezést egy egyéni Linux-tárolóra Azure App Serviceban. A folyamatos üzembe helyezés a Docker hub és az ACR esetében támogatott.
keywords: Azure app Service, Linux, Docker, ACR, OSS
author: msangapu-msft
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.topic: article
ms.date: 11/08/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: b168328f64f599de109dbd0a5bd95c0a26f5f902
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/11/2020
ms.locfileid: "88083125"
---
# <a name="continuous-deployment-with-web-app-for-containers"></a>Folyamatos üzembe helyezés a Web App for Containers használatával

Ebben az oktatóanyagban a folyamatos üzembe helyezést konfigurálja a felügyelt [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) adattárakból vagy a [Docker hub](https://hub.docker.com)-ból származó egyéni tároló-rendszerképhez.

## <a name="enable-continuous-deployment-with-acr"></a>Folyamatos üzembe helyezés engedélyezése ACR-sel

![Képernyőfelvétel az ACR webhookról](./media/deploy-ci-cd-custom-container/ci-cd-acr-02.png)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Kattintson a lap bal oldalán található **app Service** lehetőségre.
3. Válassza ki annak az alkalmazásnak a nevét, amelyhez a folyamatos üzembe helyezést konfigurálni kívánja.
4. A **tároló beállításai** lapon válassza az **egyetlen tároló** elemet.
5. **Azure Container Registry** kiválasztása
6. **Folyamatos üzembe helyezési >** kiválasztása
7. A folyamatos üzembe helyezés engedélyezéséhez válassza a **Mentés** lehetőséget.

## <a name="use-the-acr-webhook"></a>Az ACR webhook használata

Miután engedélyezte a folyamatos üzembe helyezést, megtekintheti az újonnan létrehozott webhookot a Azure Container Registry webhookok oldalán.

![Képernyőfelvétel az ACR webhookról](./media/deploy-ci-cd-custom-container/ci-cd-acr-03.png)

Az aktuális webhookok megtekintéséhez a Container Registry kattintson a webhookok elemre.

## <a name="enable-continuous-deployment-with-docker-hub-optional"></a>Folyamatos üzembe helyezés engedélyezése a Docker hub-ban (opcionális)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Kattintson a lap bal oldalán található **app Service** lehetőségre.
3. Válassza ki annak az alkalmazásnak a nevét, amelyhez a folyamatos üzembe helyezést konfigurálni kívánja.
4. A **tároló beállításai** lapon válassza az **egyetlen tároló** elemet.
5. **Docker hub** kiválasztása
6. **Folyamatos üzembe helyezési >** kiválasztása
7. A folyamatos üzembe helyezés engedélyezéséhez válassza a **Mentés** lehetőséget.

![Képernyőkép az alkalmazás beállításáról](./media/deploy-ci-cd-custom-container/ci-cd-docker-02.png)

Másolja a webhook URL-címét. Ha webhookot szeretne hozzáadni a Docker hub-hoz, kövesse a <a href="https://docs.docker.com/docker-hub/webhooks/" target="_blank">Docker hub webhookait</a>.

## <a name="automate-with-cli"></a>Automatizálás a parancssori felülettel

Ha az Azure CLI használatával szeretné konfigurálni a CI/CD-t, futtassa az az [WebApp Deployment Container config](https://docs.microsoft.com/cli/azure/webapp/deployment/container?view=azure-cli-latest#az-webapp-deployment-container-config) parancsot a webhook URL-címének létrehozásához. Az URL-cím használatával konfigurálhatja a DockerHub vagy a Azure Container Registry.

```azurecli-interactive
az webapp deployment container config --name <app-name> --resource-group <group-name> --enable-cd true
```

## <a name="next-steps"></a>További lépések

* [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
* [.NET Core-webalkalmazás létrehozása Linuxon futó App Service-ben](quickstart-dotnetcore.md?pivots=platform-linux)
* [Ruby-Webalkalmazás létrehozása App Service Linux rendszeren](quickstart-ruby.md)
* [Gyors útmutató: egyéni tároló futtatása App Serviceon](quickstart-custom-container.md?pivots=container-linux)
* [App Service Linuxon – gyakori kérdések](faq-app-service-linux.md)
* [Egyéni Linux-tárolók konfigurálása](configure-custom-container.md)
