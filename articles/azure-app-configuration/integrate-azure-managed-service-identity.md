---
title: Az oktatóanyag az Azure-ral való integrálásához felügyelt identitások |} A Microsoft Docs
description: Ebben az oktatóanyagban elsajátíthatja, hogyan használhatja az Azure által felügyelt identitásokat hitelesítve magukat hozzáférést az Azure-alkalmazások konfigurálása
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: na
ms.topic: tutorial
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: be19d37900acb8201922fa61fda61cc884d4c933
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58226011"
---
# <a name="tutorial-integrate-with-azure-managed-identities"></a>Oktatóanyag: Integrálása az Azure által felügyelt identitások

Az Azure Active Directory [felügyelt identitások](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) leegyszerűsíti a felhőbeli alkalmazások titkos kódok kezelése. Felügyelt identitással állíthat be a kódot a létrehozott egyszerű szolgáltatás használata az Azure számítási szolgáltatás díjait. Egy külön hitelesítő adatot az Azure Key Vault vagy a helyi kapcsolati karakterlánc helyett használhat egy felügyelt identitás. 

Az Azure konfigurálása és a .NET Core, a .NET és a Java Spring klienskódtárak felügyeltszolgáltatás-identitás (MSI) támogatás beépített kapható. Bár nem kell használni, MSI kiküszöböli a hozzáférési jogkivonatot, amely tartalmazza a titkos kulcsok. A kód rendelkezik tudni, hogy csak a szolgáltatásvégpont-alkalmazás konfigurálásának azt elérése érdekében tárolja. Az URL-címet a veszélye, hogy az összes titkos kód nélkül, közvetlenül a kódba ágyazhatók be.

Ez az oktatóanyag bemutatja, hogyan igénybe veheti az MSI konfigurálása eléréséhez. A web app, a rövid útmutatók rendszerben bevezetett épül. A folytatás előtt Befejezés [ASP.NET Core-alkalmazás létrehozása az alkalmazás konfigurációs](./quickstart-aspnet-core-app.md) első.

Bármely Kódszerkesztő segítségével hajtsa végre a lépéseket ebben az oktatóanyagban. [A Visual Studio Code](https://code.visualstudio.com/) kiváló lehetőség a Windows, macOS és Linux platformokon az érhető el.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Egy felügyelt identitás hozzáférést az Alkalmazáskonfigurációt.
> * Az alkalmazás egy felügyelt identitás konfigurálása való csatlakozáskor használandó konfigurálása.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* [.NET Core SDK](https://www.microsoft.com/net/download/windows).
* [Az Azure Cloud Shell konfigurált](https://docs.microsoft.com/azure/cloud-shell/quickstart).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="add-a-managed-identity"></a>Adjon hozzá egy felügyelt identitás

A portálon egy felügyelt identitás beállításához először hozzon létre egy alkalmazás a szokásos módon, és ezután engedélyezze a szolgáltatást.

1. Az alkalmazás létrehozása a [az Azure portal](https://aka.ms/azconfig/portal) szokásos módon. Nyissa meg azt a portálon.

2. Görgessen le a **beállítások** csoportot a bal oldali panelen, és válassza **identitás**.

3. A a **rendszerhez rendelt** fülre, váltson **állapota** való **a** válassza **mentése**.

    ![Felügyelt identitás beállításához az App Service-ben](./media/set-managed-identity-app-service.png)

## <a name="grant-access-to-app-configuration"></a>Hozzáférést biztosít alkalmazások konfigurálása

1. Az a [az Azure portal](https://aka.ms/azconfig/portal)válassza **összes erőforrás** , és válassza ki az alkalmazás a konfigurációs adattárolónál a rövid útmutatóban létrehozott.

2. Válassza ki **hozzáférés-vezérlés (IAM)**.

3. Az a **hozzáférés ellenőrzése** lapon jelölje be **Hozzáadás** a a **szerepkör-hozzárendelés hozzáadása** felhasználói felület kártya.

4. A **szerepkör**válassza **közreműködői**. Alatt **rendelhet hozzáféréseket**válassza **App Service-ben** alatt **rendszer hozzárendelt felügyelt identitás**.

5. A **előfizetés**, válassza ki az Azure-előfizetésében. Válassza ki az alkalmazás az App Service-erőforrást.

6. Kattintson a **Mentés** gombra.

    ![Adjon hozzá egy felügyelt identitás](./media/add-managed-identity.png)

## <a name="use-a-managed-identity"></a>Felügyelt identitás használata

1. Nyissa meg *appsettings.json*, és adja hozzá a következő szkriptet. Cserélje le *< service_endpoint >*, beleértve a zárójeleket, az alkalmazás a konfigurációs adattároló URL-címét:

    ```json
    "AppConfig": {
        "Endpoint": "<service_endpoint>"
    }
    ```

2. Nyissa meg *Program.cs*, és frissítse a `CreateWebHostBuilder` metódus lecserélésével a `config.AddAzureAppConfiguration()` metódust.

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(o => o.ConnectWithManagedIdentity(settings["AppConfig:Endpoint"]));
            })
            .UseStartup<Startup>();
    ```

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-from-local-git"></a>Üzembe helyezés a helyi Gitből

A helyi Git üzemelő példányt az alkalmazás a Kudu-buildelési kiszolgáló engedélyezése legegyszerűbben az Azure Cloud Shell használatához.

### <a name="configure-a-deployment-user"></a>Üzembe helyező felhasználó konfigurálása

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="enable-local-git-with-kudu"></a>Engedélyezi a helyi Git, a kudu használatával

Helyi Git üzemelő példányt az alkalmazás a Kudu-buildelési kiszolgáló engedélyezéséhez futtassa [ `az webapp deployment source config-local-git` ](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) a Cloud Shellben.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group <group_name>
```

Ehelyett egy Git-kompatibilis alkalmazások létrehozásához futtassa [ `az webapp create` ](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) a Cloud shellben a `--deployment-local-git` paraméter.

```azurecli-interactive
az webapp create --name <app_name> --resource-group <group_name> --plan <plan_name> --deployment-local-git
```

A `az webapp create` parancsot biztosítja a következő kimenethez hasonló:

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

A _helyi terminálablakba_ visszatérve adjon hozzá egy távoli Azure-mappát a helyi Git-adattárhoz. Cserélje le  _\<URL-címe >_ webhelyről származó alkalmazásazonosítóra távoli Git URL-címével [Git engedélyezése az alkalmazás](#enable-local-git-with-kudu).

```bash
git remote add azure <url>
```

A távoli Azure-mappához történő küldéssel helyezze üzembe az alkalmazást a következő paranccsal. Amikor jelszót kér, adja meg a jelszót, amelyet a [üzembe helyező felhasználó konfigurálása](#configure-a-deployment-user). Ne használja az Azure Portalra való bejelentkezéshez használt jelszó.

```bash
git push azure master
```

Előfordulhat, hogy a kimenetben, például az ASP.NET, MSBuild futtatókörnyezet-specifikus automation látható `npm install` a node.js-ben, és `pip install` Pythonhoz készült.

### <a name="browse-to-the-azure-web-app"></a>Az Azure webalkalmazás megkeresése

Keresse meg a webalkalmazást egy böngészővel ellenőrizze, hogy telepítve van-e a tartalmat.

```bash
http://<app_name>.azurewebsites.net
```

![az App Service-ben futó alkalmazás](../app-service/media/app-service-web-tutorial-dotnetcore-sqldb/azure-app-in-browser.png)

## <a name="use-managed-identity-in-other-languages"></a>Más nyelveken felügyelt identitás használata

A .NET-keretrendszer és a Java Spring alkalmazás konfigurációszolgáltatók is beépített támogatást nyújt a felügyelt identitás. Ezekben az esetekben használja az alkalmazás konfigurációs áruházbeli URL-végpontot a teljes kapcsolati karakterlánc helyett, ha a szolgáltató konfigurálása. Ha például a .NET-keretrendszer-konzolalkalmazást a rövid útmutatóban létrehozott, adja meg a következő beállításokat a *App.config* fájlt:

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

Ebben az oktatóanyagban egy Azure hozzáadott felügyeltszolgáltatás-identitás leegyszerűsítheti az Alkalmazáskonfigurációt hozzá, és az alkalmazás hitelesítőadat-kezelés javítására. Ismerje meg, hogyan használhatja az Alkalmazáskonfigurációt, folytassa az Azure CLI-minták.

> [!div class="nextstepaction"]
> [CLI-minták](./cli-samples.md)
