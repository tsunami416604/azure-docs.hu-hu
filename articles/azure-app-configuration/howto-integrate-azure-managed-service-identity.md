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
ms.openlocfilehash: 4318c4b4d8f1b1f0974d0fae0a2ae5bd6e94b593
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71076533"
---
# <a name="integrate-with-azure-managed-identities"></a>Integrálás az Azure felügyelt identitásokkal

Azure Active Directory [felügyelt identitások](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) megkönnyítik a Felhőbeli alkalmazásokkal kapcsolatos titkok kezelését. Felügyelt identitás esetén beállíthatja a kódot úgy, hogy az az Azure számítási szolgáltatás számára létrehozott egyszerű szolgáltatásnevet használja. A felügyelt identitást nem külön hitelesítő adat, hanem Azure Key Vault vagy helyi kapcsolatok karakterlánca tárolja. 

Az Azure app Configuration és a .NET Core, a .NET és a Java Spring Client kódtárak felügyelt szolgáltatás-identitás (MSI) támogatással rendelkeznek. Habár nem szükséges a használatához, az MSI nem igényel olyan hozzáférési tokent, amely titkos kulcsokat tartalmaz. A kódnak csak az alkalmazás-konfigurációs tároló szolgáltatási végpontját kell ismernie ahhoz, hogy hozzáférhessen. Ezt az URL-címet közvetlenül a kódban ágyazhatja be anélkül, hogy bármilyen titkos kulcsot kellene kitennie.

Ez az oktatóanyag bemutatja, hogyan használhatja az MSI-t az alkalmazások konfigurációjának eléréséhez. A szolgáltatás a gyors útmutatókban bemutatott webalkalmazásra épül. A folytatás előtt fejezze be a [ASP.net Core alkalmazás létrehozása az alkalmazás](./quickstart-aspnet-core-app.md) -konfigurációval először.

Az oktatóanyag lépéseihez bármilyen Kódszerkesztő használható. A [Visual Studio Code](https://code.visualstudio.com/) kiváló lehetőség a Windows, MacOS és Linux platformokon.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Felügyelt identitás elérésének biztosítása az alkalmazás konfigurációjához.
> * Konfigurálja úgy az alkalmazást, hogy felügyelt identitást használjon az alkalmazás konfigurálásához való csatlakozáskor.

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

2. Válassza ki **hozzáférés-vezérlés (IAM)** .

3. A **hozzáférés engedélyezése** lapon válassza a **Hozzáadás** lehetőséget a **szerepkör-hozzárendelési kártya hozzáadása** felhasználói felületen.

4. A **szerepkör**területen válassza a **közreműködő**lehetőséget. **A hozzáférés hozzárendelése lehetőségnél**válassza a **app Service** a **rendszerhez rendelt felügyelt identitás**területen.

5. Az **előfizetés**területen válassza ki az Azure-előfizetését. Válassza ki az alkalmazás App Service erőforrását.

6. Kattintson a **Mentés** gombra.

    ![Felügyelt identitás hozzáadása](./media/add-managed-identity.png)

## <a name="use-a-managed-identity"></a>Felügyelt identitás használata

1. Keresse meg az alkalmazás konfigurációs tárolójának URL-címét úgy, hogy a Azure Portal, majd a **hozzáférési kulcsok** lapra kattint a konfigurációs képernyőjén.

2. Nyissa meg a *appSettings. JSON*fájlt, és adja hozzá a következő parancsfájlt. Cserélje le  *\<az service_endpoint >* , beleértve a szögletes zárójeleket is, és az alkalmazás konfigurációs tárolójának URL-címét. 

    ```json
    "AppConfig": {
        "Endpoint": "<service_endpoint>"
    }
    ```

3. Nyissa meg a *program.cs*, `CreateWebHostBuilder` és frissítse a metódust úgy, hogy lecseréli a `config.AddAzureAppConfiguration()` metódust.

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

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-from-local-git"></a>Üzembe helyezés a helyi Gitből

A kudu-Build kiszolgálóval a legegyszerűbben engedélyezhető a helyi git üzembe helyezése az alkalmazáshoz a Azure Cloud Shell használatával.

### <a name="configure-a-deployment-user"></a>Üzembe helyező felhasználó konfigurálása

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="enable-local-git-with-kudu"></a>Helyi git engedélyezése a kudu
Ha még nem rendelkezik helyi git-adattárral az alkalmazáshoz, inicializálnia kell egyet a következő parancsok futtatásával az alkalmazás Project könyvtárából:

```cmd
git init
git add .
git commit -m "Initial version"
```

Ha engedélyezni szeretné a helyi git-telepítést az alkalmazáshoz a kudu Build- [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) kiszolgálóval, futtassa Cloud shell.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group <group_name>
```

Ha inkább git-kompatibilis alkalmazást szeretne létrehozni, futtassa [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) a parancsot Cloud Shell `--deployment-local-git` a (z) paraméterrel.

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

A _helyi terminálablakba_ visszatérve adjon hozzá egy távoli Azure-mappát a helyi Git-adattárhoz. Cserélje le  _\<az URL-címet >_ a git-távirányító URL-címére, amelyet a [git engedélyezése az alkalmazáshoz](#enable-local-git-with-kudu).

```bash
git remote add azure <url>
```

A távoli Azure-mappához történő küldéssel helyezze üzembe az alkalmazást a következő paranccsal. Amikor a rendszer jelszót kér, adja meg az [üzembe helyezési felhasználó konfigurálása](#configure-a-deployment-user)területen létrehozott jelszót. Ne használja a Azure Portalba való bejelentkezéshez használt jelszót.

```bash
git push azure master
```

Előfordulhat, hogy a kimenetben futtatókörnyezet-specifikus automatizálás látható, például az MSBuild for ASP.net `npm install` , a Node. js és `pip install` a Python esetében.

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
