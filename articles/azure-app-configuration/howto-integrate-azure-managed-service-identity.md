---
title: Integrálás az Azure felügyelt identitásokkal | Microsoft Docs
description: Ismerje meg, hogyan használhatja az Azure-beli felügyelt identitásokat a hitelesítéshez és az Azure-alkalmazások konfigurálásához való hozzáféréshez
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: na
ms.topic: conceptual
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: 3a5517c31cdac0bf6f5ea386a8614d15521d4479
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72035530"
---
# <a name="integrate-with-azure-managed-identities"></a>Integrálás az Azure felügyelt identitásokkal

Azure Active Directory [felügyelt identitások](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) megkönnyítik a Felhőbeli alkalmazásokkal kapcsolatos titkok kezelését. Felügyelt identitás esetén beállíthatja a kódot úgy, hogy az az Azure számítási szolgáltatás számára létrehozott egyszerű szolgáltatásnevet használja. A felügyelt identitást nem külön hitelesítő adat, hanem Azure Key Vault vagy helyi kapcsolatok karakterlánca tárolja. 

Az Azure app Configuration és a .NET Core, a .NET és a Java Spring Client kódtárak felügyelt szolgáltatás-identitás (MSI) támogatással rendelkeznek. Habár nem szükséges a használatához, az MSI nem igényel olyan hozzáférési tokent, amely titkos kulcsokat tartalmaz. A kód csak a szolgáltatási végpont használatával férhet hozzá az alkalmazás konfigurációs tárolójához. Ezt az URL-címet közvetlenül a kódban ágyazhatja be anélkül, hogy bármilyen titkos kulcsot kellene kitennie.

Ez az oktatóanyag bemutatja, hogyan használhatja az MSI-t az alkalmazások konfigurációjának eléréséhez. A szolgáltatás a gyors útmutatókban bemutatott webalkalmazásra épül. A folytatás előtt fejezze be a [ASP.net Core alkalmazás létrehozása az alkalmazás-konfigurációval](./quickstart-aspnet-core-app.md) először.

Emellett ez az oktatóanyag azt is bemutatja, hogyan használhatja az MSI-t az alkalmazás konfigurációjának Key Vault hivatkozásaival együtt. Ez lehetővé teszi, hogy zökkenőmentesen hozzáférhessen a Key Vault tárolt titkokhoz, valamint az alkalmazás konfigurációjában található konfigurációs értékekhez. Ha szeretné felfedezni ezt a képességet, fejezze be [a Key Vault referenciák használatát a ASP.net Core](./use-key-vault-references-dotnet-core.md) először.

Az oktatóanyag lépéseihez bármilyen Kódszerkesztő használható. A [Visual Studio Code](https://code.visualstudio.com/) kiváló lehetőség a Windows, MacOS és Linux platformokon.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Felügyelt identitás elérésének biztosítása az alkalmazás konfigurációjához.
> * Konfigurálja úgy az alkalmazást, hogy felügyelt identitást használjon az alkalmazás konfigurálásához való csatlakozáskor.
> * Ha szeretné, úgy konfigurálhatja az alkalmazást, hogy felügyelt identitást használjon, amikor egy alkalmazás-konfigurációs Key Vault-hivatkozáson keresztül csatlakozik Key Vaulthoz.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* [.NET Core SDK](https://www.microsoft.com/net/download/windows).
* [Azure Cloud Shell konfigurálva](https://docs.microsoft.com/azure/cloud-shell/quickstart).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="add-a-managed-identity"></a>Felügyelt identitás hozzáadása

Ha felügyelt identitást szeretne beállítani a portálon, először hozzon létre egy alkalmazást a szokásos módon, majd engedélyezze a szolgáltatást.

1. A szokásos módon hozzon létre egy App Services példányt a [Azure Portalban](https://portal.azure.com) . Nyissa meg a portálon.

2. Görgessen le a **Beállítások** csoportba a bal oldali ablaktáblán, és válassza az **Identity (identitás**) lehetőséget.

3. A **rendszerhez rendelt** lapon váltson az **állapot** bekapcsolva értékre, majd **válassza a** **Mentés**lehetőséget.

4. Válasz **Igen** , ha a rendszer kéri, hogy engedélyezze a rendszerhez rendelt felügyelt identitást.

    ![Felügyelt identitás beállítása App Serviceban](./media/set-managed-identity-app-service.png)

## <a name="grant-access-to-app-configuration"></a>Hozzáférés biztosítása az alkalmazás konfigurációjához

1. A [Azure Portal](https://portal.azure.com)válassza a **minden erőforrás** lehetőséget, majd válassza ki a gyors útmutatóban létrehozott alkalmazás-konfigurációs tárolót.

1. Válassza ki **hozzáférés-vezérlés (IAM)** .

1. A **hozzáférés engedélyezése** lapon válassza a **Hozzáadás** lehetőséget a **szerepkör-hozzárendelési kártya hozzáadása** felhasználói felületen.

1. A **szerepkör**területen válassza a **közreműködő**lehetőséget. **A hozzáférés hozzárendelése lehetőségnél**válassza a **app Service** a **rendszerhez rendelt felügyelt identitás**területen.

1. Az **előfizetés**területen válassza ki az Azure-előfizetését. Válassza ki az alkalmazás App Service erőforrását.

1. Kattintson a **Mentés** gombra.

    ![Felügyelt identitás hozzáadása](./media/add-managed-identity.png)

1. Nem kötelező: Ha Key Vault is szeretne hozzáférést biztosítani, kövesse az [Key Vault hitelesítés kezelése felügyelt identitással](https://docs.microsoft.com/azure/key-vault/managed-identity)című témakör utasításait.

## <a name="use-a-managed-identity"></a>Felügyelt identitás használata

1. Keresse meg az alkalmazás konfigurációs tárolójának URL-címét úgy, hogy a Azure Portal, majd a **hozzáférési kulcsok** lapra kattint a konfigurációs képernyőjén.

1. Nyissa meg a *appSettings. JSON*fájlt, és adja hozzá a következő parancsfájlt. Cserélje le a *\<service_endpoint >* , beleértve a zárójeleket is, és az alkalmazás konfigurációs tárolójának URL-címét. 

    ```json
    "AppConfig": {
        "Endpoint": "<service_endpoint>"
    }
    ```

1. Ha csak az alkalmazás konfigurációjában közvetlenül tárolt értékeket kívánja elérni, nyissa meg a *program.cs*, és frissítse az `CreateWebHostBuilder` metódust a `config.AddAzureAppConfiguration()` metódus lecserélésével.

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(options =>
                    options.ConnectWithManagedIdentity(settings["AppConfig:Endpoint"]));
            })
            .UseStartup<Startup>();
    ```

1. Ha az alkalmazás konfigurációs értékeit, valamint Key Vault hivatkozásokat kíván használni, nyissa meg a *program.cs*, és frissítse az `CreateWebHostBuilder` metódust az alább látható módon. Ez létrehoz egy új `KeyVaultClient` értéket egy `AzureServiceTokenProvider` használatával, és átadja ezt a hivatkozást az `UseAzureKeyVault` metódus hívására.

    ```csharp
        public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    var settings = config.Build();
                    AzureServiceTokenProvider azureServiceTokenProvider = new AzureServiceTokenProvider();
                    KeyVaultClient kvClient = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
                    
                    config.AddAzureAppConfiguration(options => options.ConnectWithManagedIdentity(settings["AppConfig:Endpoint"])).UseAzureKeyVault(kvClient));
                })
                .UseStartup<Startup>();
    ```

    Mostantól ugyanúgy érheti el Key Vault hivatkozásokat, mint bármely más alkalmazás-konfigurációs kulcshoz. A konfigurációs szolgáltató a `KeyVaultClient` értéket fogja használni, amelyet a hitelesítéshez konfigurált Key Vault és az érték beolvasásához.

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-from-local-git"></a>Üzembe helyezés a helyi Gitből

A kudu-Build kiszolgálóval a legegyszerűbben engedélyezhető a helyi git üzembe helyezése az alkalmazáshoz a Azure Cloud Shell használatával.

### <a name="configure-a-deployment-user"></a>Üzembe helyező felhasználó konfigurálása

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="enable-local-git-with-kudu"></a>Helyi git engedélyezése a kudu
Ha nem rendelkezik helyi git-adattárral az alkalmazáshoz, inicializálnia kell egyet. Ehhez futtassa az alábbi parancsokat az alkalmazás projekt könyvtárából:

```cmd
git init
git add .
git commit -m "Initial version"
```

Ha engedélyezni szeretné a helyi git-telepítést az alkalmazáshoz a kudu Build-kiszolgálóval, futtassa a [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) parancsot a Cloud Shellban.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group <group_name>
```

Ha inkább git-kompatibilis alkalmazást szeretne létrehozni, futtassa a [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) parancsot Cloud Shell a `--deployment-local-git` paraméterrel.

```azurecli-interactive
az webapp create --name <app_name> --resource-group <group_name> --plan <plan_name> --deployment-local-git
```

A `az webapp create` parancs a következő kimenethez hasonlót nyújt:

```json
Local git is configured with url of 'https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="deploy-your-project"></a>A projekt üzembe helyezése

A _helyi terminálablakba_ visszatérve adjon hozzá egy távoli Azure-mappát a helyi Git-adattárhoz. Cserélje le a _\<url >t_ a git-távvezérlő URL-címére, amelyet a [git engedélyezése az alkalmazáshoz](#enable-local-git-with-kudu)kapott.

```bash
git remote add azure <url>
```

A távoli Azure-mappához történő küldéssel helyezze üzembe az alkalmazást a következő paranccsal. Amikor a rendszer jelszót kér, adja meg az [üzembe helyezési felhasználó konfigurálása](#configure-a-deployment-user)területen létrehozott jelszót. Ne használja a Azure Portalba való bejelentkezéshez használt jelszót.

```bash
git push azure master
```

Előfordulhat, hogy a kimenetben futtatókörnyezet-specifikus automatizálás látható, például a ASP.NET-hez készült MSBuild, a Node. js-hez `npm install`, a Pythonhoz pedig a `pip install`.

### <a name="browse-to-the-azure-web-app"></a>Az Azure webalkalmazás megkeresése

Tallózással keresse meg a webalkalmazást böngésző használatával annak ellenőrzéséhez, hogy a tartalom telepítve van-e.

```bash
http://<app_name>.azurewebsites.net
```

![App Service futó alkalmazás](../app-service/media/app-service-web-tutorial-dotnetcore-sqldb/azure-app-in-browser.png)

## <a name="use-managed-identity-in-other-languages"></a>Felügyelt identitás használata más nyelveken

A .NET-keretrendszer és a Java Spring alkalmazás-konfigurációs szolgáltatói beépített támogatást is biztosítanak a felügyelt identitásokhoz. Ezekben az esetekben a szolgáltató konfigurálásakor a teljes kapcsolati karakterlánc helyett az alkalmazás konfigurációs tárolójának URL-végpontját használja. A gyors útmutatóban létrehozott .NET-keretrendszerbeli konzol alkalmazás esetében például a következő beállításokat kell megadni az *app. config* fájlban:

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

> [!div class="nextstepaction"]
> [CLI-minták](./cli-samples.md)
