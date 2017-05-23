A webapp létrehozásához használja az [az appservice web create](/cli/azure/appservice/web#create) parancsot. Az alábbi parancsban az `<app_name>` helyőrző helyére írja be saját, egyedi alkalmazásnevét. Az `<app_name>` a webapp alapértelmezett DNS-helyén használatos. Ha az `<app_name>` nem egyedi, a következő rövid hibaüzenet jelenik meg: „A megadott <app_név> névvel már létezik webhely.”

```azurecli
az appservice web create --name <app_name> --resource-group myResourceGroup --plan quickStartPlan
```

A webapp létrehozása után az Azure CLI az alábbi példához hasonló információkat jelenít meg:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "<app_name>.azurewebsites.net",
    "<app_name>.scm.azurewebsites.net"
  ],
  "gatewaySiteName": null,
  "hostNameSslStates": [
    {
      "hostType": "Standard",
      "name": "<app_name>.azurewebsites.net",
      "sslState": "Disabled",
      "thumbprint": null,
      "toUpdate": null,
      "virtualIp": null
    }
    < JSON data removed for brevity. >
}
```

Az újonnan létrehozott webapp megtekintéséhez tallózással keresse meg a helyet (`http://<app_name>.azurewebsites.net`).

![app-service-web-service-created](../articles/app-service-web/media/app-service-web-get-started-nodejs-poc/app-service-web-service-created.png)


## <a name="configure-local-git-deployment"></a>A Git helyi üzemelő példányának konfigurálása

Az App Service több módszert is támogat tartalmak webappba való üzembe helyezésére: FTP, helyi Git, GitHub, Visual Studio Team Services és Bitbucket. 

Ebben a gyors útmutatóban a helyi Gitet fogjuk használni. Ez azt jelenti, hogy egy Git paranccsal végez üzembe helyezést egy helyi adattárból az Azure egyik adattárába való leküldéshez. 

A webapphoz a helyi git alkalmazásával történő hozzáférés konfigurálásához használja az [az appservice web source-control config-local-git](/cli/azure/appservice/web/source-control#config-local-git) parancsot.

```azurecli
az appservice web source-control config-local-git --name <app_name> --resource-group myResourceGroup --query url --output tsv
```

A kimenet formátuma a következő:

```
https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git
```

Mentse az itt látható URI-t. A következő lépésben használni fogja. A(z) `<username>` az előző lépésben létrehozott [üzembe helyező felhasználó](#configure-a-deployment-user).

## <a name="push-to-azure-from-git"></a>Leküldéses üzenet küldése a Gitből az Azure-ra

Adjon hozzá egy távoli Azure-mappát a helyi Git-tárházhoz.

```bash
git remote add azure <URI from previous step>
```

A távoli Azure-mappához történő küldéssel helyezze üzembe az alkalmazást. A rendszer rákérdez az előzőleg, az üzembe helyező felhasználó létrehozásakor létrehozott jelszóra. Ügyeljen arra, hogy az [Üzembe helyező felhasználó konfigurálása](#configure-a-deployment-user) szakaszban létrehozott jelszót adja meg, ne azt, amelyet az Azure Portalra való bejelentkezéshez használ.

```azurecli
git push azure master
```

A fenti parancs a következő példához hasonló információkat jelenít meg:

```bash
Counting objects: 13, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (11/11), done.
Writing objects: 100% (13/13), 2.07 KiB | 0 bytes/s, done.
Total 13 (delta 2), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'cc39b1e4cb'.
remote: Generating deployment script.
remote: Generating deployment script for Web Site
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling Basic Web Site deployment.
remote: KuduSync.NET from: 'D:\home\site\repository' to: 'D:\home\site\wwwroot'
remote: Deleting file: 'hostingstart.html'
remote: Copying file: '.gitignore'
remote: Copying file: 'LICENSE'
remote: Copying file: 'README.md'
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
 * [new branch]      master -> master
```

### <a name="browse-to-the-app"></a>Az alkalmazás megkeresése tallózással


Az üzembe helyezett alkalmazás megkeresése tallózással:

```
http://<app_name>.azurewebsites.net
```

