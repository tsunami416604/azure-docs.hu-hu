---
title: Folyamatos üzembe helyezés a Web App for containers szolgáltatásban – Azure Docker container registryből |} A Microsoft Docs
description: Hogyan állítható be a folyamatos üzembe helyezés a Web App for Containers a Docker-tárolójegyzék.
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
ms.date: 06/29/2018
ms.author: msangapu
ms.openlocfilehash: 20ca63b7126a6800538129115ff339308c11d8c5
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2018
ms.locfileid: "48867025"
---
# <a name="continuous-deployment-with-web-app-for-containers"></a>Folyamatos üzembe helyezés a Web App for containers szolgáltatásban

Ebben az oktatóanyagban egy egyéni tárolórendszerképbe – folyamatos üzembe helyezés konfigurálása a felügyelt [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) tárházak vagy [Docker Hub](https://hub.docker.com).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="enable-the-continuous-deployment-feature"></a>A folyamatos üzembe helyezés funkció engedélyezése

A folyamatos üzembe helyezés funkció engedélyezése használatával [Azure CLI-vel](https://docs.microsoft.com/cli/azure/install-azure-cli) és végrehajtja a következő parancsot:

```azurecli-interactive
az webapp deployment container config --name name --resource-group myResourceGroup --enable-cd true
```

Az a [az Azure portal](https://portal.azure.com/), jelölje be a **App Service-ben** az oldal bal oldalán lehetőséget.

Válassza ki a nevét, amely a Docker Hub folyamatos üzembe helyezés konfigurálása szeretné az alkalmazást.

Az a **Tárolóbeállítások** lapon jelölje be **a**, majd válassza ki **mentése** folyamatos üzembe helyezés engedélyezése.

![Képernyőkép az alkalmazás-beállítás](./media/app-service-webapp-service-linux-ci-cd/step2.png)

## <a name="prepare-the-webhook-url"></a>Készítse elő a webhook URL-címe

Szerezze be a webhook URL-cím használatával [Azure CLI-vel](https://docs.microsoft.com/cli/azure/install-azure-cli) és végrehajtja a következő parancsot:

```azurecli-interactive
az webapp deployment container show-cd-url --name sname1 --resource-group rgname
```

Jegyezze fel a webhook URL-CÍMÉT. Szüksége lesz rá a következő szakaszban.
`https://<publishingusername>:<publishingpwd>@<sitename>.scm.azurewebsites.net/docker/hook`.

Szerezheti be a `publishingusername` és `publishingpwd` úgy, hogy letölti a webalkalmazás közzététele a profilt az Azure portal használatával.

![Képernyőkép a webhook 2 hozzáadása](./media/app-service-webapp-service-linux-ci-cd/step3-3.png)

## <a name="add-a-webhook"></a>Egy webhook hozzáadása

Hozzáadja a webhookot, ezek az útmutatók kövesse:

- [Az Azure Container Registry](../../container-registry/container-registry-webhook.md) a webhook URL-cím használatával
- [A Docker hub Webhookok](https://docs.docker.com/docker-hub/webhooks/)

## <a name="next-steps"></a>További lépések

* [Linuxon futó Azure App Service bemutatása](./app-service-linux-intro.md)
* [Az Azure Container Registrybe](https://azure.microsoft.com/services/container-registry/)
* [.NET Core-webalkalmazás létrehozása Linuxon futó App Service-ben](quickstart-dotnetcore.md)
* [Ruby-webalkalmazás létrehozása Linuxon futó App Service-ben](quickstart-ruby.md)
* [Web App for Containers a Docker-/ Go-webalkalmazás üzembe helyezése](quickstart-docker-go.md)
* [Azure App Service Linuxon – gyakori kérdések](./app-service-linux-faq.md)
* [Web App for Containers Azure CLI-vel kezelése](./app-service-linux-cli.md)
