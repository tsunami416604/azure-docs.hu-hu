---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 08/12/2019
ms.author: cephalin
ms.openlocfilehash: 4f3236c0a167a2b6f7586c6cb5fea8e30f55a86c
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/12/2019
ms.locfileid: "68954080"
---
## <a name="rest"></a>ZIP-fájl üzembe helyezése REST API-kkal 

A [központi telepítési szolgáltatás REST API-jai](https://github.com/projectkudu/kudu/wiki/REST-API) segítségével telepítheti a. zip-fájlt az Azure-beli alkalmazásba. A telepítéséhez küldjön POST-kérést a https://< APP_NAME >. SCM. azurewebsites. NET/API/zipdeploy. A POST kérelemnek tartalmaznia kell a. zip-fájlt az üzenet törzsében. Az alkalmazás üzembehelyezési hitelesítő adatai a kérelemben alapszintű HTTP-hitelesítéssel vannak megadva. További információ: [. zip leküldéses üzembe helyezési útmutató](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file). 

Az egyszerű HTTP-hitelesítéshez szükség van a App Service központi telepítési hitelesítő adataira. A központi telepítési hitelesítő adatok beállításával kapcsolatban lásd: [felhasználói szintű hitelesítő adatok beállítása és](../articles/app-service/deploy-configure-credentials.md#userscope)alaphelyzetbe állítása.

### <a name="with-curl"></a>A cURLtal

A következő példa a cURL eszközt használja egy. zip fájl üzembe helyezéséhez. `<username>`Cserélje le a helyőrzőket `<password>` `<zip_file_path>`,, és `<app_name>`. Ha a cURL rákérdez, írja be a jelszót.

```bash
curl -X POST -u <deployment_user> --data-binary @"<zip_file_path>" https://<app_name>.scm.azurewebsites.net/api/zipdeploy
```

Ez a kérelem elindítja a leküldéses telepítést a feltöltött. zip fájlból. A jelenlegi és a korábbi üzemelő példányokat a `https://<app_name>.scm.azurewebsites.net/api/deployments` végpont használatával tekintheti át, ahogy az a következő curl-példában is látható. Ismét cserélje le `<app_name>` az alkalmazást az alkalmazás nevére és `<deployment_user>` a telepítési hitelesítő adatok felhasználónevére.

```bash
curl -u <deployment_user> https://<app_name>.scm.azurewebsites.net/api/deployments
```

### <a name="with-powershell"></a>A PowerShell-lel

A következő példa a [publish-AzWebapp](/powershell/module/az.websites/publish-azwebapp) feltölti a. zip fájlt. Cserélje le a `<group-name>`helyőrzőket `<app-name>`, és `<zip-file-path>`.

```powershell
Publish-AzWebapp -ResourceGroupName <group-name> -Name <app-name> -ArchivePath <zip-file-path>
```

Ez a kérelem elindítja a leküldéses telepítést a feltöltött. zip fájlból. 

Az aktuális és a korábbi központi telepítések áttekintéséhez futtassa a következő parancsokat. Újra cserélje le a `<deployment-user>`, `<deployment-password>`, és `<app-name>` helyőrzőket.

```bash
$username = "<deployment-user>"
$password = "<deployment-password>"
$apiUrl = "https://<app-name>.scm.azurewebsites.net/api/deployments"
$base64AuthInfo = [Convert]::ToBase64String([Text.Encoding]::ASCII.GetBytes(("{0}:{1}" -f $username, $password)))
$userAgent = "powershell/1.0"
Invoke-RestMethod -Uri $apiUrl -Headers @{Authorization=("Basic {0}" -f $base64AuthInfo)} -UserAgent $userAgent -Method GET
```
