---
title: Folyamatos üzembe helyezés a Web App for containers szolgáltatásban – Azure App Service-ben |} A Microsoft Docs
description: Hogyan állítható be a folyamatos üzembe helyezés a Web App for containers szolgáltatásban.
keywords: az Azure app Service-ben, a linux, a docker, az acr, a nyílt forráskódú
services: app-service
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2018
ms.author: yili
ms.custom: seodec18
ms.openlocfilehash: 4acadc4c08ef50e7d52303689c38c43f81187669
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53315529"
---
# <a name="continuous-deployment-with-web-app-for-containers"></a>Folyamatos üzembe helyezés a Web App for containers szolgáltatásban

Ebben az oktatóanyagban egy egyéni tárolórendszerképbe – folyamatos üzembe helyezés konfigurálása a felügyelt [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) tárházak vagy [Docker Hub](https://hub.docker.com).

## <a name="enable-continuous-deployment-with-acr"></a>Az ACR-REL folyamatos üzembe helyezés engedélyezése

![Képernyőkép az ACR webhook](./media/app-service-webapp-service-linux-ci-cd/ci-cd-acr-02.png)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki a **App Service-ben** az oldal bal oldalán lehetőséget.
3. Válassza ki az alkalmazást, amelyhez a folyamatos üzembe helyezés konfigurálása kívánt nevét.
4. Az a **Tárolóbeállítások** lapon jelölje be **egyetlen tároló**
5. Válassza ki **az Azure Container Registrybe**
6. Válassza ki **folyamatos üzembe helyezés > a**
7. Válassza ki **mentése** folyamatos üzembe helyezés engedélyezése.

## <a name="use-the-acr-webhook"></a>Az ACR-webhookok használata

Ha engedélyezve van a folyamatos üzembe helyezés, az Azure Container Registry webhookok oldalán megtekintheti az újonnan létrehozott webhook.

![Képernyőkép az ACR webhook](./media/app-service-webapp-service-linux-ci-cd/ci-cd-acr-03.png)

A Tárolóregisztrációs adatbázisba kattintson a Webhookok a jelenlegi webhookok megtekintéséhez.

## <a name="enable-continuous-deployment-with-docker-hub-optional"></a>(Nem kötelező) a Docker hub folyamatos üzembe helyezés engedélyezése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki a **App Service-ben** az oldal bal oldalán lehetőséget.
3. Válassza ki az alkalmazást, amelyhez a folyamatos üzembe helyezés konfigurálása kívánt nevét.
4. Az a **Tárolóbeállítások** lapon jelölje be **egyetlen tároló**
5. Válassza ki **Docker Hub**
6. Válassza ki **folyamatos üzembe helyezés > a**
7. Válassza ki **mentése** folyamatos üzembe helyezés engedélyezése.

![Képernyőkép az alkalmazás-beállítás](./media/app-service-webapp-service-linux-ci-cd/ci-cd-docker-02.png)

Másolja a Webhook URL-CÍMÉT. Egy webhook hozzáadása a Docker hub, hajtsa végre a <a href="https://docs.docker.com/docker-hub/webhooks/" target="_blank">Docker hub webhookok</a>.

## <a name="next-steps"></a>További lépések

* [Linuxon futó Azure App Service bemutatása](./app-service-linux-intro.md)
* [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
* [.NET Core-webalkalmazás létrehozása Linuxon futó App Service-ben](quickstart-dotnetcore.md)
* [Ruby-webalkalmazás létrehozása Linuxon futó App Service-ben](quickstart-ruby.md)
* [Web App for Containers a Docker-/ Go-webalkalmazás üzembe helyezése](quickstart-docker-go.md)
* [Azure App Service Linuxon – gyakori kérdések](./app-service-linux-faq.md)
* [Web App for Containers Azure CLI-vel kezelése](./app-service-linux-cli.md)
