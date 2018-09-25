---
title: Kezelheti a Web App for Containers Azure parancssori felületével |} A Microsoft Docs
description: Web App for Containers Azure CLI használatával kezelheti.
keywords: az Azure app service, webalkalmazás, cli, linux, oss
services: app-service
documentationCenter: ''
author: ahmedelnably
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2017
ms.author: aelnably
ms.openlocfilehash: 024ca5bcf9c1f82f07656691d6ef5358ca3d5111
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46998491"
---
# <a name="manage-web-app-for-containers-using-azure-cli"></a>Web App for Containers Azure CLI-vel kezelése

A parancsokkal hozhat létre és kezelhet egy Web App for containers szolgáltatásban az Azure CLI használatával tudja ebben a cikkben.
Két módon az új verziót a parancssori felület használatának megkezdéséhez:

* [Az Azure parancssori felület telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli) a gépen.
* Használatával [az Azure Cloud Shell-(előzetes verzió)](../../cloud-shell/overview.md)

## <a name="create-a-linux-app-service-plan"></a>Linux App Service-csomag létrehozása

Hozzon létre egy Linux App Service-csomagban, használhatja a következő parancsot:

```azurecli-interactive
az appservice plan create -n appname -g rgname --is-linux -l "South Central US" --sku S1 --number-of-workers 1
```

## <a name="create-a-custom-docker-container-web-app"></a>Egyéni Docker-tárolóban webes alkalmazás létrehozása

Hozzon létre egy webalkalmazást, és konfigurálja úgy, hogy egy egyéni Docker-tároló futtatása, használhatja a következő parancsot:

```azurecli-interactive
az webapp create -n sname -g rgname -p pname -i elnably/dockerimagetest
```

## <a name="activate-the-docker-container-logging"></a>Aktiválja a Docker-tároló naplózása

A Docker-tároló naplózása aktiválásához használhatja az alábbi parancsot:

```azurecli-interactive
az webapp log config -n sname -g rgname --web-server-logging filesystem
```

## <a name="change-the-custom-docker-container-for-an-existing-web-app-for-containers-app"></a>Az egyéni Docker-tároló módosíthatja egy meglévő Web Apps for Containers alkalmazáshoz

Ha módosítani szeretné egy korábban létrehozott alkalmazást, a jelenlegi Docker-rendszerképet egy új rendszerképet, használhatja a következő parancsot:

```azurecli-interactive
az webapp config container set -n sname -g rgname -c apurvajo/mariohtml5
```

## <a name="using-docker-images-from-a-private-registry"></a>Privát tárolójegyzékből származó Docker-rendszerképek használata

Az alkalmazások a privát tárolójegyzékből származó rendszerképek konfigurálhatja. Meg kell adnia az URL-címet a beállításjegyzék, a felhasználónév és a jelszavát. Ez a érhető el a következő paranccsal:

```azurecli-interactive
az webapp config container set -n sname1 -g rgname -c <container name> -r <server url> -u <username> -p <password>
```

## <a name="enable-continuous-deployments-for-custom-docker-images"></a>Egyéni Docker-rendszerképek folyamatos központi telepítésének engedélyezése

A következő paranccsal engedélyezheti a CD-funkciókat, és a webhook URL-cím lekéréséhez. Az URL-cím konfigurálása DockerHub vagy az Azure Container Registry-adattárakkal, használható.

```azurecli-interactive
az webapp deployment container config -n sname -g rgname -e true
```

## <a name="create-a-web-app-for-containers-app-using-one-of-our-built-in-runtime-frameworks"></a>Hozzon létre egy webalkalmazást a beépített modul keretrendszerek használatával Containers alkalmazáshoz

Webalkalmazás létrehozása a PHP 5.6-os a tárolók alkalmazás, amely, a következő parancsot használhatja.

```azurecli-interactive
az webapp create -n sname -g rgname -p pname -r "php|5.6"
```

## <a name="change-framework-version-for-an-existing-web-app-for-containers-app"></a>Keretrendszer verziója módosíthatja egy meglévő Web Apps for Containers alkalmazáshoz

Ha módosítani szeretné egy korábban létrehozott alkalmazást, a jelenlegi verzióra keretrendszer Node.js 6.11, használhatja a következő parancsot:

```azurecli-interactive
az webapp config set -n sname -g rgname --linux-fx-version "node|6.11"
```

## <a name="set-up-git-deployments-for-your-web-app"></a>Git-telepítésekhez a webalkalmazás beállítása

Az alkalmazás a Git üzemelő példányok beállításához használhatja a következő parancsot:

```azurecli-interactive
az webapp deployment source config -n sname -g rgname --repo-url <gitrepo url> --branch <branch>
```

## <a name="next-steps"></a>További lépések

* [Mi az Linuxon futó Azure App Service?](app-service-linux-intro.md)
* [Az Azure parancssori felület telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli)
* [Az Azure Cloud Shell-(előzetes verzió)](../../cloud-shell/overview.md)
* [Átmeneti környezetek beállítása az Azure App Service-ben](../../app-service/web-sites-staged-publishing.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Folyamatos üzembe helyezés a Web App for containers szolgáltatásban](app-service-linux-ci-cd.md)
