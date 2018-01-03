## <a name="rest"></a>REST API-k segítségével központi telepítése 
 
Használhatja a [telepítési szolgáltatás REST API-k](https://github.com/projectkudu/kudu/wiki/REST-API) központi telepítése a .zip-fájlt az alkalmazás az Azure-ban. Csak egy POST kérést küld https://<app_name>.scm.azurewebsites.net/api/zipdeploy. A POST-kérelmet tartalmaznia kell a .zip-fájlt, az üzenet törzsében. Az üzembe helyezési hitelesítő adatok beállítása az alkalmazáshoz szerepelnek a kérelem HTTP BASIC hitelesítés használatával. További információkért lásd: a [.zip leküldéses telepítési útmutató](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file). 

Az alábbi példában a cURL eszköz a .zip-fájlt tartalmazó kérés küldése. A terminálból cURL futtathatja, Mac vagy Linux rendszerű számítógépen vagy a Bash Windows rendszeren. Cserélje le a `<zip_file_path>` helyőrzőt a projekt .zip fájl helyének elérési útvonalát. Cserélni `<app_name>` az alkalmazás egyedi névvel.

Cserélje le a `<deployment_user>` helyőrző, valamint az üzembe helyezési hitelesítő adatok. Amikor a cURL kéri, írja be a jelszót. Az alkalmazás üzembe helyezési hitelesítő adatok beállítása, lásd: [beállítása és a felhasználói szintű hitelesítő adatok alaphelyzetbe állítása](../articles/app-service/app-service-deployment-credentials.md#userscope).   

```bash
curl POST -u <deployment_user> --data-binary @"<zip_file_path>" https://<app_name>.scm.azurewebsites.net/api/zipdeploy
```

A kérelem elindítja a leküldéses telepítési feltöltött .zip fájlból. Az aktuális és korábbi telepítések https://<app_name>.scm.azurewebsites.net/api/deployments végpont használatával tekintheti meg a következő cURL-példában látható módon. Ebben az esetben cserélje le `<app_name>` az alkalmazás nevével és `<deployment_user>` az üzembe helyezési hitelesítő adatok a felhasználónévvel.

```bash
curl -u <deployment_user> https://<app_name>.scm.azurewebsites.net/api/deployments
```