---
title: Hitelesítés azure-beli felügyelt identitások használatával
titleSuffix: Azure App Configuration
description: Hitelesítés az Azure-alkalmazás konfigurációjában az Azure által felügyelt identitások használatával
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 2/25/2020
ms.openlocfilehash: bf97a1eae758778efc8d800666af4a5fcb574429
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80056843"
---
# <a name="integrate-with-azure-managed-identities"></a>Integrálás az Azure felügyelt identitásaival

Az Azure Active Directory [által felügyelt identitások leegyszerűsítik](../active-directory/managed-identities-azure-resources/overview.md) a felhőalapú alkalmazás titkos kulcsok kezelését. Felügyelt identitás használatával a kód használhatja az Azure-szolgáltatás hoz létre az Azure-szolgáltatás fut. Felügyelt identitást használ az Azure Key Vaultban tárolt külön hitelesítő adatok vagy egy helyi kapcsolati karakterlánc helyett. 

Az Azure App Configuration és a . Bár nem kell használni, a felügyelt identitás szükségtelenné teszi a titkos kulcsokat tartalmazó hozzáférési jogkivonat. A kód csak a szolgáltatásvégpont használatával érheti el az Alkalmazáskonfigurációs tárolót. Ezt az URL-címet közvetlenül beágyazhatja a kódjába anélkül, hogy bármilyen titkot felfedne.

Ez a cikk bemutatja, hogyan használhatja ki a felügyelt identitás t app konfiguráció eléréséhez. A rövid útmutatókban bevezetett webalkalmazásra épül. Mielőtt folytatna, [hozzon létre egy ASP.NET Core alkalmazást az alkalmazás konfigurációjával.](./quickstart-aspnet-core-app.md)

Ez a cikk azt is bemutatja, hogyan használhatja a felügyelt identitás együtt App Configuration Key Vault-hivatkozások. Egyetlen felügyelt identitással zökkenőmentesen hozzáférhet a Key Vault titkos kulcsaihoz és az alkalmazáskonfigurációkonfigurációs konfigurációs értékekhez. Ha szeretné felfedezni ezt a lehetőséget, fejezze be [a Key Vault-hivatkozások használata ASP.NET Core](./use-key-vault-references-dotnet-core.md) először.

Bármelyik kódszerkesztőt használhatja az oktatóanyag lépéseihez. [Visual Studio Code](https://code.visualstudio.com/) egy kiváló lehetőség elérhető a Windows, macOS, és Linux platformokon.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Felügyelt identitás-hozzáférés megadása az alkalmazáskonfigurációhoz.
> * Állítsa be az alkalmazást úgy, hogy felügyelt identitást használjon, amikor az alkalmazáskonfigurációhoz csatlakozik.
> * Szükség esetén konfigurálja az alkalmazást felügyelt identitás használatára, amikor egy alkalmazáskonfigurációs kulcstároló-hivatkozáson keresztül csatlakozik a Key Vaulthoz.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* [.NET Core SDK](https://www.microsoft.com/net/download/windows).
* [Az Azure Cloud Shell konfigurálva](https://docs.microsoft.com/azure/cloud-shell/quickstart)van.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="add-a-managed-identity"></a>Felügyelt identitás hozzáadása

Felügyelt identitás beállítása a portálon, először hozzon létre egy alkalmazást, majd engedélyezze a funkciót.

1. Hozzon létre egy App Services-példányt az [Azure Portalon,](https://portal.azure.com) ahogy általában. Menj a portálon.

1. Görgessen le a **beállítások** csoporthoz a bal oldali ablaktáblában, és válassza az **Identitás**lehetőséget.

1. A **Rendszer hozzárendelt** lapján kapcsolja be az **Állapot lehetőséget,** **és** válassza a Mentés **gombot.**

1. Válaszoljon **igennel,** amikor a rendszer hozzárendelt felügyelt identitásának engedélyezésére kéri.

    ![Felügyelt identitás beállítása az App Service-ben](./media/set-managed-identity-app-service.png)

## <a name="grant-access-to-app-configuration"></a>Hozzáférés megadása az alkalmazáskonfigurációhoz

1. Az [Azure Portalon](https://portal.azure.com)válassza a **Minden erőforrás,** és válassza ki a rövid útmutatóban létrehozott alkalmazáskonfigurációs tárolót.

1. Válassza **a Hozzáférés-vezérlés (IAM) lehetőséget.**

1. A **Hozzáférés ellenőrzése** lapon válassza a **Hozzáadás** lehetőséget a **Szerepkör-hozzárendelési** kártya hozzáadása felhasználói felületén.

1. A **Szerepkör csoportban**válassza **az Alkalmazáskonfigurációs adatolvasó lehetőséget.** A **Hozzáférés hozzárendelése csoportban**válassza az **App Service** lehetőséget a Rendszer **hozzárendelt felügyelt identitás csoportban.**

1. Az **Előfizetés**csoportban válassza ki az Azure-előfizetést. Válassza ki az alkalmazáshoz az App Service-erőforrást.

1. Kattintson a **Mentés** gombra.

    ![Felügyelt identitás hozzáadása](./media/add-managed-identity.png)

1. Nem kötelező: Ha hozzáférést szeretne adni a Key Vaulthoz is, kövesse a [Key Vault-hitelesítés felügyelt identitással való biztosítása](https://docs.microsoft.com/azure/key-vault/managed-identity)című részben található utasításokat.

## <a name="use-a-managed-identity"></a>Felügyelt identitás használata

1. Hivatkozás hozzáadása az *Azure.Identity* csomaghoz:

    ```cli
    dotnet add package Azure.Identity
    ```

1. Keresse meg a végpontot az Alkalmazáskonfigurációs áruházhoz. Ez az URL-cím az Azure Portalon található áruház **Hozzáférési kulcsok** lapján jelenik meg.

1. Nyissa meg *az appsettings.json webhelyet,* és adja hozzá a következő parancsfájlt. Cserélje * \< *le service_endpoint>, beleértve a zárójeleket is, az alkalmazáskonfigurációs áruház URL-címére. 

    ```json
    "AppConfig": {
        "Endpoint": "<service_endpoint>"
    }
    ```

1. Nyissa meg *a Program.cs,* `Azure.Identity` és `Microsoft.Azure.Services.AppAuthentication` adjon hivatkozást a névterekre és a névterekre:

    ```csharp-interactive
    using Azure.Identity;
    ```

1. Ha csak az alkalmazáskonfigurációban tárolt értékeket szeretné `CreateWebHostBuilder` elérni, `config.AddAzureAppConfiguration()` frissítse a metódust a metódus cseréjével.

    > [!IMPORTANT]
    > `CreateHostBuilder`a `CreateWebHostBuilder` .NET Core 3.0-ban.  Válassza ki a megfelelő szintaxist a környezet alapján.

    ### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

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

    ### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

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

1. Az alkalmazáskonfigurációs értékek és a Key Vault-hivatkozások használatához frissítse *Program.cs* az alábbiak szerint. Ez a kód `KeyVaultClient` létrehoz `AzureServiceTokenProvider` egy újat egy használatával, `UseAzureKeyVault` és átadja ezt a hivatkozást a metódus hívásának.

    ### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
            public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
                WebHost.CreateDefaultBuilder(args)
                    .ConfigureAppConfiguration((hostingContext, config) =>
                    {
                        var settings = config.Build();
                        var credentials = new ManagedIdentityCredential();

                        config.AddAzureAppConfiguration(options =>
                        {
                            options.Connect(new Uri(settings["AppConfig:Endpoint"]), credentials)
                                    .ConfigureKeyVault(kv =>
                                    {
                                        kv.SetCredential(credentials);
                                    });
                        });
                    })
                    .UseStartup<Startup>();
    ```

    ### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
                    {
                        var settings = config.Build();
                        var credentials = new ManagedIdentityCredential();

                        config.AddAzureAppConfiguration(options =>
                        {
                            options.Connect(new Uri(settings["AppConfig:Endpoint"]), credentials)
                                    .ConfigureKeyVault(kv =>
                                    {
                                        kv.SetCredential(credentials);
                                    });
                        });
                    })
                    .UseStartup<Startup>());
    ```
    ---

    Most már elérheti a Key Vault-hivatkozásokat, mint bármely más alkalmazáskonfigurációs kulcs. A konfigurációs szolgáltató `KeyVaultClient` a key vaultban való hitelesítésre és az érték lekérésre konfigurált konfigurációt fogja használni.

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="deploy-from-local-git"></a>Üzembe helyezés a helyi Gitből

A kudu buildkiszolgálóval az alkalmazás helyi Git-üzembe helyezésének legegyszerűbb módja az [Azure Cloud Shell](https://shell.azure.com)használata.

### <a name="configure-a-deployment-user"></a>Üzembe helyező felhasználó konfigurálása

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="enable-local-git-with-kudu"></a>Helyi Git engedélyezése kuduval
Ha nem rendelkezik helyi git-tárházzal az alkalmazáshoz, inicializálnia kell egyet. A helyi git-tárház inicializálásához futtassa a következő parancsokat az alkalmazás projektkönyvtárából:

```cmd
git init
git add .
git commit -m "Initial version"
```

Ha engedélyezni szeretné a helyi Git-telepítést az [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) alkalmazásszámára a Kudu buildkiszolgálóval, futtassa a Cloud Shell ben.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group <group_name>
```

Ez a parancs a következő kimenethez hasonló tetsző taságot ad:

```json
{
  "url": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git"
}
```

### <a name="deploy-your-project"></a>A projekt üzembe helyezése

A _helyi terminálablakban_adjon hozzá egy Azure-távvezérlőt a helyi Git-tárházhoz. Cserélje _ \<le az URL->_ a Git-távvezérlő URL-jével, amelyet a [Helyi git engedélyezése kuduval](#enable-local-git-with-kudu)kapott.

```bash
git remote add azure <url>
```

A távoli Azure-mappához történő küldéssel helyezze üzembe az alkalmazást a következő paranccsal. Amikor a rendszer jelszót kér, írja be a létrehozott jelszót a [Telepítési felhasználó konfigurálása mezőbe.](#configure-a-deployment-user) Ne használja az Azure Portalra való bejelentkezéshez használt jelszót.

```bash
git push azure master
```

Előfordulhat, hogy futásidejű automatizálás a kimenetben, például `npm install` AZ MSBuild ASP.NET, `pip install` node.js és python.

### <a name="browse-to-the-azure-web-app"></a>Az Azure webalkalmazás megkeresése

Tallózással keresse meg a webalkalmazást egy böngésző segítségével, és ellenőrizze, hogy a tartalom telepítve van-e.

```bash
http://<app_name>.azurewebsites.net
```

## <a name="use-managed-identity-in-other-languages"></a>Felügyelt identitás használata más nyelveken

A .NET Framework és a Java Spring alkalmazáskonfigurációs szolgáltatói is rendelkeznek beépített támogatással a felügyelt identitáshoz. Az áruház URL-végpontját használhatja a teljes kapcsolati karakterlánc helyett, ha konfigurálja az egyik ilyen szolgáltatót. 

A rövid útmutatóban létrehozott . *App.config*

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

## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban hozzáadott egy Azure felügyelt identitást az alkalmazáskonfigurációhoz való hozzáférés egyszerűsítése és az alkalmazás hitelesítő adatok kezelésének javítása érdekében. Ha többet szeretne megtudni az alkalmazáskonfiguráció használatáról, folytassa az Azure CLI-mintákkal.

> [!div class="nextstepaction"]
> [CLI-minták](./cli-samples.md)
