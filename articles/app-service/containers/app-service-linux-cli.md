---
title: Webalkalmazás az Azure CLI 2.0 használatával tárolókat kezelése |} Microsoft Docs
description: Webalkalmazás az Azure parancssori felület használatával tárolókat kezelni.
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
ms.openlocfilehash: 54c979313a6ffa43008aa9870332b92d2b2f182a
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/10/2017
ms.locfileid: "24105389"
---
# <a name="manage-web-app-for-containers-using-azure-cli"></a>Webalkalmazás az Azure parancssori felület használatával tárolók kezelése

Ez a cikk a parancsokkal is létrehozását és kezelését egy webalkalmazást az Azure CLI 2.0 használatával tárolókat.
Megkezdheti az új verzió a CLI két módon:

* [Azure CLI 2.0 telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli) a számítógépen.
* Használatával [Azure felhőben rendszerhéj (előzetes verzió)](../../cloud-shell/overview.md)

## <a name="create-a-linux-app-service-plan"></a>A Linux App Service-csomag létrehozása

A Linux App Service-csomag létrehozásához használhatja a következő parancsot:

```azurecli-interactive
az appservice plan create -n appname -g rgname --is-linux -l "South Central US" --sku S1 --number-of-workers 1
```

## <a name="create-a-custom-docker-container-web-app"></a>Hozzon létre egy egyéni Docker-tároló webalkalmazás

Hozzon létre egy webalkalmazást, és konfigurálja úgy, hogy egy egyéni Docker-tároló futtassa, használhatja a következő parancsot:

```azurecli-interactive
az webapp create -n sname -g rgname -p pname -i elnably/dockerimagetest
```

## <a name="activate-the-docker-container-logging"></a>A Docker-tároló naplózási aktiválása

A Docker-tároló naplózási aktiválásához használhatja az alábbi parancsot:

```azurecli-interactive
az webapp log config -n sname -g rgname --web-server-logging filesystem
```

## <a name="change-the-custom-docker-container-for-an-existing-web-app-for-containers-app"></a>Az egyéni Docker-tároló egy már meglévő webalkalmazás tárolók alkalmazás módosítása

Az aktuális Docker kép új lemezképet egy korábban létrehozott alkalmazás módosításához használhatja a következő parancsot:

```azurecli-interactive
az webapp config container set -n sname -g rgname -c apurvajo/mariohtml5
```

## <a name="using-docker-images-from-a-private-registry"></a>A titkos beállításjegyzékből Docker lemezképek használatával

Beállíthatja, hogy az alkalmazás képeket használandó titkos beállításjegyzékből. Meg kell adnia az URL-címet a beállításjegyzék, felhasználónevét és jelszavát. Ez a érhető el a következő parancsot:

```azurecli-interactive
az webapp config container set -n sname1 -g rgname -c <container name> -r <server url> -u <username> -p <password>
```

## <a name="enable-continuous-deployments-for-custom-docker-images"></a>Folyamatos egyéni Docker-lemezképek központi telepítésének engedélyezése

A következő paranccsal engedélyezheti a CD-funkciókat, és a webhook url-cím beszerzése. Az URL-cím akkor DockerHub vagy Azure tároló beállításjegyzék repók konfigurálásához használható.

```azurecli-interactive
az webapp deployment container config -n sname -g rgname -e true
```

## <a name="create-a-web-app-for-containers-app-using-one-of-our-built-in-runtime-frameworks"></a>A beépített futásidejű keretrendszerek egyikével tárolók alkalmazás-webalkalmazás létrehozása

A PHP 5.6 webalkalmazás létrehozása tárolók alkalmazás, amely, a következő parancsot használhatja.

```azurecli-interactive
az webapp create -n sname -g rgname -p pname -r "php|5.6"
```

## <a name="change-framework-version-for-an-existing-web-app-for-containers-app"></a>Módosítja a keretrendszer verzióját egy már meglévő webalkalmazás tárolók alkalmazás

Ha módosítani szeretné egy korábban létrehozott alkalmazást, a jelenlegi keretrendszer verziószáma a Node.js 6.11, használhatja a következő parancsot:

```azurecli-interactive
az webapp config set -n sname -g rgname --linux-fx-version "node|6.11"
```

## <a name="set-up-git-deployments-for-your-web-app"></a>A webalkalmazás Git-telepítésekhez beállítása

Az alkalmazás Git-telepítésekhez beállításához használhatja a következő parancsot:

```azurecli-interactive
az webapp deployment source config -n sname -g rgname --repo-url <gitrepo url> --branch <branch>
```

## <a name="next-steps"></a>Következő lépések

* [Mi az Azure App Service Linux?](app-service-linux-intro.md)
* [Az Azure CLI 2.0 telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli)
* [Azure-felhőbe rendszerhéj (előzetes verzió)](../../cloud-shell/overview.md)
* [Átmeneti környezet az Azure App Service beállítása](../../app-service/web-sites-staged-publishing.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [A tárolók a webes alkalmazás folyamatos üzembe helyezés](app-service-linux-ci-cd.md)
