---
title: "Telepítse az alkalmazást az Azure App Service egy ZIP-fájllal |} Microsoft Docs"
description: "Megtudhatja, hogyan telepítse az alkalmazást az Azure App Service egy ZIP-fájllal."
services: app-service
documentationcenter: 
author: cephalin
manager: cfowler
editor: 
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: cephalin;sisirap
ms.openlocfilehash: 9838f0810f4827df3eb4f9407d4d4fbc1ad0ff4d
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2017
---
# <a name="deploy-your-app-to-azure-app-service-with-a-zip-file"></a>Telepítse az alkalmazást az Azure App Service egy ZIP-fájllal

Ez a cikk bemutatja, hogyan telepítésére való használatával egy ZIP-fájl webalkalmazás [Azure App Service](app-service-web-overview.md). 

A ZIP-fájl központi telepítéshez használja ugyanezt a Kudu szolgáltatást, hogy powers folyamatos integráció-alapú telepítések. A kudu ZIP fájlt központi telepítését támogatja a következő funkciókat: 

- Egy korábbi telepítésből csatlakoztatási kísérletéből fájlok törlése.
- A beállítás az alapértelmezett felépítési folyamat, köztük a csomag visszaállítási bekapcsolása.
- [Telepítés testreszabása](https://github.com/projectkudu/kudu/wiki/Configurable-settings#repository-and-deployment-related-settings), többek között a következőket üzembe helyezési parancsfájlok futtatása.  
- Központi telepítés naplófájljai. 

További információkért lásd: [Kudu dokumentáció](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

## <a name="prerequisites"></a>Előfeltételek

Ebben a cikkben a lépések elvégzéséhez:

* [App Service-alkalmazás létrehozása](/azure/app-service/), vagy egy másik az oktatóanyaghoz létrehozott alkalmazást használni.

## <a name="create-a-project-zip-file"></a>A projekt ZIP-fájl létrehozása

>[!NOTE]
> Ha letöltötte a fájlokat egy ZIP-fájlban szereplő, először bontsa ki a fájlokat. Például ha egy ZIP-fájl le a Githubról, nem telepíthet központilag a fájlt,-van. GitHub további beágyazott könyvtárak, amelyek nem működnek az App Service hozzáadja. 
>

A helyi terminálablakot keresse meg az alkalmazás projekt gyökérkönyvtárában. 

Ez a könyvtár fájlnak szerepelnie kell a bejegyzés a webalkalmazáshoz, például a _index.html_, _index.php_, és _app.js_. Például a felügyeleti csomagfájlok tartalmazhat _project.json_, _composer.json_, _package.json_, _bower.json_, és _requirements.txt_.

Hozzon létre minden, a ZIP-archívum létrehozása a projekthez. A következő parancsot a terminálon alapértelmezett eszközt használja:

```
# Bash
zip -r <file-name>.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath <file-name>.zip
``` 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="upload-zip-file-to-cloud-shell"></a>Felhő rendszerhéj ZIP-fájl feltöltése

Ha úgy dönt, hogy az Azure parancssori felület helyett a helyi terminál-ről futtatva, kihagyhatja ezt a lépést.

A ZIP-fájl feltöltése a felhőalapú rendszerhéj hajtsa végre az utasításokat itt. 

[!INCLUDE [app-service-web-upload-zip.md](../../includes/app-service-web-upload-zip-no-h.md)]

További információkért lásd: [megőrizni a fájlok az Azure felhőalapú Shell](../cloud-shell/persisting-shell-storage.md).

## <a name="deploy-zip-file"></a>ZIP-fájl telepítése

A webalkalmazás a feltöltött ZIP-fájl segítségével telepítheti a [az webalkalmazás központi telepítési forrás config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az_webapp_deployment_source_config_zip) parancsot. Ha úgy dönt, hogy nem használja a felhő rendszerhéj, ellenőrizze az Azure CLI verziója 2.0.21 vagy újabb. Melyik típusát, futtatása `az --version` parancsot a helyi terminálablakot. 

A következő példa telepíti a feltöltött ZIP-fájlban. Egy helyi telepítését teszi Azure CLI használata esetén adja meg a helyi, a ZIP-fájl elérési útja `--src`.   

```azurecli-interactive
az webapp deployment source config-zip --resource-group myResouceGroup --name <app_name> --src clouddrive/<filename>.zip
```

Ez a parancs telepíti a fájlok és könyvtárak innen: a ZIP fájlt az alapértelmezett App Service alkalmazás mappába (`\home\site\wwwroot`) és az alkalmazás újraindul. Bármely további egyéni felépítési folyamat úgy van beállítva, ha fut, valamint. További információkért lásd: [Kudu dokumentáció](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

Ez az alkalmazás központi telepítésének listájának megtekintése a REST API-k (lásd a következő szakaszt) kell használnia. 

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]  

## <a name="next-steps"></a>Következő lépések

Összetettebb központi telepítési forgatókönyve esetén próbálja [üzembe helyezése az Azure git](app-service-deploy-local-git.md). Az Azure Git-alapú telepítést lehetővé teszi a verziókövetés, a csomag visszaállítás, az MSBuild és több.

## <a name="more-resources"></a>További források

* [Kudu: A zip-fájlból történő telepítése](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)
* [Az Azure App Service üzembe helyezési hitelesítő adatok](app-service-deploy-ftp.md)
