---
title: Oktatóanyag az Azure által felügyelt identitások integrálása |} A Microsoft Docs
description: Ebben az oktatóanyagban elsajátíthatja, hogyan használják az Azure által felügyelt identitásokat hitelesítve magukat hozzáférést az Azure-alkalmazások konfigurálása
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
ms.openlocfilehash: bf8a1955f2c8e4a72e7cf88b013f8d5d5c2e672f
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56884767"
---
# <a name="tutorial-integrate-with-azure-managed-identities"></a>Oktatóanyag: Az Azure felügyelt identitások integrálása

Az Azure Active Directory [felügyelt identitások](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) egyszerűsíti a felhőbeli alkalmazások titkos kódok kezelése. Felügyelt identitással állíthat be a kódot már létrehozott egyszerű szolgáltatás használata az Azure számítási szolgáltatás díjait, külön hitelesítő adatokat az Azure Key Vault vagy a helyi kapcsolati karakterlánc helyett. Az Azure konfigurálása és a .NET Core, .NET és Java Spring klienskódtárak beépített MSI támogatási kapható. Bár nem kötelező használni, MSI kiküszöböli a hozzáférési jogkivonatot, amely tartalmazza a titkos kulcsok. A kód csak akkor férhet hozzá, az alkalmazás a konfigurációs adattárolónál a szolgáltatásvégpontot tudni van, és a veszélye, hogy az összes titkos kód nélkül, közvetlenül a kódba ágyazható be az URL-címet.

Ez az oktatóanyag bemutatja, hogyan igénybe veheti az MSI konfigurálása eléréséhez. A web app, a rövid útmutatók rendszerben bevezetett épül. Teljes [ASP.NET Core-alkalmazás létrehozása az alkalmazás konfigurációs](./quickstart-aspnet-core-app.md) első, a folytatás előtt.

Ez az oktatóanyag lépéseinek végrehajtásához használhatja a bármely Kódszerkesztő. A [Visual Studio Code](https://code.visualstudio.com/) például jó választás lehet, és Windows, macOS és Linux platformokon is használható.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Egy felügyelt identitás hozzáférést biztosítani az alkalmazások konfigurálása
> * Az alkalmazás egy felügyelt identitás konfigurálása való csatlakozáskor használandó konfigurálása

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez szüksége lesz:

* [.NET Core SDK](https://www.microsoft.com/net/download/windows)
* [Konfigurált Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="add-a-managed-identity"></a>Adjon hozzá egy felügyelt identitás

A portálon egy felügyelt identitás beállításához először létrehoz egy alkalmazás a szokásos módon, és ezután engedélyezze a szolgáltatást.

1. Az alkalmazás létrehozása a [az Azure portal](https://aka.ms/azconfig/portal) ahogy azt szokásosan tenné. Keresse meg azt a portálon.

2. Görgessen le a **beállítások** a bal oldali navigációs csoportot, és válassza ki **identitás**.

3. Belül a **rendszerhez rendelt** fülre, váltson **állapot** való **a** kattintson **mentése**.

    ![Felügyelt identitás beállításához az App Service-ben](./media/set-managed-identity-app-service.png)

## <a name="grant-access-to-app-configuration"></a>Hozzáférést biztosít alkalmazások konfigurálása

1. Az a [az Azure portal](https://aka.ms/azconfig/portal), kattintson a **összes erőforrás** és az alkalmazás a konfigurációs adattárolónál a rövid útmutatóban létrehozott.

2. Válassza ki **hozzáférés-vezérlés (IAM)**.

3. Belül a **hozzáférés ellenőrzése** lapra, majd **Hozzáadás** a a **egy szerepkör-hozzárendelés hozzáadása** felhasználói felület kártyát.

4. Állítsa be **szerepkör** kell *közreműködői* és **rendelhet hozzáféréseket** kell *App Service-ben* (alatt *rendszerhez rendelt felügyelt identitás*).

5. Állítsa be **előfizetés** az Azure-előfizetést, és válassza az App Service erőforrás alkalmazáshoz.

6. Kattintson a **Save** (Mentés) gombra.

    ![Adja hozzá a felügyelt identitás](./media/add-managed-identity.png)

## <a name="use-a-managed-identity"></a>Felügyelt identitás használata

1. Nyissa meg *appsettings.json*, adja hozzá a következőket, és cserélje le *< service_endpoint >* (beleértve a zárójeleket is) az alkalmazás a konfigurációs adattároló URL-címét:

    ```json
    "AppConfig": {
        "Url": "<service_endpoint>"
    }
    ```

2. Nyissa meg *Program.cs* és frissítheti a `CreateWebHostBuilder` metódus lecserélésével a `config.AddAzureAppConfiguration()` metódust.

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(o => o.ConnectWithManagedIdentity(settings["AppConfig:Url"]));
            })
            .UseStartup<Startup>();
    ```

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-from-local-git"></a>Üzembe helyezés a helyi Gitből

Helyi Git üzemelő példányt az alkalmazás a Kudu-buildelési kiszolgáló engedélyezése a legegyszerűbb módja, hogy a Cloud Shellt használja.

### <a name="configure-a-deployment-user"></a>Üzembe helyező felhasználó konfigurálása

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="enable-local-git-with-kudu"></a>Engedélyezi a helyi Git, a kudu használatával

Helyi Git üzemelő példányt az alkalmazás a Kudu-buildelési kiszolgáló engedélyezéséhez futtassa [ `az webapp deployment source config-local-git` ](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) a Cloud shellben.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group <group_name>
```

Ehelyett egy Git-kompatibilis alkalmazások létrehozásához futtassa [ `az webapp create` ](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) az a Cloud shellben a `--deployment-local-git` paraméter.

```azurecli-interactive
az webapp create --name <app_name> --resource-group <group_name> --plan <plan_name> --deployment-local-git
```

A `az webapp create` parancs adjon meg a következő kimenethez hasonló:

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

A távoli Azure-mappához történő küldéssel helyezze üzembe az alkalmazást a következő paranccsal. Amikor a rendszer jelszót kér, ügyeljen arra, hogy az [Üzembe helyező felhasználó konfigurálása](#configure-a-deployment-user) szakaszban létrehozott jelszót adja meg, ne azt, amelyet az Azure Portalra való bejelentkezéshez használ.

```bash
git push azure master
```

A kimenetben, például az ASP.NET, MSBuild futtatókörnyezet-specifikus automation látni `npm install` a node.js-ben, és `pip install` Pythonhoz készült.

### <a name="browse-to-the-azure-web-app"></a>Az Azure webalkalmazás megkeresése

Keresse meg a webalkalmazás egy böngészővel ellenőrizze, hogy telepítve van-e a tartalmat.

```bash
http://<app_name>.azurewebsites.net
```

![App Service-ben futó alkalmazás](../app-service/media/app-service-web-tutorial-dotnetcore-sqldb/azure-app-in-browser.png)

<!-- ### Use a managed identity in a .NET Core app -->

<!-- ### Use a managed identity in a .NET Framework app -->

<!-- ### Use a managed identity in a Java Spring app -->

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy Azure hozzáadott felügyeltszolgáltatás-identitás leegyszerűsítheti az Alkalmazáskonfigurációt hozzá, és az alkalmazás hitelesítőadat-kezelés javítására. Alkalmazáskonfiguráció használatával kapcsolatos további információkért folytassa az Azure CLI-minták.

> [!div class="nextstepaction"]
> [CLI-minták](./cli-samples.md)
