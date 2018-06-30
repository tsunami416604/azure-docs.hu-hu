---
title: Folyamatos üzembe helyezés és a tárolók - Azure Web App egy Docker tároló beállításjegyzékből |} Microsoft Docs
description: Hogyan állítható be az tárolókat Web App alkalmazásban egy Docker tároló beállításjegyzékből folyamatos üzembe helyezést.
keywords: az Azure app service, linux, docker, acr, oss
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
ms.date: 06/29/2018
ms.author: msangapu
ms.openlocfilehash: 0f2d4626308eed376b71f1b3df2f9e43f1b2a4f7
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37130965"
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

Jegyezze fel a webhook URL-CÍMÉT. Szüksége lehet rájuk a következő szakaszban.
`https://<publishingusername>:<publishingpwd>@<sitename>.scm.azurewebsites.net/docker/hook`.

Ezt úgy szerezheti be a `publishingusername` és `publishingpwd` úgy, hogy letölti a webes alkalmazás közzététele a profil az Azure portál használatával.

![Képernyőkép a webhook 2 hozzáadása](./media/app-service-webapp-service-linux-ci-cd/step3-3.png)

## <a name="add-a-webhook"></a>A webhook hozzáadása

A webhook hozzáadásához kövesse a lépéseket, ezek az útmutatók a:

- [Az Azure tároló beállításjegyzék](../../container-registry/container-registry-webhook.md) a webhook URL-cím használatával
- [Webhook Docker központ](https://docs.docker.com/docker-hub/webhooks/)

## <a name="next-steps"></a>További lépések

* [Bevezetés az Azure App Service Linux rendszeren](./app-service-linux-intro.md)
* [Azure-tárolót beállításjegyzék](https://azure.microsoft.com/services/container-registry/)
* [.NET Core-webalkalmazás létrehozása Linuxon futó App Service-ben](quickstart-dotnetcore.md)
* [Ruby-webalkalmazás létrehozása az App Service Linux rendszeren](quickstart-ruby.md)
* [A Web App az tárolókat Docker/Ugrás webalkalmazás üzembe helyezése](quickstart-docker-go.md)
* [Azure App Service Linuxon – gyakori kérdések](./app-service-linux-faq.md)
* [Webalkalmazás az Azure parancssori felület használatával tárolók kezelése](./app-service-linux-cli.md)
