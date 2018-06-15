---
title: Folyamatos üzembe helyezés és a tárolók - Azure Web App egy Docker tároló beállításjegyzékből |} Microsoft Docs
description: Hogyan állítható be az tárolókat Web App alkalmazásban egy Docker tároló beállításjegyzékből folyamatos üzembe helyezést.
keywords: az Azure app service, linux, docker, acr, oss
services: app-service
documentationcenter: ''
author: ahmedelnably
manager: cfowler
editor: ''
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: aelnably;msangapu
ms.openlocfilehash: ac35dbd041de50ab8aae1a0fb4c00fe3917a7297
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
ms.locfileid: "30168326"
---
# <a name="continuous-deployment-with-web-app-for-containers"></a>A webalkalmazás az tárolókat folyamatos üzembe helyezés

Ebben az oktatóanyagban a folyamatos üzembe helyezés egy egyéni tároló lemezkép konfigurálása a felügyelt [Azure tároló beállításjegyzék](https://azure.microsoft.com/services/container-registry/) tárházak vagy [Docker Hub](https://hub.docker.com).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="enable-the-continuous-deployment-feature"></a>A folyamatos üzembe helyezés funkció engedélyezése

Engedélyezze a folyamatos üzembe helyezés szolgáltatás [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) és futtassa a következő parancsot:

```azurecli-interactive
az webapp deployment container config --name name --resource-group myResourceGroup --enable-cd true
```

Az a [Azure-portálon](https://portal.azure.com/), jelölje be a **App Service** a lap bal oldalán lehetőséget.

Válassza ki a nevét, amelynek a Docker Hub folyamatos üzembe helyezés konfigurálni szeretné az alkalmazást.

A a **Docker-tároló** lapon jelölje be **a**, majd válassza ki **mentése** folyamatos üzembe helyezés engedélyezéséhez.

![Képernyőkép a Alkalmazásbeállítás](./media/app-service-webapp-service-linux-ci-cd/step2.png)

## <a name="prepare-the-webhook-url"></a>Készítse elő a webhook URL-CÍMÉT

A webhook URL-cím beszerzése a [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) és futtassa a következő parancsot:

```azurecli-interactive
az webapp deployment container show-cd-url --name sname1 --resource-group rgname
```

A webhook URL-cím van szüksége a következő végpontot: `https://<publishingusername>:<publishingpwd>@<sitename>.scm.azurewebsites.net/docker/hook`.

Ezt úgy szerezheti be a `publishingusername` és `publishingpwd` úgy, hogy letölti a webes alkalmazás közzététele a profil az Azure portál használatával.

![Képernyőkép a webhook 2 hozzáadása](./media/app-service-webapp-service-linux-ci-cd/step3-3.png)

## <a name="add-a-webhook"></a>A webhook hozzáadása

### <a name="azure-container-registry"></a>Azure Container Registry

1. A beállításjegyzék portál lapján válassza ki a **Webhookok**.
2. Hozzon létre egy új webhook, jelölje be **Hozzáadás**. 
3. Az a **webhook létrehozása** ablaktáblán nevezze el a webhook. A webhook URI adja meg az előző szakaszban beszerzett URL-CÍMÉT.

Ellenőrizze, hogy a hatókör határozza meg, a tárház, amely tartalmazza a tároló lemezkép.

![Képernyőkép a webhook](./media/app-service-webapp-service-linux-ci-cd/step3ACRWebhook-1.png)

A lemezkép frissítésekor a webes alkalmazás automatikusan frissül az új lemezképet.

### <a name="docker-hub"></a>Docker központ

A Docker központ lapon jelölje be **Webhookok**, majd **A WEBHOOK létrehozása**.

![Képernyőkép a webhook 1 hozzáadása](./media/app-service-webapp-service-linux-ci-cd/step3-1.png)

A webhook URL-CÍMÉT adja meg a korábban beszerzett URL-CÍMÉT.

![Képernyőkép a webhook 2 hozzáadása](./media/app-service-webapp-service-linux-ci-cd/step3-2.png)

A lemezkép frissítésekor a webes alkalmazás automatikusan frissül az új lemezképet.

## <a name="next-steps"></a>További lépések

* [Bevezetés az Azure App Service Linux rendszeren](./app-service-linux-intro.md)
* [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
* [.NET Core-webalkalmazás létrehozása Linuxon futó App Service-ben](quickstart-dotnetcore.md)
* [Ruby-webalkalmazás létrehozása az App Service Linux rendszeren](quickstart-ruby.md)
* [A Web App az tárolókat Docker/Ugrás webalkalmazás üzembe helyezése](quickstart-docker-go.md)
* [Azure App Service Linuxon – gyakori kérdések](./app-service-linux-faq.md)
* [Webalkalmazás az Azure parancssori felület használatával tárolók kezelése](./app-service-linux-cli.md)
