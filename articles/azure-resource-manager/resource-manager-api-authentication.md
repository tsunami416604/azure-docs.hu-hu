---
title: Az Azure Active Directory-hitelesítés és erőforrás-kezelő |} Microsoft Docs
description: A fejlesztői útmutató az alkalmazások integrálása más Azure-előfizetések az Azure Resource Manager API és az Azure Active Directory-hitelesítés.
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
ms.date: 11/15/2017
ms.author: dugill
ms.openlocfilehash: 1dea8d173432b05a72de72e8b17db4c97ea7924d
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34359862"
---
# <a name="use-resource-manager-authentication-api-to-access-subscriptions"></a>Erőforrás-kezelő használata hitelesítési API access előfizetésekhez
## <a name="introduction"></a>Bevezetés
Ha egy szoftver fejlesztőről, aki létre kell hoznia egy alkalmazást, amely felügyeli az ügyfél Azure-erőforrások, a Ez a cikk bemutatja, hogyan hitelesítik magukat az Azure Resource Manager API-k és más előfizetések lévő erőforrások eléréséhez.

Az alkalmazás férhetnek hozzá a Resource Manager API-k több módon:

1. **Felhasználói + alkalmazás-hozzáférés**: az alkalmazáshoz, amelyet a bejelentkezett felhasználó nevében erőforrásokhoz férnek hozzá. Ez a módszer az alkalmazások, például webes alkalmazásokat vagy csak "interaktív kezelése" Azure-erőforrások kezelésére szolgáló parancssori eszköz működik.
2. **Csak alkalmazás hozzáférés**: démon szolgáltatások és az ütemezett feladatok futó alkalmazások számára. Az alkalmazás identitását közvetlen hozzáférést az erőforrásokhoz. Ez a megközelítés hosszú távú távfelügyeleti (felügyelet) Azure-hozzáférést igénylő alkalmazások esetében működik.

Ez a cikk részletesen ezek hitelesítési módszerek által-alkalmazás létrehozása. Azt illusztrálja, hogyan REST API-t vagy a C# egyes lépéseinek végrehajtásához. A teljes ASP.NET MVC alkalmazás érhető el: [ https://github.com/dushyantgill/VipSwapper/tree/master/CloudSense ](https://github.com/dushyantgill/VipSwapper/tree/master/CloudSense).

## <a name="what-the-web-app-does"></a>A webalkalmazás funkciója
A webalkalmazás:

1. Bejelentkezik egy Azure felhasználói.
2. Megkérdezi a felhasználót, hogy a hozzáférést a webes alkalmazás erőforrás-kezelő.
3. Lekérdezi a felhasználó + alkalmazás hozzáférési jogkivonat erőforrás-kezelő eléréséhez.
4. Használja a jogkivonatot (a 3. lépés) az alkalmazás egyszerű hozzárendelése egy szerepkört az előfizetés. Ezt a lépést az alkalmazás hosszú távú hozzáférést ad az előfizetést.
5. Csak alkalmazás-hozzáférési jogkivonat lekérése.
6. (Az 5. lépés) token használatával kezelheti az erőforrásokat az előfizetés Resource Manageren keresztül.

Ez a webalkalmazás a folyamatot.

![Erőforrás-kezelő hitelesítési folyamat](./media/resource-manager-api-authentication/Auth-Swim-Lane.png)

Egy felhasználó nevében a használni kívánt előfizetést adja meg az előfizetés-azonosító:

![Adja meg az előfizetés-azonosító](./media/resource-manager-api-authentication/sample-ux-1.png)

Válassza ki a bejelentkezéshez használt fiókot.

![Válasszon fiókot](./media/resource-manager-api-authentication/sample-ux-2.png)

Adja meg a hitelesítő adatokat.

![Adjon meg hitelesítő adatokat](./media/resource-manager-api-authentication/sample-ux-3.png)

Hozzáférést biztosítania az alkalmazásnak az Azure-előfizetések:

![Hozzáférés biztosítása](./media/resource-manager-api-authentication/sample-ux-4.png)

A csatlakoztatott előfizetések kezelése:

![Csatlakozás az előfizetés](./media/resource-manager-api-authentication/sample-ux-7.png)

## <a name="register-application"></a>Alkalmazás regisztrálása
A kezdéshez kódolási regisztrálja a webalkalmazás az Azure Active Directory (AD). Az alkalmazás regisztrálása egy központi identitás, az alkalmazás az Azure AD-hoz létre. Magánál tartja alapvető információkat az alkalmazásról, mint OAuth-Ügyfélazonosító, a válasz URL-címek és a hitelesítő adatokkal, amelyek az alkalmazás hitelesítéséhez, és az Azure Resource Manager API-k eléréséhez. Az alkalmazás regisztrálása a különböző delegált jogosultságokkal sikeresen telepítették az alkalmazást igénylő Microsoft APIs elérésekor, a felhasználó nevében is rögzíti.

Az alkalmazás más előfizetés fér hozzá, mert a több-bérlős alkalmazásként kell konfigurálnia. Érvényesítési átadni, adja meg egy társított az Azure Active Directory tartományi. Az Azure Active Directory társított tartományokat megtekintéséhez jelentkezzen be a portálra.

A következő példa bemutatja, hogyan kell regisztrálni az alkalmazás az Azure PowerShell használatával. A parancs működéséhez az Azure PowerShell legfrissebb (2016 augusztusától) kell rendelkeznie.

    $app = New-AzureRmADApplication -DisplayName "{app name}" -HomePage "https://{your domain}/{app name}" -IdentifierUris "https://{your domain}/{app name}" -Password "{your password}" -AvailableToOtherTenants $true

Jelentkezzen be az AD-alkalmazás, az alkalmazás Azonosítóját és jelszavát kell. Az Alkalmazásazonosító, amely küld vissza az előző parancsot használja:

    $app.ApplicationId

A következő példa bemutatja, hogyan kell regisztrálni az alkalmazás az Azure parancssori felület használatával.

    azure ad app create --name {app name} --home-page https://{your domain}/{app name} --identifier-uris https://{your domain}/{app name} --password {your password} --available true

Az eredmények tartalmazzák az alkalmazásazonosító, amely megegyezik az alkalmazáséval hitelesítéséhez szükséges.

### <a name="optional-configuration---certificate-credential"></a>Opcionális konfigurációs - tanúsítvány hitelesítő adatok
Az alkalmazások is támogatja tanúsítvány hitelesítő adatokat az Azure AD: hozzon létre egy önaláírt tanúsítványt, tartsa a titkos kulcsot, majd adja hozzá a nyilvános kulcsot az Azure AD-alkalmazás regisztrációja. A hitelesítéshez az alkalmazás egy kis hasznos küld az Azure AD a titkos kulccsal aláírva, és az Azure AD ellenőrzi az aláírást a nyilvános kulccsal regisztrált.

Az AD-alkalmazás létrehozása a tanúsítvánnyal kapcsolatos információkért lásd: [a Azure PowerShell szolgáltatás használatával hozzon létre egy egyszerű erőforrások eléréséhez](resource-group-authenticate-service-principal.md#create-service-principal-with-certificate-from-certificate-authority) vagy [használja az Azure parancssori felület erőforrásokhoz való hozzáféréshez egyszerű szolgáltatás létrehozása](resource-group-authenticate-service-principal-cli.md) .

## <a name="get-tenant-id-from-subscription-id"></a>Előfizetés-azonosító Bérlőazonosító beszerzése
Kérjen meg egy jogkivonatot, amely hívja az erőforrás-kezelő használható, az alkalmazás tudnia kell, a bérlő azonosítója, amelyen az Azure-előfizetéshez az Azure AD-bérlő. A legvalószínűbb ok az a felhasználók tudják, az előfizetési azonosítók, de előfordulhat, hogy nem tudják a bérlői azonosító az Azure Active Directory. Ahhoz, hogy a felhasználó Bérlőazonosító, kérje meg, az előfizetés-azonosítóhoz. Adja meg, hogy előfizetési azonosító az előfizetéssel kapcsolatos igényel:

    https://management.azure.com/subscriptions/{subscription-id}?api-version=2015-01-01

A kérés nem teljesíthető, mert a felhasználó nem bejelentkezése még, de a válasz kérhetnek le a bérlő azonosítója. Ezt a kivételt a beolvasni a bérlő Azonosítóját a válasz fejléc értéke **WWW-Authenticate**. Ebben az implementációban a látja a [GetDirectoryForSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L20) metódust.

## <a name="get-user--app-access-token"></a>Felhasználói + alkalmazás hozzáférési jogkivonat beszerzése
Az alkalmazás átirányítja a felhasználót az Azure AD és az OAuth 2.0 engedélyezik kérelem - hitelesítéshez a felhasználó hitelesítő adatait, és vissza egy engedélyezési kódot. Az alkalmazás az engedélyezési kód használatával szerezze be a hozzáférési tokent az erőforrás-kezelő. A [ConnectSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/Controllers/HomeController.cs#L42) hoz létre a hitelesítési kérelmet.

Ez a cikk bemutatja a REST API-kérés hitelesíteni a felhasználót. Segítő tárak segítségével is végezhet hitelesítést a kódban. Ezek a kódtárak kapcsolatos további információkért lásd: [Azure Active Directory hitelesítési Kódtárai](../active-directory/active-directory-authentication-libraries.md). Egy alkalmazás Identitáskezelés integrálásával útmutatóért lásd: [Azure Active Directory fejlesztői útmutatója](../active-directory/active-directory-developers-guide.md).

### <a name="auth-request-oauth-20"></a>Hitelesítési kérelmek (OAuth 2.0-s)
Egy nyitott azonosító Connect/OAuth2.0 engedélyezik lekérdezési ki az Azure AD-hitelesítési végpontra:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize

A lekérdezési karakterlánc elérhető paramétereket a kérelemhez tartozó ismerteti a [egy engedélyezési kód kérése](../active-directory/develop/active-directory-protocols-oauth-code.md#request-an-authorization-code) cikk.

A következő példa bemutatja, hogyan OAuth2.0 engedély kérése:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize?client_id=a0448380-c346-4f9f-b897-c18733de9394&response_mode=query&response_type=code&redirect_uri=http%3a%2f%2fwww.vipswapper.com%2fcloudsense%2fAccount%2fSignIn&resource=https%3a%2f%2fgraph.windows.net%2f&domain_hint=live.com

Az Azure AD akkor hitelesíti a felhasználót, és, ha szükséges, megkérdezi a felhasználót, hogy az alkalmazás engedélyt. Az engedélyezési kódot adja vissza, az alkalmazás válasz URL-címét. Attól függően, a kért response_mode, az Azure AD vagy küld vissza az adatok a lekérdezési karakterláncban vagy post-adatokat.

    code=AAABAAAAiL****FDMZBUwZ8eCAA&session_state=2d16bbce-d5d1-443f-acdf-75f6b0ce8850

### <a name="auth-request-open-id-connect"></a>Hitelesítési kérelmek (Open ID Connect)
Ha nem csak szeretné elérni az Azure Resource Manager a felhasználó nevében, de is lehetővé teszi a felhasználó bejelentkezni az alkalmazás használatával az Azure AD-fiókot, ki egy nyitott azonosító csatlakozás engedélyezése lekérdezési. A Open ID Connect az alkalmazás is kap egy id_token, amelyek az alkalmazás a felhasználói bejelentkezés az Azure AD-ből.

A lekérdezési karakterlánc elérhető paramétereket a kérelemhez tartozó ismerteti a [a bejelentkezési kérelem küldése](../active-directory/develop/active-directory-protocols-openid-connect-code.md#send-the-sign-in-request) cikk.

A következő egy példa egy Open ID Connect kérelem:

     https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize?client_id=a0448380-c346-4f9f-b897-c18733de9394&response_mode=form_post&response_type=code+id_token&redirect_uri=http%3a%2f%2fwww.vipswapper.com%2fcloudsense%2fAccount%2fSignIn&resource=https%3a%2f%2fgraph.windows.net%2f&scope=openid+profile&nonce=63567Dc4MDAw&domain_hint=live.com&state=M_12tMyKaM8

Az Azure AD akkor hitelesíti a felhasználót, és, ha szükséges, megkérdezi a felhasználót, hogy az alkalmazás engedélyt. Az engedélyezési kódot adja vissza, az alkalmazás válasz URL-címét. Attól függően, a kért response_mode, az Azure AD vagy küld vissza az adatok a lekérdezési karakterláncban vagy post-adatokat.

Az Open ID Connect válasz példája:

    code=AAABAAAAiL*****I4rDWd7zXsH6WUjlkIEQxIAA&id_token=eyJ0eXAiOiJKV1Q*****T3GrzzSFxg&state=M_12tMyKaM8&session_state=2d16bbce-d5d1-443f-acdf-75f6b0ce8850

### <a name="token-request-oauth20-code-grant-flow"></a>Jogkivonatkérelem (OAuth2.0 Code Grant Flow)
Most, hogy az alkalmazás az Azure AD-ből kapott engedélyezési kódot, akkor szerezze be a hozzáférési tokent az Azure Resource Manager időt.  Írjon egy OAuth2.0 Code Grant Token elküldeni a kérelmet az Azure AD Token-végpont:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Token

A lekérdezési karakterlánc elérhető paramétereket a kérelemhez tartozó ismerteti a [használata az engedélyezési kód](../active-directory/develop/active-directory-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token) cikk.

A következő példa bemutatja egy kérelem a code grant token jelszó hitelesítő adat:

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=authorization_code&code=AAABAAAAiL9Kn2Z*****L1nVMH3Z5ESiAA&redirect_uri=http%3A%2F%2Flocalhost%3A62080%2FAccount%2FSignIn&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_secret=olna84E8*****goScOg%3D

Az használatakor tanúsítvány hitelesítő adatait, hozzon létre egy JSON webes jogkivonat (JWT) és a bejelentkezési (RSA SHA256), a titkos kulcsot a az alkalmazás tanúsítvány hitelesítő adatok használatával. A jogkivonat esetében a jogcím-típusok megjelennek-e [JWT jogkivonat jogcímek](../active-directory/develop/active-directory-protocols-oauth-code.md#jwt-token-claims). Referenciáért lásd: a [Active Directory hitelesítési könyvtár (.NET) kód](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/blob/dev/src/ADAL.PCL.Desktop/CryptographyHelper.cs) ügyfél helyességi feltétel JWT-jogkivonatokat aláírásához.

Tekintse meg a [Open ID Connect spec](http://openid.net/specs/openid-connect-core-1_0.html#ClientAuthentication) talál részletes információt ügyfél-hitelesítéshez.

A következő példa bemutatja egy kérelem a code grant token tanúsítvány hitelesítő adat:

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=authorization_code&code=AAABAAAAiL9Kn2Z*****L1nVMH3Z5ESiAA&redirect_uri=http%3A%2F%2Flocalhost%3A62080%2FAccount%2FSignIn&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&client_assertion=eyJhbG*****Y9cYo8nEjMyA

Egy példa egy válasz code grant jogkivonat:

    HTTP/1.1 200 OK

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1432039858","not_before":"1432035958","resource":"https://management.core.windows.net/","access_token":"eyJ0eXAiOiJKV1Q****M7Cw6JWtfY2lGc5A","refresh_token":"AAABAAAAiL9Kn2Z****55j-sjnyYgAA","scope":"user_impersonation","id_token":"eyJ0eXAiOiJKV*****-drP1J3P-HnHi9Rr46kGZnukEBH4dsg"}

#### <a name="handle-code-grant-token-response"></a>Kezeli a code grant token válasz
A sikeres token válasz tartalmazza a (felhasználó + alkalmazás) hozzáférési jogkivonatot az Azure Resource Manager. Az alkalmazás erőforrás-kezelő eléréséhez a felhasználó nevében a hozzáférési jogkivonatot használja. Az Azure AD által kibocsátott hozzáférési jogkivonatok élettartama 1 óra. Nem valószínű, hogy a webes alkalmazás kell megújítani a (felhasználó + alkalmazás) hozzáférési jogkivonat. Ha a hozzáférési jogkivonat megújításához szükséges, használja a frissítési jogkivonatot, amely a token válasz kap az alkalmazáshoz. Írjon egy OAuth2.0 Token elküldeni a kérelmet az Azure AD Token-végpont:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Token

A frissítési kérelem használandó paraméterek ismertetett [a hozzáférési jogkivonat frissítését](../active-directory/develop/active-directory-protocols-oauth-code.md#refreshing-the-access-tokens).

A következő példa bemutatja, hogyan használja a frissítési token:

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=refresh_token&refresh_token=AAABAAAAiL9Kn2Z****55j-sjnyYgAA&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_secret=olna84E8*****goScOg%3D

Bár a frissítési jogkivonatokat új hozzáférési jogkivonatok lekérésére, ha az Azure Resource Manager használatával lehet, nem alkalmasak legyenek offline hozzáférés az alkalmazás. A frissítési jogkivonatok élettartama korlátozott, és a felhasználó köti a frissítési jogkivonatokat. Ha a felhasználó elhagyja a szervezetet, az alkalmazás a frissítési jogkivonat használatával elveszítette a hozzáférését. Ez a módszer nem megfelelő-e az Azure erőforrások kezeléséhez csapatok által használt alkalmazások.

## <a name="check-if-user-can-assign-access-to-subscription"></a>Ha a felhasználó hozzáférési jogosultságot rendelhet előfizetés ellenőrzése
Az alkalmazás most már rendelkezik egy token Azure Resource Manager hozzáférni a felhasználó nevében. A következő lépés, hogy az alkalmazás csatlakoztatása az előfizetést. A csatlakozás után az alkalmazás kezelhető előfizetésekben akkor is, ha a felhasználó nem található (hosszú távú offline hozzáférést).

Az egyes előfizetésekhez való csatlakozáshoz, hívja az [erőforrás-kezelő listázási engedély](https://docs.microsoft.com/rest/api/authorization/permissions) API annak meghatározásához, hogy a felhasználó rendelkezik-e felügyeleti jogokkal az előfizetéshez.

A [UserCanManagerAccessForSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L44) az ASP.NET MVC mintaalkalmazás metódus valósítja meg a hívás.

A következő példa bemutatja, hogyan előfizetésekből a felhasználói engedélyek kéréséhez. 83cfe939-2402-4581-b761-4f59b0a041e4 az előfizetés Azonosítóját.

    GET https://management.azure.com/subscriptions/83cfe939-2402-4581-b761-4f59b0a041e4/providers/microsoft.authorization/permissions?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiLC***lwO1mM7Cw6JWtfY2lGc5A

A válasz felhasználói jogosultságok megszerzéséhez előfizetésben példa, hogy:

    HTTP/1.1 200 OK

    {"value":[{"actions":["*"],"notActions":["Microsoft.Authorization/*/Write","Microsoft.Authorization/*/Delete"]},{"actions":["*/read"],"notActions":[]}]}

Az engedélyek API több engedélyeket ad vissza. Minden engedély tartalmazza az engedélyezett műveletek (**műveletek**) és a nem engedélyezett műveletek (**notactions**). Ha egy művelet megtalálható-e bármilyen engedéllyel az engedélyezett műveleteket, és nem szerepel az engedélyt, nem engedélyezett műveletek, a felhasználó számára engedélyezett az adott művelet végrehajtására. **Microsoft.Authorization/RoleAssignments/Write** hozzáférés jogosultságait, amely engedélyezi a műveletet. Az alkalmazás olvassa a engedélyek eredmény, ez a művelet a karakterlánc a reguláris kifejezéssel egyező keresse meg a **műveletek** és **notactions** minden engedély.

## <a name="get-app-only-access-token"></a>Csak alkalmazás-token beszerzése
Most már tudja, ha a felhasználó hozzáférési jogosultságot rendelhet az Azure-előfizetés. A következő lépések a következők:

1. A megfelelő RBAC szerepkör hozzárendelése az alkalmazásidentitás az előfizetésben.
2. Ellenőrizze a hozzáférés hozzárendelése az alkalmazás engedélyt az előfizetésben lekérdezésével vagy erőforrás-kezelő csak alkalmazás tokent elérésével.
3. Jegyezze fel a kapcsolatot az alkalmazások "csatlakoztatott előfizetések" adatszerkezet - megőrzése az előfizetés Azonosítóját a.

Nézzük szorosabb az első lépésben. A megfelelő RBAC szerepkör hozzárendelése az alkalmazás azonosítóját, meg kell határoznia:

* Az alkalmazás azonosítóját a felhasználó Azure Active Directoryban objektum azonosítója
* Az RBAC szerepkör, az alkalmazás által az előfizetés azonosítóját

Az alkalmazás hitelesíti a felhasználót az Azure AD-ből, ha azt hoz létre egyszerű az alkalmazáshoz, hogy az Azure AD-ben. Azure lehetővé teszi, hogy az RBAC-szerepkörök közvetlen hozzáférést biztosít az Azure-erőforrások a megfelelő alkalmazások szolgáltatásnevekről hozzárendelését. Erre akkor pontosan mit szeretne tenni. Lekérdezés az Azure AD Graph API meghatározni a szolgáltatás egyszerű annak az alkalmazásnak a bejelentkezett felhasználó azonosítóját az Azure AD meg.

Csak akkor kell egy hozzáférési jogkivonatot az Azure Resource Manager - az Azure AD Graph API hívása új tokenre van szüksége. Az Azure ad-ben minden alkalmazásnak a saját szolgáltatás egyszerű objektum lekérdezése, így csak alkalmazás-hozzáférési tokent elegendő jogosultsága.

<a id="app-azure-ad-graph" />

### <a name="get-app-only-access-token-for-azure-ad-graph-api"></a>Szerezze be a csak alkalmazás-hozzáférési tokent az Azure AD Graph API
Hitelesítsék az alkalmazást, és egy tokent a Azure AD Graph API, adjon ki egy ügyfél-hitelesítő adat Grant OAuth2.0 folyamat token kérelmet az Azure AD-jogkivonat végpontjához (**https://login.microsoftonline.com/{directory_domain_name}/OAuth2/Token**).

A [GetObjectIdOfServicePrincipalInOrganization](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureADGraphAPIUtil.cs) metódus az ASP.net MVC mintaalkalmazásnak lekéri csak alkalmazás hozzáférés token Graph API segítségével az Active Directory Authentication Library .NET-keretrendszerhez készült.

A lekérdezési karakterlánc elérhető paramétereket a kérelemhez tartozó ismerteti a [kérelem egy hozzáférési jogkivonat](../active-directory/develop/active-directory-protocols-oauth-service-to-service.md#request-an-access-token) cikk.

Egy példa egy kérelem ügyfél-hitelesítési jogkivonat engedélyezése:

    POST https://login.microsoftonline.com/62e173e9-301e-423e-bcd4-29121ec1aa24/oauth2/token HTTP/1.1
    Content-Type: application/x-www-form-urlencoded
    Content-Length: 187</pre>
    <pre>grant_type=client_credentials&client_id=a0448380-c346-4f9f-b897-c18733de9394&resource=https%3A%2F%2Fgraph.windows.net%2F &client_secret=olna8C*****Og%3D

Egy példa egy válasz ügyfél-hitelesítési jogkivonat engedélyezése:

    HTTP/1.1 200 OK

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1432039862","not_before":"1432035962","resource":"https://graph.windows.net/","access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRv****G5gUTV-kKorR-pg"}

### <a name="get-objectid-of-application-service-principal-in-user-azure-ad"></a>A felhasználó az Azure AD az alkalmazás egyszerű ObjectId beolvasása
A lekérdezés csak alkalmazás hozzáférési jogkivonatát, használja a [Azure AD Graph Szolgáltatásnevekről](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity) API használatával határozza meg a könyvtárban az alkalmazás egyszerű Objektumazonosítója.

A [GetObjectIdOfServicePrincipalInOrganization](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureADGraphAPIUtil.cs#) az ASP.net MVC mintaalkalmazásnak metódus valósítja meg a hívás.

A következő példa bemutatja, hogyan kérjen egy alkalmazás egyszerű. a0448380-c346-4f9f-b897-c18733de9394 az alkalmazás ügyfél-azonosító.

    GET https://graph.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/servicePrincipals?api-version=1.5&$filter=appId%20eq%20'a0448380-c346-4f9f-b897-c18733de9394' HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJK*****-kKorR-pg

A következő példa bemutatja egy olyan alkalmazás szolgáltatás kérelemre adott válasz egyszerű

    HTTP/1.1 200 OK

    {"odata.metadata":"https://graph.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/$metadata#directoryObjects/Microsoft.DirectoryServices.ServicePrincipal","value":[{"odata.type":"Microsoft.DirectoryServices.ServicePrincipal","objectType":"ServicePrincipal","objectId":"9b5018d4-6951-42ed-8a92-f11ec283ccec","deletionTimestamp":null,"accountEnabled":true,"appDisplayName":"CloudSense","appId":"a0448380-c346-4f9f-b897-c18733de9394","appOwnerTenantId":"62e173e9-301e-423e-bcd4-29121ec1aa24","appRoleAssignmentRequired":false,"appRoles":[],"displayName":"CloudSense","errorUrl":null,"homepage":"http://www.vipswapper.com/cloudsense","keyCredentials":[],"logoutUrl":null,"oauth2Permissions":[{"adminConsentDescription":"Allow the application to access CloudSense on behalf of the signed-in user.","adminConsentDisplayName":"Access CloudSense","id":"b7b7338e-683a-4796-b95e-60c10380de1c","isEnabled":true,"type":"User","userConsentDescription":"Allow the application to access CloudSense on your behalf.","userConsentDisplayName":"Access CloudSense","value":"user_impersonation"}],"passwordCredentials":[],"preferredTokenSigningKeyThumbprint":null,"publisherName":"vipswapper"quot;,"replyUrls":["http://www.vipswapper.com/cloudsense","http://www.vipswapper.com","http://vipswapper.com","http://vipswapper.azurewebsites.net","http://localhost:62080"],"samlMetadataUrl":null,"servicePrincipalNames":["http://www.vipswapper.com/cloudsense","a0448380-c346-4f9f-b897-c18733de9394"],"tags":["WindowsAzureActiveDirectoryIntegratedApp"]}]}

### <a name="get-azure-rbac-role-identifier"></a>Azure RBAC szerepkör-azonosító lekérése
A megfelelő RBAC szerepkört rendelni az egyszerű szolgáltatásnév, meg kell határoznia, az Azure RBAC-szerepkör azonosítóját.

A megfelelő RBAC szerepkört az alkalmazáshoz:

* Ha az alkalmazás csak figyeli az előfizetést, változtatások nélkül, az előfizetés csak olvasási engedélyre van szükség. Rendelje hozzá a **olvasó** szerepkör.
* Ha az alkalmazás kezeli az Azure az előfizetés létrehozása/módosítása vagy törlése entitások egyikét igényli a közreműködői engedélyekkel.
  * Kezelheti egy adott típusú erőforrás, rendelje hozzá az erőforrás-specifikus közreműködői szerepkör (virtuális gép közreműködő, virtuális hálózat közreműködő, tárolási fiók közreműködői, stb.)
  * Az összes erőforrástípus kezeléséhez rendelje hozzá a **közreműködő** szerepkör.

A szerepkör-hozzárendelést az alkalmazás akkor látható, a felhasználók számára, így jelölje be a legkevesebb szükséges jogosultsággal.

Hívja a [erőforrás-kezelő szerepkör-definíció API](https://docs.microsoft.com/rest/api/authorization/roledefinitions) kilistázhatja az összes Azure RBAC-szerepkörök és keresési, akkor az eredmény a kívánt szerepkör-definíció név szerinti keresése ismétlés.

A [GetRoleId](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L246) az ASP.net MVC mintaalkalmazás metódus valósítja meg a hívás.

A következő kérés példa bemutatja, hogyan kérhet az Azure RBAC szerepkör-azonosító. 09cbd307-aa71-4aca-b346-5f253e6e3ebb az előfizetés Azonosítóját.

    GET https://management.azure.com/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV*****fY2lGc5

A válasz a következő formátumban kell megadni:

    HTTP/1.1 200 OK

    {"value":[{"properties":{"roleName":"API Management Service Contributor","type":"BuiltInRole","description":"Lets you manage API Management services, but not access to them.","scope":"/","permissions":[{"actions":["Microsoft.ApiManagement/Services/*","Microsoft.Authorization/*/read","Microsoft.Resources/subscriptions/resources/read","Microsoft.Resources/subscriptions/resourceGroups/read","Microsoft.Resources/subscriptions/resourceGroups/resources/read","Microsoft.Resources/subscriptions/resourceGroups/deployments/*","Microsoft.Insights/alertRules/*","Microsoft.Support/*"],"notActions":[]}]},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/312a565d-c81f-4fd8-895a-4e21e48d571c","type":"Microsoft.Authorization/roleDefinitions","name":"312a565d-c81f-4fd8-895a-4e21e48d571c"},{"properties":{"roleName":"Application Insights Component Contributor","type":"BuiltInRole","description":"Lets you manage Application Insights components, but not access to them.","scope":"/","permissions":[{"actions":["Microsoft.Insights/components/*","Microsoft.Insights/webtests/*","Microsoft.Authorization/*/read","Microsoft.Resources/subscriptions/resources/read","Microsoft.Resources/subscriptions/resourceGroups/read","Microsoft.Resources/subscriptions/resourceGroups/resources/read","Microsoft.Resources/subscriptions/resourceGroups/deployments/*","Microsoft.Insights/alertRules/*","Microsoft.Support/*"],"notActions":[]}]},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/ae349356-3a1b-4a5e-921d-050484c6347e","type":"Microsoft.Authorization/roleDefinitions","name":"ae349356-3a1b-4a5e-921d-050484c6347e"}]}

Nem kell helyezni az API hívása. Miután meghatározta a jól ismert GUID azonosítója a szerepkör-definíció, hogyan hozhat létre a szerepkör-definíció azonosítója, mint:

    /subscriptions/{subscription_id}/providers/Microsoft.Authorization/roleDefinitions/{well-known-role-guid}

Az alábbiakban a gyakran használt beépített szerepkörök azonosítói:

| Szerepkör | GUID |
| --- | --- |
| Olvasó |acdd72a7-3385-48EF-bd42-f606fba81ae7 |
| Közreműködő |b24988ac-6180-42a0-ab88-20f7382dd24c |
| Virtuális gépek közreműködője |d73bb868-a0df-4d4d-bd69-98a00b01fccb |
| Virtuális hálózat közreműködő |b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
| Tárfiók-közreműködő |86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
| Webhelyek közreműködője |de139f84-1756-47ae-9be6-808fbbe84772 |
| Webes tarifacsomagok közreműködője |2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
| SQL Server közreműködője |6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
| SQL-adatbázisok közreműködője |9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |

### <a name="assign-rbac-role-to-application"></a>Az RBAC szerepkör hozzárendelése alkalmazás
Minden kell a megfelelő RBAC szerepkör hozzárendelése az egyszerű szolgáltatásnév segítségével az [erőforrás-kezelő szerepkör-hozzárendelés létrehozására](https://docs.microsoft.com/rest/api/authorization/roleassignments) API.

A [GrantRoleToServicePrincipalOnSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L170) az ASP.net MVC mintaalkalmazás metódus valósítja meg a hívás.

Egy példa egy kérelem RBAC szerepkör hozzárendelése alkalmazás:

    PUT https://management.azure.com/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/microsoft.authorization/roleassignments/4f87261d-2816-465d-8311-70a27558df4c?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiL*****FlwO1mM7Cw6JWtfY2lGc5
    Content-Type: application/json
    Content-Length: 230

    {"properties": {"roleDefinitionId":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7","principalId":"c3097b31-7309-4c59-b4e3-770f8406bad2"}}

A kérelem a következő értékeket használjuk:

| GUID | Leírás |
| --- | --- |
| 09cbd307-aa71-4aca-b346-5f253e6e3ebb |az előfizetés Azonosítóját |
| c3097b31-7309-4c59-b4e3-770f8406bad2 |az alkalmazás a szolgáltatás egyszerű objektum azonosítója |
| acdd72a7-3385-48EF-bd42-f606fba81ae7 |az olvasó szerepkört azonosítója |
| 4f87261d-2816-465d-8311-70a27558df4c |egy új guid létre új szerepkör-hozzárendelés |

A válasz a következő formátumban kell megadni:

    HTTP/1.1 201 Created

    {"properties":{"roleDefinitionId":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7","principalId":"c3097b31-7309-4c59-b4e3-770f8406bad2","scope":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb"},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleAssignments/4f87261d-2816-465d-8311-70a27558df4c","type":"Microsoft.Authorization/roleAssignments","name":"4f87261d-2816-465d-8311-70a27558df4c"}

### <a name="get-app-only-access-token-for-azure-resource-manager"></a>Szerezze be a csak alkalmazás-hozzáférési tokent az Azure Resource Manager
Az alkalmazás ellenőrzése rendelkezik a kívánt előfizetésnek érni, a vizsgálati művelet végrehajtásához a az előfizetés csak alkalmazás token használatával.

Ahhoz, hogy csak alkalmazás-hozzáférési tokent, kövesse az utasításokat szakaszából [csak alkalmazás-token beszerzése az Azure AD Graph API](#app-azure-ad-graph), egy másik érték megadásával az erőforrás paraméter:

    https://management.core.windows.net/

A [ServicePrincipalHasReadAccessToSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L110) metódus az ASP.NET MVC mintaalkalmazásnak lekéri csak alkalmazás hozzáférés token az Azure Resource Manager segítségével az Active Directory hitelesítési könyvtár a .NET-hez.

#### <a name="get-applications-permissions-on-subscription"></a>Az alkalmazás engedélyeket szerezni az előfizetésben
Ellenőrizze, hogy az alkalmazás hozzáférhet a kívánt Azure-előfizetéssel, akkor előfordulhat, hogy is hívja meg a [erőforrás-kezelő engedélyei](https://docs.microsoft.com/rest/api/authorization/permissions) API. Ez a megközelítés hogyan megadta, hogy a felhasználó rendelkezik-e a hozzáférés-kezelés jogosultságokkal az előfizetés hasonlít. Most, azonban a az alkalmazás csak jogkivonat az előző lépésben kapott engedélyeket API hívása.

A [ServicePrincipalHasReadAccessToSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L110) az ASP.NET MVC mintaalkalmazás metódus valósítja meg a hívás.

## <a name="manage-connected-subscriptions"></a>Csatlakoztatott előfizetések kezelése
A megfelelő RBAC szerepkör van rendelve az alkalmazás egyszerű szolgáltatásnév az előfizetéshez, amikor az alkalmazás is tartsa figyelés vagy kezelése csak alkalmazás-hozzáférési jogkivonatok segítségével csatlakozzon az Azure Resource Manager.

Ha egy előfizetés tulajdonosa a portál vagy a parancssori eszközök használatával az alkalmazás szerepkör-hozzárendelés eltávolítja, az alkalmazás most már érhessék el az adott előfizetéshez. Ebben az esetben kell értesíti a felhasználót, hogy az előfizetés létesített kapcsolat megszakadt, a az alkalmazáson kívül, és egy lehetőség, hogy a "javítás" a kapcsolódási számukra. "Javítás" újra létrehoznia a szerepkör-hozzárendelés, hogy a kapcsolat nélküli törölve lett.

Előfizetések kapcsolódni az alkalmazáshoz a felhasználó engedélyezve van, mint engedélyeznie kell a felhasználó előfizetések túl megszakítja a kapcsolatot. Az access felügyeleti szempontból válassza le azt jelenti, hogy az alkalmazás egyszerű rendelkezik-e az előfizetés a szerepkör-hozzárendelés eltávolítása. Ha szükséges az alkalmazásban, az előfizetés olyan állapotokat előfordulhat, hogy el kell távolítani túl.
Válassza le az előfizetés csak az előfizetés kezelése engedéllyel rendelkező felhasználók is.

A [RevokeRoleFromServicePrincipalOnSubscription metódus](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L200) , az ASP.net MVC mintaalkalmazás valósítja meg a hívás.

Ennyi az egész – a felhasználók most már egyszerűen csatlakozhat, és kezelheti az Azure-előfizetések az alkalmazással.
