---
title: A kód egy ZIP- vagy WAR-fájllal – az Azure App Service üzembe helyezése |} A Microsoft Docs
description: Megtudhatja, hogyan helyezze üzembe az alkalmazást az Azure App Service egy ZIP-fájlba (vagy a Java-fejlesztőknek készült WAR-fájl).
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2018
ms.author: cephalin;sisirap
ms.custom: seodec18
ms.openlocfilehash: 437f1aa631e058ee62bb3c08d2ad654c3d0666f3
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2018
ms.locfileid: "53259086"
---
# <a name="deploy-your-app-to-azure-app-service-with-a-zip-or-war-file"></a>Alkalmazás üzembe helyezése az Azure App Service egy ZIP- vagy WAR-fájlt

Ez a cikk bemutatja, hogyan használhatja egy ZIP-fájl- vagy WAR-fájlt a webalkalmazás üzembe helyezéséhez [Azure App Service](app-service-web-overview.md). 

A ZIP-fájl központi telepítés használja ugyanazt a Kudu szolgáltatást adott powers folyamatos integráció-alapú telepítések. Kudu ZIP fájl központi telepítés az alábbi funkciókat támogatja: 

- Egy korábbi telepítésből maradtak-fájlok törlése.
- A beállítás az alapértelmezett buildelési folyamat, amely tartalmazza a csomag-visszaállítás engedélyezése.
- [Telepítés testreszabása](https://github.com/projectkudu/kudu/wiki/Configurable-settings#repository-and-deployment-related-settings), többek között üzembe helyezési parancsfájlok futtatásához.  
- Telepítési naplók. 

További információkért lásd: [Kudu dokumentáció](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

A WAR-fájl központi telepítési üzembe helyezi a [WAR](https://wikipedia.org/wiki/WAR_(file_format)) fájl futtatásához a Java-webalkalmazás App Service-ben. Lásd: [üzembe helyezése WAR-fájl](#deploy-war-file).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

A jelen cikkben ismertetett lépések végrehajtásához:

* [Hozzon létre egy App Service-alkalmazást](/azure/app-service/), vagy használjon egy másik oktatóanyaghoz létrehozott alkalmazást.

## <a name="create-a-project-zip-file"></a>ZIP-fájl létrehozása a projekthez

>[!NOTE]
> Ha letöltötte a fájlokat egy ZIP-fájlban, először bontsa ki a fájlokat. Például, ha a letöltött ZIP-fájlt a Githubról, nem telepíthet központilag a fájlt,-van. GitHub ad a további beágyazott könyvtárak, amelyek az App Service szolgáltatással nem működik. 
>

Egy helyi terminálablakban keresse meg az alkalmazásprojektet gyökérkönyvtárában. 

Ez a könyvtár tartalmaznia kell a webalkalmazás fájlja például _index.html_, _index.php_, és _app.js_. Például a felügyeleti csomagfájlok tartalmazhat _project.json_, _composer.json_, _package.json_, _bower.json_, és _requirements.txt_.

Készítsen ZIP-archívumot a projekt minden eleméről. A következő parancs a terminál alapértelmezett eszközét használja:

```
# Bash
zip -r <file-name>.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath <file-name>.zip
``` 

[!INCLUDE [Deploy ZIP file](../../includes/app-service-web-deploy-zip.md)]

## <a name="deploy-zip-file-with-azure-cli"></a>Az Azure CLI-vel ZIP-fájl telepítése

Győződjön meg arról, hogy az Azure CLI-verzió 2.0.21-es vagy újabb verziója. Melyik verziót, futtatnia kell, hogy `az --version` paranccsal a terminálablakban.

A feltöltött ZIP-fájlt a webalkalmazás segítségével telepítheti a [az webapp deployment source config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip) parancsot.  

A következő példa telepíti a feltöltött ZIP-fájlt. Az Azure parancssori felület helyi telepítését használja, amikor adja meg a helyi, a ZIP-fájl elérési útját `--src`.   

```azurecli-interactive
az webapp deployment source config-zip --resource-group myResourceGroup --name <app_name> --src clouddrive/<filename>.zip
```

Ez a parancs üzembe helyezi a ZIP-fájlban szereplő fájlokat és könyvtárakat az alapértelmezett App Service-alkalmazásmappában (`\home\site\wwwroot`), majd újraindítja az alkalmazást. Ha további egyéni kiépítési folyamatok vannak megadva, azokat is futtatja. További információkért lásd: [Kudu dokumentáció](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]  

## <a name="deploy-war-file"></a>WAR-fájl telepítése

A WAR-fájlt az App Service üzembe helyezéséhez https://<app_name>.scm.azurewebsites.net/api/wardeploy egy POST kérést küld. A POST kérelem üzenettörzsének tartalmaznia kell a .war fájlt. Az alkalmazás üzembehelyezési hitelesítő adatai a kérelemben alapszintű HTTP-hitelesítéssel vannak megadva. 

Az ALAPSZINTŰ HTTP-hitelesítést az App Service-üzembehelyezési hitelesítő adatokat kell. Az üzembe helyezési hitelesítő adatok beállítása, olvassa el [beállítása és a felhasználói szintű hitelesítő adatok alaphelyzetbe állítása](app-service-deployment-credentials.md#userscope).

### <a name="with-curl"></a>A curl használatával

Az alábbi példa a cURL eszköz üzembe .war-fájlt használ. Cserélje le a zárójelben `<username>`, `<war_file_path>`, és `<app_name>`. Amikor a cURL kéri, írja be a jelszót.

```bash
curl -X POST -u <username> --data-binary @"<war_file_path>" https://<app_name>.scm.azurewebsites.net/api/wardeploy
```

### <a name="with-powershell"></a>A PowerShell-lel

Az alábbi példában [Invoke-RestMethod](/powershell/module/microsoft.powershell.utility/invoke-restmethod) küldeni egy kérelmet, amely tartalmazza a .war-fájlt. Cserélje le a zárójelben `<deployment_user>`, `<deployment_password>`, `<zip_file_path>`, és `<app_name>`.

```PowerShell
$username = "<deployment_user>"
$password = "<deployment_password>"
$filePath = "<war_file_path>"
$apiUrl = "https://<app_name>.scm.azurewebsites.net/api/wardeploy"
$base64AuthInfo = [Convert]::ToBase64String([Text.Encoding]::ASCII.GetBytes(("{0}:{1}" -f $username, $password)))
Invoke-RestMethod -Uri $apiUrl -Headers @{Authorization=("Basic {0}" -f $base64AuthInfo)} -Method POST -InFile $filePath -ContentType "multipart/form-data"
```

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>További lépések

Összetettebb központi telepítési forgatókönyvek esetén próbálja meg [üzembe helyezése az Azure-ban a Git](app-service-deploy-local-git.md). Git-alapú üzembe helyezés az Azure lehetővé teszi a verziókezeléshez, a csomag-visszaállítás, az MSBuild és egyéb.

## <a name="more-resources"></a>További erőforrások

* [Kudu: Egy zip-fájl üzembe helyezése](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)
* [Az Azure App Service üzembe helyezési hitelesítő adatok](app-service-deploy-ftp.md)
