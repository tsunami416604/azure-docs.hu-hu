---
title: "Telepítse az alkalmazást az Azure App Service egy ZIP- vagy háborús fájllal |} Microsoft Docs"
description: "Megtudhatja, hogyan telepítse az alkalmazást az Azure App Service egy ZIP-fájlt (vagy a Java-fejlesztők számára a WAR-fájl)."
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
ms.date: 03/07/2018
ms.author: cephalin;sisirap
ms.openlocfilehash: 41fb529f6b4ae923f2920919306324c86a2baa45
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2018
---
# <a name="deploy-your-app-to-azure-app-service-with-a-zip-or-war-file"></a>Telepítse az alkalmazást az Azure App Service egy ZIP- vagy háborús fájllal

Ez a cikk bemutatja, hogyan egy ZIP-fájl vagy a WAR-fájl használatával telepíti a webalkalmazás [Azure App Service](app-service-web-overview.md). 

A ZIP-fájl központi telepítéshez használja ugyanezt a Kudu szolgáltatást, hogy powers folyamatos integráció-alapú telepítések. A kudu ZIP fájlt központi telepítését támogatja a következő funkciókat: 

- Egy korábbi telepítésből maradtak fájlok törlése.
- A beállítás az alapértelmezett felépítési folyamat, köztük a csomag visszaállítási bekapcsolása.
- [Telepítés testreszabása](https://github.com/projectkudu/kudu/wiki/Configurable-settings#repository-and-deployment-related-settings), többek között a következőket üzembe helyezési parancsfájlok futtatása.  
- Központi telepítés naplófájljai. 

További információkért lásd: [Kudu dokumentáció](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

A WAR-fájl központi telepítési telepíti a [WAR](https://wikipedia.org/wiki/WAR_(file_format)) futtatásához a Java-webalkalmazás az App Service-fájlt. Lásd: [telepítése WAR-fájlt](#deploy-war-file).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Ebben a cikkben a lépések elvégzéséhez:

* [Hozzon létre egy App Service-alkalmazást](/azure/app-service/), vagy használjon egy másik oktatóanyaghoz létrehozott alkalmazást.

## <a name="create-a-project-zip-file"></a>ZIP-fájl létrehozása a projekthez

>[!NOTE]
> Ha letöltötte a fájlokat egy ZIP-fájlban szereplő, először bontsa ki a fájlokat. Például ha egy ZIP-fájl le a Githubról, nem telepíthet központilag a fájlt,-van. GitHub további beágyazott könyvtárak, amelyek nem működnek az App Service hozzáadja. 
>

A helyi terminálablakot keresse meg az alkalmazás projekt gyökérkönyvtárában. 

Ez a könyvtár fájlnak szerepelnie kell a bejegyzés a webalkalmazáshoz, például a _index.html_, _index.php_, és _app.js_. Például a felügyeleti csomagfájlok tartalmazhat _project.json_, _composer.json_, _package.json_, _bower.json_, és _requirements.txt_.

Készítsen ZIP-archívumot a projekt minden eleméről. A következő parancs a terminál alapértelmezett eszközét használja:

```
# Bash
zip -r <file-name>.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath <file-name>.zip
``` 

[!INCLUDE [Deploy ZIP file](../../includes/app-service-web-deploy-zip.md)]

## <a name="deploy-zip-file-with-azure-cli"></a>ZIP-fájl az Azure parancssori felület telepítése

Győződjön meg arról, hogy az Azure CLI verziója 2.0.21 vagy újabb. Melyik típusát, futtatása `az --version` a terminálablakot parancsot.

A webalkalmazás a feltöltött ZIP-fájl segítségével telepítheti a [az webalkalmazás központi telepítési forrás config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az_webapp_deployment_source_config_zip) parancsot.  

A következő példa telepíti a feltöltött ZIP-fájlban. Egy helyi telepítését teszi Azure CLI használata esetén adja meg a helyi, a ZIP-fájl elérési útja `--src`.   

```azurecli-interactive
az webapp deployment source config-zip --resource-group myResouceGroup --name <app_name> --src clouddrive/<filename>.zip
```

Ez a parancs üzembe helyezi a ZIP-fájlban szereplő fájlokat és könyvtárakat az alapértelmezett App Service-alkalmazásmappában (`\home\site\wwwroot`), majd újraindítja az alkalmazást. Ha további egyéni kiépítési folyamatok vannak megadva, azokat is futtatja. További információkért lásd: [Kudu dokumentáció](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]  

## <a name="deploy-war-file"></a>WAR-fájl központi telepítése

Az App Service a WAR-fájl telepítéséhez https://<app_name>.scm.azurewebsites.net/api/wardeploy egy POST kérést küld. A POST-kérelmet tartalmaznia kell a .war fájlt, az üzenet törzsében. Az üzembe helyezési hitelesítő adatok beállítása az alkalmazáshoz szerepelnek a kérelem HTTP BASIC hitelesítés használatával. 

A HTTP BASIC hitelesítés van szüksége az App Service üzembe helyezési hitelesítő adatokat. Az üzembe helyezési hitelesítő adatok beállításával kapcsolatos információk: [beállítása és a felhasználói szintű hitelesítő adatok alaphelyzetbe állítása](app-service-deployment-credentials.md#userscope).

### <a name="with-curl"></a>A cURL

Az alábbi példában a cURL eszköz egy .war fájl központi telepítése. Cserélje le a helyőrzőket `<username>`, `<war_file_path>`, és `<app_name>`. Amikor a cURL kéri, írja be a jelszót.

```bash
curl -X POST -u <username> --data-binary @"<war_file_path>" https://<app_name>.scm.azurewebsites.net/api/wardeploy
```

### <a name="with-powershell"></a>A PowerShell-lel

Az alábbi példában [Invoke-RestMethod](/powershell/module/microsoft.powershell.utility/invoke-restmethod) .war fájlt tartalmazó kérést küldeni. Cserélje le a helyőrzőket `<deployment_user>`, `<deployment_password>`, `<zip_file_path>`, és `<app_name>`.

```PowerShell
$username = "<deployment_user>"
$password = "<deployment_password>"
$filePath = "<war_file_path>"
$apiUrl = "https://<app_name>.scm.azurewebsites.net/api/wardeploy"
$base64AuthInfo = [Convert]::ToBase64String([Text.Encoding]::ASCII.GetBytes(("{0}:{1}" -f $username, $password)))
Invoke-RestMethod -Uri $apiUrl -Headers @{Authorization=("Basic {0}" -f $base64AuthInfo)} -Method POST -InFile $filePath -ContentType "multipart/form-data"
```

## <a name="next-steps"></a>További lépések

Összetettebb központi telepítési forgatókönyve esetén próbálja [üzembe helyezése az Azure git](app-service-deploy-local-git.md). Az Azure Git-alapú telepítést lehetővé teszi a verziókövetés, a csomag visszaállítás, az MSBuild és több.

## <a name="more-resources"></a>További erőforrások

* [Kudu: A zip-fájlból történő telepítése](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)
* [Az Azure App Service üzembe helyezési hitelesítő adatok](app-service-deploy-ftp.md)
