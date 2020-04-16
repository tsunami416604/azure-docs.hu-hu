---
title: Felügyelt identitások
description: Ismerje meg, hogyan működnek a felügyelt identitások az Azure App Service és az Azure Functions szolgáltatásban, hogyan konfigurálhat egy felügyelt identitást, és hogyan hozhat létre jogkivonatot egy háttér-erőforráshoz.
author: mattchenderson
ms.topic: article
ms.date: 04/14/2020
ms.author: mahender
ms.reviewer: yevbronsh
ms.openlocfilehash: 875d2bbebdfa95c6d180979399d876eb2afc01b4
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81392529"
---
# <a name="how-to-use-managed-identities-for-app-service-and-azure-functions"></a>Felügyelt identitások használata az App Service és az Azure Functions szolgáltatáshoz

> [!Important] 
> Az App Service és az Azure Functions felügyelt identitások nem a várt módon fognak elmélkedni, ha az alkalmazás áttelepül az előfizetések/bérlők között. Az alkalmazásnak új identitást kell beszereznie, amely a funkció letiltásával és újbóli engedélyezésével végezhető el. Lásd: Az [identitás eltávolítása](#remove) alább. Az alsóbb rétegbeli erőforrásoknak is frissíteniük kell a hozzáférési házirendeket az új identitás használatához.

Ez a témakör bemutatja, hogyan hozhat létre felügyelt identitást az App Service- és Az Azure Functions-alkalmazásokhoz, és hogyan használhatja azt más erőforrások eléréséhez. Az Azure Active Directory (Azure AD) felügyelt identitása lehetővé teszi, hogy az alkalmazás egyszerűen hozzáférhessen más Azure AD-védett erőforrásokhoz, például az Azure Key Vaulthoz. Az identitás t an azure platform kezeli, és nem követeli meg, hogy kiépítse vagy forgassa a titkos kulcsokat. Az Azure AD felügyelt identitásairól az [Azure-erőforrások felügyelt identitásai.](../active-directory/managed-identities-azure-resources/overview.md)

A kérelem kétféle identitást kaphat:

- A **rendszer által hozzárendelt identitás** az alkalmazáshoz van kötve, és törlődik, ha az alkalmazás törlődik. Egy alkalmazás csak egy rendszer-hozzárendelt identitással rendelkezhet.
- A **felhasználó által hozzárendelt identitás** egy önálló Azure-erőforrás, amely hozzárendelhető az alkalmazáshoz. Egy alkalmazás több felhasználó által hozzárendelt identitással is rendelkezhet.

## <a name="add-a-system-assigned-identity"></a>Rendszerhez rendelt identitás hozzáadása

A rendszeráltal hozzárendelt identitással rendelkező alkalmazás létrehozásához egy további tulajdonságot kell beállítani az alkalmazáson.

### <a name="using-the-azure-portal"></a>Az Azure Portal használata

Felügyelt identitás beállítása a portálon, először hozzon létre egy alkalmazást a szokásos módon, majd engedélyezze a funkciót.

1. Hozzon létre egy alkalmazást a portálon, ahogy szokott. Keresse meg a portálon.

2. Ha függvényalkalmazást használ, keresse meg a **Platform funkcióit.** Más alkalmazástípusok esetén görgessen le a **beállítások** csoporthoz a bal oldali navigációs sávon.

3. Válassza **az Identitás**lehetőséget.

4. A **Rendszer hozzárendelt** lapján kapcsolja be az **Állapot szolgáltatást** **bekapcsolva**állásra. Kattintson a **Save** (Mentés) gombra.

    ![Felügyelt identitás az App Service-ben](media/app-service-managed-service-identity/system-assigned-managed-identity-in-azure-portal.png)

### <a name="using-the-azure-cli"></a>Az Azure parancssori felületének használata

Felügyelt identitás beállítása az Azure CLI használatával, a `az webapp identity assign` parancsot egy meglévő alkalmazás ellen kell használnia. Ebben a szakaszban három lehetőség közül választhat a példák futtatásához:

- Használja az [Azure Cloud Shell](../cloud-shell/overview.md) az Azure Portalon.
- Használja a beágyazott Azure Cloud Shell segítségével a "Try It" gomb, található a jobb felső sarokban minden kódblokk alatt.
- [Telepítse az Azure CLI legújabb verzióját](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.31 vagy újabb), ha helyi CLI-konzolt szeretne használni. 

A következő lépések végigvezetik egy webalkalmazás létrehozásán és identitásának hozzárendelésén a CLI használatával:

1. Ha az Azure CLI-t helyi konzolban használja, akkor először az [az login](/cli/azure/reference-index#az-login) paranccsal jelentkezzen be az Azure-ba. Használjon olyan fiókot, amely az Azure-előfizetéshez van társítva, amely nek az alkalmazás üzembe helyezéséhez tartozó:

    ```azurecli-interactive
    az login
    ```

2. Hozzon létre egy webalkalmazást a CLI használatával. A CLI App Service szolgáltatással való használatának további példáit az [App Service CLI-mintái című témakörben talál:](../app-service/samples-cli.md)

    ```azurecli-interactive
    az group create --name myResourceGroup --location westus
    az appservice plan create --name myPlan --resource-group myResourceGroup --sku S1
    az webapp create --name myApp --resource-group myResourceGroup --plan myPlan
    ```

3. Futtassa a `identity assign` parancsot az alkalmazás identitásának létrehozásához:

    ```azurecli-interactive
    az webapp identity assign --name myApp --resource-group myResourceGroup
    ```

### <a name="using-azure-powershell"></a>Az Azure PowerShell használata

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A következő lépések végigvezetik egy webalkalmazás létrehozásán, és identitás hozzárendelésén az Azure PowerShell használatával:

1. Ha szükséges, telepítse az Azure PowerShellaz [Azure PowerShell-útmutatóban](/powershell/azure/overview)található utasításokat, és futtassa `Login-AzAccount` az Azure-ral való kapcsolat létrehozásához.

2. Hozzon létre egy webalkalmazást az Azure PowerShell használatával. Az Azure PowerShell App Service szolgáltatással való használatának további példáiról az [App Service PowerShell-mintákban](../app-service/samples-powershell.md)talál további példákat:

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name myResourceGroup -Location $location

    # Create an App Service plan in Free tier.
    New-AzAppServicePlan -Name $webappname -Location $location -ResourceGroupName myResourceGroup -Tier Free

    # Create a web app.
    New-AzWebApp -Name $webappname -Location $location -AppServicePlan $webappname -ResourceGroupName myResourceGroup
    ```

3. Futtassa a `Set-AzWebApp -AssignIdentity` parancsot az alkalmazás identitásának létrehozásához:

    ```azurepowershell-interactive
    Set-AzWebApp -AssignIdentity $true -Name $webappname -ResourceGroupName myResourceGroup 
    ```

### <a name="using-an-azure-resource-manager-template"></a>Azure Resource Manager-sablon használata

Az Azure Resource Manager-sablon segítségével automatizálható az Azure-erőforrások üzembe helyezésének. Az App Service és a Functions szolgáltatásban való üzembe helyezésről az [Erőforrás-telepítés automatizálása az App Service-ben](../app-service/deploy-complex-application-predictably.md) és [az erőforrások üzembe helyezésének automatizálása az Azure Functions ben.](../azure-functions/functions-infrastructure-as-code.md)

Bármilyen típusú `Microsoft.Web/sites` erőforrás létrehozható identitással, ha a következő tulajdonságot is beírja az erőforrás-definícióba:

```json
"identity": {
    "type": "SystemAssigned"
}
```

> [!NOTE]
> Egy alkalmazás egyszerre rendelkezhet rendszer- és felhasználóhoz rendelt identitásokkal is. Ebben az esetben `type` az ingatlan`SystemAssigned,UserAssigned`

A rendszer által hozzárendelt típus hozzáadása lekéri az Azure-t, hogy hozza létre és kezelje az alkalmazás identitását.

Egy webalkalmazás például a következőkre néz ki:

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

A hely létrehozásakor a következő további tulajdonságokkal rendelkezik:

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

A tenantId tulajdonság azonosítja, hogy az Azure AD-bérlő az identitás tartozik. A rendszerazonosító az alkalmazás új identitásának egyedi azonosítója. Az Azure AD-n belül az egyszerű szolgáltatás ugyanaz a név, amelyet az App Service- vagy az Azure Functions-példánynak adott.

## <a name="add-a-user-assigned-identity"></a>Felhasználó által hozzárendelt identitás hozzáadása

Egy alkalmazás létrehozása a felhasználó által hozzárendelt identitás szükséges, hogy létrehozza az identitást, és majd adja hozzá az erőforrás-azonosítót az alkalmazás config.

### <a name="using-the-azure-portal"></a>Az Azure Portal használata

Először létre kell hoznia egy felhasználó által hozzárendelt identitás-erőforrást.

1. Hozzon létre egy felhasználó által hozzárendelt felügyelt identitáserőforrást [az utasításoknak](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)megfelelően.

2. Hozzon létre egy alkalmazást a portálon, ahogy szokott. Keresse meg a portálon.

3. Ha függvényalkalmazást használ, keresse meg a **Platform funkcióit.** Más alkalmazástípusok esetén görgessen le a **beállítások** csoporthoz a bal oldali navigációs sávon.

4. Válassza **az Identitás**lehetőséget.

5. A **Felhasználó hozzárendelt** lapján kattintson a **Hozzáadás**gombra.

6. Keresse meg a korábban létrehozott identitást, és jelölje ki. Kattintson a **Hozzáadás** parancsra.

    ![Felügyelt identitás az App Service-ben](media/app-service-managed-service-identity/user-assigned-managed-identity-in-azure-portal.png)

### <a name="using-an-azure-resource-manager-template"></a>Azure Resource Manager-sablon használata

Az Azure Resource Manager-sablon segítségével automatizálható az Azure-erőforrások üzembe helyezésének. Az App Service és a Functions szolgáltatásban való üzembe helyezésről az [Erőforrás-telepítés automatizálása az App Service-ben](../app-service/deploy-complex-application-predictably.md) és [az erőforrások üzembe helyezésének automatizálása az Azure Functions ben.](../azure-functions/functions-infrastructure-as-code.md)

Bármilyen típusú `Microsoft.Web/sites` erőforrás létrehozható identitással, ha a következő blokkot `<RESOURCEID>` beírja az erőforrás-definícióba, és a kívánt identitás erőforrásazonosítójával helyettesíti:

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {}
    }
}
```

> [!NOTE]
> Egy alkalmazás egyszerre rendelkezhet rendszer- és felhasználóhoz rendelt identitásokkal is. Ebben az esetben `type` az ingatlan`SystemAssigned,UserAssigned`

A felhasználó által hozzárendelt típus hozzáadása lekéri az Azure-t, hogy használja az alkalmazáshoz megadott felhasználó által hozzárendelt identitást.

Egy webalkalmazás például a következőkre néz ki:

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

A hely létrehozásakor a következő további tulajdonságokkal rendelkezik:

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

A rendszeri azonosító az Azure AD-felügyelethez használt identitás egyedi azonosítója. Az ügyfélazonosító az alkalmazás új identitásának egyedi azonosítója, amely a futásidejű hívások során használandó identitás megadására szolgál.

## <a name="obtain-tokens-for-azure-resources"></a>Az Azure-erőforrások jogkivonatai

Egy alkalmazás a felügyelt identitás használatával get jogkivonatok eléréséhez más erőforrások által védett Azure AD, például az Azure Key Vault. Ezek a jogkivonatok az erőforráshoz hozzáférő alkalmazást jelölik, és nem az alkalmazás bármely felhasználóját. 

Előfordulhat, hogy konfigurálnia kell a célerőforrást az alkalmazásból való hozzáférés engedélyezéséhez. Ha például jogkivonatot kér a Key Vault eléréséhez, meg kell győződnie arról, hogy hozzáadott egy hozzáférési szabályzatot, amely tartalmazza az alkalmazás identitását. Ellenkező esetben a Key Vault-hívások at elutasítjuk, még akkor is, ha tartalmazzák a jogkivonatot. Ha többet szeretne megtudni arról, hogy mely erőforrások támogatják az Azure Active Directory-tokeneket, olvassa el az [Azure AD-hitelesítést támogató Azure-szolgáltatások](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)című témakört.

> [!IMPORTANT]
> A felügyelt identitások háttérszolgáltatásai körülbelül 8 órán keresztül gyorsítótárat tartanak fenn erőforrás-URI-nként. Ha frissíti egy adott célerőforrás hozzáférési szabályzatát, és azonnal lekéri az adott erőforrás jogkivonatát, továbbra is lekérheti az elavult engedélyekkel rendelkező gyorsítótárazott jogkivonatot, amíg az a jogkivonat le nem jár. Jelenleg nincs mód a tokenfrissítés kényszerítésére.

Van egy egyszerű REST protokoll egy jogkivonat beszerzéséhez az App Service és az Azure Functions. Ez minden alkalmazáshoz és nyelvhez használható. A .NET és a Java esetében az Azure SDK absztrakciót biztosít a protokollon keresztül, és megkönnyíti a helyi fejlesztési élményt.

### <a name="using-the-rest-protocol"></a>A REST protokoll használata

A felügyelt identitással rendelkező alkalmazások két környezeti változót határoztak meg:

- IDENTITY_ENDPOINT – a helyi jogkivonat-szolgáltatás URL-címe.
- IDENTITY_HEADER – a kiszolgálóoldali kérelmek hamisításának (SSRF) elleni támadások csökkentésére szolgáló fejléc. Az értéket a platform elforgatja.

A **IDENTITY_ENDPOINT** egy helyi URL-cím, amelyből az alkalmazás jogkivonatokat kérhet. Egy erőforrás jogkivonatának lekéréséhez http GET-kérelmet kell benyújtania erre a végpontra, beleértve a következő paramétereket:

> | Paraméter neve    | A     | Leírás                                                                                                                                                                                                                                                                                                                                |
> |-------------------|--------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | Erőforrás          | Lekérdezés  | Az erőforrás Azure AD-erőforrás URI-ja, amelyhez jogkivonatot kell beszerezni. Ez lehet az Egyik [Azure-szolgáltatások, amelyek támogatják az Azure AD-hitelesítés](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) vagy bármely más erőforrás URI.    |
> | api-verzió       | Lekérdezés  | A jogkivonat API-t használni kell. Kérjük, használja a "2019-08-01" vagy újabb.                                                                                                                                                                                                                                                                 |
> | X-IDENTITY-FEJLÉC | Fejléc | A IDENTITY_HEADER környezeti változó értéke. Ez a fejléc a kiszolgálóoldali kérelmek hamisításának (SSRF) elleni támadások csökkentésére szolgál.                                                                                                                                                                                                    |
> | client_id         | Lekérdezés  | (Nem kötelező) A használandó felhasználó által hozzárendelt identitás ügyfélazonosítója. Nem használható olyan kérésre, `mi_res_id`amely `object_id`tartalmazza `principal_id`a , vagy a . Ha az összes azonosító`client_id` `principal_id`paraméter `object_id`( `mi_res_id`, , , és ) nincs megadva, a rendszer által hozzárendelt identitás lesz használatban.                                             |
> | principal_id      | Lekérdezés  | (Nem kötelező) A használandó felhasználó által hozzárendelt identitás fő azonosítója. `object_id`egy olyan alias, amelyet helyette használhat. Nem használható olyan kérésre, amely client_id, mi_res_id vagy object_id tartalmaz. Ha az összes azonosító`client_id` `principal_id`paraméter `object_id`( `mi_res_id`, , , és ) nincs megadva, a rendszer által hozzárendelt identitás lesz használatban. |
> | mi_res_id         | Lekérdezés  | (Nem kötelező) A felhasználó által hozzárendelt identitás Azure-erőforrásazonosítója. Nem használható olyan kérésre, `client_id`amely `object_id`tartalmazza `principal_id`a , vagy a . Ha az összes azonosító`client_id` `principal_id`paraméter `object_id`( `mi_res_id`, , , és ) nincs megadva, a rendszer által hozzárendelt identitás lesz használatban.                                      |

> [!IMPORTANT]
> Ha a felhasználó által hozzárendelt identitások jogkivonatait próbálja megszerezni, meg kell adnia a választható tulajdonságok egyikét. Ellenkező esetben a jogkivonat-szolgáltatás megkísérli a rendszer által hozzárendelt identitás jogkivonatának beszerzését, amely létezhet vagy nem létezik.

A sikeres 200 OK válasz tartalmaz egy JSON-törzset a következő tulajdonságokkal:

> | Tulajdonság neve | Leírás                                                                                                                                                                                                                                        |
> |---------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | access_token  | A kért hozzáférési jogkivonat. A hívó webszolgáltatás használhatja ezt a jogkivonatot a fogadó webszolgáltatás hitelesítéséhez.                                                                                                                               |
> | client_id     | A használt identitás ügyfélazonosítója.                                                                                                                                                                                                       |
> | expires_on    | A hozzáférési jogkivonat lejáratának időtartománya. A dátum az "1970-01-01T0:0:0Z UTC" (a jogkivonat jogcímének `exp` megfelelő) másodpercek számaként jelenik meg.                                                                                |
> | not_before    | A hozzáférési jogkivonat érvénybe lépésének és elfogadott időtartamának. A dátum az "1970-01-01T0:0:0Z UTC" (a jogkivonat jogcímének `nbf` megfelelő) másodpercek számaként jelenik meg.                                                      |
> | Erőforrás      | Az erőforrás, amelyhez hozzáférési jogkivonatot kértek, amely megfelel a `resource` kérelem lekérdezési karakterlánc paraméterének.                                                                                                                               |
> | token_type    | A token típusának értékét jelzi. Az Azure AD által támogatott egyetlen típus az FBearer. A tulajdonosi jogkivonatokról további információt [az OAuth 2.0 engedélyezési keretrendszer: Tulajdonosi jogkivonat-használat (RFC 6750) című](https://www.rfc-editor.org/rfc/rfc6750.txt)témakörben talál. |

Ez a válasz megegyezik [az Azure AD szolgáltatás-szolgáltatás hozzáférési jogkivonat-kérelemre adott válaszsal.](../active-directory/develop/v1-oauth2-client-creds-grant-flow.md#service-to-service-access-token-response)

> [!NOTE]
> A protokoll egy régebbi verziója a "2017-09-01" API-verzióval a fejlécet használta, `secret` `X-IDENTITY-HEADER` és csak a `clientid` felhasználó által hozzárendelt tulajdonságot fogadta el. Időbélyeg `expires_on` formátumban is visszaadta. MSI_ENDPOINT IDENTITY_ENDPOINT aliasaként is használható, MSI_SECRET pedig IDENTITY_HEADER aliasként.

### <a name="rest-protocol-examples"></a>Példák REST protokollra

Egy példakérés a következőkre néz ki:

```http
GET /MSI/token?resource=https://vault.azure.net&api-version=2019-08-01 HTTP/1.1
Host: localhost:4141
X-IDENTITY-HEADER: 853b9a84-5bfa-4b22-a3f3-0b9a43d9ad8a
```

A mintaválasz pedig a következőnek tűnhet:

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
    "access_token": "eyJ0eXAi…",
    "expires_on": "1586984735",
    "resource": "https://vault.azure.net",
    "token_type": "Bearer",
    "client_id": "5E29463D-71DA-4FE0-8E69-999B57DB23B0"
}
```

### <a name="code-examples"></a>Kódpéldák

# <a name="net"></a>[.NET](#tab/dotnet)

> [!TIP]
> A .NET nyelvek esetében a [Microsoft.Azure.Services.AppAuthentication](#asal) is használható a kérés saját kezű kivitelezése helyett.

```csharp
private readonly HttpClient _client;
// ...
public async Task<HttpResponseMessage> GetToken(string resource)  {
    var request = new HttpRequestMessage(HttpMethod.Get, 
        String.Format("{0}/?resource={1}&api-version=2019-08-01", Environment.GetEnvironmentVariable("IDENTITY_ENDPOINT"), resource));
    request.Headers.Add("X-IDENTITY-HEADER", Environment.GetEnvironmentVariable("IDENTITY_HEADER"));
    return await _client.SendAsync(request);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const rp = require('request-promise');
const getToken = function(resource, cb) {
    let options = {
        uri: `${process.env["IDENTITY_ENDPOINT"]}/?resource=${resource}&api-version=2019-08-01`,
        headers: {
            'X-IDENTITY-HEADER': process.env["IDENTITY_HEADER"]
        }
    };
    rp(options)
        .then(cb);
}
```

# <a name="python"></a>[Python](#tab/python)

```python
import os
import requests

identity_endpoint = os.environ["IDENTITY_ENDPOINT"]
identity_header = os.environ["IDENTITY_HEADER"]

def get_bearer_token(resource_uri):
    token_auth_uri = f"{identity_endpoint}?resource={resource_uri}&api-version=2019-08-01"
    head_msi = {'X-IDENTITY-HEADER':identity_header}

    resp = requests.get(token_auth_uri, headers=head_msi)
    access_token = resp.json()['access_token']

    return access_token
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$resourceURI = "https://<AAD-resource-URI-for-resource-to-obtain-token>"
$tokenAuthURI = $env:IDENTITY_ENDPOINT + "?resource=$resourceURI&api-version=2019-08-01"
$tokenResponse = Invoke-RestMethod -Method Get -Headers @{"X-IDENTITY-HEADER"="$env:IDENTITY_HEADER"} -Uri $tokenAuthURI
$accessToken = $tokenResponse.access_token
```

---

### <a name="using-the-microsoftazureservicesappauthentication-library-for-net"></a><a name="asal"></a>A Microsoft.Azure.Services.AppAuthentication könyvtár használata a .

A .NET-alkalmazások és -függvények esetében a felügyelt identitással való munka legegyszerűbb módja a Microsoft.Azure.Services.AppAuthentication csomag. Ez a könyvtár azt is lehetővé teszi, hogy tesztelje a kódot helyileg a fejlesztői gépen, a Visual Studio, az [Azure CLI](/cli/azure)vagy az Active Directory integrált hitelesítés felhasználói fiókjának használatával. A tár helyi fejlesztési lehetőségeiről a [Microsoft.Azure.Services.AppAuthentication hivatkozáscímű témakörben lehet további további adatokat.] Ez a szakasz bemutatja, hogyan kezdheti el a kódban lévő tárt.

1. Hivatkozások hozzáadása a [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) és bármely más szükséges NuGet csomagok az alkalmazáshoz. Az alábbi példa a [Microsoft.Azure.KeyVault programot](https://www.nuget.org/packages/Microsoft.Azure.KeyVault)is használja.

2. Adja hozzá a következő kódot az alkalmazáshoz, és módosítsa a megfelelő erőforrás t. Ez a példa az Azure Key Vault használatával való munka két módját mutatja be:

    ```csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;
    // ...
    var azureServiceTokenProvider = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://vault.azure.net");
    // OR
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
    ```

Ha többet szeretne megtudni a Microsoft.Azure.Services.AppAuthentication szolgáltatásról és az általa elérhetővé tett műveletekről, olvassa el a [Microsoft.Azure.Services.AppAuthentication hivatkozást,] valamint az [MSI .NET mintával rendelkező AppService és KeyVault című témakört.](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet)

### <a name="using-the-azure-sdk-for-java"></a>Az Azure SDK Java-hoz

Java-alkalmazások és -függvények esetén a felügyelt identitással való munka legegyszerűbb módja az [Azure SDK Java-hoz.](https://github.com/Azure/azure-sdk-for-java) Ez a szakasz bemutatja, hogyan kezdheti el a kódban lévő tárt.

1. Hivatkozás hozzáadása az [Azure SDK-könyvtárhoz.](https://mvnrepository.com/artifact/com.microsoft.azure/azure) Maven-projektek esetén hozzáadhatja ezt a `dependencies` kódrészletet a projekt POM-fájljának szakaszához:

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure</artifactId>
        <version>1.23.0</version>
    </dependency>
    ```

2. Használja `AppServiceMSICredentials` az objektumot a hitelesítéshez. Ez a példa bemutatja, hogyan használható ez a mechanizmus az Azure Key Vault használatával való együttműködéshez:

    ```java
    import com.microsoft.azure.AzureEnvironment;
    import com.microsoft.azure.management.Azure;
    import com.microsoft.azure.management.keyvault.Vault
    //...
    Azure azure = Azure.authenticate(new AppServiceMSICredentials(AzureEnvironment.AZURE))
            .withSubscription(subscriptionId);
    Vault myKeyVault = azure.vaults().getByResourceGroup(resourceGroup, keyvaultName);

    ```


## <a name="remove-an-identity"></a><a name="remove"></a>Identitás eltávolítása

A rendszer által hozzárendelt identitás eltávolítható a szolgáltatás letiltásával a portálon, a PowerShell vagy a CLI használatával ugyanúgy, ahogy létrehozták. A felhasználó által hozzárendelt identitások egyenként is eltávolíthatók. Az összes identitás eltávolításához állítsa a típust "Nincs" beállításra az [ARM sablonban:](#using-an-azure-resource-manager-template)

```json
"identity": {
    "type": "None"
}
```

A rendszer által hozzárendelt identitás ily módon eltávolítja azt is törli az Azure AD-ből. A rendszer által hozzárendelt identitások is automatikusan törlődnek az Azure AD-ből, amikor az alkalmazás-erőforrás törlődik.

> [!NOTE]
> Van is egy alkalmazás beállítás, amely beállítható, WEBSITE_DISABLE_MSI, amely csak letiltja a helyi jogkivonat-szolgáltatás. Azonban az identitás a helyén marad, és az eszközkezelés továbbra is "on" vagy "enabled" formában jeleníti meg a felügyelt identitást. Ennek eredményeképpen nem ajánlott ezt a beállítást használni.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Access SQL Database biztonságosan felügyelt identitás használatával](app-service-web-tutorial-connect-msi.md)

[Microsoft.Azure.Services.AppAuthentication – referencia]: https://go.microsoft.com/fwlink/p/?linkid=862452
