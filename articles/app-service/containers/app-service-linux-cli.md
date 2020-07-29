---
title: Egyéni Linux-tárolók kezelése a CLI-vel
description: Ismerje meg, hogyan kezelheti az egyéni Linux-tárolókat Azure App Service a parancssorból. Automatizálhatja az alkalmazások üzembe helyezését vagy karbantartását.
keywords: Azure app Service, webalkalmazás, CLI, Linux, OSS
ms.topic: article
ms.date: 08/22/2017
ms.custom: seodec18
ms.openlocfilehash: 5ca5322467402af710df68c82d747f8f8d65e142
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "78255924"
---
# <a name="manage-web-app-for-containers-using-azure-cli"></a>A Web App for Containers felügyelete az Azure CLI-vel

A cikkben szereplő parancsokkal létrehozhat és kezelhet egy Web App for Containers az Azure CLI használatával.
A parancssori felület új verzióját kétféleképpen használhatja:

* Az [Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli) a gépre.
* [Azure Cloud Shell használata (előzetes verzió)](../../cloud-shell/overview.md)

## <a name="create-a-linux-app-service-plan"></a>Linux App Service terv létrehozása

Linuxos App Service terv létrehozásához a következő parancsot használhatja:

```azurecli-interactive
az appservice plan create -n appname -g rgname --is-linux -l "South Central US" --sku S1 --number-of-workers 1
```

## <a name="create-a-custom-docker-container-web-app"></a>Egyéni Docker-tárolót tartalmazó Webalkalmazás létrehozása

Webalkalmazás létrehozásához és az egyéni Docker-tároló futtatásához való konfigurálásához a következő parancsot használhatja:

```azurecli-interactive
az webapp create -n sname -g rgname -p pname -i elnably/dockerimagetest
```

## <a name="activate-the-docker-container-logging"></a>A Docker-tároló naplózásának aktiválása

A Docker-tároló naplózásának aktiválásához a következő parancsot használhatja:

```azurecli-interactive
az webapp log config -n sname -g rgname --web-server-logging filesystem
```

## <a name="change-the-custom-docker-container-for-an-existing-web-app-for-containers-app"></a>Meglévő Web App for Containers-alkalmazás egyéni Docker-tárolójának módosítása

Egy korábban létrehozott alkalmazás módosításához az aktuális Docker-rendszerképből egy új képre használhatja a következő parancsot:

```azurecli-interactive
az webapp config container set -n sname -g rgname -c apurvajo/mariohtml5
```

## <a name="using-docker-images-from-a-private-registry"></a>Docker-rendszerképek használata privát beállításjegyzékből

Beállíthatja, hogy az alkalmazás egy privát beállításjegyzékből származó képeket használjon. Meg kell adnia a beállításjegyzék, a Felhasználónév és a jelszó URL-címét. Ezt a következő parancs használatával lehet megvalósítani:

```azurecli-interactive
az webapp config container set -n sname1 -g rgname -c <container name> -r <server url> -u <username> -p <password>
```

## <a name="enable-continuous-deployments-for-custom-docker-images"></a>Az egyéni Docker-rendszerképek folyamatos üzembe helyezésének engedélyezése

A következő parancs segítségével engedélyezheti a CD-funkciót, és lekérheti a webhook URL-címét. Ez az URL-cím konfigurálható a DockerHub vagy a Azure Container Registry repók konfigurálásához.

```azurecli-interactive
az webapp deployment container config -n sname -g rgname -e true
```

## <a name="create-a-web-app-for-containers-app-using-one-of-our-built-in-runtime-frameworks"></a>Hozzon létre egy Web App for Containers alkalmazást a beépített futtatókörnyezeti keretrendszerek egyikének használatával

Ha PHP 5,6 Web App for Containers alkalmazást szeretne létrehozni, akkor a következő parancsot használhatja.

```azurecli-interactive
az webapp create -n sname -g rgname -p pname -r "php|5.6"
```

## <a name="change-framework-version-for-an-existing-web-app-for-containers-app"></a>Keretrendszer verziójának módosítása meglévő Web App for Containers alkalmazáshoz

Egy korábban létrehozott alkalmazás módosításához a jelenlegi keretrendszer-verzióról Node.js 6,11-re a következő parancsot használhatja:

```azurecli-interactive
az webapp config set -n sname -g rgname --linux-fx-version "node|6.11"
```

## <a name="set-up-git-deployments-for-your-web-app"></a>Git-alapú központi telepítések beállítása a webalkalmazáshoz

Az alkalmazáshoz tartozó git-telepítések beállításához használja a következő parancsot:

```azurecli-interactive
az webapp deployment source config -n sname -g rgname --repo-url <gitrepo url> --branch <branch>
```

## <a name="next-steps"></a>További lépések

* [Mi a Linux Azure App Service?](app-service-linux-intro.md)
* [Telepítse az Azure CLI-t](https://docs.microsoft.com/cli/azure/install-azure-cli)
* [Azure Cloud Shell (előzetes verzió)](../../cloud-shell/overview.md)
* [Átmeneti környezetek beállítása az Azure App Service-ben](../../app-service/deploy-staging-slots.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Folyamatos üzembe helyezés a Web App for Containers](app-service-linux-ci-cd.md)
