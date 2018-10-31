---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 59c56f6a6549056fc1db5a8aff325097bcc42aa7
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50254541"
---
## <a name="rest"></a>A REST API-kkal ZIP-fájl telepítése 

Használhatja a [telepítési szolgáltatás REST API-k](https://github.com/projectkudu/kudu/wiki/REST-API) központi telepítése a .zip-fájlt az alkalmazás az Azure-ban. Üzembe helyezéséhez https://<app_name>.scm.azurewebsites.net/api/zipdeployui egy POST kérést küld. A POST-kérés tartalmaznia kell a .zip-fájlt, az üzenet törzsében. Az alkalmazás üzembehelyezési hitelesítő adatai a kérelemben alapszintű HTTP-hitelesítéssel vannak megadva. További információkért lásd: a [.zip leküldéses telepítési útmutató](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file). 

Az ALAPSZINTŰ HTTP-hitelesítést az App Service-üzembehelyezési hitelesítő adatokat kell. Az üzembe helyezési hitelesítő adatok beállítása, olvassa el [beállítása és a felhasználói szintű hitelesítő adatok alaphelyzetbe állítása](../articles/app-service/app-service-deployment-credentials.md#userscope).

### <a name="with-curl"></a>A curl használatával

Az alábbi példában a cURL eszköz üzembe helyezése egy .zip-fájlban. Cserélje le a zárójelben `<username>`, `<password>`, `<zip_file_path>`, és `<app_name>`. Amikor a cURL kéri, írja be a jelszót.

```bash
curl -X POST -u <deployment_user> --data-binary @"<zip_file_path>" https://<app_name>.scm.azurewebsites.net/api/zipdeployui
```

A kérés aktiválása leküldéses telepítését a feltöltött zip-fájlt. Áttekintheti az aktuális és korábbi központi telepítések segítségével a `https://<app_name>.scm.azurewebsites.net/api/deployments` végpont, a következő cURL-példában látható módon. Ezúttal is helyettesítse be `<app_name>` az alkalmazás nevével és `<deployment_user>` , amelynek az üzembehelyezési hitelesítő adatokat.

```bash
curl -u <deployment_user> https://<app_name>.scm.azurewebsites.net/api/deployments
```

### <a name="with-powershell"></a>A PowerShell-lel

Az alábbi példában [Invoke-RestMethod](/powershell/module/microsoft.powershell.utility/invoke-restmethod) küldeni egy kérelmet, amely tartalmazza a .zip-fájlt. Cserélje le a zárójelben `<deployment_user>`, `<deployment_password>`, `<zip_file_path>`, és `<app_name>`.

```PowerShell
#PowerShell
$username = "<deployment_user>"
$password = "<deployment_password>"
$filePath = "<zip_file_path>"
$apiUrl = "https://<app_name>.scm.azurewebsites.net/api/zipdeployui"
$base64AuthInfo = [Convert]::ToBase64String([Text.Encoding]::ASCII.GetBytes(("{0}:{1}" -f $username, $password)))
$userAgent = "powershell/1.0"
Invoke-RestMethod -Uri $apiUrl -Headers @{Authorization=("Basic {0}" -f $base64AuthInfo)} -UserAgent $userAgent -Method POST -InFile $filePath -ContentType "multipart/form-data"
```

A kérés aktiválása leküldéses telepítését a feltöltött zip-fájlt. Tekintse át a jelenlegi és korábbi központi telepítéseket, futtassa a következő parancsokat. Ezúttal is helyettesítse be a `<app_name>` helyőrző.

```bash
$apiUrl = "https://<app_name>.scm.azurewebsites.net/api/deployments"
Invoke-RestMethod -Uri $apiUrl -Headers @{Authorization=("Basic {0}" -f $base64AuthInfo)} -UserAgent $userAgent -Method GET
```
