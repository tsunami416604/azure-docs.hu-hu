---
title: "A tárolók - Azure webalkalmazás egy Docker-tároló beállításjegyzék folyamatos üzembe helyezés |} Microsoft Docs"
description: "A telepítő a folyamatos üzembe helyezés hogyan tárolók a Web App alkalmazásban egy Docker tároló beállításjegyzékből."
keywords: az Azure app service, linux, docker, acr, oss
services: app-service
documentationcenter: 
author: ahmedelnably
manager: cfowler
editor: 
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: aelnably;wesmc
ms.openlocfilehash: 9759eac01e2db62154cd7f6bf9fcccf13282b4b4
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2018
---
# <a name="continuous-deployment-with-web-app-for-containers"></a>A webalkalmazás az tárolókat folyamatos üzembe helyezés

Ebben az oktatóanyagban konfigurálása a folyamatos üzembe egy egyéni tároló lemezkép felügyelt [Azure tároló beállításjegyzék](https://azure.microsoft.com/services/container-registry/) tárházak vagy [Docker Hub](https://hub.docker.com).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be a [Azure-portálon](https://portal.azure.com)

## <a name="enable-container-continuous-deployment-feature"></a>Tároló folyamatos üzembe helyezés funkció engedélyezése

Engedélyezheti a folyamatos üzembe helyezés funkció használatával [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) és a következő parancs végrehajtása

```azurecli-interactive
az webapp deployment container config -n sname -g rgname -e true
```

Az a  **[Azure-portálon](https://portal.azure.com/)**, kattintson a **App Service** lehetőséget a bal oldali a lap.

Kattintson a nevére, amely a folyamatos üzembe Docker Hub konfigurálni szeretné az alkalmazás.

A **Docker-tároló**, válassza a "On", majd kattintson a Mentés folyamatos üzembe helyezés engedélyezéséhez.

![Helyezze be a Alkalmazásbeállítás képe](./media/app-service-webapp-service-linux-ci-cd/step2.png)

## <a name="prepare-webhook-url"></a>Készítse elő a Webhook URL-CÍMÉT

Ezt úgy szerezheti be a Webhook URL-cím használatával [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) és a következő parancs végrehajtása

```azurecli-interactive
az webapp deployment container show-cd-url -n sname1 -g rgname
```

A Webhook URL-címhez kell rendelkeznie a következő végpontot: `https://<publishingusername>:<publishingpwd>@<sitename>.scm.azurewebsites.net/docker/hook`.

Ezt úgy szerezheti be a `publishingusername` és `publishingpwd` úgy, hogy letölti a webes alkalmazás közzététele a profil az Azure portál használatával.

![Helyezze be a webhook 2 hozzáadása képe](./media/app-service-webapp-service-linux-ci-cd/step3-3.png)

## <a name="add-a-web-hook"></a>Egy webes hook hozzáadása

### <a name="azure-container-registry"></a>Azure Container Registry

A beállításjegyzék portálpanelén kattintson **Webhookok**, hozzon létre egy új webhook kattintva **Hozzáadás**. Az a **webhook létrehozása** panelen adjon a webhook nevét. A Webhook URI-hoz, meg kell adnia az URL-címet szerzett **3. lépés**

Győződjön meg arról, mint a tárház, amely tartalmazza a tároló lemezkép hatókörének meghatározása.

![Helyezze be a webhook képe](./media/app-service-webapp-service-linux-ci-cd/step3ACRWebhook-1.png)

A lemezkép frissítésekor a lekérdezi a web app frissül automatikusan a új lemezképpel.

### <a name="docker-hub"></a>Docker központ

A Docker Hub oldalon kattintson **Webhookok**, majd **A WEBHOOK létrehozása**.

![Helyezze be a webhook 1 hozzáadása képe](./media/app-service-webapp-service-linux-ci-cd/step3-1.png)

A Webhook URL-címhez, meg kell adnia az URL-címet szerzett **3. lépés**

![Helyezze be a webhook 2 hozzáadása képe](./media/app-service-webapp-service-linux-ci-cd/step3-2.png)

A lemezkép frissítésekor a lekérdezi a web app frissül automatikusan a új lemezképpel.

## <a name="next-steps"></a>További lépések

* [Mi az Azure App Service Linux?](./app-service-linux-intro.md)
* [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
* [A .NET Core használata a Linuxon futó Azure App Service-ben](quickstart-dotnetcore.md)
* [A Ruby használata a Linuxon futó Azure App Service-ben](quickstart-ruby.md)
* [Egyéni Docker-rendszerkép használata a Web App for Containers szolgáltatásban](quickstart-docker-go.md)
* [Azure App Service Web App for Containers – gyakori kérdések](./app-service-linux-faq.md)
* [Webalkalmazás az Azure CLI 2.0 használatával tárolók kezelése](./app-service-linux-cli.md)
