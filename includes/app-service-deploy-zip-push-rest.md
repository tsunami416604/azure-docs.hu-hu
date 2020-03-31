---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 08/12/2019
ms.author: cephalin
ms.openlocfilehash: 92e39f128e90ba83a919388e217f0edc86f81770
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75769666"
---
## <a name="deploy-zip-file-with-rest-apis"></a><a name="rest"></a>ZIP-fájl telepítése REST API-kkal 

A DEPLOYMENT [szolgáltatás REST API-k](https://github.com/projectkudu/kudu/wiki/REST-API) segítségével telepítheti a .zip fájlt az alkalmazásaz Azure-ban. Üzembe helyezéshez küldjön postai kérelmet a https://<app_name>.scm.azurewebsites.net/api/zipdeploy címre. A POST kérelemnek tartalmaznia kell a .zip fájlt az üzenet törzsében. Az alkalmazás üzembehelyezési hitelesítő adatai a kérelemben alapszintű HTTP-hitelesítéssel vannak megadva. További információt a [.zip push telepítési útmutatóban](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)talál. 

A HTTP BASIC-hitelesítéshez szüksége van az App Service központi telepítési hitelesítő adataira. A telepítési hitelesítő adatok beállításáról a [Felhasználói szintű hitelesítő adatok beállítása és alaphelyzetbe állítása témakörben](../articles/app-service/deploy-configure-credentials.md#userscope)talál.

### <a name="with-curl"></a>CURL-lel

A következő példa a cURL eszközt használja a .zip fájl telepítéséhez. Cserélje ki `<deployment_user>`a `<zip_file_path>`helyőrzőket , és `<app_name>`a. Amikor a cURL kéri, írja be a jelszót.

```bash
curl -X POST -u <deployment_user> --data-binary @"<zip_file_path>" https://<app_name>.scm.azurewebsites.net/api/zipdeploy
```

Ez a kérelem leküldéses telepítést indít el a feltöltött .zip fájlból. Tekintse át az aktuális és a `https://<app_name>.scm.azurewebsites.net/api/deployments` korábbi központi telepítések a végpont használatával, ahogy az a következő cURL-példában látható. Ismét cserélje `<app_name>` le az alkalmazás `<deployment_user>` nevét, és a felhasználói nevét a központi telepítési hitelesítő adatok.

```bash
curl -u <deployment_user> https://<app_name>.scm.azurewebsites.net/api/deployments
```

### <a name="with-powershell"></a>A PowerShell-lel

A következő példa a [Publish-AzWebapp](/powershell/module/az.websites/publish-azwebapp) segítségével tölti fel a .zip fájlt. Cserélje ki `<group-name>`a `<app-name>`helyőrzőket , és `<zip-file-path>`a.

```powershell
Publish-AzWebapp -ResourceGroupName <group-name> -Name <app-name> -ArchivePath <zip-file-path>
```

Ez a kérelem leküldéses telepítést indít el a feltöltött .zip fájlból. 

Az aktuális és a korábbi központi telepítések áttekintéséhez futtassa a következő parancsokat. Ismét cserélje `<deployment-user>`ki `<deployment-password>`a `<app-name>` , és helyőrzőket.

```bash
$username = "<deployment-user>"
$password = "<deployment-password>"
$apiUrl = "https://<app-name>.scm.azurewebsites.net/api/deployments"
$base64AuthInfo = [Convert]::ToBase64String([Text.Encoding]::ASCII.GetBytes(("{0}:{1}" -f $username, $password)))
$userAgent = "powershell/1.0"
Invoke-RestMethod -Uri $apiUrl -Headers @{Authorization=("Basic {0}" -f $base64AuthInfo)} -UserAgent $userAgent -Method GET
```
