---
title: CI/CD egyéni Linux-tárolókhoz
description: Ismerje meg, hogyan állíthatja be a folyamatos üzembe helyezést egy egyéni Linux-tárolóhoz az Azure App Service-ben. Folyamatos üzembe helyezés a Docker Hub és az ACR támogatott.
keywords: Azure app szolgáltatás, linux, docker, acr,oss
author: msangapu-msft
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.topic: article
ms.date: 11/08/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: d43491de7500204ed470757a1b744017a8180b57
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74687636"
---
# <a name="continuous-deployment-with-web-app-for-containers"></a>Folyamatos üzembe helyezés a Web App for Containers használatával

Ebben az oktatóanyagban konfigurálja a folyamatos üzembe helyezést egy egyéni tárolórendszerképhez a felügyelt [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) adattárakból vagy a [Docker Hubból.](https://hub.docker.com)

## <a name="enable-continuous-deployment-with-acr"></a>Folyamatos üzembe helyezés engedélyezése az ACR segítségével

![Képernyőkép az ACR webhookról](./media/app-service-webapp-service-linux-ci-cd/ci-cd-acr-02.png)

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. Válassza az **App Service** lehetőséget a lap bal oldalán.
3. Válassza ki annak az alkalmazásnak a nevét, amelyhez folyamatos telepítést szeretne konfigurálni.
4. A **Tároló beállításai** lapon válassza az **Egytárolós**
5. **Az Azure Container-beállításjegyzék** kiválasztása
6. **Folyamatos üzembe helyezés > bejelölése**
7. A folyamatos telepítés engedélyezéséhez válassza a **Mentés** lehetőséget.

## <a name="use-the-acr-webhook"></a>Az ACR webhook használata

Miután a folyamatos üzembe helyezés engedélyezve lett, megtekintheti az újonnan létrehozott webhook az Azure Container Registry webhooks lapon.

![Képernyőkép az ACR webhookról](./media/app-service-webapp-service-linux-ci-cd/ci-cd-acr-03.png)

A tároló-beállításjegyzékben kattintson a Webhooks elemre az aktuális webhookok megtekintéséhez.

## <a name="enable-continuous-deployment-with-docker-hub-optional"></a>Folyamatos üzembe helyezés engedélyezése a Docker Hubbal (nem kötelező)

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. Válassza az **App Service** lehetőséget a lap bal oldalán.
3. Válassza ki annak az alkalmazásnak a nevét, amelyhez folyamatos telepítést szeretne konfigurálni.
4. A **Tároló beállításai** lapon válassza az **Egytárolós**
5. Válassza a **Docker Hub lehetőséget**
6. **Folyamatos üzembe helyezés > bejelölése**
7. A folyamatos telepítés engedélyezéséhez válassza a **Mentés** lehetőséget.

![Képernyőkép az alkalmazásbeállításról](./media/app-service-webapp-service-linux-ci-cd/ci-cd-docker-02.png)

Másolja a Webhook URL-címét. Webhook hozzáadásához a Docker Hub, kövesse <a href="https://docs.docker.com/docker-hub/webhooks/" target="_blank">webhooks a Docker Hub.</a>

## <a name="next-steps"></a>További lépések

* [A Linuxon futó Azure App Service bemutatása](./app-service-linux-intro.md)
* [Azure container-beállításjegyzék](https://azure.microsoft.com/services/container-registry/)
* [.NET Core-webalkalmazás létrehozása Linuxon futó App Service-ben](quickstart-dotnetcore.md)
* [Ruby webalkalmazás létrehozása linuxos App Service-ben](quickstart-ruby.md)
* [Docker-/Go-webalkalmazás üzembe helyezése az Azure Web App for Containersben](quickstart-docker-go.md)
* [Azure App Service Linuxon – gyakori kérdések](./app-service-linux-faq.md)
* [A Web App for Containers felügyelete az Azure CLI-vel](./app-service-linux-cli.md)
