A Git helyi üzemelő példányának konfigurálása a webalkalmazásban az [az webapp deployment source config-local-git](/cli/azure/webapp/deployment/source#config-local-git) parancs segítségével.

Az App Service több módszert is támogat tartalmak webappba való üzembe helyezésére: FTP, helyi Git, GitHub, Visual Studio Team Services és Bitbucket. Ebben a gyors útmutatóban a helyi Gitet fogjuk használni. Ez azt jelenti, hogy egy Git paranccsal végez üzembe helyezést egy helyi adattárból az Azure egyik adattárába való leküldéshez. 

A következő parancsban az *\<app_name>* helyett használja a webalkalmazás nevét.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group myResourceGroup --query url --output tsv
```

A kimenet formátuma a következő:

```bash
https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git
```

A(z) `<username>` az előző lépésben létrehozott [üzembe helyező felhasználó](#configure-a-deployment-user).

Másolja ki a megjelenő URI-t, mert a következő lépésben használni fogja.
