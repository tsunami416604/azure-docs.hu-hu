---
title: Kód üzembe helyezése ZIP-vagy WAR-fájllal
description: Megtudhatja, hogyan helyezheti üzembe az alkalmazást egy ZIP-fájllal (vagy Java-fejlesztőknek szánt WAR-fájllal) való Azure App Service.
ms.topic: article
ms.date: 08/12/2019
ms.reviewer: sisirap
ms.custom: seodec18
ms.openlocfilehash: 3569c6a066b09daa0c24975b9de840a844b6ba2c
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2019
ms.locfileid: "74670228"
---
# <a name="deploy-your-app-to-azure-app-service-with-a-zip-or-war-file"></a>Az alkalmazás üzembe helyezése egy ZIP-vagy WAR-fájllal való Azure App Service

Ebből a cikkből megtudhatja, hogyan helyezhet üzembe a webalkalmazást egy ZIP-fájl vagy háborús fájl használatával [Azure app Service](overview.md). 

Ez a ZIP-fájl központi telepítése ugyanazt a kudu szolgáltatást használja, amely a folyamatos integráció-alapú központi telepítéseket is kihasználja. A kudu a következő funkciókat támogatja a ZIP-fájlok üzembe helyezéséhez: 

- A korábbi telepítésből törölt fájlok törlése.
- Az alapértelmezett felépítési folyamat bekapcsolásának lehetősége, amely magában foglalja a csomagok visszaállítását.
- [Központi telepítés testreszabása](https://github.com/projectkudu/kudu/wiki/Configurable-settings#repository-and-deployment-related-settings), beleértve az üzembe helyezési parancsfájlok futtatását is.  
- Üzembe helyezési naplók. 
- 2048 MB-os fájlméret-korlát.

További információ: kudu- [dokumentáció](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

A WAR-fájl központi telepítése a App Service futtatja a [War](https://wikipedia.org/wiki/WAR_(file_format)) -fájlt a Java-webalkalmazás futtatásához. Lásd: a [War-fájl üzembe helyezése](#deploy-war-file).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

A cikkben szereplő lépések végrehajtásához:

* [Hozzon létre egy App Service-alkalmazást](/azure/app-service/), vagy használjon egy másik oktatóanyaghoz létrehozott alkalmazást.

## <a name="create-a-project-zip-file"></a>ZIP-fájl létrehozása a projekthez

>[!NOTE]
> Ha letöltötte a fájlokat egy ZIP-fájlba, először bontsa ki a fájlokat. Ha például letöltött egy ZIP-fájlt a GitHubról, akkor nem telepítheti a fájlt. A GitHub további beágyazott könyvtárakat is felvehet, amelyek nem működnek App Service. 
>

A helyi terminál ablakban navigáljon az alkalmazás-projekt gyökérkönyvtárához. 

Ennek a könyvtárnak tartalmaznia kell a webalkalmazáshoz tartozó bejegyzést, például az _index. html_, az _index. php_és az _app. js_fájlt. Olyan csomagkezelő fájlokat is tartalmazhat, mint például a _Project. JSON_, a _Composer. JSON_, a _Package. JSON_, a _Bower. JSON_és a _követelmények. txt_.

Készítsen ZIP-archívumot a projekt minden eleméről. A következő parancs a terminál alapértelmezett eszközét használja:

```
# Bash
zip -r <file-name>.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath <file-name>.zip
``` 

[!INCLUDE [Deploy ZIP file](../../includes/app-service-web-deploy-zip.md)]
A fenti végpont jelenleg nem működik Linux App Services esetén. Ehelyett használjon FTP-t vagy a [zip üzembe helyezési API](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-faq#continuous-integration-and-deployment) -t.

## <a name="deploy-zip-file-with-azure-cli"></a>ZIP-fájl üzembe helyezése az Azure CLI-vel

Győződjön meg arról, hogy az Azure CLI verziója 2.0.21 vagy újabb. Ha szeretné megtekinteni, hogy melyik verziót futtatja, futtassa `az --version` parancsot a terminál ablakban.

Telepítse a feltöltött ZIP-fájlt a webalkalmazásba az az [WebApp Deployment Source config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip) parancs használatával.  

A következő példa telepíti a feltöltött ZIP-fájlt. Ha az Azure CLI helyi telepítését használja, adja meg `--src`helyi ZIP-fájljának elérési útját.

```azurecli-interactive
az webapp deployment source config-zip --resource-group myResourceGroup --name <app_name> --src clouddrive/<filename>.zip
```

Ez a parancs üzembe helyezi a ZIP-fájlban szereplő fájlokat és könyvtárakat az alapértelmezett App Service-alkalmazásmappában (`\home\site\wwwroot`), majd újraindítja az alkalmazást.

Alapértelmezés szerint az üzembe helyezési motor feltételezi, hogy egy ZIP-fájl készen áll a futtatásra, és nem futtat Build automationt. Ha ugyanazt a Build-automatizálást szeretné engedélyezni, mint a [git-telepítésben](deploy-local-git.md), akkor a következő parancs futtatásával állítsa be a `SCM_DO_BUILD_DURING_DEPLOYMENT` alkalmazás beállítását a [Cloud Shellban](https://shell.azure.com):

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings SCM_DO_BUILD_DURING_DEPLOYMENT=true
```



További információ: kudu- [dokumentáció](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url).

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]  

## <a name="deploy-war-file"></a>WAR-fájl üzembe helyezése

Ha a WAR-fájlt App Servicere szeretné telepíteni, küldjön egy POST-kérést `https://<app_name>.scm.azurewebsites.net/api/wardeploy`. A POST kérelem üzenettörzsének tartalmaznia kell a .war fájlt. Az alkalmazás üzembehelyezési hitelesítő adatai a kérelemben alapszintű HTTP-hitelesítéssel vannak megadva.

Az egyszerű HTTP-hitelesítéshez szükség van a App Service központi telepítési hitelesítő adataira. A központi telepítési hitelesítő adatok beállításával kapcsolatban lásd: [felhasználói szintű hitelesítő adatok beállítása és alaphelyzetbe állítása](deploy-configure-credentials.md#userscope).

### <a name="with-curl"></a>A cURLtal

A következő példa a cURL eszközt használja egy. War fájl üzembe helyezéséhez. Cserélje le a helyőrzőket `<username>`, `<war-file-path>`és `<app-name>`. Ha a cURL rákérdez, írja be a jelszót.

```bash
curl -X POST -u <username> --data-binary @"<war-file-path>" https://<app_name>.scm.azurewebsites.net/api/wardeploy
```

### <a name="with-powershell"></a>A PowerShell-lel

A következő példa a [publish-AzWebapp](/powershell/module/az.websites/publish-azwebapp) feltölti a. War fájlt. Cserélje le a helyőrzőket `<group-name>`, `<app-name>`és `<war-file-path>`.

```powershell
Publish-AzWebapp -ResourceGroupName <group-name> -Name <app-name> -ArchivePath <war-file-path>
```

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>Következő lépések

A speciális üzembe helyezési forgatókönyvek esetében próbálja meg [üzembe helyezni az Azure-ban a git használatával](deploy-local-git.md). Az Azure-hoz készült git-alapú üzembe helyezés lehetővé teszi a verziókövetés, a csomagok visszaállítása, az MSBuild és egyebek használatát.

## <a name="more-resources"></a>További források

* [Kudu: telepítés zip-fájlból](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)
* [Központi telepítési hitelesítő adatok Azure App Service](deploy-ftp.md)
