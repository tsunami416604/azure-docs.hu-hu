---
title: Az App Service és az Azure Functions-identitás |} Microsoft Docs
description: A kezelt Service identitás támogatása az Azure App Service és az Azure Functions fogalmi hivatkozást és a telepítési útmutatója
services: app-service
author: mattchenderson
manager: cfowler
editor: ''
ms.service: app-service
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 06/25/2018
ms.author: mahender
ms.openlocfilehash: 8305a447ac75cf4c72a332910c9c4c90c1d8eac6
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37061437"
---
# <a name="how-to-use-azure-managed-service-identity-in-app-service-and-azure-functions"></a>Azure által felügyelt Szolgáltatásidentitás App Service és az Azure Functions használatával

> [!NOTE] 
> Linux és a tárolók webalkalmazást az App Service jelenleg nem támogatják a Szolgáltatásidentitás felügyelt.

> [!Important] 
> Az App Service és az Azure Functions felügyelt Szolgáltatásidentitás nem várakozásoknak megfelelően működik, ha az alkalmazás előfizetések/bérlők keresztül telepítik át. Az alkalmazás kell szereznie egy új identitásra, amely letiltásával és újbóli engedélyezésével végezhető. Lásd: [identitásának eltávolítása](#remove) alatt. Alsóbb rétegbeli erőforrásokat is kell rendelkeznie a hozzáférési házirendek frissítése az új identitás használatára.

Ez a témakör bemutatja, hogyan hoz létre egy felügyelt alkalmazást az App Service és az Azure Functions alkalmazásokhoz és egyéb erőforrásainak elérésére használatával. Az Azure Active Directory felügyelt szolgáltatásidentitás lehetővé teszi az alkalmazás egyszerűen hozzáférhessenek más AAD által védett erőforrások, például az Azure Key Vault. Az azonosító az Azure platform kezeli, és nem kell kiosztania vagy a titkos kulcsok elforgatása. Felügyelt Szolgáltatásidentitás kapcsolatban bővebben lásd: a [Szolgáltatásidentitás felügyelete – áttekintés](../active-directory/managed-service-identity/overview.md).

## <a name="creating-an-app-with-an-identity"></a>Azonosító adatok az alkalmazások létrehozása

Egy további tulajdonsággal állítható be az alkalmazást egy alkalmazás létrehozásához identitással van szükség.

### <a name="using-the-azure-portal"></a>Az Azure Portal használata

A portálon kezelt szolgáltatásidentitás beállításához először létrehoz egy alkalmazást a szokásos módon és majd engedélyezze a szolgáltatást.

1. Alkalmazás létrehozása a portálon, a szokásos módon. Keresse meg azt a portálon.

2. Ha egy függvény alkalmazást használ, lépjen **Platform funkciói**. Egyéb alkalmazás esetében, görgessen le a **beállítások** csoportot a bal oldali navigációs.

3. Válassza ki **identitás**.

4. Kapcsoló **az Azure Active Directoryban regisztrálni** való **a**. Kattintson a **Save** (Mentés) gombra.

![Az App Service-identitás](media/app-service-managed-service-identity/msi-blade.png)

### <a name="using-the-azure-cli"></a>Az Azure parancssori felületének használata

Az Azure parancssori felület használatával felügyelt szolgáltatásidentitás beállításához kell használni a `az webapp identity assign` parancs egy meglévő alkalmazást. Ez a szakasz a példák futó három lehetőség közül választhat:

- Használjon [Azure Cloud rendszerhéj](../cloud-shell/overview.md) Azure-portálról.
- A beágyazott Azure Cloud rendszerhéj a "próbálja" gombra, az alábbi kódrészletet jobb felső sarkában található keresztül használja.
- [Telepítse a legújabb verziót a CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.31 vagy újabb verzió) Ha a helyi CLI-konzollal szeretné. 

Az alábbi lépéseket végigvezeti hoz létre egy webalkalmazást, és azt a parancssori felület használatával identitás:

1. Az Azure parancssori felület a helyi konzol használata, először jelentkezzen be az Azure használatával [az bejelentkezési](/cli/azure/reference-index#az_login). Egy fiók, amelybe szeretne telepíteni az alkalmazást az Azure-előfizetéshez társított használata:

    ```azurecli-interactive
    az login
    ```
2. Hozzon létre egy webalkalmazást, a parancssori felület használatával. A CLI használata az App Service további példákért lásd [App Service CLI minták](../app-service/app-service-cli-samples.md):

    ```azurecli-interactive
    az group create --name myResourceGroup --location westus
    az appservice plan create --name myPlan --resource-group myResourceGroup --sku S1
    az webapp create --name myApp --resource-group myResourceGroup --plan myPlan
    ```

3. Futtassa a `identity assign` parancsot az alkalmazás identitását létrehozásához:

    ```azurecli-interactive
    az webapp identity assign --name myApp --resource-group myResourceGroup
    ```

### <a name="using-azure-powershell"></a>Az Azure PowerShell használata

Az alábbi lépéseket végigvezeti egy webalkalmazás létrehozása és hozzárendelése az Azure PowerShell használatával identitás:

1. Szükség esetén telepítse az Azure PowerShellt az [Azure PowerShell útmutatójának](/powershell/azure/overview) utasításait követve, majd a `Login-AzureRmAccount` futtatásával hozza létre a kapcsolatot az Azure-ral.

2. Az Azure PowerShell-webalkalmazás létrehozása. Az App Service Azure PowerShell használatával további példákért lásd [App Service PowerShell-példák](../app-service/app-service-powershell-samples.md):

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzureRmResourceGroup -Name myResourceGroup -Location $location
    
    # Create an App Service plan in Free tier.
    New-AzureRmAppServicePlan -Name $webappname -Location $location -ResourceGroupName myResourceGroup -Tier Free
    
    # Create a web app.
    New-AzureRmWebApp -Name $webappname -Location $location -AppServicePlan $webappname -ResourceGroupName myResourceGroup
    ```

3. Futtassa a `identity assign` parancsot az alkalmazás identitását létrehozásához:

    ```azurepowershell-interactive
    Set-AzureRmWebApp -AssignIdentity $true -Name $webappname -ResourceGroupName myResourceGroup 
    ```

### <a name="using-an-azure-resource-manager-template"></a>Azure Resource Manager-sablonnal

Az Azure Resource Manager-sablon segítségével automatizálhatja az Azure-erőforrások telepítése. App Service és a funkciók telepítésével kapcsolatos további tudnivalókért lásd: [az App Service erőforrás telepítés automatizálásáról](../app-service/app-service-deploy-complex-application-predictably.md) és [erőforrások telepítése az Azure Functions automatizálása](../azure-functions/functions-infrastructure-as-code.md).

Bármely típusú erőforrást `Microsoft.Web/sites` hozhat létre egy identitás többek között a következő tulajdonság az erőforrás-definícióban:
```json
"identity": {
    "type": "SystemAssigned"
}    
```

Ez alapján Azure létrehozásához és kezeléséhez az alkalmazás identitását.

Például a webes alkalmazás nézhet ki például a következőket:
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
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

Ha `<TENANTID>` és `<PRINCIPALID>` váltják fel GUID. A tenantId tulajdonság tartozik, az identitás milyen AAD-bérlőt azonosítja. A principalId új Alkalmazásidentitás egyedi azonosítója. Belül aad-ben, az egyszerű szolgáltatásnév az azonos névvel rendelkezik, amelynek az App Service vagy az Azure Functions példányához adott.

## <a name="obtaining-tokens-for-azure-resources"></a>Az Azure-erőforrások tokenek beszerzése

Egy alkalmazás felhasználhat az identitása más erőforrásainak védelméhez azzal, például az Azure Key Vault az AAD-jogkivonat. Ezeket a jogkivonatokat generáló az erőforrás, és nem adott felhasználó az alkalmazás eléréséhez. 

> [!IMPORTANT]
> Szükség lehet a célerőforrás számára engedélyezzék az alkalmazás konfigurálásához. Például egy tokent a Key Vault kér le, ha szeretné ellenőrizze, hogy hozzáadta a hozzáférési házirend, amely tartalmazza az alkalmazás azonosítóját. Ellenkező esetben a Key Vault hívásainak elutasításra kerül, még akkor is, ha a jogkivonat tartalmaznak. További erőforrások kapcsolatos Szolgáltatásidentitás felügyelt jogkivonatok támogatási kapcsolatban lásd: [Azure-szolgáltatások, hogy támogatja az Azure AD hitelesítési](../active-directory/managed-service-identity/overview.md#which-azure-services-support-managed-service-identity).

Nincs az App Service és az Azure Functions jogkivonat beszerzése az egyszerű REST protokoll. .NET-alkalmazások a Microsoft.Azure.Services.AppAuthentication könyvtár absztrakciós biztosít a protokollon keresztül, és egy helyi fejlesztési felület támogatja.

### <a name="asal"></a>A Microsoft.Azure.Services.AppAuthentication könyvtár használata a .NET-hez

A .NET-alkalmazások és a funkciók felügyelt szolgáltatásidentitás együttműködve legegyszerűbb módja a Microsoft.Azure.Services.AppAuthentication csomag keresztül van. Ebben a könyvtárban is lehetővé teszi a tesztelheti a kódját helyileg a fejlesztői gépen, a felhasználói fiókkal a Visual Studio eszközből a [Azure CLI 2.0](https://docs.microsoft.com/cli/azure?view=azure-cli-latest), vagy az Active Directory integrált hitelesítést. Ezt a szalagtárat a helyi fejlesztési lehetőségek bővebben lásd: a [Microsoft.Azure.Services.AppAuthentication reference]. Ez a szakasz bemutatja, hogyan lásson a könyvtárban, a kódban.

1. Hivatkozásokat adni a [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) és [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) NuGet-csomagok, hogy az alkalmazást.

2.  Adja hozzá a következő kódot az alkalmazásba:

```csharp
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.Azure.KeyVault;
// ...
var azureServiceTokenProvider = new AzureServiceTokenProvider();
string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://management.azure.com/");
// OR
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
```

Microsoft.Azure.Services.AppAuthentication és érheti el a műveletek kapcsolatos további tudnivalókért tekintse meg a [Microsoft.Azure.Services.AppAuthentication reference] és a [App Service és a MSI .NET KeyVault a minta](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet).

### <a name="using-the-rest-protocol"></a>A REST-protokollal

Egy alkalmazást a felügyelt szolgáltatásidentitás van két környezeti változókkal:
- MSI_ENDPOINT
- MSI_SECRET

A **MSI_ENDPOINT** , amelyből az alkalmazás jogkivonatokat kérhetnek helyi URL-cím. Szolgáltatáshitelesítést egy token-erőforrásokhoz, hogy HTTP GET kérelemre ehhez a végponthoz, beleértve a következő paraméterekkel:

> [!div class="mx-tdBreakAll"]
> |Paraméter neve|Eleme ennek|Leírás|
> |-----|-----|-----|
> |erőforrás|Lekérdezés|Az aad-ben erőforrás az erőforrás URI azonosítója a jogkivonat meg kell kapott.|
> |API-verzió|Lekérdezés|A token API használt verziója. "2017-09-01" jelenleg az egyetlen támogatott verzió.|
> |titkos kód|Fejléc|A MSI_SECRET környezeti változó értékét.|


A sikeres 200 OK válasz tartalmazza a következő tulajdonságokkal egy JSON-törzsére:

> [!div class="mx-tdBreakAll"]
> |Tulajdonság neve|Leírás|
> |-------------|----------|
> |access_token|A kért hozzáférési jogkivonat. A hívó webszolgáltatás a jogkivonat segítségével hitelesíti a fogadó webszolgáltatás.|
> |expires_on|A hozzáférési jogkivonat lejárati idejének. A dátum jelzi másodpercben a 1970-01-01T0:0:0Z UTC, amíg az elévülési időt. Ezt az értéket a gyorsítótárazott jogkivonatok élettartama meghatározására szolgál.|
> |erőforrás|A fogadó webszolgáltatás App ID URI.|
> |token_type|A jogkivonat típusa értékét jelöli. A csak az Azure AD támogató típus tulajdonosi. Tulajdonosi jogkivonatok kapcsolatos további információkért lásd: [az OAuth 2.0 hitelesítési keretrendszer: tulajdonosi jogkivonat használati (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt).|


Ez a válasz megegyezik a [az AAD szolgáltatás hozzáférési jogkivonat kérelemre adott válasz](../active-directory/develop/active-directory-protocols-oauth-service-to-service.md#service-to-service-access-token-response).

> [!NOTE] 
> Környezeti változók vannak beállítva indításakor a folyamat, így Szolgáltatásidentitás felügyelt alkalmazás engedélyezése után szükség lehet az alkalmazás újraindítása, vagy telepítse újra a kódot, mielőtt `MSI_ENDPOINT` és `MSI_SECRET` elérhetők a kódot.

### <a name="rest-protocol-examples"></a>REST-protokoll példák
Egy példa egy kérelem a következőhöz hasonló lehet:
```
GET /MSI/token?resource=https://vault.azure.net&api-version=2017-09-01 HTTP/1.1
Host: localhost:4141
Secret: 853b9a84-5bfa-4b22-a3f3-0b9a43d9ad8a
```
És egy mintaválasz nézhet ki például a következőket:
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

### <a name="code-examples"></a>Kódpéldák
<a name="token-csharp"></a>A kérelem végrehajtásához a C#:
```csharp
public static async Task<HttpResponseMessage> GetToken(string resource, string apiversion)  {
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Add("Secret", Environment.GetEnvironmentVariable("MSI_SECRET"));
    return await client.GetAsync(String.Format("{0}/?resource={1}&api-version={2}", Environment.GetEnvironmentVariable("MSI_ENDPOINT"), resource, apiversion));
}
```
> [!TIP]
> .NET-nyelveket is használhatja [Microsoft.Azure.Services.AppAuthentication](#asal) helyett ez létrehozásával kérelem magát.

<a name="token-js"></a>A node.js:
```javascript
const rp = require('request-promise');
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

<a name="token-powershell"></a>A PowerShell:
```powershell
$apiVersion = "2017-09-01"
$resourceURI = "https://<AAD-resource-URI-for-resource-to-obtain-token>"
$tokenAuthURI = $env:MSI_ENDPOINT + "?resource=$resourceURI&api-version=$apiVersion"
$tokenResponse = Invoke-RestMethod -Method Get -Headers @{"Secret"="$env:MSI_SECRET"} -Uri $tokenAuthURI
$accessToken = $tokenResponse.access_token
```

## <a name="remove"></a>Identitás eltávolítása

A funkciót, a portálon, a PowerShell vagy a parancssori felület használatával, készült ugyanúgy letiltásával identitás távolíthatja el. A többi/ARM-sablon protokoll ez történik, a típus a "None" értékre állításával:

```json
"identity": {
    "type": "None"
}    
```

Eltávolítása a identitás ily módon is törli a rendszerbiztonsági tag az aad-ben. Alapértelmezett identitások automatikusan törlődik az AAD, az alkalmazás-erőforrást törlésekor.

> [!NOTE] 
> Alkalmazás beállítása is be lehet állítani, WEBSITE_DISABLE_MSI, amely csak letiltja a helyi jogkivonat-szolgáltatás is van. Azonban elhagyja az identitás helyen, és tooling továbbra is megjeleníti MSI mint "on" vagy "engedélyezett". Ennek köszönhetően ez a beállítás használata nem recommmended.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Biztonságos hozzáférés az SQL Database-hez felügyelt szolgáltatásidentitás használatával](app-service-web-tutorial-connect-msi.md)

[Microsoft.Azure.Services.AppAuthentication reference]: https://go.microsoft.com/fwlink/p/?linkid=862452
