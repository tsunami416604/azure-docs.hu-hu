---
author: erhopf
ms.author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 07/23/2019
ms.openlocfilehash: 8754504655cdd08c9bf9f89311cb6c5d1057f0e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78262267"
---
## <a name="authenticate-with-azure-active-directory"></a>Hitelesítés az Azure Active Directoryval

> [!IMPORTANT]
> 1. Jelenleg **csak** a Computer Vision API, Face API, Text Analytics API, Immersive Reader, Form Recognizer, Anomaly Detector és az összes Bing-szolgáltatás, kivéve a Bing egyéni keresés támogatás hitelesítése az Azure Active Directory (AAD) használatával.
> 2. Az AAD-hitelesítést mindig együtt kell használni az Azure-erőforrás egyéni altartománynevével együtt. [A regionális végpontok](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains#is-there-a-list-of-regional-endpoints) nem támogatják az AAD-hitelesítést.

Az előző szakaszokban megmutattuk, hogyan hitelesítheti magát az Azure Cognitive Services szolgáltatással egy szolgáltatásból vagy többszolgáltatásos előfizetési kulccsal. Bár ezek a kulcsok gyors és egyszerű utat biztosítanak a fejlesztés megkezdéséhez, a szerepköralapú hozzáférés-vezérlést igénylő összetettebb forgatókönyvekben elmaradnak. Vessünk egy pillantást, mi szükséges a hitelesítéshez az Azure Active Directory (AAD) használatával.

A következő szakaszokban az Azure Cloud Shell-környezet vagy az Azure CLI használatával hozzon létre egy altartományt, rendeljen hozzá szerepköröket, és szerezzen be egy tulajdonosi jogkivonatot az Azure Cognitive Services hívásához. Ha elakad, az egyes szakaszokban hivatkozások találhatók az Azure Cloud Shell/Azure CLI minden egyes parancsához elérhető összes beállítással.

### <a name="create-a-resource-with-a-custom-subdomain"></a>Erőforrás létrehozása egyéni altartománnyal

Az első lépés egy egyéni altartomány létrehozása. Ha olyan meglévő Cognitive Services-erőforrást szeretne használni, amely nem rendelkezik egyéni altartománynévvel, kövesse a [Cognitive Services egyéni altartományok utasításait](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains#how-does-this-impact-existing-resources) az erőforrás egyéni altartományának engedélyezéséhez.

1. Kezdje az Azure Cloud Shell megnyitásával. Ezután [válasszon egy előfizetést:](https://docs.microsoft.com/powershell/module/az.accounts/set-azcontext?view=azps-3.3.0)

   ```powershell-interactive
   Set-AzContext -SubscriptionName <SubscriptionName>
   ```

2. Ezután [hozzon létre egy Cognitive Services-erőforrást](https://docs.microsoft.com/powershell/module/az.cognitiveservices/new-azcognitiveservicesaccount?view=azps-1.8.0) egy egyéni altartománnyal. Az altartomány nevének globálisan egyedinek kell lennie, és nem tartalmazhat speciális karaktereket, például: ".","!"!", ",".

   ```powershell-interactive
   New-AzCognitiveServicesAccount -ResourceGroupName <RESOURCE_GROUP_NAME> -name <ACCOUNT_NAME> -Type <ACCOUNT_TYPE> -SkuName <SUBSCRIPTION_TYPE> -Location <REGION> -CustomSubdomainName <UNIQUE_SUBDOMAIN>
   ```

3. Ha sikeres, a **végpontnak** meg kell jelennie az erőforrás egyedi altartománynevének.


### <a name="assign-a-role-to-a-service-principal"></a>Szerepkör hozzárendelése egyszerű szolgáltatáshoz

Most, hogy rendelkezik egy egyéni altartománnyal az erőforráshoz társítva, szerepkört kell rendelnie egy egyszerű szolgáltatáshoz.

> [!NOTE]
> Ne feledje, hogy az AAD szerepkör-hozzárendelések propagálása akár öt percet is igénybe vehet.

1. Először is regisztráljunk egy [AAD alkalmazást.](https://docs.microsoft.com/powershell/module/Az.Resources/New-AzADApplication?view=azps-1.8.0)

   ```powershell-interactive
   $SecureStringPassword = ConvertTo-SecureString -String <YOUR_PASSWORD> -AsPlainText -Force

   New-AzADApplication -DisplayName <APP_DISPLAY_NAME> -IdentifierUris <APP_URIS> -Password $SecureStringPassword
   ```

   Szüksége lesz az **ApplicationId-re** a következő lépésben.

2. Ezután létre kell [hoznia egy egyszerű szolgáltatás](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal?view=azps-1.8.0) az AAD-alkalmazáshoz.

   ```powershell-interactive
   New-AzADServicePrincipal -ApplicationId <APPLICATION_ID>
   ```

   >[!NOTE]
   > Ha regisztrál egy alkalmazást az Azure Portalon, ez a lépés befejeződik az Ön számára.

3. Az utolsó lépés az, hogy [rendelje hozzá a "Cognitive Services User" szerepkört](https://docs.microsoft.com/powershell/module/az.Resources/New-azRoleAssignment?view=azps-1.8.0) a szolgáltatásnévhez (hatóköre az erőforráshoz). Egy szerepkör hozzárendelésével egyszerű szolgáltatáshozzáférést biztosít ehhez az erőforráshoz. Ugyanazt a szolgáltatásalapú hozzáférést biztosíthatja az előfizetés több erőforrásához.
   >[!NOTE]
   > A szolgáltatásnév ObjectId ja, nem az alkalmazás ObjectId azonosítója.
   > A ACCOUNT_ID a létrehozott Cognitive Services-fiók Azure-erőforrásazonosítója lesz. Az Azure Portalon az Azure-portál "tulajdonságai" alapján megtalálhatja az Azure-erőforrás-azonosítót.

   ```azurecli-interactive
   New-AzRoleAssignment -ObjectId <SERVICE_PRINCIPAL_OBJECTID> -Scope <ACCOUNT_ID> -RoleDefinitionName "Cognitive Services User"
   ```

### <a name="sample-request"></a>Mintakérelem

Ebben a példában egy jelszó a szolgáltatásnév hitelesítéséhez szolgál. A megadott jogkivonatezután a Computer Vision API hívására szolgál.

1. Szerezd meg **tenantId:**
   ```powershell-interactive
   $context=Get-AzContext
   $context.Tenant.Id
   ```

2. Token beszerezni:
   ```powershell-interactive
   $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList "https://login.windows.net/<TENANT_ID>"
   $secureSecretObject = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.SecureClientSecret" -ArgumentList $SecureStringPassword   
   $clientCredential = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential" -ArgumentList $app.ApplicationId, $secureSecretObject
   $token=$authContext.AcquireTokenAsync("https://cognitiveservices.azure.com/", $clientCredential).Result
   $token
   ```
3. Hívja a Computer Vision API-t:
   ```powershell-interactive
   $url = $account.Endpoint+"vision/v1.0/models"
   $result = Invoke-RestMethod -Uri $url  -Method Get -Headers @{"Authorization"=$token.CreateAuthorizationHeader()} -Verbose
   $result | ConvertTo-Json
   ```

Másik lehetőségként a szolgáltatásnév hitelesíthető egy tanúsítvánnyal. A szolgáltatásnév mellett a felhasználó egyszerű felhasználót is támogatja egy másik AAD-alkalmazáson keresztül delegált engedélyek. Ebben az esetben a jelszavak vagy tanúsítványok helyett a felhasználók kétfaktoros hitelesítést kérnek a jogkivonat beolvasásakor.
