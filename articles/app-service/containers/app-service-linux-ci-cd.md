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
ms.openlocfilehash: d43491de7500204ed470757a1b744017a8180b57
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "74687636"
---
# <a name="continuous-deployment-with-web-app-for-containers"></a>Folyamatos üzembe helyezés a Web App for Containers használatával

Ebben az oktatóanyagban a folyamatos üzembe helyezést konfigurálja a felügyelt [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) adattárakból vagy a [Docker hub](https://hub.docker.com)-ból származó egyéni tároló-rendszerképhez.

## <a name="enable-continuous-deployment-with-acr"></a>Folyamatos üzembe helyezés engedélyezése ACR-sel

![Képernyőfelvétel az ACR webhookról](./media/app-service-webapp-service-linux-ci-cd/ci-cd-acr-02.png)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Kattintson a lap bal oldalán található **app Service** lehetőségre.
3. Válassza ki annak az alkalmazásnak a nevét, amelyhez a folyamatos üzembe helyezést konfigurálni kívánja.
4. A **tároló beállításai** lapon válassza az **egyetlen tároló** elemet.
5. **Azure Container Registry** kiválasztása
6. **Folyamatos üzembe helyezési >** kiválasztása
7. A folyamatos üzembe helyezés engedélyezéséhez válassza a **Mentés** lehetőséget.

## <a name="use-the-acr-webhook"></a>Az ACR webhook használata

Miután engedélyezte a folyamatos üzembe helyezést, megtekintheti az újonnan létrehozott webhookot a Azure Container Registry webhookok oldalán.

![Képernyőfelvétel az ACR webhookról](./media/app-service-webapp-service-linux-ci-cd/ci-cd-acr-03.png)

Az aktuális webhookok megtekintéséhez a Container Registry kattintson a webhookok elemre.

## <a name="enable-continuous-deployment-with-docker-hub-optional"></a>Folyamatos üzembe helyezés engedélyezése a Docker hub-ban (opcionális)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Kattintson a lap bal oldalán található **app Service** lehetőségre.
3. Válassza ki annak az alkalmazásnak a nevét, amelyhez a folyamatos üzembe helyezést konfigurálni kívánja.
4. A **tároló beállításai** lapon válassza az **egyetlen tároló** elemet.
5. **Docker hub** kiválasztása
6. **Folyamatos üzembe helyezési >** kiválasztása
7. A folyamatos üzembe helyezés engedélyezéséhez válassza a **Mentés** lehetőséget.

![Képernyőkép az alkalmazás beállításáról](./media/app-service-webapp-service-linux-ci-cd/ci-cd-docker-02.png)

Másolja a webhook URL-címét. Ha webhookot szeretne hozzáadni a Docker hub-hoz, kövesse a <a href="https://docs.docker.com/docker-hub/webhooks/" target="_blank">Docker hub webhookait</a>.

## <a name="next-steps"></a>További lépések

* [A Linuxon futó Azure App Service bemutatása](./app-service-linux-intro.md)
* [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
* [.NET Core-webalkalmazás létrehozása Linuxon futó App Service-ben](quickstart-dotnetcore.md)
* [Ruby-Webalkalmazás létrehozása App Service Linux rendszeren](quickstart-ruby.md)
* [Docker-/Go-webalkalmazás üzembe helyezése az Azure Web App for Containersben](quickstart-docker-go.md)
* [Azure App Service Linuxon – gyakori kérdések](./app-service-linux-faq.md)
* [A Web App for Containers felügyelete az Azure CLI-vel](./app-service-linux-cli.md)
