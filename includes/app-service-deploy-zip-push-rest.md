## <a name="rest"></a>Telepítheti a REST API-k használatával 
 
Használhatja a [telepítési szolgáltatás REST API-k](https://github.com/projectkudu/kudu/wiki/REST-API) központi telepítése a .zip-fájlt az alkalmazás az Azure-ban. Csak a FELADÁS egy vagy több vonatkozó kérés küldése `https://<app_name>.scm.azurewebsites.net/api/zipdeploy` , amely tartalmazza a .zip-fájlt, az üzenet törzsében. Az üzembe helyezési hitelesítő adatok az alkalmazás a kérelem HTTP ALAPSZINTŰ hitelesítést használó szerepelnek. További információkért lásd: a [.zip leküldéses telepítési referencia-témakör](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file). 

Az alábbi példában a cURL eszköz a .zip-fájlt tartalmazó kérés küldése. Futtathatja cURL a terminálból Mac vagy Linux rendszerű számítógépen vagy a Bash használatával Windows rendszeren. Cserélje le a `<zip_file_path>` helyőrzőt a projekt .zip fájl helyének elérési útvonalát. Cserélni `<app_name>` az alkalmazás egyedi névvel.

Cserélje le a `<deployment_user>` helyőrző, valamint az üzembe helyezési hitelesítő adatok. Amikor a cURL kéri, írja be a jelszót. Az alkalmazás üzembe helyezési hitelesítő adatok beállítása, lásd: [beállítása és a felhasználói szintű hitelesítő adatok alaphelyzetbe állítása](../articles/app-service/app-service-deployment-credentials.md#userscope).   

```bash
curl POST -u <deployment_user> --data-binary @"<zip_file_path>" https://<app_name>.scm.azurewebsites.net/api/zipdeploy
```

A kérelem elindítja a leküldéses telepítési feltöltött .zip fájlból. Tekintse át az aktuális és korábbi üzembe helyezése a `https://<app_name>.scm.azurewebsites.net/api/deployments` végpont, a következő cURL-példában látható módon. Ebben az esetben cserélje le `<app_name>` az alkalmazás nevével és `<deployment_user>` az üzembe helyezési hitelesítő adatok a felhasználónévvel.

```bash
curl -u <deployment_user> https://<app_name>.scm.azurewebsites.net/api/deployments
```