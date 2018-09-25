---
title: Az Azure Active Directory-hitelesítés és a Resource Manager |} A Microsoft Docs
description: Fejlesztői útmutató az alkalmazás integrálása más Azure-előfizetések az Azure Resource Manager API-hoz és az Azure Active Directory-hitelesítés.
services: azure-resource-manager,active-directory
documentationcenter: na
author: dushyantgill
manager: timlt
editor: tysonn
ms.assetid: 17b2b40d-bf42-4c7d-9a88-9938409c5088
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/12/2018
ms.author: dugill
ms.openlocfilehash: b841a1104a0cc1e74d9ab1f16ef39d3892ba7d55
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46996689"
---
# <a name="use-resource-manager-authentication-api-to-access-subscriptions"></a>Erőforrás-kezelő használata hitelesítési API az előfizetések hozzáféréséhez
## <a name="introduction"></a>Bevezetés
Ha Ön egy szoftverfejlesztő, aki létre kell hoznia egy alkalmazást, amely felügyeli az ügyfél Azure-erőforrások, a Ez a cikk bemutatja, hogyan hitelesítéséhez az Azure Resource Manager API-khoz, és más előfizetésekben erőforrásokhoz férhetnek hozzá.

Az alkalmazás hozzáférhessen a Resource Manager API-k több módon:

1. **Felhasználó + alkalmazás-hozzáférés**: bejelentkezett felhasználó nevében erőforrásokhoz hozzáférő alkalmazások esetében. Ez a megközelítés alkalmazások, például a web apps és a parancssori eszközöket, amelyek csak "interaktív felügyeleti" Azure-erőforrások kezelésére használható.
2. **Csak az alkalmazásra vonatkozó hozzáférési**: démonszolgáltatásokat vagy ütemezett feladatokat futtató alkalmazásokhoz. Az alkalmazás identitását közvetlen hozzáférést az erőforrásokhoz. Ez a megközelítés az Azure-bA hosszú távú távfelügyelt (felügyelet) hozzáférést igénylő alkalmazások esetében működik.

Ez a cikk részletes utasításokat követve hozzon létre egy alkalmazást, amely a két hitelesítési módszert alkalmaz. Ez bemutatja, hogyan szeretné végrehajtani a műveletet a REST API vagy a C#. A teljes ASP.NET MVC alkalmazás mindig elérhető legyen [ https://github.com/dushyantgill/VipSwapper/tree/master/CloudSense ](https://github.com/dushyantgill/VipSwapper/tree/master/CloudSense).

## <a name="what-the-web-app-does"></a>A webes alkalmazás leírása
A webalkalmazás:

1. Jelentkezik be egy Azure-felhasználó.
2. Megkérdezi a felhasználót, hogy a webalkalmazásokhoz való hozzáférés biztosítása a Resource Managerhez.
3. Resource Manager elérése felhasználó + alkalmazás-hozzáférési token beolvasása.
4. Használja a jogkivonatot (a 3. lépés) az alkalmazás egyszerű szolgáltatás hozzárendelése egy szerepkört az előfizetésben. Ebben a lépésben az alkalmazás hosszú távú hozzáférést biztosít az előfizetéshez.
5. Csak az alkalmazásra vonatkozó hozzáférési token beolvasása.
6. Használja a jogkivonatot (az 5. lépés) az előfizetés Resource Manager-erőforrások kezeléséhez.

Ez a folyamat a webalkalmazás.

![Erőforrás-kezelő hitelesítési folyamat](./media/resource-manager-api-authentication/Auth-Swim-Lane.png)

Egy felhasználó a használni kívánt előfizetés adja meg az előfizetés-azonosító:

![Adja meg az előfizetés-azonosító](./media/resource-manager-api-authentication/sample-ux-1.png)

Válassza ki a fiókkal jelentkezik be a.

![fiók kiválasztása](./media/resource-manager-api-authentication/sample-ux-2.png)

Adja meg a hitelesítő adatokat.

![hitelesítő adatok megadása](./media/resource-manager-api-authentication/sample-ux-3.png)

Az alkalmazás hozzáférést az Azure-előfizetést:

![Hozzáférés biztosítása](./media/resource-manager-api-authentication/sample-ux-4.png)

A csatlakoztatott előfizetések kezelése:

![Előfizetés csatlakoztatása](./media/resource-manager-api-authentication/sample-ux-7.png)

## <a name="register-application"></a>Alkalmazás regisztrálása
Mielőtt elkezdené, kódolás, webes alkalmazás regisztrálása az Azure Active Directory (AD). Az alkalmazás regisztrációját az alkalmazás központi azonosítót hoz létre az Azure ad-ben. Alapszintű információkat az alkalmazásról, mint például az OAuth-Ügyfélazonosító, a válasz URL-címek és a hitelesítő adatok, az alkalmazás által használt hitelesítés és Azure Resource Manager API-k elérése akkor tárolja. Az alkalmazás regisztrációját is rögzíti a különböző delegált engedélyeket az alkalmazása szükséges, a Microsoft APIs elérésekor a felhasználó nevében.

Mivel az alkalmazás más előfizetésben hozzáfér, mint egy több-bérlős alkalmazást kell konfigurálnia. Érvényesítési átadni, adja meg egy társított az Azure Active Directory-tartományhoz. A tartományok az Azure Active Directory társított megtekintéséhez jelentkezzen be a portálra.

Az alábbi példa bemutatja, hogyan regisztrálja az alkalmazás az Azure PowerShell-lel. A parancs működéséhez az Azure PowerShell legújabb (2016. augusztus) verzióját kell telepítenie.

```azurepowershell-interactive
$app = New-AzureRmADApplication -DisplayName "{app name}" -HomePage "https://{your domain}/{app name}" -IdentifierUris "https://{your domain}/{app name}" -Password "{your password}" -AvailableToOtherTenants $true
```

Jelentkezzen be az AD-alkalmazást, szüksége van az alkalmazás Azonosítóját és jelszavát. Az Alkalmazásazonosítót az előző parancs által visszaadott használja:

```azurepowershell-interactive
$app.ApplicationId
```

Az alábbi példa bemutatja, hogyan az alkalmazás regisztrálása az Azure parancssori felület használatával.

```azurecli-interactive
az ad app create --display-name {app name} --homepage https://{your domain}/{app name} --identifier-uris https://{your domain}/{app name} --password {your password} --available-to-other-tenants true
```

Az eredmények tartalmazzák a AppId, amelyre akkor van szükség, ahol az alkalmazás hitelesítéséhez.

### <a name="optional-configuration---certificate-credential"></a>Választható konfiguráció – tanúsítványalapú hitelesítő adatot
Azure ad-ben is támogatja a tanúsítványalapú alkalmazásokhoz: hozzon létre egy önaláírt tanúsítvány, a titkos kulcs tároljuk, és a nyilvános kulcs hozzáadása az Azure AD-alkalmazás regisztrációja. A hitelesítéshez az alkalmazás Azure ad-hez, a titkos kulcs használatával aláírt küld el egy kis terhelés, és az Azure AD ellenőrzi az aláírást a nyilvános kulccsal, hogy regisztrált.

Egy tanúsítványt az AD-alkalmazás létrehozásával kapcsolatos információkért lásd: [erőforrások eléréséhez az egyszerű szolgáltatás létrehozása az Azure PowerShell használatával](resource-group-authenticate-service-principal.md#create-service-principal-with-certificate-from-certificate-authority) vagy [erőforrások eléréséhez egy szolgáltatásnév létrehozásához használható Azure CLI](resource-group-authenticate-service-principal-cli.md) .

## <a name="get-tenant-id-from-subscription-id"></a>Az előfizetés-azonosító Bérlőazonosító beszerzése
A kérelem egy jogkivonatot, amely a Resource Manager hívja használhatók, az alkalmazás tudnia kell, a bérlő Azonosítóját az Azure AD-bérlő, amelyen az Azure-előfizetés. Nagy valószínűséggel a felhasználók tudják, hogy az előfizetés azonosítókat, de előfordulhat, hogy nem ismerik a bérlő azonosítók az Azure Active Directory. A felhasználó-Bérlőazonosító beszerzése, kérje meg a felhasználó számára az előfizetés azonosítóját. Adja meg az előfizetés AZONOSÍTÓJÁT az előfizetéssel kapcsolatos kérés küldésekor:

    https://management.azure.com/subscriptions/{subscription-id}?api-version=2015-01-01

A kérelem meghiúsul, mert a felhasználó nem jelentkezett még, de a válasz lehet lekérdezni a bérlő Azonosítóját. Az adott kivétel beolvasni a bérlő Azonosítóját a válasz fejléc értéke **WWW-Authenticate**. Ezt a megvalósítást, az megjelenik a [GetDirectoryForSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L20) metódust.

## <a name="get-user--app-access-token"></a>Felhasználó + alkalmazás-hozzáférési jogkivonat beszerzése
Az alkalmazás átirányítja a felhasználót az OAuth 2.0 engedélyezése kérelem - hitelesítéshez a felhasználó hitelesítő adatait, majd az engedélyezési kódot az Azure ad-hez. Az alkalmazás az engedélyezési kód használatával hozzáférési token lekérése a Resource Managerhez. A [ConnectSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/Controllers/HomeController.cs#L42) metódus az engedélyezési kérést hoz létre.

Ez a cikk bemutatja a REST API-kérelem a felhasználó hitelesítéséhez. Segítő kódtárak használatával hitelesítést végezni a kódban. Ezek a kódtárak kapcsolatos további információkért lásd: [Azure Active Directory Authentication Libraries](../active-directory/active-directory-authentication-libraries.md). Identitáskezelés az alkalmazások integrálásával kapcsolatos útmutatásért lásd: [Azure Active Directory fejlesztői útmutatója](../active-directory/develop/azure-ad-developers-guide.md).

### <a name="auth-request-oauth-20"></a>Hitelesítési kérelem (OAuth 2.0)
Egy nyílt ID Connect/OAuth2.0 engedélyezése kérelem kiadása az Azure AD-hitelesítési végpontra:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize

A lekérdezési karakterlánc paraméterei a kérelem rendelkezésre álló ismertetett a [hozzáférési kód kérése](../active-directory/develop/v1-protocols-oauth-code.md#request-an-authorization-code) cikk.

Az alábbi példa bemutatja, hogyan OAuth2.0 engedélyezési kérése:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize?client_id=a0448380-c346-4f9f-b897-c18733de9394&response_mode=query&response_type=code&redirect_uri=http%3a%2f%2fwww.vipswapper.com%2fcloudsense%2fAccount%2fSignIn&resource=https%3a%2f%2fgraph.windows.net%2f&domain_hint=live.com

Az Azure AD akkor hitelesíti a felhasználót, és, ha szükséges, megkérdezi a felhasználót, hogy adjon engedélyt az alkalmazás. Az engedélyezési kódot adja vissza, az alkalmazás válasz URL-címre. Attól függően, a kért response_mode, az Azure ad-ben vagy küld vissza az adatok a lekérdezési karakterláncban vagy post adatként.

    code=AAABAAAAiL****FDMZBUwZ8eCAA&session_state=2d16bbce-d5d1-443f-acdf-75f6b0ce8850

### <a name="auth-request-open-id-connect"></a>Hitelesítési kérelem (Open ID Connect)
Ha nem csak az Azure Resource Manager eléréséhez a felhasználó nevében szeretne, de is engedélyezi, hogy a felhasználó jelentkezzen be az alkalmazás használatával az Azure AD-fiókja, ki egy nyílt azonosítója csatlakozás engedélyezése kérése. Az Open ID Connect az alkalmazás is fogad id_token, amely az alkalmazás használhatja a bejelentkezni a felhasználó Azure AD-ből.

A lekérdezési karakterlánc paraméterei a kérelem rendelkezésre álló ismertetett a [a bejelentkezési kérelem elküldéséhez](../active-directory/develop/v1-protocols-openid-connect-code.md#send-the-sign-in-request) cikk.

Van egy Open ID Connect kérelem (példa):

     https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize?client_id=a0448380-c346-4f9f-b897-c18733de9394&response_mode=form_post&response_type=code+id_token&redirect_uri=http%3a%2f%2fwww.vipswapper.com%2fcloudsense%2fAccount%2fSignIn&resource=https%3a%2f%2fgraph.windows.net%2f&scope=openid+profile&nonce=63567Dc4MDAw&domain_hint=live.com&state=M_12tMyKaM8

Az Azure AD akkor hitelesíti a felhasználót, és, ha szükséges, megkérdezi a felhasználót, hogy adjon engedélyt az alkalmazás. Az engedélyezési kódot adja vissza, az alkalmazás válasz URL-címre. Attól függően, a kért response_mode, az Azure ad-ben vagy küld vissza az adatok a lekérdezési karakterláncban vagy post adatként.

Egy példa a válasz Open ID Connect van:

    code=AAABAAAAiL*****I4rDWd7zXsH6WUjlkIEQxIAA&id_token=eyJ0eXAiOiJKV1Q*****T3GrzzSFxg&state=M_12tMyKaM8&session_state=2d16bbce-d5d1-443f-acdf-75f6b0ce8850

### <a name="token-request-oauth20-code-grant-flow"></a>Jogkivonat kérése (OAuth2.0 Code Grant Flow)
Most, hogy az alkalmazás Azure AD-ből kapott engedélyezési kód, ideje a hozzáférési jogkivonat beszerzése az Azure Resource Manager.  Közzététele egy OAuth2.0 kód engedélyezési jogkivonat kérése az Azure AD jogkivonat-végpont:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Token

A lekérdezési karakterlánc paraméterei a kérelem rendelkezésre álló ismertetett a [az engedélyezési kód](../active-directory/develop/v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token) cikk.

Az alábbi példa bemutatja a kód engedélyezési jogkivonat jelszavára vonatkozó hitelesítőadat-kérelem:

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=authorization_code&code=AAABAAAAiL9Kn2Z*****L1nVMH3Z5ESiAA&redirect_uri=http%3A%2F%2Flocalhost%3A62080%2FAccount%2FSignIn&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_secret=olna84E8*****goScOg%3D

Ha tanúsítványalapú dolgozik, hozzon létre egy JSON webes jogkivonat (JWT) és a bejelentkezés (RSA-SHA256) az alkalmazás Tanúsítványalapú hitelesítő adatot a titkos kulccsal. Ez a token létrehozásához látható a [ügyfél-hitelesítő adatok folyamata](../active-directory/develop/v1-oauth2-client-creds-grant-flow.md#second-case-access-token-request-with a-certificate).  Ha, olvassa el a [Active Directory hitelesítési könyvtár (.NET) kódját](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/blob/dev/src/ADAL.PCL.Desktop/CryptographyHelper.cs) ügyfél helyességi feltétel JWT-jogkivonatok aláírásához.

Tekintse meg a [Open ID Connect specifikációja](http://openid.net/specs/openid-connect-core-1_0.html#ClientAuthentication) ügyfél-hitelesítés részleteiért.

Az alábbi példa bemutatja egy kérelem kód engedélyezési jogkivonatot tanúsítványalapú hitelesítő adatot:

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=authorization_code&code=AAABAAAAiL9Kn2Z*****L1nVMH3Z5ESiAA&redirect_uri=http%3A%2F%2Flocalhost%3A62080%2FAccount%2FSignIn&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&client_assertion=eyJhbG*****Y9cYo8nEjMyA

Egy példaválasz kód engedélyezési jogkivonat:

    HTTP/1.1 200 OK

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1432039858","not_before":"1432035958","resource":"https://management.core.windows.net/","access_token":"eyJ0eXAiOiJKV1Q****M7Cw6JWtfY2lGc5A","refresh_token":"AAABAAAAiL9Kn2Z****55j-sjnyYgAA","scope":"user_impersonation","id_token":"eyJ0eXAiOiJKV*****-drP1J3P-HnHi9Rr46kGZnukEBH4dsg"}

#### <a name="handle-code-grant-token-response"></a>Kód engedélyezési jogkivonat válasz kezeléséhez
Token sikeres válasz tartalmazza a (felhasználó + alkalmazás) hozzáférési jogkivonatot az Azure Resource Manager. Az alkalmazás a hozzáférési jogkivonattal erőforrás-kezelő eléréséhez a felhasználó nevében. Az Azure AD által kiállított hozzáférési jogkivonatok élettartama érték egy óra. Nem valószínű, hogy a webalkalmazás kell megújítani a (felhasználó + alkalmazás) hozzáférési jogkivonatot. Ha a hozzáférési jogkivonat megújításához van szüksége, használja a frissítési jogkivonatot, amely az alkalmazás fogad a token válaszban. Közzététele egy OAuth2.0 jogkivonat kérése az Azure AD jogkivonat-végpont:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Token

A paraméterek használata a frissítési kérelem ismertetett [a hozzáférési jogkivonat frissítését](../active-directory/develop/v1-protocols-oauth-code.md#refreshing-the-access-tokens).

Az alábbi példa bemutatja, hogyan használata a frissítési token:

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=refresh_token&refresh_token=AAABAAAAiL9Kn2Z****55j-sjnyYgAA&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_secret=olna84E8*****goScOg%3D

Frissítési biztonsági jogkivonat beszerzése az új hozzáférési jogkivonatok az Azure Resource Manager segítségével, de azok nem alkalmasak az alkalmazás által a kapcsolat nélküli elérés. A frissítési jogkivonatok élettartamának korlátozódik, és frissítési biztonsági jogkivonat a felhasználó vannak kötve. Ha a felhasználó elhagyja a szervezetet, az alkalmazás a frissítési jogkivonat használatával elveszítette a hozzáférését. Ez a megközelítés nem alkalmas az Azure erőforrások kezeléséhez csapatok által használt alkalmazások számára.

## <a name="check-if-user-can-assign-access-to-subscription"></a>Ha a felhasználó hozzáférési jogosultságot rendelhet előfizetés ellenőrzése
Az alkalmazás most már rendelkezik egy tokent az Azure Resource Manager eléréséhez a felhasználó nevében. A következő lépés, hogy az alkalmazás csatlakoztatása az előfizetéshez. A csatlakozás után az alkalmazás kezelheti ezen előfizetések akkor is, ha a felhasználó nincs jelen (hosszú távú offline hozzáférést).

Az egyes előfizetésekhez való kapcsolódáshoz, hívja a [Resource Manager-lista engedélyeit](https://docs.microsoft.com/rest/api/authorization/permissions) határozza meg, hogy a felhasználó rendelkezik-e felügyeleti jogokkal az előfizetéshez tartozó API-t.

A [UserCanManagerAccessForSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L44) minta ASP.NET MVC alkalmazás metódus valósítja meg a hívás.

Az alábbi példa bemutatja, hogyan kérhet egy előfizetésre a felhasználó engedélyeit. 83cfe939-2402-4581-b761-4f59b0a041e4 az előfizetés Azonosítóját.

    GET https://management.azure.com/subscriptions/83cfe939-2402-4581-b761-4f59b0a041e4/providers/microsoft.authorization/permissions?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiLC***lwO1mM7Cw6JWtfY2lGc5A

Előfizetés felhasználói jogosultságok megszerzéséhez a válasz egy példát a következő:

    HTTP/1.1 200 OK

    {"value":[{"actions":["*"],"notActions":["Microsoft.Authorization/*/Write","Microsoft.Authorization/*/Delete"]},{"actions":["*/read"],"notActions":[]}]}

Az engedélyek API több engedélyeket ad vissza. Minden egyes engedély engedélyezett műveletek áll (**műveletek**) és nem engedélyezett műveletek (**notactions**). Ha egy művelet szerepel az engedélyezett műveleteket bármilyen engedéllyel, és nem szerepel a nem engedélyezett műveletek körét az engedélyt, a felhasználó számára engedélyezett a művelet végrehajtásához. **Microsoft.Authorization/RoleAssignments/Write** , hogy a hozzáférési felügyeleti jogok a műveletet. Az alkalmazás olvassa be az engedélyek eredmény, ez a művelet a karakterlánc a reguláris kifejezéssel egyező keresse meg a **műveletek** és **notactions** minden engedélyt.

## <a name="get-app-only-access-token"></a>Csak az alkalmazásra vonatkozó hozzáférési jogkivonat beszerzése
Most már tudja, ha a felhasználó hozzáférési jogosultságot rendelhet az Azure-előfizetés. A következő lépések a következők:

1. A megfelelő RBAC-szerepkörök hozzárendelése az alkalmazásidentitás az előfizetésben.
2. Ellenőrizze a hozzáférés hozzárendelése, adatlekérdezést, az az alkalmazás engedélye az előfizetésben, vagy csak alkalmazás token használatával a Resource Manager elérése.
3. Jegyezze fel a kapcsolatot az alkalmazások "csatlakoztatott előfizetések" adatszerkezet - megőrzése az előfizetés Azonosítóját a.

Vizsgáljuk meg részletesebben az első lépés. A megfelelő RBAC-szerepkörök hozzárendelése az alkalmazás azonosítóját, meg kell határoznia:

* Az alkalmazás azonosítóját a felhasználó Azure Active Directoryban objektum azonosítója
* Az RBAC-szerepkör, amely az alkalmazás által kért az előfizetés azonosítóját

Amikor az alkalmazás hitelesíti a felhasználót az Azure AD-ből, az adott Azure ad-ben létrehoz egy szolgáltatásnév-objektumot az alkalmazás. Az Azure lehetővé teszi, hogy az RBAC-szerepkörök egyszerű szolgáltatások Azure-erőforrások a megfelelő alkalmazások közvetlen hozzáférést rendelni. Ez a művelet pontosan akkor, mit szeretne tenni. Az Azure AD Graph API lekérdezése a szolgáltatásnevet, az alkalmazás a bejelentkezett felhasználó azonosítóját határozza meg a Azure ad-ben.

Csak akkor kell egy hozzáférési jogkivonatot az Azure Resource Manager - kell új hozzáférési jogkivonatot az Azure AD Graph API-t. Az Azure ad-ben minden alkalmazás lekérdezése a saját egyszerű szolgáltatási objektumot, így csak az alkalmazásra vonatkozó hozzáférési jogkivonat elegendő engedéllyel rendelkezik.

<a id="app-azure-ad-graph" />

### <a name="get-app-only-access-token-for-azure-ad-graph-api"></a>Csak az alkalmazásra vonatkozó hozzáférési jogkivonat beszerzése az Azure AD Graph API-hoz
Azure AD Graph API-nak egy token beszerzése és az alkalmazás hitelesítéséhez, adjon ki egy ügyfél-hitelesítő adatok megadása OAuth2.0 folyamat token kérelmet az Azure AD jogkivonat-végpont (**https://login.microsoftonline.com/{directory_domain_name}/OAuth2/Token**).

A [GetObjectIdOfServicePrincipalInOrganization](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureADGraphAPIUtil.cs) metódus az ASP.net MVC-mintaalkalmazás beolvassa csak az alkalmazásra vonatkozó hozzáférési jogkivonat Graph API használatával az Active Directory Authentication Library for .NET.

A lekérdezési karakterlánc paraméterei a kérelem rendelkezésre álló ismertetett a [egy hozzáférési jogkivonat kérése](../active-directory/develop/v1-oauth2-client-creds-grant-flow.md#request-an-access-token) cikk.

Egy kérelem (példa) ügyfél-hitelesítő adat számára megadja a jogkivonatot:

    POST https://login.microsoftonline.com/62e173e9-301e-423e-bcd4-29121ec1aa24/oauth2/token HTTP/1.1
    Content-Type: application/x-www-form-urlencoded
    Content-Length: 187</pre>
    <pre>grant_type=client_credentials&client_id=a0448380-c346-4f9f-b897-c18733de9394&resource=https%3A%2F%2Fgraph.windows.net%2F &client_secret=olna8C*****Og%3D

Ügyfél-hitelesítő adat számára egy példaválasz token megadása:

    HTTP/1.1 200 OK

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1432039862","not_before":"1432035962","resource":"https://graph.windows.net/","access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRv****G5gUTV-kKorR-pg"}

### <a name="get-objectid-of-application-service-principal-in-user-azure-ad"></a>Az Azure AD-felhasználói objectid azonosítóját, az alkalmazás egyszerű szolgáltatás beolvasása
Mostantól használhatja a lekérdezés csak az alkalmazásra vonatkozó hozzáférési jogkivonatot a [Azure AD Graph szolgáltatásnevek](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity) API segítségével határozza meg a könyvtárban az alkalmazás szolgáltatásnév Objektumazonosítóját.

A [GetObjectIdOfServicePrincipalInOrganization](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureADGraphAPIUtil.cs#) az ASP.net MVC-mintaalkalmazáson metódus valósítja meg a hívás.

Az alábbi példa bemutatja, hogyan kérhet egy alkalmazás egyszerű szolgáltatást. a0448380-c346-4f9f-b897-c18733de9394 az alkalmazás ügyfél-azonosító.

    GET https://graph.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/servicePrincipals?api-version=1.5&$filter=appId%20eq%20'a0448380-c346-4f9f-b897-c18733de9394' HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJK*****-kKorR-pg

Az alábbi példa bemutatja egy válasz a kérésre egy alkalmazás szolgáltatás egyszerű

    HTTP/1.1 200 OK

    {"odata.metadata":"https://graph.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/$metadata#directoryObjects/Microsoft.DirectoryServices.ServicePrincipal","value":[{"odata.type":"Microsoft.DirectoryServices.ServicePrincipal","objectType":"ServicePrincipal","objectId":"9b5018d4-6951-42ed-8a92-f11ec283ccec","deletionTimestamp":null,"accountEnabled":true,"appDisplayName":"CloudSense","appId":"a0448380-c346-4f9f-b897-c18733de9394","appOwnerTenantId":"62e173e9-301e-423e-bcd4-29121ec1aa24","appRoleAssignmentRequired":false,"appRoles":[],"displayName":"CloudSense","errorUrl":null,"homepage":"http://www.vipswapper.com/cloudsense","keyCredentials":[],"logoutUrl":null,"oauth2Permissions":[{"adminConsentDescription":"Allow the application to access CloudSense on behalf of the signed-in user.","adminConsentDisplayName":"Access CloudSense","id":"b7b7338e-683a-4796-b95e-60c10380de1c","isEnabled":true,"type":"User","userConsentDescription":"Allow the application to access CloudSense on your behalf.","userConsentDisplayName":"Access CloudSense","value":"user_impersonation"}],"passwordCredentials":[],"preferredTokenSigningKeyThumbprint":null,"publisherName":"vipswapper"quot;,"replyUrls":["http://www.vipswapper.com/cloudsense","http://www.vipswapper.com","http://vipswapper.com","http://vipswapper.azurewebsites.net","http://localhost:62080"],"samlMetadataUrl":null,"servicePrincipalNames":["http://www.vipswapper.com/cloudsense","a0448380-c346-4f9f-b897-c18733de9394"],"tags":["WindowsAzureActiveDirectoryIntegratedApp"]}]}

### <a name="get-azure-rbac-role-identifier"></a>Azure RBAC szerepkör-azonosító beszerzése
A megfelelő RBAC-szerepkörök hozzárendelése az egyszerű szolgáltatás, meg kell határoznia az Azure RBAC szerepkör azonosítóját.

A megfelelő RBAC szerepkör az alkalmazáshoz:

* Ha az alkalmazás csak figyeli az előfizetés, változtatások nélkül, csak olvasási engedélyei az előfizetés szükséges. Rendelje hozzá a **olvasó** szerepkör.
* Ha az alkalmazás kezeli az Azure az előfizetést, az entitások létrehozása/módosítása/törlése egyikét igényli a közreműködői engedélyekkel.
  * Egy adott típusú erőforrás kezeléséhez, rendelje hozzá az erőforrás-specifikus közreműködői szerepkörrel (virtuális gép közreműködő, virtuális hálózat közreműködő, Tárfiók-közreműködő, stb.)
  * Típustól függetlenül az erőforrás kezeléséhez, rendelje hozzá a **közreműködői** szerepkör.

Az alkalmazás szerepkör-hozzárendelés jelenik meg a felhasználók számára, ezért válassza a legkevésbé szükséges jogosultsággal.

Hívja a [erőforrás-kezelő szerepkör-definíció API](https://docs.microsoft.com/rest/api/authorization/roledefinitions) az Azure RBAC-szerepkörökhöz, és a keresési listában, majd megismételheti a kívánt szerepkör-definíció keresése név alapján az eredményt.

A [GetRoleId](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L246) minta ASP.net MVC alkalmazás metódus valósítja meg a hívás.

A következő kérés példa bemutatja, hogyan tehet szert az Azure RBAC szerepkör-azonosító. 09cbd307-aa71-4aca-b346-5f253e6e3ebb az előfizetés Azonosítóját.

    GET https://management.azure.com/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV*****fY2lGc5

A válasz a következő formátumban kell megadni:

    HTTP/1.1 200 OK

    {"value":[{"properties":{"roleName":"API Management Service Contributor","type":"BuiltInRole","description":"Lets you manage API Management services, but not access to them.","scope":"/","permissions":[{"actions":["Microsoft.ApiManagement/Services/*","Microsoft.Authorization/*/read","Microsoft.Resources/subscriptions/resources/read","Microsoft.Resources/subscriptions/resourceGroups/read","Microsoft.Resources/subscriptions/resourceGroups/resources/read","Microsoft.Resources/subscriptions/resourceGroups/deployments/*","Microsoft.Insights/alertRules/*","Microsoft.Support/*"],"notActions":[]}]},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/312a565d-c81f-4fd8-895a-4e21e48d571c","type":"Microsoft.Authorization/roleDefinitions","name":"312a565d-c81f-4fd8-895a-4e21e48d571c"},{"properties":{"roleName":"Application Insights Component Contributor","type":"BuiltInRole","description":"Lets you manage Application Insights components, but not access to them.","scope":"/","permissions":[{"actions":["Microsoft.Insights/components/*","Microsoft.Insights/webtests/*","Microsoft.Authorization/*/read","Microsoft.Resources/subscriptions/resources/read","Microsoft.Resources/subscriptions/resourceGroups/read","Microsoft.Resources/subscriptions/resourceGroups/resources/read","Microsoft.Resources/subscriptions/resourceGroups/deployments/*","Microsoft.Insights/alertRules/*","Microsoft.Support/*"],"notActions":[]}]},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/ae349356-3a1b-4a5e-921d-050484c6347e","type":"Microsoft.Authorization/roleDefinitions","name":"ae349356-3a1b-4a5e-921d-050484c6347e"}]}

Nem kell folyamatosan az API meghívásához. A jól ismert GUID azonosítóját a szerepkör-definíció meghatározása után összeállíthatja a szerepkör-definíció azonosítója, mint:

    /subscriptions/{subscription_id}/providers/Microsoft.Authorization/roleDefinitions/{well-known-role-guid}

A gyakran használt beépített szerepkör-azonosítók a következők:

| Szerepkör | GUID |
| --- | --- |
| Olvasó |acdd72a7-3385-48EF-bd42-f606fba81ae7 |
| Közreműködő |b24988ac-6180-42a0-ab88-20f7382dd24c |
| Virtuális gépek közreműködője |d73bb868-a0df-4d4d-bd69-98a00b01fccb |
| Virtuális hálózati közreműködő |b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
| Tárfiók-közreműködő |86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
| Webhelyek közreműködője |de139f84-1756-47ae-9be6-808fbbe84772 |
| Webes tarifacsomagok közreműködője |2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
| SQL Server közreműködője |6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
| SQL-adatbázisok közreműködője |9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |

### <a name="assign-rbac-role-to-application"></a>RBAC szerepkör hozzárendelése alkalmazáshoz
Rendelkezik a megfelelő RBAC-szerepkörök hozzárendelése az egyszerű szolgáltatás használatával minden, amit a [erőforrás-kezelő szerepkör-hozzárendelés létrehozása](https://docs.microsoft.com/rest/api/authorization/roleassignments) API-t.

A [GrantRoleToServicePrincipalOnSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L170) minta ASP.net MVC alkalmazás metódus valósítja meg a hívás.

Egy kérelem (példa) RBAC szerepkör hozzárendelése az alkalmazáshoz:

    PUT https://management.azure.com/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/microsoft.authorization/roleassignments/4f87261d-2816-465d-8311-70a27558df4c?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiL*****FlwO1mM7Cw6JWtfY2lGc5
    Content-Type: application/json
    Content-Length: 230

    {"properties": {"roleDefinitionId":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7","principalId":"c3097b31-7309-4c59-b4e3-770f8406bad2"}}

A kérelem a következő értékeket használjuk:

| GUID | Leírás |
| --- | --- |
| 09cbd307-aa71-4aca-b346-5f253e6e3ebb |az előfizetés azonosítója |
| c3097b31-7309-4c59-b4e3-770f8406bad2 |az alkalmazás a szolgáltatásnév Objektumazonosítóját |
| acdd72a7-3385-48EF-bd42-f606fba81ae7 |az Olvasó szerepkör azonosítója |
| 4f87261d-2816-465d-8311-70a27558df4c |az új szerepkör-hozzárendelés létrehozása új guid |

A válasz a következő formátumban kell megadni:

    HTTP/1.1 201 Created

    {"properties":{"roleDefinitionId":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7","principalId":"c3097b31-7309-4c59-b4e3-770f8406bad2","scope":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb"},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleAssignments/4f87261d-2816-465d-8311-70a27558df4c","type":"Microsoft.Authorization/roleAssignments","name":"4f87261d-2816-465d-8311-70a27558df4c"}

### <a name="get-app-only-access-token-for-azure-resource-manager"></a>Csak az alkalmazásra vonatkozó hozzáférési jogkivonat beszerzése az Azure Resource Manager
Érvényesítéséhez, amelyet az alkalmazás rendelkezik a kívánt az előfizetés eléréséhez, vizsgálati művelet végrehajtásához a az előfizetés csak alkalmazás token használatával.

Csak az alkalmazásra vonatkozó hozzáférési jogkivonatot kapjon, kövesse az utasításokat szakaszban [csak az alkalmazásra vonatkozó hozzáférési jogkivonat beszerzése az Azure AD Graph API](#app-azure-ad-graph), egy másik érték a resource paraméter:

    https://management.core.windows.net/

A [ServicePrincipalHasReadAccessToSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L110) metódus az ASP.NET MVC-mintaalkalmazáson lekéri csak az alkalmazásra vonatkozó hozzáférési tokent az Azure Resource Manager használatával az Active Directory Authentication Library for .net.

#### <a name="get-applications-permissions-on-subscription"></a>Alkalmazás engedélyek előfizetés beszerzése
Ellenőrizze, hogy rendelkezik-e az alkalmazás a kívánt hozzáférés az Azure-előfizetéssel, akkor is hívhat a [erőforrás-kezelő engedélyei](https://docs.microsoft.com/rest/api/authorization/permissions) API-t. Ez a módszer hogyan megadta, hogy a felhasználó rendelkezik-e az előfizetés hozzáférés-kezelés rights hasonlít. Ennek során azonban hívja meg az engedélyeket API, amely az előző lépésben kapott csak az alkalmazásra vonatkozó hozzáférési jogkivonat.

A [ServicePrincipalHasReadAccessToSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L110) minta ASP.NET MVC alkalmazás metódus valósítja meg a hívás.

## <a name="manage-connected-subscriptions"></a>Csatlakoztatott előfizetések kezelése
A megfelelő RBAC-szerepkör van rendelve az alkalmazás szolgáltatásnév az előfizetésre, amikor az alkalmazás is folyamatosan figyelési és kezeléséhez, csak az alkalmazásra vonatkozó hozzáférési jogkivonatok az Azure Resource Manager használatával.

Ha az előfizetés tulajdonosa a portál vagy a parancssori eszközök használatával az alkalmazás szerepkör-hozzárendelés eltávolítása, az alkalmazás már nem érhetik el az adott előfizetéshez. Ebben az esetben kell értesíti a felhasználót, hogy az alkalmazáson kívüli az előfizetéshez a kapcsolat megszakadt és tegye lehetővé számukra a kapcsolat "javítás" lehetőséget. "Javítás" újra hozna létre a szerepkör-hozzárendelés, amelyek offline törölve lett.

Ugyanúgy, mint az előfizetések kapcsolódni az alkalmazáshoz a felhasználó engedélyezte, engedélyeznie kell a felhasználó megszakítja a előfizetések túl. Egy hozzáférési felügyeleti szempontból válassza le azt jelenti, hogy a szerepkör-hozzárendelés, amely az alkalmazás egyszerű szolgáltatás rendelkezik az előfizetés eltávolítása. Igény szerint bármely, az alkalmazás az előfizetés állapota lehet, hogy el kell távolítani túl.
Csak az előfizetés hozzáférés-felügyeleti engedéllyel rendelkező felhasználók képesek leválasztani az előfizetést.

A [RevokeRoleFromServicePrincipalOnSubscription metódus](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L200) az ASP.net MVC-mintaalkalmazást a hívás valósítja meg.

Ennyi az egész – a felhasználók mostantól könnyedén csatlakoztatása és az alkalmazását az Azure-előfizetések kezelése.
