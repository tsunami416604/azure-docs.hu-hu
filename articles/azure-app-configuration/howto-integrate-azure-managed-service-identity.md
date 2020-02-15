---
title: Integrálás az Azure felügyelt identitásokkal
description: Ismerje meg, hogyan használhatja az Azure-beli felügyelt identitásokat a hitelesítéshez és az Azure-alkalmazások konfigurálásához való hozzáféréshez
ms.service: azure-app-configuration
author: lisaguthrie
ms.topic: conceptual
ms.date: 12/29/2019
ms.author: lcozzens
ms.openlocfilehash: f85f63af94beb5c0d99632be69368c0c7c727b7b
ms.sourcegitcommit: 0eb0673e7dd9ca21525001a1cab6ad1c54f2e929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2020
ms.locfileid: "77212210"
---
# <a name="integrate-with-azure-managed-identities"></a>Integrálás az Azure felügyelt identitásokkal

Azure Active Directory [felügyelt identitások](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) megkönnyítik a Felhőbeli alkalmazásokkal kapcsolatos titkok kezelését. Felügyelt identitás esetén a kód használhatja azt az egyszerű szolgáltatásnevet, amelyet a futtatott Azure-szolgáltatáshoz hozott létre. A felügyelt identitást nem külön hitelesítő adat, hanem Azure Key Vault vagy helyi kapcsolatok karakterlánca tárolja. 

Az Azure app Configuration és a .NET Core, a .NET Framework és a Java Spring-ügyfél kódtárai felügyelt identitás-támogatással rendelkeznek. Habár nem szükséges a használatához, a felügyelt identitás szükségtelenné teszi a titkos kulcsokat tartalmazó hozzáférési token használatát. A kód csak a szolgáltatási végpont használatával férhet hozzá az alkalmazás konfigurációs tárolójához. Ezt az URL-címet közvetlenül a kódban ágyazhatja be anélkül, hogy bármilyen titkos kulcsot kellene kitennie.

Ez az oktatóanyag bemutatja, hogyan veheti igénybe a felügyelt identitást az alkalmazások konfigurációjának eléréséhez. A szolgáltatás a gyors útmutatókban bemutatott webalkalmazásra épül. A folytatás előtt fejezze be a [ASP.net Core alkalmazás létrehozása az alkalmazás-konfigurációval](./quickstart-aspnet-core-app.md) először.

Ez az oktatóanyag azt is bemutatja, hogyan használható a felügyelt identitás az alkalmazás konfigurációjának Key Vault hivatkozásaival együtt. Egyetlen felügyelt identitással zökkenőmentesen érheti el mindkét titkot Key Vault és konfigurációs értékektől az alkalmazás konfigurációjában. Ha szeretné felfedezni ezt a képességet, fejezze be [a Key Vault referenciák használatát a ASP.net Core](./use-key-vault-references-dotnet-core.md) először.

Az oktatóanyag lépéseihez bármilyen Kódszerkesztő használható. A [Visual Studio Code](https://code.visualstudio.com/) kiváló lehetőség a Windows, MacOS és Linux platformokon.

Ez az oktatóanyag bemutatja, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Felügyelt identitás elérésének biztosítása az alkalmazás konfigurációjához.
> * Konfigurálja úgy az alkalmazást, hogy felügyelt identitást használjon az alkalmazás konfigurálásához való csatlakozáskor.
> * Ha szeretné, úgy konfigurálhatja az alkalmazást, hogy felügyelt identitást használjon, amikor egy alkalmazás-konfigurációs Key Vault-hivatkozáson keresztül csatlakozik Key Vaulthoz.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* [.Net Core SDK](https://www.microsoft.com/net/download/windows).
* [Azure Cloud Shell konfigurálva](https://docs.microsoft.com/azure/cloud-shell/quickstart).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="add-a-managed-identity"></a>Felügyelt identitás hozzáadása

Ha felügyelt identitást szeretne beállítani a portálon, először hozzon létre egy alkalmazást, majd engedélyezze a szolgáltatást.

1. A szokásos módon hozzon létre egy App Services példányt a [Azure Portalban](https://portal.azure.com) . Nyissa meg a portálon.

1. Görgessen le a **Beállítások** csoportba a bal oldali ablaktáblán, és válassza az **Identity (identitás**) lehetőséget.

1. A **rendszerhez rendelt** lapon váltson az **állapot** bekapcsolva értékre, majd **válassza a** **Mentés**lehetőséget.

1. Válasz **Igen** , ha a rendszer kéri, hogy engedélyezze a rendszerhez rendelt felügyelt identitást.

    ![Felügyelt identitás beállítása App Serviceban](./media/set-managed-identity-app-service.png)

## <a name="grant-access-to-app-configuration"></a>Hozzáférés biztosítása az alkalmazás konfigurációjához

1. A [Azure Portal](https://portal.azure.com)válassza a **minden erőforrás** lehetőséget, majd válassza ki a gyors útmutatóban létrehozott alkalmazás-konfigurációs tárolót.

1. Válassza a **Hozzáférés-vezérlés (IAM)** lehetőséget.

1. A **hozzáférés engedélyezése** lapon válassza a **Hozzáadás** lehetőséget a **szerepkör-hozzárendelési kártya hozzáadása** felhasználói felületen.

1. A **szerepkör**területen válassza **az alkalmazás-konfigurációs Adatolvasó**lehetőséget. **A hozzáférés hozzárendelése lehetőségnél**válassza a **app Service** a **rendszerhez rendelt felügyelt identitás**területen.

1. Az **előfizetés**területen válassza ki az Azure-előfizetését. Válassza ki az alkalmazás App Service erőforrását.

1. Kattintson a **Mentés** gombra.

    ![Felügyelt identitás hozzáadása](./media/add-managed-identity.png)

1. Opcionális: Ha hozzáférést szeretne biztosítani Key Vaulthoz is, kövesse az [Key Vault hitelesítés felügyelt identitással való megadása](https://docs.microsoft.com/azure/key-vault/managed-identity)című témakör utasításait.

## <a name="use-a-managed-identity"></a>Felügyelt identitás használata

1. Adjon hozzá egy hivatkozást az *Azure. Identity* csomaghoz:

    ```cli
    dotnet add package Azure.Identity --version 1.1.0
    ```

1. Keresse meg az alkalmazás konfigurációs tárolójához tartozó végpontot. Ez az URL-cím a Azure Portal tárolójának **hozzáférési kulcsok** lapján jelenik meg.

1. Nyissa meg a *appSettings. JSON*fájlt, és adja hozzá a következő parancsfájlt. Cserélje le a *\<service_endpoint >* , beleértve a zárójeleket is, és az alkalmazás konfigurációs tárolójának URL-címét. 

    ```json
    "AppConfig": {
        "Endpoint": "<service_endpoint>"
    }
    ```

1. Nyissa meg a *program.cs*, és adjon hozzá egy hivatkozást a `Azure.Identity` és a `Microsoft.Azure.Services.AppAuthentication` névterekhez:

    ```csharp-interactive
    using Azure.Identity;
    ```

1. Ha csak az alkalmazás konfigurációjában közvetlenül tárolt értékeket kívánja elérni, frissítse a `CreateWebHostBuilder` metódust a `config.AddAzureAppConfiguration()` metódus cseréjével.

    > [!IMPORTANT]
    > a `CreateHostBuilder` a .NET Core 3,0 `CreateWebHostBuilder` helyettesíti.  Válassza ki a megfelelő szintaxist a környezet alapján.

    ### <a name="net-core-2xtabcore2x"></a>[.NET Core 2. x](#tab/core2x)

    ```csharp
        public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    var settings = config.Build();
                    config.AddAzureAppConfiguration(options =>
                        options.Connect(new Uri(settings["AppConfig:Endpoint"]), new ManagedIdentityCredential()));
                })
                .UseStartup<Startup>();
    ```

    ### <a name="net-core-3xtabcore3x"></a>[.NET Core 3. x](#tab/core3x)

    ```csharp
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                    config.AddAzureAppConfiguration(options =>
                        options.Connect(new Uri(settings["AppConfig:Endpoint"]), new ManagedIdentityCredential()));
                })
                .UseStartup<Startup>());
    ```
    ---

1. Ha az alkalmazás konfigurációs értékeit és Key Vault hivatkozásokat is szeretné használni, frissítse a *program.cs* az alább látható módon. Ez a kód egy új `KeyVaultClient` hoz létre egy `AzureServiceTokenProvider` használatával, és átadja ezt a hivatkozást az `UseAzureKeyVault` metódus hívására.

    ### <a name="net-core-2xtabcore2x"></a>[.NET Core 2. x](#tab/core2x)

    ```csharp
            public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
                WebHost.CreateDefaultBuilder(args)
                    .ConfigureAppConfiguration((hostingContext, config) =>
                    {
                        var settings = config.Build();
                        AzureServiceTokenProvider azureServiceTokenProvider = new AzureServiceTokenProvider();
                        KeyVaultClient kvClient = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
                        
                        config.AddAzureAppConfiguration(options => options.Connect(new Uri(settings["AppConfig:Endpoint"]), new ManagedIdentityCredential()).UseAzureKeyVault(kvClient));
                    })
                    .UseStartup<Startup>();
    ```

    ### <a name="net-core-3xtabcore3x"></a>[.NET Core 3. x](#tab/core3x)

    ```csharp
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                        AzureServiceTokenProvider azureServiceTokenProvider = new AzureServiceTokenProvider();
                        KeyVaultClient kvClient = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
                        
                        config.AddAzureAppConfiguration(options => options.Connect(new Uri(settings["AppConfig:Endpoint"]), new ManagedIdentityCredential()).UseAzureKeyVault(kvClient));
                    })
                    .UseStartup<Startup>());
    ```
    ---

    Mostantól ugyanúgy érheti el Key Vault hivatkozásokat, mint bármely más alkalmazás-konfigurációs kulcshoz. A konfigurációs szolgáltató az `KeyVaultClient`, amelyet a hitelesítéshez konfigurált, Key Vault és az érték lekérése után fog használni.

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="deploy-from-local-git"></a>Üzembe helyezés a helyi Gitből

A kudu-Build kiszolgálóval a legegyszerűbben engedélyezhető a helyi git üzembe helyezése az alkalmazáshoz a [Azure Cloud Shell](https://shell.azure.com)használatával.

### <a name="configure-a-deployment-user"></a>Üzembe helyező felhasználó konfigurálása

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="enable-local-git-with-kudu"></a>Helyi git engedélyezése a kudu
Ha nem rendelkezik helyi git-adattárral az alkalmazáshoz, inicializálnia kell egyet. A helyi git-tárház inicializálásához futtassa a következő parancsokat az alkalmazás projekt könyvtárából:

```cmd
git init
git add .
git commit -m "Initial version"
```

Ha engedélyezni szeretné a helyi git-telepítést az alkalmazáshoz a kudu Build-kiszolgálóval, futtassa a [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) Cloud Shellban.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group <group_name>
```

Ez a parancs a következő kimenethez hasonlót nyújt:

```json
{
  "url": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git"
}
```

### <a name="deploy-your-project"></a>A projekt üzembe helyezése

A _helyi terminál ablakban_adjon hozzá egy távoli Azure-t a helyi git-tárházhoz. Cserélje le _\<url >-címet_ a kudu-mel a [helyi git engedélyezése](#enable-local-git-with-kudu)lehetőséggel rendelkező git távoli URL-címére.

```bash
git remote add azure <url>
```

A távoli Azure-mappához történő küldéssel helyezze üzembe az alkalmazást a következő paranccsal. Amikor a rendszer jelszót kér, adja meg az [üzembe helyezési felhasználó konfigurálása](#configure-a-deployment-user)területen létrehozott jelszót. Ne használja a Azure Portalba való bejelentkezéshez használt jelszót.

```bash
git push azure master
```

Előfordulhat, hogy a kimenetben futtatókörnyezet-specifikus automatizálás látható, például a ASP.NET-hez készült MSBuild, a Node. js-hez `npm install` és a Python `pip install`.

### <a name="browse-to-the-azure-web-app"></a>Az Azure webalkalmazás megkeresése

Tallózással keresse meg a webalkalmazást böngésző használatával annak ellenőrzéséhez, hogy a tartalom telepítve van-e.

```bash
http://<app_name>.azurewebsites.net
```

![App Service futó alkalmazás](../app-service/media/app-service-web-tutorial-dotnetcore-sqldb/azure-app-in-browser.png)

## <a name="use-managed-identity-in-other-languages"></a>Felügyelt identitás használata más nyelveken

A .NET-keretrendszer és a Java Spring alkalmazás-konfigurációs szolgáltatói beépített támogatást is biztosítanak a felügyelt identitásokhoz. Az áruház URL-végpontját a teljes kapcsolati karakterlánca helyett használhatja a szolgáltatók egyikének konfigurálásakor. 

Frissítheti például a gyors útmutatóban létrehozott .NET-keretrendszer konzol alkalmazást a következő beállítások megadásához az *app. config* fájlban:

```xml
    <configSections>
        <section name="configBuilders" type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" restartOnExternalChanges="false" requirePermission="false" />
    </configSections>

    <configBuilders>
        <builders>
            <add name="MyConfigStore" mode="Greedy" endpoint="${Endpoint}" type="Microsoft.Configuration.ConfigurationBuilders.AzureAppConfigurationBuilder, Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration" />
            <add name="Environment" mode="Greedy" type="Microsoft.Configuration.ConfigurationBuilders.EnvironmentConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Environment" />
        </builders>
    </configBuilders>

    <appSettings configBuilders="Environment,MyConfigStore">
        <add key="AppName" value="Console App Demo" />
        <add key="Endpoint" value ="Set via an environment variable - for example, dev, test, staging, or production endpoint." />
    </appSettings>
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Következő lépések
Ebben az oktatóanyagban egy Azure által felügyelt identitást adott hozzá, amellyel egyszerűbbé válik az alkalmazások konfigurációjának elérése, és javítható a hitelesítő adatok kezelése az alkalmazásban. Ha többet szeretne megtudni az alkalmazások konfigurációjának használatáról, folytassa az Azure CLI-mintákkal.

> [!div class="nextstepaction"]
> [CLI-minták](./cli-samples.md)
