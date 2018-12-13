---
title: Felügyelt identitások áttekintése – Azure App Service-ben |} A Microsoft Docs
description: Fogalmi referencia és beállítási útmutató a felügyelt identitások az Azure App Service és az Azure Functions
services: app-service
author: mattchenderson
manager: cfowler
editor: ''
ms.service: app-service
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/20/2018
ms.author: mahender
ms.openlocfilehash: 40ae45eb401aeb302b2474cd181d29c0ee26c611
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2018
ms.locfileid: "53254360"
---
# <a name="how-to-use-managed-identities-for-app-service-and-azure-functions"></a>Felügyelt identitások használata az App Service-ben és az Azure Functions

> [!NOTE] 
> App Service Linux és a Web App for containers szolgáltatásban felügyelt identitás támogatása jelenleg előzetes verzióban érhető el.

> [!Important] 
> Az App Service-ben és az Azure Functions felügyelt identitások nem várakozásoknak megfelelően működik, ha az alkalmazás előfizetések/bérlők keresztül telepítik át. Az alkalmazás kell szereznie egy új identitás, amely letiltásával és újbóli engedélyezésével végezhető. Lásd: [eltávolítása az identitás](#remove) alatt. Alsóbb rétegbeli erőforrások is kell rendelkeznie a hozzáférési szabályzatok frissítve az új identitás használatára.

Ez a témakör bemutatja, hogyan hozhat létre egy felügyelt identitás App Service-ben és az Azure Functions-alkalmazások és egyéb erőforrásainak elérésére használatával. Egy felügyelt identitás, az Azure Active Directoryból lehetővé teszi az alkalmazás más AAD által védett erőforrások, például az Azure Key Vault könnyen hozzáférhet. Az identitás az Azure platform kezeli, és nem igényli, üzembe helyezése és titkos kulcsok elforgatása. Az aad-beli felügyelt identitások kapcsolatos további információkért lásd: [felügyelt identitások az Azure-erőforrások](../active-directory/managed-identities-azure-resources/overview.md).

Alkalmazását kétféle típusú identitások adható meg: 
- A **rendszer által hozzárendelt identitás** vannak kötve, az alkalmazás és az alkalmazás törlésekor törlődik. Az alkalmazás legfeljebb egy rendszer által hozzárendelt identitás. Rendszer által hozzárendelt identitást támogató szolgáltatás általánosan elérhető Windows-alkalmazások. 
- A **felhasználó által hozzárendelt identitás** egy önálló Azure-erőforrás, amely hozzárendelheti az alkalmazást. Egy alkalmazás több felhasználó által hozzárendelt identitások is rendelkezhet. Felhasználó által hozzárendelt identitás támogatása egy minden alkalmazástípus az előzetes verzióban érhető el.

## <a name="adding-a-system-assigned-identity"></a>A rendszer által hozzárendelt identitás hozzáadása

Alkalmazás létrehozása egy rendszer által hozzárendelt identitással szükséges állítható be az alkalmazás egy új tulajdonság.

### <a name="using-the-azure-portal"></a>Az Azure Portal használata

A portálon egy felügyelt identitás beállításához először létrehoz egy alkalmazás a szokásos módon, és ezután engedélyezze a szolgáltatást.

1. Alkalmazás létrehozása a portálon, ahogy azt szokásosan tenné. Keresse meg azt a portálon.

2. Függvényalkalmazás használata esetén lépjen **platformfunkciók**. Minden olyan alkalmazás esetében, görgessen le a **beállítások** csoportot a bal oldali navigációs.

3. Válassza ki **identitás**.

4. Belül a **rendszerhez rendelt** fülre, váltson **állapot** való **a**. Kattintson a **Save** (Mentés) gombra.

![Az App Service-ben felügyelt identitás](media/app-service-managed-service-identity/msi-blade-system.png)

### <a name="using-the-azure-cli"></a>Az Azure parancssori felületének használata

Az Azure CLI használatával felügyelt identitás beállításához kell használni a `az webapp identity assign` parancsot a meglévő alkalmazások ellen. A példa futtatásához az ebben a szakaszban három lehetősége van:

- Használat [Azure Cloud Shell](../cloud-shell/overview.md) az Azure Portalról.
- Használja a beágyazott Azure Cloud Shell-t a "próbálja" gombra, az alábbi kódmintában jobb felső sarkában található.
- [Azure CLI legújabb verziójának telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.31-es vagy újabb) Ha inkább a helyi CLI-konzol használatával. 

Az alábbi lépéseket végigvezeti egy webalkalmazás létrehozása és hozzárendelése az identitás a CLI használatával:

1. Ha az Azure CLI-t helyi konzolban használja, akkor először az [az login](/cli/azure/reference-index#az-login) paranccsal jelentkezzen be az Azure-ba. Egy fiók használatával, amelyek mellett, amelyet szeretne az alkalmazás üzembe helyezése az Azure-előfizetés társítva van:

    ```azurecli-interactive
    az login
    ```
2. Hozzon létre egy webalkalmazást, a parancssori felület használatával. A parancssori felület használata az App Service további példákért lásd [App Service CLI-minták](../app-service/app-service-cli-samples.md):

    ```azurecli-interactive
    az group create --name myResourceGroup --location westus
    az appservice plan create --name myPlan --resource-group myResourceGroup --sku S1
    az webapp create --name myApp --resource-group myResourceGroup --plan myPlan
    ```

3. Futtassa a `identity assign` paranccsal hozza létre a az alkalmazás identitását:

    ```azurecli-interactive
    az webapp identity assign --name myApp --resource-group myResourceGroup
    ```

### <a name="using-azure-powershell"></a>Az Azure PowerShell használata

Az alábbi lépéseket részletesen bemutatja, hogyan webes alkalmazás létrehozása és hozzárendelése az Azure PowerShell-lel identitás:

1. Szükség esetén telepítse az Azure PowerShellt az [Azure PowerShell útmutatójának](/powershell/azure/overview) utasításait követve, majd a `Login-AzureRmAccount` futtatásával hozza létre a kapcsolatot az Azure-ral.

2. Hozzon létre egy webalkalmazást az Azure PowerShell használatával. Azure PowerShell használata az App Service további példákért lásd [App Service PowerShell-minták](../app-service/app-service-powershell-samples.md):

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzureRmResourceGroup -Name myResourceGroup -Location $location
    
    # Create an App Service plan in Free tier.
    New-AzureRmAppServicePlan -Name $webappname -Location $location -ResourceGroupName myResourceGroup -Tier Free
    
    # Create a web app.
    New-AzureRmWebApp -Name $webappname -Location $location -AppServicePlan $webappname -ResourceGroupName myResourceGroup
    ```

3. Futtassa a `Set-AzureRmWebApp -AssignIdentity` paranccsal hozza létre a az alkalmazás identitását:

    ```azurepowershell-interactive
    Set-AzureRmWebApp -AssignIdentity $true -Name $webappname -ResourceGroupName myResourceGroup 
    ```

### <a name="using-an-azure-resource-manager-template"></a>Egy Azure Resource Manager-sablon használatával

Az Azure Resource Manager-sablonok segítségével az Azure-erőforrások üzembe helyezésének automatizálása. App Service és Functions történő központi telepítésével kapcsolatos további információkért lásd: [az App Service-erőforrások üzembe helyezésének automatizálása](../app-service/app-service-deploy-complex-application-predictably.md) és [az Azure Functions erőforrások üzembe helyezésének automatizálása](../azure-functions/functions-infrastructure-as-code.md).

Bármilyen típusú erőforrás `Microsoft.Web/sites` többek között a következő tulajdonság az erőforrás-definícióban az identitást tartalmazó hozható létre:
```json
"identity": {
    "type": "SystemAssigned"
}    
```

> [!NOTE] 
> Egy alkalmazás alapértelmezett mind a felhasználó által hozzárendelt identitások rendelkezhet egyszerre. Ebben az esetben a `type` tulajdonság lenne. `SystemAssigned,UserAssigned`

Hozzáadás, a rendszer által hozzárendelt típusát jelzi az Azure hozhat létre és kezelhet az identitást az alkalmazása.

Ha például egy webalkalmazás előfordulhat, hogy a következőhöz hasonló:
```json
{
    "apiVersion": "2016-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('appName')]",
    "location": "[resourceGroup().location]",
    "identity": {
        "type": "SystemAssigned"
    },
    "properties": {
        "name": "[variables('appName')]",
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "hostingEnvironment": "",
        "clientAffinityEnabled": false,
        "alwaysOn": true
    },
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"
    ]
}
```

A helyen jön létre, amikor a következő további tulajdonságokkal rendelkezik:
```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

Ahol `<TENANTID>` és `<PRINCIPALID>` cserélése GUID-azonosítói. A tenantId tulajdonság azonosítja milyen AAD-bérlőjéhez tartozik, az identitást. A principalId az identitás az alkalmazás egyedi azonosító. Aad-ben lévő egyszerű szolgáltatás rendelkezik programmal az App Service-ben vagy az Azure Functions-példányra ugyanazzal a névvel.


## <a name="adding-a-user-assigned-identity-preview"></a>A felhasználó által hozzárendelt identitás (előzetes verzió) hozzáadása

> [!NOTE] 
> Felhasználó által hozzárendelt identitások jelenleg előzetes verzióban érhető el. Független felhőkben még nem támogatott.

Alkalmazás létrehozása egy felhasználó által hozzárendelt identitással kell létrehozni az identitást, és hozzáadhatja az erőforrás-azonosító az alkalmazás konfigurációja.

### <a name="using-the-azure-portal"></a>Az Azure Portal használata

> [!NOTE] 
> A portál kezelőfelületén lesz üzembe helyezve, és előfordulhat, hogy még nem érhető el minden régióban.

Először is szüksége egy felhasználó által hozzárendelt identitás-erőforrás létrehozásához.

1. Hozzon létre egy felügyelt identitás felhasználó által hozzárendelt erőforrást a következők szerint [ezek az utasítások](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

2. Alkalmazás létrehozása a portálon, ahogy azt szokásosan tenné. Keresse meg azt a portálon.

3. Függvényalkalmazás használata esetén lépjen **platformfunkciók**. Minden olyan alkalmazás esetében, görgessen le a **beállítások** csoportot a bal oldali navigációs.

4. Válassza ki **identitás**.

5. Belül a **felhasználóhoz (előzetes verzió)** lapra, majd **Hozzáadás**.

6. Keresse meg a korábban létrehozott identitását, és válassza ki azt. Kattintson a **Hozzáadás** parancsra.

![Az App Service-ben felügyelt identitás](media/app-service-managed-service-identity/msi-blade-user.png)

### <a name="using-an-azure-resource-manager-template"></a>Egy Azure Resource Manager-sablon használatával

Az Azure Resource Manager-sablonok segítségével az Azure-erőforrások üzembe helyezésének automatizálása. App Service és Functions történő központi telepítésével kapcsolatos további információkért lásd: [az App Service-erőforrások üzembe helyezésének automatizálása](../app-service/app-service-deploy-complex-application-predictably.md) és [az Azure Functions erőforrások üzembe helyezésének automatizálása](../azure-functions/functions-infrastructure-as-code.md).

Bármilyen típusú erőforrás `Microsoft.Web/sites` az erőforrás-definícióban, többek között a következő kódblokk egy identitással hozható létre cseréje `<RESOURCEID>` a kívánt identitás erőforrás-azonosító:
```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {}
    }
}    
```

> [!NOTE] 
> Egy alkalmazás alapértelmezett mind a felhasználó által hozzárendelt identitások rendelkezhet egyszerre. Ebben az esetben a `type` tulajdonság lenne. `SystemAssigned,UserAssigned`

A felhasználó által hozzárendelt típus hozzáadása és a egy cotells Azure hozhat létre és kezelhet az identitást az alkalmazása.

Ha például egy webalkalmazás előfordulhat, hogy a következőhöz hasonló:
```json
{
    "apiVersion": "2016-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('appName')]",
    "location": "[resourceGroup().location]",
    "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
            "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]": {}
        }
    },
    "properties": {
        "name": "[variables('appName')]",
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "hostingEnvironment": "",
        "clientAffinityEnabled": false,
        "alwaysOn": true
    },
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]"
    ]
}
```

A helyen jön létre, amikor a következő további tulajdonságokkal rendelkezik:
```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {
            "principalId": "<PRINCIPALID>",
            "clientId": "<CLIENTID>"
        }
    }
}
```

Ahol `<PRINCIPALID>` és `<CLIENTID>` cserélése GUID-azonosítói. A principalId az a-identitás az AAD-felügyelethez használt egyedi azonosító. A clientId az alkalmazás új azonosítóját adja meg a futtatókörnyezet hívások során használandó melyik identitás használt egyedi azonosítója.


## <a name="obtaining-tokens-for-azure-resources"></a>Az Azure-erőforrások jogkivonatok beszerzéséhez

Egy alkalmazás használatával az identitása tokenekhez az aad-ben, például az Azure Key Vault által védett erőforrásokhoz. Ezek a jogkivonatok jelölik az alkalmazás az erőforrást, és nem bármely adott felhasználó az alkalmazás eléréséhez. 

> [!IMPORTANT]
> Szükség lehet a célként megadott erőforrás, hogy engedélyezze a hozzáférést az alkalmazás konfigurálásához. Például ha egy Key Vault tokent kér, szüksége, hogy hozzáadott egy hozzáférési szabályzatot, amely tartalmazza az alkalmazás azonosítóját. Ellenkező esetben a Key Vault hívásainak rendszer elutasítja, akkor is, ha a jogkivonat tartalmazzák. További erőforrások kapcsolatos Azure Active Directory-jogkivonatok támogatási kapcsolatban lásd: [Azure-szolgáltatások, hogy a támogatás az Azure AD-hitelesítés](../active-directory/managed-identities-azure-resources/services-support-msi.md#azure-services-that-support-azure-ad-authentication).

Nincs a beszerzésével egy lexikális elem szerepel az App Service-ben és az Azure Functions egy egyszerű REST-protokollon. A .NET-alkalmazásokban a Microsoft.Azure.Services.AppAuthentication library absztrakciós biztosít a protokoll, és támogatja a helyi fejlesztési környezetet biztosít.

### <a name="asal"></a>A Microsoft.Azure.Services.AppAuthentication kódtár használatával a .NET-hez

A .NET-alkalmazások és funkciók az a legegyszerűbb módja egy felügyelt identitás használata a Microsoft.Azure.Services.AppAuthentication csomag keresztül történik. Ebben a könyvtárban is lehetővé teszi, hogy a kód a fejlesztői gépen, a felhasználói fiókkal a Visual Studióban a helyi tesztelése az [Azure CLI-vel](/cli/azure), vagy az Active Directory beépített hitelesítést. További információ a helyi fejlesztési lehetőségek az ebben a könyvtárban, tekintse meg a [Microsoft.Azure.Services.AppAuthentication reference]. Ez a szakasz bemutatja, hogyan első lépések a kódtárat a programkódba.

1. Adja hozzá hivatkozásokat az [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) és a többi szükséges NuGet-csomagok az alkalmazáshoz. Az alábbi példában is használt [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault).

2.  Adja hozzá a következő kódot az alkalmazást, amelyre alkalmazza a megfelelő erőforrás módosítása. Ebben a példában az Azure Key Vault használata két módszert mutat be:

```csharp
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.Azure.KeyVault;
// ...
var azureServiceTokenProvider = new AzureServiceTokenProvider();
string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://vault.azure.net");
// OR
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
```

Microsoft.Azure.Services.AppAuthentication és teszi elérhetővé a műveletek kapcsolatos további információkért tekintse meg a [Microsoft.Azure.Services.AppAuthentication reference] és a [App Service-ben és a KeyVault MSI .NET-tel minta](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet).

### <a name="using-the-rest-protocol"></a>A REST protokoll használatával

Az alkalmazás felügyelt identitással rendelkezik definiált két környezeti változó:
- MSI_ENDPOINT
- MSI_SECRET

A **MSI_ENDPOINT** egy helyi URL-cím, amelyről az alkalmazás jogkivonatokat kérhetnek. Erőforrás egy token beszerzéséhez hajtsa végre egy HTTP GET kérés ehhez a végponthoz, többek között a következő paraméterekkel:

> [!div class="mx-tdBreakAll"]
> |Paraméter neve|Eleme ennek|Leírás|
> |-----|-----|-----|
> |erőforrás|Lekérdezés|Az AAD erőforrás URI-t az erőforrás számára, ami egy token beszerzése. Ez lehet egy a [Azure-szolgáltatások, hogy a támogatás az Azure AD-hitelesítés](../active-directory/managed-identities-azure-resources/services-support-msi.md#azure-services-that-support-azure-ad-authentication) vagy bármely egyéb erőforrás URI-t.|
> |API-verzió|Lekérdezés|A használt jogkivonat API-verzió. "2017-09-01" jelenleg az egyetlen támogatott verzió.|
> |titkos kód|Fejléc|A MSI_SECRET környezeti változó értékét.|
> |ClientID|Lekérdezés|(Nem kötelező) A felhasználó által hozzárendelt identitás használt azonosítója. Ha nincs megadva, a rendszer által hozzárendelt identitás szolgál.|


Sikeres 200 OK válasz tartalmaz egy JSON-törzse a következő tulajdonságokkal:

> [!div class="mx-tdBreakAll"]
> |Tulajdonság neve|Leírás|
> |-------------|----------|
> |access_token|A kért hozzáférési jogkivonatot. A hívó webszolgáltatás a jogkivonat használatával hitelesítik magukat a fogadó webszolgáltatás.|
> |expires_on|A hozzáférési jogkivonat lejáratának időpontja. A dátum jelenik meg a másodpercek számát, 1970-01-01T0:0:0Z UTC a lejárati időpontig. Ez az érték a gyorsítótárazott jogkivonatok élettartama meghatározására szolgál.|
> |erőforrás|Az Alkalmazásazonosító URI-t a fogadó webszolgáltatás.|
> |token_type|Typ tokenu értékét jelöli. Az egyetlen, amely támogatja az Azure ad-ben típus tulajdonosi. További információ a tulajdonosi jogkivonatokat: [az OAuth 2.0 engedélyezési keretrendszer: Tulajdonosi jogkivonat-használat (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt).|


Ez a válasz megegyezik a [válasza az AAD-service-to-service hozzáférési jogkivonat kérése](../active-directory/develop/v1-oauth2-client-creds-grant-flow.md#service-to-service-access-token-response).

> [!NOTE] 
> Környezeti változók úgy van beállítva a folyamat indításakor először, miután engedélyezte az alkalmazás egy felügyelt identitás, szükség lehet az alkalmazás újraindítása, vagy ismételt telepítése előtt a kódok `MSI_ENDPOINT` és `MSI_SECRET` érhetők el a kódhoz.

### <a name="rest-protocol-examples"></a>Példák a REST protokoll
Egy kérelem (példa) a következőhöz hasonlóan nézhet ki:
```
GET /MSI/token?resource=https://vault.azure.net&api-version=2017-09-01 HTTP/1.1
Host: localhost:4141
Secret: 853b9a84-5bfa-4b22-a3f3-0b9a43d9ad8a
```
És a egy mintaválasz előfordulhat, hogy a következőhöz hasonló:
```
HTTP/1.1 200 OK
Content-Type: application/json
 
{
    "access_token": "eyJ0eXAi…",
    "expires_on": "09/14/2017 00:00:00 PM +00:00",
    "resource": "https://vault.azure.net",
    "token_type": "Bearer"
}
```

### <a name="code-examples"></a>Hitelesítésikód-példák
<a name="token-csharp"></a>Az ilyen kérést a C#-ban:
```csharp
public static async Task<HttpResponseMessage> GetToken(string resource, string apiversion)  {
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Add("Secret", Environment.GetEnvironmentVariable("MSI_SECRET"));
    return await client.GetAsync(String.Format("{0}/?resource={1}&api-version={2}", Environment.GetEnvironmentVariable("MSI_ENDPOINT"), resource, apiversion));
}
```
> [!TIP]
> .NET nyelvekhez is használhatja [Microsoft.Azure.Services.AppAuthentication](#asal) ahelyett, hogy elvégezte a kérelem saját magának.

<a name="token-js"></a>A node.js-ben:
```javascript
const rp = require('request-promise');
const getToken = function(resource, apiver, cb) {
    var options = {
        uri: `${process.env["MSI_ENDPOINT"]}/?resource=${resource}&api-version=${apiver}`,
        headers: {
            'Secret': process.env["MSI_SECRET"]
        }
    };
    rp(options)
        .then(cb);
}
```

<a name="token-powershell"></a>A PowerShellben:
```powershell
$apiVersion = "2017-09-01"
$resourceURI = "https://<AAD-resource-URI-for-resource-to-obtain-token>"
$tokenAuthURI = $env:MSI_ENDPOINT + "?resource=$resourceURI&api-version=$apiVersion"
$tokenResponse = Invoke-RestMethod -Method Get -Headers @{"Secret"="$env:MSI_SECRET"} -Uri $tokenAuthURI
$accessToken = $tokenResponse.access_token
```

## <a name="remove"></a>Az identitás eltávolítása

A funkció a portal, PowerShell vagy parancssori felület használatával, hogy készült ugyanúgy letiltásával a rendszer által hozzárendelt identitás távolíthatja el. Felhasználó által hozzárendelt identitások külön-külön is távolítható el. Szeretné eltávolítani az identitások, a REST/ARM-sablon protokoll, ez történik, a típus "None" értékre állításával:

```json
"identity": {
    "type": "None"
}    
```

Ezzel a módszerrel a rendszer által hozzárendelt identitás eltávolítása is törli azt az aad-ből. Rendszer által hozzárendelt identitások is automatikusan törlődnek az aad-ből, ha az alkalmazás-erőforrást törölték.

> [!NOTE] 
> Nincs alkalmazás beállítást is be lehet állítani, WEBSITE_DISABLE_MSI, amely csak letiltja a jogkivonat-szolgáltatás. Azonban az identitás elhagyják a helyen, és az eszközök továbbra is megjeleníti a felügyelt identitást, "on" vagy "engedélyezve". Ennek eredményeképpen a beállítás használata nem ajánlott.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Hozzáférés az SQL Database egy felügyelt identitás biztonságos használatával](app-service-web-tutorial-connect-msi.md)

[Microsoft.Azure.Services.AppAuthentication reference]: https://go.microsoft.com/fwlink/p/?linkid=862452
