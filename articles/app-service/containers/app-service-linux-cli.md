---
title: Egyéni Linux-tárolók kezelése CLI-vel
description: Ismerje meg, hogyan kezelheti az egyéni Linux-tárolókat az Azure App Service-ben a parancssorból. Automatizálhatja az alkalmazások kiépítését vagy karbantartását.
keywords: Azure app szolgáltatás, web app, cli, linux, oss
ms.topic: article
ms.date: 08/22/2017
ms.custom: seodec18
ms.openlocfilehash: 5ca5322467402af710df68c82d747f8f8d65e142
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255924"
---
# <a name="manage-web-app-for-containers-using-azure-cli"></a>A Web App for Containers felügyelete az Azure CLI-vel

Ebben a cikkben található parancsok használatával létrehozhat és kezelhet egy webalkalmazást a tárolókhoz az Azure CLI használatával.
A CLI új verziójának használatát kétféleképpen kezdheti el:

* [Az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli) a számítógépre.
* Az [Azure Cloud Shell használata (előzetes verzió)](../../cloud-shell/overview.md)

## <a name="create-a-linux-app-service-plan"></a>Linux-alkalmazásszolgáltatási csomag létrehozása

Linux app service-csomag létrehozásához a következő parancsot használhatja:

```azurecli-interactive
az appservice plan create -n appname -g rgname --is-linux -l "South Central US" --sku S1 --number-of-workers 1
```

## <a name="create-a-custom-docker-container-web-app"></a>Egyéni Docker-tároló webalkalmazás létrehozása

Webalkalmazás létrehozásához és egyéni Docker-tároló futtatásához konfigurálásához használja a következő parancsot:

```azurecli-interactive
az webapp create -n sname -g rgname -p pname -i elnably/dockerimagetest
```

## <a name="activate-the-docker-container-logging"></a>A Docker-tároló naplózásának aktiválása

A Docker-tároló naplózásának aktiválásához használja a következő parancsot:

```azurecli-interactive
az webapp log config -n sname -g rgname --web-server-logging filesystem
```

## <a name="change-the-custom-docker-container-for-an-existing-web-app-for-containers-app"></a>Egy meglévő WebApp tárolókhoz való egyéni Docker-tárolójának módosítása

Egy korábban létrehozott alkalmazás módosításához az aktuális Docker-lemezképről egy új lemezképre a következő parancsot használhatja:

```azurecli-interactive
az webapp config container set -n sname -g rgname -c apurvajo/mariohtml5
```

## <a name="using-docker-images-from-a-private-registry"></a>Docker-lemezképek használata privát rendszerleíró adatbázisból

Beállíthatja, hogy az alkalmazás egy privát beállításjegyzékből származó képeket használjon. Meg kell adnia a rendszerleíró adatbázis url-címét, a felhasználónevet és a jelszót. Ez a következő paranccsal érhető el:

```azurecli-interactive
az webapp config container set -n sname1 -g rgname -c <container name> -r <server url> -u <username> -p <password>
```

## <a name="enable-continuous-deployments-for-custom-docker-images"></a>Folyamatos üzembe helyezésengedélyezése egyéni Docker-lemezképekhez

A következő paranccsal engedélyezheti a CD-funkciót, és beszerezheti a webhook URL-címét. Ez az URL-cím a DockerHub vagy az Azure Container Registry adatpos konfigurálására használható.

```azurecli-interactive
az webapp deployment container config -n sname -g rgname -e true
```

## <a name="create-a-web-app-for-containers-app-using-one-of-our-built-in-runtime-frameworks"></a>Hozzon létre egy Web App tárolókhoz alkalmazást az egyik beépített futásidejű keretrendszerünkkel

Php 5.6 Web App for Containers App létrehozásához használhatja a következő parancsot.

```azurecli-interactive
az webapp create -n sname -g rgname -p pname -r "php|5.6"
```

## <a name="change-framework-version-for-an-existing-web-app-for-containers-app"></a>Meglévő Webapp tárolókhoz való alkalmazás keretrendszerverziójának módosítása

Egy korábban létrehozott alkalmazás módosításához a jelenlegi keretrendszer-verzióról a Node.js 6.11-re a következő parancsot használhatja:

```azurecli-interactive
az webapp config set -n sname -g rgname --linux-fx-version "node|6.11"
```

## <a name="set-up-git-deployments-for-your-web-app"></a>Git-telepítések beállítása a Web Apphoz

Git-telepítések beállításához használja a következő parancsot:

```azurecli-interactive
az webapp deployment source config -n sname -g rgname --repo-url <gitrepo url> --branch <branch>
```

## <a name="next-steps"></a>További lépések

* [Mi az Azure App Service Linuxon?](app-service-linux-intro.md)
* [Telepítse az Azure CLI-t](https://docs.microsoft.com/cli/azure/install-azure-cli)
* [Azure Cloud Shell (előzetes verzió)](../../cloud-shell/overview.md)
* [Átmeneti környezetek beállítása az Azure App Service-ben](../../app-service/deploy-staging-slots.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Folyamatos telepítés a webalkalmazással tárolókhoz](app-service-linux-ci-cd.md)
