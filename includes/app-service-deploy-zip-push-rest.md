## <a name="rest"></a>A REST API-k ZIP-fájl központi telepítése 

Használhatja a [telepítési szolgáltatás REST API-k](https://github.com/projectkudu/kudu/wiki/REST-API) központi telepítése a .zip-fájlt az alkalmazás az Azure-ban. Központi telepítéséhez https://<app_name>.scm.azurewebsites.net/api/zipdeploy egy POST kérést küld. A POST-kérelmet tartalmaznia kell a .zip-fájlt, az üzenet törzsében. Az üzembe helyezési hitelesítő adatok beállítása az alkalmazáshoz szerepelnek a kérelem HTTP BASIC hitelesítés használatával. További információkért lásd: a [.zip leküldéses telepítési útmutató](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file). 

A HTTP BASIC hitelesítés van szüksége az App Service üzembe helyezési hitelesítő adatokat. Az üzembe helyezési hitelesítő adatok beállításával kapcsolatos információk: [beállítása és a felhasználói szintű hitelesítő adatok alaphelyzetbe állítása](../articles/app-service/app-service-deployment-credentials.md#userscope).

### <a name="with-curl"></a>A cURL

Az alábbi példában a cURL eszköz egy .zip fájl központi telepítése. Cserélje le a helyőrzőket `<username>`, `<password>`, `<zip_file_path>`, és `<app_name>`. Amikor a cURL kéri, írja be a jelszót.

```bash
curl -X POST -u <deployment_user> --data-binary @"<zip_file_path>" https://<app_name>.scm.azurewebsites.net/api/zipdeploy
```

A kérelem elindítja a leküldéses telepítési feltöltött .zip fájlból. Az aktuális és korábbi telepítések https://<app_name>.scm.azurewebsites.net/api/deployments végpont használatával tekintheti meg a következő cURL-példában látható módon. Ebben az esetben cserélje le `<app_name>` az alkalmazás nevével és `<deployment_user>` az üzembe helyezési hitelesítő adatok a felhasználónévvel.

```bash
curl -u <deployment_user> https://<app_name>.scm.azurewebsites.net/api/deployments
```

### <a name="with-powershell"></a>A PowerShell-lel

Az alábbi példában [Invoke-RestMethod](/powershell/module/microsoft.powershell.utility/invoke-restmethod) a .zip-fájlt tartalmazó kérést küldeni. Cserélje le a helyőrzőket `<deployment_user>`, `<deployment_password>`, `<zip_file_path>`, és `<app_name>`.

```PowerShell
#PowerShell
$username = "<deployment_user>"
$password = "<deployment_password>"
$filePath = "<zip_file_path>"
$apiUrl = "https://<app_name>.scm.azurewebsites.net/api/zipdeploy"
$base64AuthInfo = [Convert]::ToBase64String([Text.Encoding]::ASCII.GetBytes(("{0}:{1}" -f $username, $password)))
$userAgent = "powershell/1.0"
Invoke-RestMethod -Uri $apiUrl -Headers @{Authorization=("Basic {0}" -f $base64AuthInfo)} -UserAgent $userAgent -Method POST -InFile $filePath -ContentType "multipart/form-data"
```

A kérelem elindítja a leküldéses telepítési feltöltött .zip fájlból. Az aktuális és korábbi központi telepítésének ellenőrzéséhez futtassa a következő parancsokat. Ebben az esetben cserélje le a `<app_name>` helyőrző.

```bash
$apiUrl = "https://<app_name>.scm.azurewebsites.net/api/deployments"
Invoke-RestMethod -Uri $apiUrl -Headers @{Authorization=("Basic {0}" -f $base64AuthInfo)} -UserAgent $userAgent -Method GET
```
