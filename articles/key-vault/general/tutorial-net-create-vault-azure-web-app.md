---
title: Oktatóanyag – Azure Key Vault használata Azure WebApp használatával a .NET-ben | Microsoft Docs
description: Ebben az oktatóanyagban egy ASP.NET Core-alkalmazást konfigurál, hogy beolvassa a titkos kulcsot a kulcstartóból.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 05/06/2020
ms.author: mbaldwin
ms.openlocfilehash: f6e70caaedf906142b19ba45f0eb4d818e2955e7
ms.sourcegitcommit: ff19f4ecaff33a414c0fa2d4c92542d6e91332f8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2020
ms.locfileid: "85051897"
---
# <a name="tutorial-use-a-managed-identity-to-connect-key-vault-to-an-azure-web-app-with-net"></a>Oktatóanyag: felügyelt identitás használata a Key Vault Azure-webalkalmazáshoz való összekapcsolásához a .NET használatával

Azure Key Vault lehetővé teszi a hitelesítő adatok és egyéb titkos kódok biztonságos tárolását, de a kódnak hitelesítenie kell a Key Vault, hogy beolvassa őket. A [felügyelt identitások az Azure-erőforrások áttekintésében](../../active-directory/managed-identities-azure-resources/overview.md) segít a probléma megoldásában azáltal, hogy az Azure-szolgáltatások automatikusan felügyelt identitást biztosítanak az Azure ad-ben. Ezt az identitást használhatja bármely olyan szolgáltatás hitelesítéséhez, amely támogatja az Azure AD-hitelesítést, beleértve a Key Vaultt, anélkül, hogy meg kellene adni a hitelesítő adatokat a kódban.

Ez az oktatóanyag felügyelt identitást használ egy Azure-webalkalmazás hitelesítéséhez Azure Key Vault. Bár a lépések a [.net-hez készült Azure Key Vault v4 ügyféloldali kódtárat](/dotnet/api/overview/azure/key-vault?view=azure-dotnet) és az [Azure CLI](/cli/azure/get-started-with-azure-cli)-t használják, ugyanazok az alapelvek érvényesek a választott fejlesztési nyelv, Azure PowerShell és/vagy a Azure Portal használatakor.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* A [.net Core 3,1 SDK vagy újabb verzió](https://dotnet.microsoft.com/download/dotnet-core/3.1).
* [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) vagy [Azure PowerShell](/powershell/azure/overview)

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Hozzon létre egy erőforráscsoportot, amely a Key vaultot és a webalkalmazást is felkészíti az az [Group Create](/cli/azure/group?view=azure-cli-latest#az-group-create) paranccsal:

```azurecli-interactive
az group create --name "myResourceGroup" -l "EastUS"
```

## <a name="set-up-your-key-vault"></a>A Key Vault beállítása

Most létrehoz egy kulcstartót, és elhelyez egy titkos kulcsot az oktatóanyag későbbi részében való használatra.

Kulcstartó létrehozásához használja az az Key [Vault Create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) parancsot:

> [!Important]
> Minden kulcstartónak egyedi névvel kell rendelkeznie. A következő példákban cserélje le <a-kulcstartó-Name> a Key Vault nevére.

```azurecli-interactive
az keyvault create --name "<your-keyvault-name>" -g "myResourceGroup"
```

Jegyezze fel a visszaadott értéket `vaultUri` , amely a "https://<Your-kulcstartó-name>. Vault.Azure.net/" formátumban jelenik meg. Ezt a [kód frissítése](#update-the-code) lépésben fogjuk használni.

Most már elhelyezheti a titkos kulcsot a Key vaultban az az kulcstartó [Secret set](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-set) paranccsal. Állítsa be a titkos kulcs nevét "keresési kifejezésként" értékre, és a "sikeres!" értéket.

```azurecli-interactive
az keyvault secret set --vault-name "<your-keyvault-name>" --name "MySecret" --value "Success!"
```

## <a name="create-a-net-web-app"></a>.NET-alapú webalkalmazás létrehozása

### <a name="create-a-local-app"></a>Helyi alkalmazás létrehozása

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

Ekkor megjelenik a **"Helló világ!" alkalmazás** üzenet az oldalon megjelenő minta alkalmazásból.

### <a name="initialize-the-git-repository"></a>A git-Tárház inicializálása

A terminálablakban nyomja le a **Ctrl+C** billentyűkombinációt a webkiszolgálóból történő kilépéshez.  Inicializáljon egy Git-adattárat a .NET Core-projekthez.

```bash
git init
git add .
git commit -m "first commit"
```

### <a name="configure-a-deployment-user"></a>Üzembe helyező felhasználó konfigurálása

Az FTP és a helyi git egy *üzembe helyezési felhasználó*használatával üzembe helyezhető egy Azure-webalkalmazásban. Miután konfigurálta az üzembe helyezési felhasználót, használhatja azt az összes Azure-környezetben. A fiók szintű központi telepítési felhasználóneve és jelszava eltér az Azure-előfizetés hitelesítő adataitól. 

Az üzembe helyezési felhasználó konfigurálásához futtassa az az [WebApp Deployment User set](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set) parancsot. Válasszon egy felhasználónevet és egy jelszót, amely megfelel az alábbi irányelveknek: 

- A felhasználónévnek egyedinek kell lennie az Azure-ban, a helyi git-leküldések esetében pedig nem tartalmazhatja a "@" szimbólumot. 
- A jelszónak legalább nyolc karakterből kell állnia, és a következő három elem közül kettőnek kell lennie: betűk, számok és szimbólumok. 

```azurecli-interactive
az webapp deployment user set --user-name "<username>" --password "<password>"
```

A JSON-kimenet a jelszót jeleníti meg `null` . `'Conflict'. Details: 409` hibaüzenet esetén változtassa meg a felhasználónevet. `'Bad Request'. Details: 400` hibaüzenet esetén használjon erősebb jelszót. 

Jegyezze fel a felhasználónevet és a jelszót a webalkalmazások üzembe helyezéséhez.

### <a name="create-an-app-service-plan"></a>App Service-csomag létrehozása

Hozzon létre egy App Service tervet az Azure CLI-vel az [appservice Plan Create](/cli/azure/appservice/plan?view=azure-cli-latest) paranccsal. Az alábbi példa egy `myAppServicePlan` , az **ingyenes** díjszabási szinten elnevezett app Service-csomagot hoz létre:

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


### <a name="create-a-remote-web-app"></a>Távoli webes alkalmazás létrehozása

Hozzon létre egy [Azure-webalkalmazást](../../app-service/containers/app-service-linux-intro.md) a `myAppServicePlan` app Service tervben. 

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

Tallózással keresse meg az újonnan létrehozott alkalmazást. Cserélje le _ &lt; a-WebApp-Name>_ az alkalmazás nevére.

```bash
https://<your-webapp-name>.azurewebsites.net
```

Ekkor megjelenik az újonnan létrehozott Azure-webalkalmazás alapértelmezett weboldala.

### <a name="deploy-your-local-app"></a>A helyi alkalmazás üzembe helyezése

Lépjen vissza a helyi terminál ablakba, és adjon hozzá egy távoli Azure-t a helyi git-tárházhoz, és cserélje le a *\<deploymentLocalGitUrl-from-create-step>* [Távoli webes alkalmazás létrehozása](#create-a-remote-web-app) lépésből mentett git-távoli URL-címére.

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

A távoli Azure-mappához történő küldéssel helyezze üzembe az alkalmazást a következő paranccsal. Ha a git Hitelesítőadat-kezelő kéri a hitelesítő adatok megadását, használja az [üzembe helyezés felhasználói lépés konfigurálása](#configure-a-deployment-user) lépésben létrehozott hitelesítő adatokat.

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

Megjelenik a ""Helló világ!"alkalmazás!" a meglátogatás során korábban megjelenő üzenet `http://localhost:5000` .

## <a name="create-and-assign-a-managed-identity"></a>Felügyelt identitás létrehozása és társítása

Az Azure CLI-ben az alkalmazás identitásának létrehozásához futtassa az az [WebApp-Identity assign](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign) parancsot:

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

Ahhoz, hogy a webalkalmazás engedélyt kapjon a Key Vault **beolvasási** és **listázási** műveleteire, adja át a principalID az Azure CLI-hez az kulcstartó [set-Policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) paranccsal:

```azurecli-interactive
az keyvault set-policy --name "<your-keyvault-name>" --object-id "<principalId>" --secret-permissions get list
```


## <a name="modify-the-app-to-access-your-key-vault"></a>Az alkalmazás módosítása a Key Vault eléréséhez

### <a name="install-the-packages"></a>A csomagok telepítése

A terminál ablakban telepítse a .NET-csomagok Azure Key Vault ügyféloldali kódtárat:

```console
dotnet add package Azure.Identity
dotnet add package Azure.Security.KeyVault.Secrets
```

### <a name="update-the-code"></a>A kód frissítése

Keresse meg és nyissa meg a Startup.cs fájlt a akvwebapp-projektben. 

Adja hozzá ezt a két sort a fejléchez:

```csharp
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;
using Azure.Core;
```

Adja hozzá ezeket a sorokat a `app.UseEndpoints` hívás előtt, és frissítse az URI-t, hogy az tükrözze a `vaultUri` kulcstartót. Az alábbi kód az ["DefaultAzureCredential ()"](/dotnet/api/azure.identity.defaultazurecredential?view=azure-dotnet) módszert használja a Key Vault hitelesítéséhez, amely tokent használ az alkalmazás által felügyelt identitástól a hitelesítéshez. Emellett exponenciális leállítási is használ az újrapróbálkozásokhoz a Key Vault szabályozása esetén.

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

KeyVaultSecret secret = client.GetSecret("mySecret");

string secretValue = secret.Value;
```

A sor frissítése az `await context.Response.WriteAsync("Hello World!");` olvasásra:

```csharp
await context.Response.WriteAsync(secretValue);
```

A következő lépés végrehajtása előtt mentse a módosításokat.

### <a name="redeploy-your-web-app"></a>A webalkalmazás újbóli üzembe helyezése

A kód frissítése után újra üzembe helyezheti azt az Azure-ban a következő git-parancsokkal:

```bash
git add .
git commit -m "Updated web app to access my key vault"
git push azure master
```

## <a name="visit-your-completed-web-app"></a>Az elkészült webalkalmazás meglátogatása

```bash
http://<your-webapp-name>.azurewebsites.net
```

A **"Helló világ!" alkalmazás**megkezdése előtt látnia kell a titkos kulcs értékét: **sikeres!**

## <a name="next-steps"></a>További lépések

- További információ az [Azure-erőforrások felügyelt identitásáról](../../active-directory/managed-identities-azure-resources/overview.md)
- További információ a [app Service felügyelt identitásáról](../../app-service/overview-managed-identity.md?tabs=dotnet)
- Tekintse [meg a .NET API-hoz készült Azure Key Vault ügyféloldali kódtár dokumentációját](/dotnet/api/overview/azure/key-vault?view=azure-dotnet)
- Tekintse [meg a .net forráskódjának Azure Key Vault ügyféloldali kódtárat](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault)
- Lásd: [v4 Azure Key Vault ügyféloldali kódtár a .net NuGet csomaghoz](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/)


