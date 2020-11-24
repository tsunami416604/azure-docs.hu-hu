---
title: Oktatóanyag – Azure Key Vault használata Azure WebApp használatával a .NET-ben | Microsoft Docs
description: Ebben az oktatóanyagban egy ASP.NET Core-alkalmazásban konfigurálja és az Azure webappot, hogy beolvassa a titkos kulcsot a kulcstartóból.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 05/06/2020
ms.author: mbaldwin
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 4ed999e282aa9bcd80b000f3db2ecf9a8386a489
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/24/2020
ms.locfileid: "95537951"
---
# <a name="tutorial-use-a-managed-identity-to-connect-key-vault-to-an-azure-web-app-with-net"></a>Oktatóanyag: felügyelt identitás használata a Key Vault Azure-webalkalmazáshoz való összekapcsolásához a .NET használatával

[Azure Key Vault](https://docs.microsoft.com/azure/key-vault/general/overview) lehetővé teszi a hitelesítő adatok és egyéb titkos kódok biztonságos tárolását, de a kódnak hitelesítenie kell a Key Vault, hogy beolvassa őket. A [felügyelt identitások az Azure-erőforrások áttekintésében](../../active-directory/managed-identities-azure-resources/overview.md) segít a probléma megoldásában azáltal, hogy az Azure-szolgáltatások automatikusan felügyelt identitást biztosítanak az Azure ad-ben. Ezt az identitást használhatja bármely olyan szolgáltatás hitelesítéséhez, amely támogatja az Azure AD-hitelesítést, beleértve a Key Vaultt, anélkül, hogy meg kellene adni a hitelesítő adatokat a kódban.

Ez az oktatóanyag felügyelt identitást használ egy Azure-webalkalmazás hitelesítéséhez Azure Key Vault. Bár a lépések a [.net-hez készült Azure Key Vault v4 ügyféloldali kódtárat](/dotnet/api/overview/azure/key-vault) és az [Azure CLI](/cli/azure/get-started-with-azure-cli)-t használják, ugyanazok az alapelvek érvényesek a választott fejlesztési nyelv, Azure PowerShell és/vagy a Azure Portal használatakor.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* A [.net Core 3,1 SDK vagy újabb verzió](https://dotnet.microsoft.com/download/dotnet-core/3.1).
* [Telepítse a git](https://www.git-scm.com/downloads)-t.
* [Azure CLI](/cli/azure/install-azure-cli) vagy [Azure PowerShell](/powershell/azure/)
* [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/general/overview). A Key Vault [Azure Portal](quick-create-portal.md), [Azure CLI](quick-create-cli.md)vagy [Azure PowerShell](quick-create-powershell.md)használatával hozható létre.
* Key Vault [titok](https://docs.microsoft.com/azure/key-vault/secrets/about-secrets). Titkos kulcsot a [Azure Portal](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal), a [PowerShell](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-powershell)vagy az [Azure CLI](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-cli) használatával hozhat létre

## <a name="create-a-net-core-app-and-deploy-it-to-azure"></a>.NET Core-alkalmazás létrehozása és üzembe helyezése az Azure-ban
Ebben a lépésben a helyi .NET Core-projektet állíthatja be.

A gép egy terminálablakában hozzon létre egy `akvwebapp` nevű könyvtárat, és módosítsa erre a jelenlegi könyvtárat.

```bash
mkdir akvwebapp
cd akvwebapp
```

Most hozzon létre egy új .NET Core-alkalmazást a [DotNet New Web](/dotnet/core/tools/dotnet-new) paranccsal:

```bash
dotnet new web
```

Futtassa helyileg az alkalmazást, hogy lássa, hogyan fog kinézni az Azure-ban üzembe helyezve. 

```bash
dotnet run
```

Nyisson meg egy webböngészőt, majd keresse fel az alkalmazást a következő címen: `http://localhost:5000`.

Ekkor megjelenik a **„Helló világ!” alkalmazás** üzenet az oldalon megjelenő minta alkalmazásból.

## <a name="deploy-app-to-azure"></a>Alkalmazás üzembe helyezése az Azure-ban

Ebben a lépésben központilag telepíti a .NET Core-alkalmazást App Service a helyi git használatával. További információ az alkalmazások létrehozásáról és központi telepítéséről: [ASP.net Core Webalkalmazás létrehozása az Azure-ban](https://docs.microsoft.com/azure/app-service/quickstart-dotnetcore)

### <a name="configure-local-git-deployment"></a>A Git helyi üzemelő példányának konfigurálása

A terminálablakban nyomja le a **Ctrl+C** billentyűkombinációt a webkiszolgálóból történő kilépéshez.  Inicializáljon egy Git-adattárat a .NET Core-projekthez.

```bash
git init
git add .
git commit -m "first commit"
```

Az FTP és a helyi git egy *üzembe helyezési felhasználó* használatával üzembe helyezhető egy Azure-webalkalmazásban. Miután konfigurálta az üzembe helyezési felhasználót, használhatja azt az összes Azure-környezetben. A fiók szintű központi telepítési felhasználóneve és jelszava eltér az Azure-előfizetés hitelesítő adataitól. 

Az üzembe helyezési felhasználó konfigurálásához futtassa az az [WebApp Deployment User set](/cli/azure/webapp/deployment/user?#az-webapp-deployment-user-set) parancsot. Válasszon egy felhasználónevet és egy jelszót, amely megfelel az alábbi irányelveknek: 

- A felhasználónévnek egyedinek kell lennie az Azure-ban, a helyi git-leküldések esetében pedig nem tartalmazhatja a "@" szimbólumot. 
- A jelszónak legalább nyolc karakterből kell állnia, és a következő három elem közül kettőnek kell lennie: betűk, számok és szimbólumok. 

```azurecli-interactive
az webapp deployment user set --user-name "<username>" --password "<password>"
```

A JSON-kimenet a jelszót jeleníti meg `null` . `'Conflict'. Details: 409` hibaüzenet esetén változtassa meg a felhasználónevet. `'Bad Request'. Details: 400` hibaüzenet esetén használjon erősebb jelszót. 

Jegyezze fel a felhasználónevet és a jelszót a webalkalmazások üzembe helyezéséhez.

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Hozzon létre egy erőforráscsoportot, amely a Key vaultot és a webalkalmazást is felkészíti az az [Group Create](/cli/azure/group?#az-group-create) paranccsal:

```azurecli-interactive
az group create --name "myResourceGroup" -l "EastUS"
```

### <a name="create-an-app-service-plan"></a>App Service-csomag létrehozása

Hozzon létre egy [app Service tervet](https://docs.microsoft.com/azure/app-service/overview-hosting-plans) az Azure CLI-vel az [appservice Plan Create](/cli/azure/appservice/plan) paranccsal. Az alábbi példa egy `myAppServicePlan` , az **ingyenes** díjszabási szinten elnevezett app Service-csomagot hoz létre:

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE
```

Az App Service-csomag létrehozása után az Azure CLI az alábbi példához hasonló információkat jelenít meg:

<pre>
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "West Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "app",
  "location": "West Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  &lt; JSON data removed for brevity. &gt;
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
} 
</pre>

További információ a App Service-csomag kezeléséről: [app Service-terv kezelése az Azure-ban](https://docs.microsoft.com/azure/app-service/app-service-plan-manage)

### <a name="create-a-web-app"></a>Webalkalmazás létrehozása

Hozzon létre egy [Azure-webalkalmazást](../../app-service/overview.md) a `myAppServicePlan` app Service tervben. 

> [!Important]
> Az Key Vaulthoz hasonlóan az Azure-webalkalmazásoknak egyedi névvel kell rendelkezniük. Cserélje le a \<your-webapp-name\> nevet a webalkalmazás nevére a következő példákkal.


```azurecli-interactive
az webapp create --resource-group "myResourceGroup" --plan "myAppServicePlan" --name "<your-webapp-name>" --deployment-local-git
```

A webalkalmazás létrehozása után az Azure CLI az alábbi példához hasonló eredményeket jelenít meg:

<pre>
Local git is configured with url of 'https://&lt;username&gt;@&lt;your-webapp-name&gt;.scm.azurewebsites.net/&lt;ayour-webapp-name&gt;.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "clientCertExclusionPaths": null,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "&lt;your-webapp-name&gt;.azurewebsites.net",
  "deploymentLocalGitUrl": "https://&lt;username&gt;@&lt;your-webapp-name&gt;.scm.azurewebsites.net/&lt;your-webapp-name&gt;.git",
  "enabled": true,
  &lt; JSON data removed for brevity. &gt;
}
</pre>


A távoli Git URL-címe a `deploymentLocalGitUrl` tulajdonságban látható, a következő formátumban: `https://<username>@<your-webapp-name>.scm.azurewebsites.net/<your-webapp-name>.git`. Mentse ezt az URL-címet, mert később szüksége lesz rá.

Tallózással keresse meg az újonnan létrehozott alkalmazást. Cserélje le _&lt; a-WebApp-Name>_ az alkalmazás nevére.

```bash
https://<your-webapp-name>.azurewebsites.net
```

Ekkor megjelenik az újonnan létrehozott Azure-webalkalmazás alapértelmezett weboldala.

### <a name="deploy-your-local-app"></a>A helyi alkalmazás üzembe helyezése

Lépjen vissza a helyi terminál ablakba, és adjon hozzá egy távoli Azure-t a helyi git-tárházhoz, és cserélje le a *\<deploymentLocalGitUrl-from-create-step>* [Webalkalmazás létrehozása](#create-a-web-app) lépésből mentett git-távirányító URL-címére.

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

A távoli Azure-mappához történő küldéssel helyezze üzembe az alkalmazást a következő paranccsal. Ha a git Hitelesítőadat-kezelő kéri a hitelesítő adatok megadását, használja a [helyi git-telepítés konfigurálása](#configure-local-git-deployment) lépésben létrehozott hitelesítő adatokat.

```bash
git push azure master
```

A parancs futtatása eltarthat néhány percig. Futtatás közben a parancs a következő példához hasonló információkat jelenít meg:
<pre>
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 285 bytes | 95.00 KiB/s, done.
Total 3 (delta 2), reused 0 (delta 0), pack-reused 0
remote: Deploy Async
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'd6b54472f7'.
remote: Repository path is /home/site/repository
remote: Running oryx build...
remote: Build orchestrated by Microsoft Oryx, https://github.com/Microsoft/Oryx
remote: You can report issues at https://github.com/Microsoft/Oryx/issues
remote:
remote: Oryx Version      : 0.2.20200114.13, Commit: 204922f30f8e8d41f5241b8c218425ef89106d1d, ReleaseTagName: 20200114.13
remote: Build Operation ID: |imoMY2y77/s=.40ca2a87_
remote: Repository Commit : d6b54472f7e8e9fd885ffafaa64522e74cf370e1
.
.
.
remote: Deployment successful.
remote: Deployment Logs : 'https://&lt;your-webapp-name&gt;.scm.azurewebsites.net/newui/jsonviewer?view_url=/api/deployments/d6b54472f7e8e9fd885ffafaa64522e74cf370e1/log'
To https://&lt;your-webapp-name&gt;.scm.azurewebsites.net:443/&lt;your-webapp-name&gt;.git
   d87e6ca..d6b5447  master -> master
</pre>

Tallózással keresse meg (vagy frissítse) a központilag telepített alkalmazást a webböngésző használatával.

```bash
http://<your-webapp-name>.azurewebsites.net
```

Megjelenik a "„Helló világ!” alkalmazás!" a meglátogatás során korábban megjelenő üzenet `http://localhost:5000` .
 
## <a name="configure-web-app-to-connect-to-key-vault"></a>Webalkalmazás konfigurálása Key Vaulthoz való kapcsolódáshoz

Ebben a szakaszban a Key Vault webes elérését és az alkalmazás kódjának frissítését fogja beállítani a Key vaultból való titkos kulcs lekéréséhez.

### <a name="create-and-assign-a-managed-identity"></a>Felügyelt identitás létrehozása és társítása

Ebben az oktatóanyagban az alkalmazás által [felügyelt identitást](../../active-directory/managed-identities-azure-resources/overview.md) használjuk a Key vaultba való hitelesítéshez, amely automatikusan kezeli az alkalmazás hitelesítő adatait.

Az Azure CLI-ben az alkalmazás identitásának létrehozásához futtassa az az [WebApp-Identity assign](/cli/azure/webapp/identity?#az-webapp-identity-assign) parancsot:

```azurecli-interactive
az webapp identity assign --name "<your-webapp-name>" --resource-group "myResourceGroup"
```

A művelet a következő JSON-kódrészletet fogja visszaadni:

```json
{
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "SystemAssigned"
}
```

Ahhoz, hogy a webalkalmazás engedélyt kapjon a Key Vault **beolvasási** és **listázási** műveleteire, adja át a principalID az Azure CLI-hez az kulcstartó [set-Policy](/cli/azure/keyvault?#az-keyvault-set-policy) paranccsal:

```azurecli-interactive
az keyvault set-policy --name "<your-keyvault-name>" --object-id "<principalId>" --secret-permissions get list
```

Hozzáférési házirendeket [Azure Portal](https://docs.microsoft.com/azure/key-vault/general/assign-access-policy-portal) vagy [PowerShell](https://docs.microsoft.com/azure/key-vault/general/assign-access-policy-powershell)használatával is hozzárendelhet.

### <a name="modify-the-app-to-access-your-key-vault"></a>Az alkalmazás módosítása a Key Vault eléréséhez

#### <a name="install-the-packages"></a>A csomagok telepítése

A terminál ablakban telepítse a .NET-csomagok Azure Key Vault ügyféloldali kódtárat:

```console
dotnet add package Azure.Identity
dotnet add package Azure.Security.KeyVault.Secrets
```

#### <a name="update-the-code"></a>A kód frissítése

Keresse meg és nyissa meg a Startup.cs fájlt a akvwebapp-projektben. 

Adja hozzá ezt a két sort a fejléchez:

```csharp
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;
using Azure.Core;
```

Adja hozzá ezeket a sorokat a `app.UseEndpoints` hívás előtt, és frissítse az URI-t, hogy az tükrözze a `vaultUri` kulcstartót. Az alábbi kód az  ["DefaultAzureCredential ()"](/dotnet/api/azure.identity.defaultazurecredential) módszert használja a Key Vault hitelesítéséhez, amely tokent használ az alkalmazás által felügyelt identitástól a hitelesítéshez. További információ a Key Vault hitelesítéséről: [fejlesztői útmutató](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code). Emellett exponenciális leállítási is használ az újrapróbálkozásokhoz a Key Vault szabályozása esetén. A Key Vault tranzakciós korlátaival kapcsolatos további információkért olvassa el [Azure Key Vault szabályozási útmutatót](https://docs.microsoft.com/azure/key-vault/general/overview-throttling)

```csharp
SecretClientOptions options = new SecretClientOptions()
    {
        Retry =
        {
            Delay= TimeSpan.FromSeconds(2),
            MaxDelay = TimeSpan.FromSeconds(16),
            MaxRetries = 5,
            Mode = RetryMode.Exponential
         }
    };
var client = new SecretClient(new Uri("https://<your-unique-key-vault-name>.vault.azure.net/"), new DefaultAzureCredential(),options);

KeyVaultSecret secret = client.GetSecret("<mySecret>");

string secretValue = secret.Value;
```

A sor frissítése az `await context.Response.WriteAsync("Hello World!");` olvasásra:

```csharp
await context.Response.WriteAsync(secretValue);
```

A következő lépés végrehajtása előtt mentse a módosításokat.

#### <a name="redeploy-your-web-app"></a>A webalkalmazás újbóli üzembe helyezése

A kód frissítése után újra üzembe helyezheti azt az Azure-ban a következő git-parancsokkal:

```bash
git add .
git commit -m "Updated web app to access my key vault"
git push azure master
```

### <a name="visit-your-completed-web-app"></a>Az elkészült webalkalmazás meglátogatása

```bash
http://<your-webapp-name>.azurewebsites.net
```

A **„Helló világ!” alkalmazás** megkezdése előtt látnia kell a titkos kulcs értékét: **sikeres!**

## <a name="next-steps"></a>Következő lépések

- [Azure Key Vault használata a .NET-ben virtuális gépre telepített alkalmazásokkal](https://docs.microsoft.com/azure/key-vault/general/tutorial-net-virtual-machine)
- További információ az [Azure-erőforrások felügyelt identitásáról](../../active-directory/managed-identities-azure-resources/overview.md)
- További információ a [app Service felügyelt identitásáról](../../app-service/overview-managed-identity.md?tabs=dotnet)
- [Fejlesztői útmutató](https://docs.microsoft.com/azure/key-vault/general/developers-guide)
- [Biztonságos hozzáférés a kulcstartóhoz](https://docs.microsoft.com/azure/key-vault/general/secure-your-key-vault)


