---
title: Kód telepítése ZIP- vagy WAR-fájllal
description: Ismerje meg, hogyan telepítheti alkalmazását az Azure App Service-ben egy ZIP-fájllal (vagy egy WAR-fájl java fejlesztőknek).
ms.topic: article
ms.date: 08/12/2019
ms.reviewer: sisirap
ms.custom: seodec18
ms.openlocfilehash: 716f6813e37aec086a7d496e001fe2ca0f4aab57
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75945177"
---
# <a name="deploy-your-app-to-azure-app-service-with-a-zip-or-war-file"></a>Az alkalmazás üzembe helyezése az Azure App Service-ben ZIP- vagy WAR-fájllal

Ez a cikk bemutatja, hogyan telepítheti webalkalmazását az [Azure App Service-ben.](overview.md) 

Ez a ZIP-fájl központi telepítése ugyanazt a Kudu szolgáltatást használja, amely folyamatos integrációalapú telepítéseket hajt elő. A Kudu a következő funkciókat támogatja a ZIP-fájlok telepítéséhez: 

- Egy korábbi központi telepítésből megmaradt fájlok törlése.
- Lehetőség az alapértelmezett létrehozási folyamat bekapcsolására, amely magában foglalja a csomag-visszaállítást.
- A telepítés testreszabása, beleértve a központi telepítési parancsfájlok futtatását is.  
- Telepítési naplók. 
- A fájl mérethatára 2048 MB.

További információ: [Kudu dokumentáció](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

A WAR fájl telepítése telepíti a [WAR](https://wikipedia.org/wiki/WAR_(file_format)) fájlt az App Service-hez a Java webalkalmazás futtatásához. Lásd: [WAR-fájl telepítése](#deploy-war-file).

## <a name="prerequisites"></a>Előfeltételek

A cikkben leírt lépések végrehajtásához [hozzon létre egy App Service-alkalmazást](/azure/app-service/), vagy használjon egy másik oktatóanyaghoz létrehozott alkalmazást.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Create a project ZIP file](../../includes/app-service-web-deploy-zip-prepare.md)]

[!INCLUDE [Deploy ZIP file](../../includes/app-service-web-deploy-zip.md)]
A fenti végpont jelenleg nem működik a Linux App Services esetében. Fontolja meg az FTP vagy a [ZIP-telepítés API](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-faq#continuous-integration-and-deployment) helyett.

## <a name="deploy-zip-file-with-azure-cli"></a>ZIP-fájl üzembe helyezése az Azure CLI-vel

Telepítse a feltöltött ZIP-fájlt a webapp az [az webapp telepítési forrás config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip) parancs használatával.  

A következő példa telepíti a feltöltött ZIP-fájlt. Az Azure CLI helyi telepítésének használatakor adja meg `--src`a helyi ZIP-fájl elérési útját.

```azurecli-interactive
az webapp deployment source config-zip --resource-group <group-name> --name <app-name> --src clouddrive/<filename>.zip
```

Ez a parancs üzembe helyezi a ZIP-fájlban szereplő fájlokat és könyvtárakat az alapértelmezett App Service-alkalmazásmappában (`\home\site\wwwroot`), majd újraindítja az alkalmazást.

Alapértelmezés szerint a központi telepítési motor feltételezi, hogy a ZIP-fájl készen áll a futtatásra, ahogy van, és nem fut semmilyen build automatizálási. Ha ugyanazt a build-automatizálást szeretné engedélyezni, mint egy [Git-telepítésben,](deploy-local-git.md)állítsa be az `SCM_DO_BUILD_DURING_DEPLOYMENT` alkalmazásbeállítást a következő parancs futtatásával a Cloud [Shellben:](https://shell.azure.com)

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings SCM_DO_BUILD_DURING_DEPLOYMENT=true
```

További információ: [Kudu dokumentáció](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url).

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]  

## <a name="deploy-war-file"></a>WAR-fájl telepítése

Ha WAR-fájlt szeretne telepíteni az App `https://<app-name>.scm.azurewebsites.net/api/wardeploy`Service szolgáltatásba, küldjön postai kérelmet a rendszernek. A POST kérelem üzenettörzsének tartalmaznia kell a .war fájlt. Az alkalmazás üzembehelyezési hitelesítő adatai a kérelemben alapszintű HTTP-hitelesítéssel vannak megadva.

War `/api/wardeploy` fájlok telepítésekor mindig használja. Ez az API kibővíti a WAR fájlt, és elhelyezi a megosztott fájlmeghajtón. más központi API-k használata inkonzisztens viselkedést eredményezhet. 

A HTTP BASIC-hitelesítéshez szüksége van az App Service központi telepítési hitelesítő adataira. A telepítési hitelesítő adatok beállításáról a [Felhasználói szintű hitelesítő adatok beállítása és alaphelyzetbe állítása témakörben](deploy-configure-credentials.md#userscope)talál.

### <a name="with-curl"></a>CURL-lel

A következő példa a cURL eszközt használja egy .war fájl telepítéséhez. Cserélje ki `<username>`a `<war-file-path>`helyőrzőket , és `<app-name>`a. Amikor a cURL kéri, írja be a jelszót.

```bash
curl -X POST -u <username> --data-binary @"<war-file-path>" https://<app-name>.scm.azurewebsites.net/api/wardeploy
```

### <a name="with-powershell"></a>A PowerShell-lel

A következő példa a [Publish-AzWebapp](/powershell/module/az.websites/publish-azwebapp) segítségével feltölti a .war fájlt. Cserélje ki `<group-name>`a `<app-name>`helyőrzőket , és `<war-file-path>`a.

```powershell
Publish-AzWebapp -ResourceGroupName <group-name> -Name <app-name> -ArchivePath <war-file-path>
```

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>További lépések

A speciálisabb üzembe helyezési forgatókönyvek érdekében próbálja meg [telepíteni az Azure-ba a Git segítségével.](deploy-local-git.md) Git-alapú üzembe helyezés az Azure-ban lehetővé teszi a verziókövetést, a csomag-visszaállítást, az MSBuild-et és egyebeket.

## <a name="more-resources"></a>További erőforrások

* [Kudu: Üzembe helyezés zip fájlból](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)
* [Az Azure App Service telepítési hitelesítő adatai](deploy-ftp.md)
