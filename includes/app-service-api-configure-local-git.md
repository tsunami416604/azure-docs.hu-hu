A Git helyi üzemelő példányát az [az webapp source-control config-local-git](/cli/azure/appservice/web/source-control#config-local-git) paranccsal állíthatja be az API-alkalmazáshoz.   

Az App Service több módszert is támogat tartalmak webappba való üzembe helyezésére: FTP, helyi Git, GitHub, Visual Studio Team Services és Bitbucket. Ebben a gyors útmutatóban a helyi Gitet fogjuk használni. Ez azt jelenti, hogy egy Git paranccsal végez üzembe helyezést egy helyi adattárból az Azure egyik adattárába való leküldéshez.  

Használja az alábbi szkriptet azoknak a fiókszintű üzembehelyezési hitelesítő adatoknak a beállításához, amelyeket a kód továbbításához használni kíván, és győződjön meg róla, hogy a saját értékeit használja a felhasználónév és a jelszó megadásakor.   

```azurecli-interactive
az webapp deployment user set --user-name <desired user name> --password <desired password>
```
