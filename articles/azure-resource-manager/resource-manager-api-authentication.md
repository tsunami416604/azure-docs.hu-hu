---
title: Azure Active Directory hitelesítés és erőforrás-kezelő | Microsoft Docs
description: Fejlesztői útmutató a Azure Resource Manager API-val történő hitelesítéshez és Azure Active Directory az alkalmazások más Azure-előfizetésekkel való integrálásához.
author: dushyantgill
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: dugill
ms.openlocfilehash: 033f3ca9ca79903f884c625dc694b06a3e4fd04c
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2019
ms.locfileid: "72263004"
---
# <a name="use-resource-manager-authentication-api-to-access-subscriptions"></a>A Resource Manager hitelesítési API használata az előfizetések eléréséhez

Ez a cikk bemutatja, hogyan végezheti el a hitelesítést a Azure Resource Manager API-kkal, és hogyan férhet hozzá az erőforrásokhoz más előfizetésekben.

Az alkalmazás több módon is elérheti a Resource Manager API-kat:

1. **Felhasználó + alkalmazás-hozzáférés**: a bejelentkezett felhasználó erőforrásaihoz hozzáférő alkalmazások esetében. Ez a megközelítés alkalmazások, például webalkalmazások és parancssori eszközök esetében működik, amelyek csak az Azure-erőforrások "interaktív kezelésével" foglalkoznak.
2. **Csak alkalmazáshoz való hozzáférés**: a Daemon Servicest és az ütemezett feladatokat futtató alkalmazások esetében. Az alkalmazás identitása közvetlen hozzáférést kap az erőforrásokhoz. Ez a megközelítés olyan alkalmazásokhoz használható, amelyeknek hosszú távú (felügyelet nélküli) Azure-hozzáférésre van szükségük.

Ez a cikk lépésről lépésre bemutatja, hogyan hozhat létre egy alkalmazást, amely mindkét engedélyezési módszert alkalmazza. Bemutatja, hogyan végezheti el az egyes lépéseket a C#REST API vagy a használatával. A teljes ASP.NET MVC-alkalmazás a következő címen érhető el: [https://github.com/dushyantgill/VipSwapper/tree/master/CloudSense](https://github.com/dushyantgill/VipSwapper/tree/master/CloudSense).

## <a name="what-the-web-app-does"></a>A webalkalmazás

A webalkalmazás:

1. Signs – Azure-felhasználó.
2. Megkéri a felhasználót, hogy adjon hozzáférést a webalkalmazásnak a Resource Managerhez.
3. Beolvassa a felhasználó és az alkalmazás hozzáférési tokenjét a Resource Manager eléréséhez.
4. Tokent használ (a 3. lépésből) az alkalmazás egyszerű szolgáltatásnév hozzárendeléséhez az előfizetésben lévő szerepkörhöz. Ezzel a lépéssel az alkalmazás hosszú távú hozzáférést biztosít az előfizetéshez.
5. Csak az alkalmazáshoz tartozó hozzáférési tokent kapja meg.
6. A tokent használ (az 5. lépésből) az előfizetésben lévő erőforrások kezeléséhez a Resource Manageren keresztül.

Itt látható a webalkalmazás folyamata.

![Resource Manager-alapú hitelesítési folyamat](./media/resource-manager-api-authentication/Auth-Swim-Lane.png)

Felhasználóként adja meg a használni kívánt előfizetés előfizetési AZONOSÍTÓját:

![előfizetés-azonosító megadása](./media/resource-manager-api-authentication/sample-ux-1.png)

Válassza ki a bejelentkezéshez használandó fiókot.

![fiók kiválasztása](./media/resource-manager-api-authentication/sample-ux-2.png)

Adja meg a hitelesítő adatait.

![hitelesítő adatok megadása](./media/resource-manager-api-authentication/sample-ux-3.png)

Hozzáférés biztosítása az alkalmazás számára az Azure-előfizetésekhez:

![Hozzáférés biztosítása](./media/resource-manager-api-authentication/sample-ux-4.png)

A csatlakoztatott előfizetések kezelése:

![Előfizetés összekötése](./media/resource-manager-api-authentication/sample-ux-7.png)

## <a name="register-application"></a>Alkalmazás regisztrálása
A kódolás megkezdése előtt regisztrálja webalkalmazását a Azure Active Directory (AD) szolgáltatásban. Az alkalmazás regisztrálása létrehoz egy központi identitást az alkalmazáshoz az Azure AD-ben. A szolgáltatás alapvető információkat tartalmaz az alkalmazásról, például a OAuth, a válasz URL-címekről és az alkalmazás által a Azure Resource Manager API-k hitelesítéséhez és eléréséhez használt hitelesítő adatokról. Az alkalmazás regisztrálása a Microsoft API-k felhasználóhoz való hozzáféréséhez szükséges különböző delegált engedélyeket is rögzíti.

Az alkalmazás regisztrálásához tekintse meg a rövid útmutató [: alkalmazás regisztrálása a Microsoft Identity platformmal](../active-directory/develop/quickstart-register-app.md)című témakört. Adjon nevet az alkalmazásnak, és válassza a fiókok lehetőséget a támogatott fióktípus **bármely szervezeti címtárában** . Az átirányítási URL-cím mezőben adjon meg egy tartományt, amely a Azure Active Directoryhoz van társítva.

Ha AD-alkalmazásként szeretne bejelentkezni, szüksége lesz az alkalmazás AZONOSÍTÓJÁRA és a titkos kulcsra. Az alkalmazás azonosítója megjelenik az alkalmazás áttekintésében. A Secret és a Request API-engedélyek létrehozásával kapcsolatban lásd: rövid útmutató [: ügyfélalkalmazás konfigurálása a webes API-k eléréséhez](../active-directory/develop/quickstart-configure-app-access-web-apis.md). Adja meg az új ügyfél titkos kulcsát. API-engedélyek esetén válassza az **Azure Service Management**lehetőséget. Válassza a **delegált engedélyek** és **user_impersonation**lehetőséget.

### <a name="optional-configuration---certificate-credential"></a>Opcionális konfiguráció – tanúsítvány hitelesítő adatai
Az Azure AD az alkalmazások tanúsítvány-hitelesítő adatait is támogatja: létrehozhat egy önaláírt tanúsítványt, megtarthatja a titkos kulcsot, és hozzáadhatja a nyilvános kulcsot az Azure AD-alkalmazás regisztrálásához. A hitelesítéshez az alkalmazás egy kis adattartalmat küld az Azure AD-nek a titkos kulcs használatával, az Azure AD pedig a regisztrált nyilvános kulccsal ellenőrzi az aláírást.

Az AD-alkalmazás tanúsítvánnyal való létrehozásával kapcsolatos információkért lásd: [Azure PowerShell használata az egyszerű szolgáltatásnév létrehozásához az erőforrások eléréséhez](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-certificate-from-certificate-authority) vagy az [Azure CLI használatával az erőforrások eléréséhez](resource-group-authenticate-service-principal-cli.md).

## <a name="get-tenant-id-from-subscription-id"></a>Bérlő AZONOSÍTÓjának beolvasása az előfizetés-AZONOSÍTÓból
A Resource Manager hívásához használható token igényléséhez az alkalmazásnak ismernie kell az Azure-előfizetést üzemeltető Azure AD-bérlő bérlői AZONOSÍTÓját. Legvalószínűbb, hogy a felhasználók ismerik az előfizetési azonosítókat, de nem tudják, hogy a bérlői azonosítójuk Azure Active Directory. A felhasználó bérlői AZONOSÍTÓjának lekéréséhez kérje meg az előfizetés AZONOSÍTÓját a felhasználótól. Adja meg az előfizetés-azonosítót az előfizetésre vonatkozó kérelem küldésekor:

    https://management.azure.com/subscriptions/{subscription-id}?api-version=2015-01-01

A kérelem sikertelen, mert a felhasználó még nem jelentkezett be, de a válaszból lekérheti a bérlő AZONOSÍTÓját. Ebben a kivételben lekéri a bérlő AZONOSÍTÓját a **www-hitelesítés**válasz fejlécének értékéről. Ezt a megvalósítást a [GetDirectoryForSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L20) metódusban tekintheti meg.

## <a name="get-user--app-access-token"></a>Felhasználó + alkalmazás hozzáférési jogkivonatának beolvasása
Az alkalmazás átirányítja a felhasználót az Azure AD-re egy OAuth 2,0-as kéréssel, hogy hitelesítse a felhasználó hitelesítő adatait, és kérjen vissza egy engedélyezési kódot. Az alkalmazás az engedélyezési kóddal szerez hozzáférési tokent a Resource Managerhez. Az [ConnectSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/Controllers/HomeController.cs#L42) metódus létrehozza az engedélyezési kérelmet.

Ez a cikk a felhasználó hitelesítésére REST API kérelmeket mutatja be. A kód során a segítő kódtárakat is használhatja a hitelesítéshez. További információ ezekről a tárakról: [Azure Active Directory Authentication librarys](../active-directory/active-directory-authentication-libraries.md). Az Identitáskezelés alkalmazásban való integrálásával kapcsolatos útmutatásért lásd: [Azure Active Directory fejlesztői útmutatója](../active-directory/develop/v1-overview.md).

### <a name="auth-request-oauth-20"></a>Hitelesítési kérelem (OAuth 2,0)
Nyisson meg egy Open ID csatlakozási/OAuth 2.0-t az Azure AD engedélyezési végpontjának való engedélyezéshez:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize

A kérelemhez elérhető lekérdezési karakterlánc paramétereit az [engedélyezési kód kérése](../active-directory/develop/v1-protocols-oauth-code.md#request-an-authorization-code) című cikk ismerteti.

Az alábbi példa bemutatja, hogyan kérhet OAuth 2.0-engedélyt:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize?client_id=a0448380-c346-4f9f-b897-c18733de9394&response_mode=query&response_type=code&redirect_uri=http%3a%2f%2fwww.vipswapper.com%2fcloudsense%2fAccount%2fSignIn&resource=https%3a%2f%2fgraph.windows.net%2f&domain_hint=live.com

Az Azure AD hitelesíti a felhasználót, és ha szükséges, megkéri a felhasználót, hogy adjon engedélyt az alkalmazásnak. Az alkalmazás válasz URL-címére adja vissza az engedélyezési kódot. A kért response_mode függően az Azure AD visszaküldi az adatokat a lekérdezési karakterláncban vagy post adatként.

    code=AAABAAAAiL****FDMZBUwZ8eCAA&session_state=2d16bbce-d5d1-443f-acdf-75f6b0ce8850

### <a name="auth-request-open-id-connect"></a>Hitelesítési kérelem (Open ID-kapcsolat)
Ha nem csak a felhasználó Azure Resource Manager szeretné elérni, hanem lehetővé teszi a felhasználó számára, hogy az Azure AD-fiókjával jelentkezzen be az alkalmazásba, adjon meg egy Open ID-csatlakozási engedélyt a kéréshez. Az Open ID-vel való kapcsolódással az alkalmazás egy id_token is kap az Azure AD-ből, amelyet az alkalmazás használhat a felhasználó bejelentkezésére.

A kérelemhez elérhető lekérdezési karakterlánc-paramétereket a [bejelentkezési kérelem küldése](../active-directory/develop/v1-protocols-openid-connect-code.md#send-the-sign-in-request) című cikkben találja.

Példa az Open ID csatlakozási kérelemre:

     https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize?client_id=a0448380-c346-4f9f-b897-c18733de9394&response_mode=form_post&response_type=code+id_token&redirect_uri=http%3a%2f%2fwww.vipswapper.com%2fcloudsense%2fAccount%2fSignIn&resource=https%3a%2f%2fgraph.windows.net%2f&scope=openid+profile&nonce=63567Dc4MDAw&domain_hint=live.com&state=M_12tMyKaM8

Az Azure AD hitelesíti a felhasználót, és ha szükséges, megkéri a felhasználót, hogy adjon engedélyt az alkalmazásnak. Az alkalmazás válasz URL-címére adja vissza az engedélyezési kódot. A kért response_mode függően az Azure AD visszaküldi az adatokat a lekérdezési karakterláncban vagy post adatként.

Egy példa a nyitott azonosító csatlakozási válaszára:

    code=AAABAAAAiL*****I4rDWd7zXsH6WUjlkIEQxIAA&id_token=eyJ0eXAiOiJKV1Q*****T3GrzzSFxg&state=M_12tMyKaM8&session_state=2d16bbce-d5d1-443f-acdf-75f6b0ce8850

### <a name="token-request-oauth20-code-grant-flow"></a>Jogkivonat-kérelem (OAuth 2.0 kód engedélyezési folyamata)
Most, hogy az alkalmazás megkapta az engedélyezési kódot az Azure AD-től, itt az ideje, hogy beszerezze Azure Resource Manager hozzáférési jogkivonatát.  OAuth 2.0-kód küldése a jogkivonat-kérelemre az Azure AD jogkivonat-végpontnak:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Token

A kérelemhez elérhető lekérdezési karakterlánc paramétereit az [engedélyezési kód használata](../active-directory/develop/v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token) című cikkben találja.

Az alábbi példa egy, a jelszó hitelesítő adatait tartalmazó kód-engedélyezési tokenre vonatkozó kérelmet mutat be:

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=authorization_code&code=AAABAAAAiL9Kn2Z*****L1nVMH3Z5ESiAA&redirect_uri=http%3A%2F%2Flocalhost%3A62080%2FAccount%2FSignIn&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_secret=olna84E8*****goScOg%3D

Tanúsítvány hitelesítő adatainak használatakor hozzon létre egy JSON Web Token (JWT) és jelentkezzen be (RSA-SHA256) az alkalmazás tanúsítványának hitelesítő adatainak titkos kulcsával. A jogkivonat kiépítése az ügyfél- [hitelesítő adatok folyamatában](../active-directory/develop/v1-oauth2-client-creds-grant-flow.md#second-case-access-token-request-with-a-certificate)látható.

Az ügyfél-hitelesítéssel kapcsolatos részletekért tekintse meg az [Open ID kapcsolat specifikációját](https://openid.net/specs/openid-connect-core-1_0.html#ClientAuthentication) .

Az alábbi példa egy, a hitelesítő adatokkal rendelkező kód-engedélyezési jogkivonat kérelmét mutatja be:

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=authorization_code&code=AAABAAAAiL9Kn2Z*****L1nVMH3Z5ESiAA&redirect_uri=http%3A%2F%2Flocalhost%3A62080%2FAccount%2FSignIn&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&client_assertion=eyJhbG*****Y9cYo8nEjMyA

Példa a Code Grant tokenre:

    HTTP/1.1 200 OK

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1432039858","not_before":"1432035958","resource":"https://management.core.windows.net/","access_token":"eyJ0eXAiOiJKV1Q****M7Cw6JWtfY2lGc5A","refresh_token":"AAABAAAAiL9Kn2Z****55j-sjnyYgAA","scope":"user_impersonation","id_token":"eyJ0eXAiOiJKV*****-drP1J3P-HnHi9Rr46kGZnukEBH4dsg"}

#### <a name="handle-code-grant-token-response"></a>A kód engedélyezési jogkivonat-válaszának kezelése
A sikeres jogkivonat-válasz a (felhasználó + alkalmazás) hozzáférési jogkivonatát tartalmazza Azure Resource Managerhoz. Az alkalmazás használja ezt a hozzáférési jogkivonatot a felhasználó erőforrás-kezelőjének eléréséhez. Az Azure AD által kiadott hozzáférési jogkivonatok élettartama egy óra. Nem valószínű, hogy a webalkalmazásnak meg kell újítania a (felhasználó + alkalmazás) hozzáférési tokent. Ha meg kell újítania a hozzáférési jogkivonatot, használja az alkalmazás által a jogkivonat-válaszban kapott frissítési jogkivonatot. OAuth 2.0 jogkivonat-kérelem küldése az Azure AD jogkivonat-végpontnak:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Token

A frissítési kérelemmel használandó paramétereket a [hozzáférési jogkivonat frissítése](../active-directory/develop/v1-protocols-oauth-code.md#refreshing-the-access-tokens)című témakörben találja.

Az alábbi példa bemutatja, hogyan használható a frissítési jogkivonat:

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=refresh_token&refresh_token=AAABAAAAiL9Kn2Z****55j-sjnyYgAA&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_secret=olna84E8*****goScOg%3D

Bár a frissítési tokenek új hozzáférési jogkivonatok beszerzésére használhatók a Azure Resource Manager számára, nem alkalmasak az alkalmazás offline elérésére. A frissítési tokenek élettartama korlátozott, és a frissítési tokenek a felhasználóhoz vannak kötve. Ha a felhasználó elhagyja a szervezetet, a frissítési tokent használó alkalmazás elveszti a hozzáférést. Ez a megközelítés nem alkalmas olyan alkalmazások számára, amelyeket a csapatok az Azure-erőforrások kezeléséhez használnak.

## <a name="check-if-user-can-assign-access-to-subscription"></a>Ellenőrizze, hogy a felhasználó hozzárendelhet-e hozzáférést az előfizetéshez
Az alkalmazás most már rendelkezik egy jogkivonattal, amely hozzáfér Azure Resource Managerhoz a felhasználó számára. A következő lépés az alkalmazás összekötése az előfizetéssel. A csatlakozás után az alkalmazás akkor is kezelheti ezeket az előfizetéseket, ha a felhasználó nincs jelen (hosszú távú offline hozzáférés).

Az egyes kapcsolódási előfizetések esetében hívja meg a [Resource Manager-lista engedélyek](https://docs.microsoft.com/rest/api/authorization/permissions) API-ját annak megállapítására, hogy a felhasználó rendelkezik-e hozzáférési jogosultsággal az előfizetéshez.

A ASP.NET MVC minta alkalmazás [UserCanManagerAccessForSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L44) metódusa implementálja ezt a hívást.

Az alábbi példa bemutatja, hogyan kérhető le egy felhasználó engedélyeit egy előfizetéshez. a 83cfe939-2402-4581-b761-4f59b0a041e4 az előfizetés azonosítója.

    GET https://management.azure.com/subscriptions/83cfe939-2402-4581-b761-4f59b0a041e4/providers/microsoft.authorization/permissions?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiLC***lwO1mM7Cw6JWtfY2lGc5A

Az előfizetésre vonatkozó felhasználói engedélyek beszerzésére vonatkozó válasz például a következő:

    HTTP/1.1 200 OK

    {"value":[{"actions":["*"],"notActions":["Microsoft.Authorization/*/Write","Microsoft.Authorization/*/Delete"]},{"actions":["*/read"],"notActions":[]}]}

Az engedélyek API több engedélyt ad vissza. Minden engedély engedélyezett műveletből (**műveletből**) és nem engedélyezett műveletből (nem**megfelelő) áll**. Ha egy művelet szerepel az engedélyezett műveletekben, és nem szerepel az adott engedély nem engedélyezett műveleteiben, a felhasználó számára engedélyezett a művelet. a **Microsoft. Authorization/RoleAssignments/Write** az a művelet, amely hozzáférést biztosít a hozzáférés-kezelési jogosultságokhoz. Az alkalmazásnak el kell végeznie az engedélyek elemzését, hogy az egyes engedélyek **műveleteiben** **és az azokon** belüli eltérések között keressen egy regex egyezést a művelet karakterláncában.

## <a name="get-app-only-access-token"></a>Csak az alkalmazás hozzáférési jogkivonatának beolvasása
Most már tudja, hogy a felhasználó hozzá tud-e férni az Azure-előfizetéshez. A következő lépések a következők:

1. Rendelje hozzá a megfelelő RBAC-szerepkört az alkalmazás identitásához az előfizetésben.
2. Érvényesítse a hozzáférési hozzárendelést úgy, hogy lekérdezi az alkalmazás engedélyét az előfizetésen, vagy a Resource Managerhez csak alkalmazás-jogkivonat használatával fér hozzá.
3. Jegyezze fel a kapcsolatot az alkalmazásokban "csatlakoztatott előfizetések" adatstruktúrában – az előfizetés AZONOSÍTÓjának megőrzése.

Nézzük meg közelebbről az első lépést. A megfelelő RBAC-szerepkör az alkalmazás identitásához való hozzárendeléséhez meg kell határoznia a következőket:

* Az alkalmazás identitásának azonosítója a felhasználó Azure Active Directory
* Annak az RBAC-szerepkörnek az azonosítója, amelyre az alkalmazásnak szüksége van az előfizetéshez

Ha az alkalmazás egy Azure AD-beli felhasználót hitelesít, az Azure AD-ben létrehoz egy egyszerű szolgáltatásnevet az alkalmazáshoz. Az Azure lehetővé teszi az RBAC-szerepkörök hozzárendelését az egyszerű szolgáltatásokhoz, hogy közvetlen hozzáférést biztosítson az Azure-erőforrások megfelelő alkalmazásaihoz. Ez a művelet pontosan ezt kívánja elvégezni. Az Azure AD Graph API lekérdezésével meghatározhatja az alkalmazás egyszerű szolgáltatásnév azonosítóját a bejelentkezett felhasználó Azure AD-ben.

Csak a Azure Resource Manager hozzáférési jogkivonata van – új hozzáférési jogkivonatra van szükség az Azure AD-Graph API meghívásához. Az Azure AD minden alkalmazásának van engedélye a saját szolgáltatásnév-objektum lekérdezésére, így az alkalmazás-hozzáférési jogkivonat elegendő.

<a id="app-azure-ad-graph" />

### <a name="get-app-only-access-token-for-azure-ad-graph-api"></a>Csak alkalmazás-hozzáférési jogkivonat beszerzése az Azure AD-Graph API

Az alkalmazás hitelesítéséhez és a token Azure AD-Graph APIhoz való lekéréséhez adja ki az ügyfél hitelesítő adatait az Azure AD token-végpontnak (**https: \//login. microsoftonline. com/{directory_domain_name}/OAuth2/token**) OAuth.

A ASP.net MVC minta alkalmazás [GetObjectIdOfServicePrincipalInOrganization](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureADGraphAPIUtil.cs) metódusa csak alkalmazás-hozzáférési jogkivonatot kap a Graph API a .net-Active Directory-hitelesítési tár használatával.

A kérelemhez elérhető lekérdezési karakterlánc-paramétereket a [hozzáférési jogkivonat kérése](../active-directory/develop/v1-oauth2-client-creds-grant-flow.md#request-an-access-token) című cikkben találja.

Példa az ügyfél hitelesítő adatainak megadására szolgáló jogkivonat igénylésére:

    POST https://login.microsoftonline.com/62e173e9-301e-423e-bcd4-29121ec1aa24/oauth2/token HTTP/1.1
    Content-Type: application/x-www-form-urlencoded
    Content-Length: 187</pre>
    <pre>grant_type=client_credentials&client_id=a0448380-c346-4f9f-b897-c18733de9394&resource=https%3A%2F%2Fgraph.windows.net%2F &client_secret=olna8C*****Og%3D

Példa az ügyfél hitelesítő adatainak megadására szolgáló tokenre:

    HTTP/1.1 200 OK

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1432039862","not_before":"1432035962","resource":"https://graph.windows.net/","access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRv****G5gUTV-kKorR-pg"}

### <a name="get-objectid-of-application-service-principal-in-user-azure-ad"></a>A felhasználó Azure AD-beli ObjectId beolvasása
Most az alkalmazáshoz tartozó hozzáférési tokent használva lekérdezheti az [Azure ad Graph szolgáltatás](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#serviceprincipal-entity) rendszertagjainak API-ját, hogy meghatározza az alkalmazás egyszerű szolgáltatásnév azonosítóját a címtárban.

A ASP.net MVC minta alkalmazás [GetObjectIdOfServicePrincipalInOrganization](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureADGraphAPIUtil.cs#) metódusa implementálja ezt a hívást.

Az alábbi példa bemutatja, hogyan kérheti le az alkalmazás egyszerű szolgáltatását. a a0448380-c346-4f9f-b897-c18733de9394 az alkalmazás ügyfél-azonosítója.

    GET https://graph.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/servicePrincipals?api-version=1.5&$filter=appId%20eq%20'a0448380-c346-4f9f-b897-c18733de9394' HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJK*****-kKorR-pg

Az alábbi példa az alkalmazás egyszerű szolgáltatására vonatkozó kérésre adott választ mutat be.

    HTTP/1.1 200 OK

    {"odata.metadata":"https://graph.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/$metadata#directoryObjects/Microsoft.DirectoryServices.ServicePrincipal","value":[{"odata.type":"Microsoft.DirectoryServices.ServicePrincipal","objectType":"ServicePrincipal","objectId":"9b5018d4-6951-42ed-8a92-f11ec283ccec","deletionTimestamp":null,"accountEnabled":true,"appDisplayName":"CloudSense","appId":"a0448380-c346-4f9f-b897-c18733de9394","appOwnerTenantId":"62e173e9-301e-423e-bcd4-29121ec1aa24","appRoleAssignmentRequired":false,"appRoles":[],"displayName":"CloudSense","errorUrl":null,"homepage":"http://www.vipswapper.com/cloudsense","keyCredentials":[],"logoutUrl":null,"oauth2Permissions":[{"adminConsentDescription":"Allow the application to access CloudSense on behalf of the signed-in user.","adminConsentDisplayName":"Access CloudSense","id":"b7b7338e-683a-4796-b95e-60c10380de1c","isEnabled":true,"type":"User","userConsentDescription":"Allow the application to access CloudSense on your behalf.","userConsentDisplayName":"Access CloudSense","value":"user_impersonation"}],"passwordCredentials":[],"preferredTokenSigningKeyThumbprint":null,"publisherName":"vipswapper"quot;,"replyUrls":["http://www.vipswapper.com/cloudsense","http://www.vipswapper.com","http://vipswapper.com","http://vipswapper.azurewebsites.net","http://localhost:62080"],"samlMetadataUrl":null,"servicePrincipalNames":["http://www.vipswapper.com/cloudsense","a0448380-c346-4f9f-b897-c18733de9394"],"tags":["WindowsAzureActiveDirectoryIntegratedApp"]}]}

### <a name="get-azure-rbac-role-identifier"></a>Azure RBAC szerepkör-azonosító beszerzése
Ahhoz, hogy a megfelelő RBAC-szerepkört hozzárendelje a szolgáltatáshoz, meg kell határoznia az Azure RBAC szerepkör azonosítóját.

Az alkalmazás megfelelő RBAC-szerepköre:

* Ha az alkalmazás csak az előfizetést figyeli, a módosítások végrehajtása nélkül, csak az előfizetésre vonatkozó olvasói engedélyek szükségesek. Rendelje hozzá az **olvasó** szerepkört.
* Ha az alkalmazás az Azure-t felügyeli az előfizetéssel, az entitások létrehozásával/módosításával/törlésével, a közreműködői engedélyek egyikét igényli.
  * Egy adott típusú erőforrás kezeléséhez rendelje hozzá az erőforrás-specifikus közreműködő szerepköröket (a virtuális gép közreműködőjét, Virtual Network közreműködőt, a Storage-fiók közreműködőjét stb.).
  * Bármely erőforrástípus kezeléséhez rendelje hozzá a **közreműködő** szerepkört.

Az alkalmazáshoz tartozó szerepkör-hozzárendelés látható a felhasználók számára, ezért válassza ki a legkevésbé szükséges jogosultságot.

Hívja meg a [Resource Manager szerepkör-definíciós API](https://docs.microsoft.com/rest/api/authorization/roledefinitions) -t az összes Azure RBAC-szerepkör listázásához, majd ismételje meg az eredményt, hogy megkeresse a szerepkör-definíciót név alapján.

A ASP.net MVC minta alkalmazás [GetRoleId](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L246) metódusa implementálja ezt a hívást.

Az alábbi példa bemutatja, hogyan kérhető le az Azure RBAC szerepkör-azonosító. a 09cbd307-aa71-4aca-b346-5f253e6e3ebb az előfizetés azonosítója.

    GET https://management.azure.com/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV*****fY2lGc5

A válasz formátuma a következő:

    HTTP/1.1 200 OK

    {"value":[{"properties":{"roleName":"API Management Service Contributor","type":"BuiltInRole","description":"Lets you manage API Management services, but not access to them.","scope":"/","permissions":[{"actions":["Microsoft.ApiManagement/Services/*","Microsoft.Authorization/*/read","Microsoft.Resources/subscriptions/resources/read","Microsoft.Resources/subscriptions/resourceGroups/read","Microsoft.Resources/subscriptions/resourceGroups/resources/read","Microsoft.Resources/subscriptions/resourceGroups/deployments/*","Microsoft.Insights/alertRules/*","Microsoft.Support/*"],"notActions":[]}]},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/312a565d-c81f-4fd8-895a-4e21e48d571c","type":"Microsoft.Authorization/roleDefinitions","name":"312a565d-c81f-4fd8-895a-4e21e48d571c"},{"properties":{"roleName":"Application Insights Component Contributor","type":"BuiltInRole","description":"Lets you manage Application Insights components, but not access to them.","scope":"/","permissions":[{"actions":["Microsoft.Insights/components/*","Microsoft.Insights/webtests/*","Microsoft.Authorization/*/read","Microsoft.Resources/subscriptions/resources/read","Microsoft.Resources/subscriptions/resourceGroups/read","Microsoft.Resources/subscriptions/resourceGroups/resources/read","Microsoft.Resources/subscriptions/resourceGroups/deployments/*","Microsoft.Insights/alertRules/*","Microsoft.Support/*"],"notActions":[]}]},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/ae349356-3a1b-4a5e-921d-050484c6347e","type":"Microsoft.Authorization/roleDefinitions","name":"ae349356-3a1b-4a5e-921d-050484c6347e"}]}

Az API-t nem kell folyamatosan meghívnia. Miután meghatározta a szerepkör-definíció jól ismert GUID azonosítóját, a szerepkör-definíció AZONOSÍTÓját a következőképpen hozhatja létre:

    /subscriptions/{subscription_id}/providers/Microsoft.Authorization/roleDefinitions/{well-known-role-guid}

Itt láthatók a leggyakrabban használt beépített szerepkörök azonosítói:

| Szerepkör | GUID |
| --- | --- |
| Olvasó |acdd72a7-3385-48ef-bd42-f606fba81ae7 |
| Közreműködő |b24988ac-6180-42a0-ab88-20f7382dd24c |
| Virtuális gépek közreműködője |d73bb868-a0df-4d4d-bd69-98a00b01fccb |
| Virtual Network közreműködő |b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
| Storage-fiók közreműködői |86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
| Webhely közreműködői |de139f84-1756-47ae-9be6-808fbbe84772 |
| Webes csomag közreműködői |2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
| SQL Server közreműködő |6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
| SQL-adatbázis közreműködői |9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |

### <a name="assign-rbac-role-to-application"></a>RBAC-szerepkör kiosztása az alkalmazáshoz
Minden szükséges, hogy a megfelelő RBAC-szerepkört az egyszerű szolgáltatáshoz rendelje hozzá a [Resource Manager szerepkör-hozzárendelési](https://docs.microsoft.com/rest/api/authorization/roleassignments) API használatával.

A ASP.net MVC minta alkalmazás [GrantRoleToServicePrincipalOnSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L170) metódusa implementálja ezt a hívást.

Példa RBAC szerepkör hozzárendelésére az alkalmazáshoz:

    PUT https://management.azure.com/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/microsoft.authorization/roleassignments/4f87261d-2816-465d-8311-70a27558df4c?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiL*****FlwO1mM7Cw6JWtfY2lGc5
    Content-Type: application/json
    Content-Length: 230

    {"properties": {"roleDefinitionId":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c","principalId":"c3097b31-7309-4c59-b4e3-770f8406bad2"}}

A kérelemben a következő értékek használatosak:

| GUID | Leírás |
| --- | --- |
| 09cbd307-aa71-4aca-b346-5f253e6e3ebb |az előfizetés azonosítója |
| c3097b31-7309-4c59-b4e3-770f8406bad2 |az alkalmazás egyszerű szolgáltatásának objektumazonosító |
| b24988ac-6180-42a0-ab88-20f7382dd24c |a közreműködő szerepkör azonosítója |
| 4f87261d-2816-465d-8311-70a27558df4c |az új szerepkör-hozzárendeléshez létrehozott új GUID |

A válasz formátuma a következő:

    HTTP/1.1 201 Created

    {"properties":{"roleDefinitionId":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c","principalId":"c3097b31-7309-4c59-b4e3-770f8406bad2","scope":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb"},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleAssignments/4f87261d-2816-465d-8311-70a27558df4c","type":"Microsoft.Authorization/roleAssignments","name":"4f87261d-2816-465d-8311-70a27558df4c"}

### <a name="get-app-only-access-token-for-azure-resource-manager"></a>Csak alkalmazás-hozzáférési jogkivonat beszerzése Azure Resource Manager
Annak ellenőrzéséhez, hogy az alkalmazás hozzáfér-e az előfizetéshez, végezzen el egy tesztelési feladatot az előfizetésben egy csak alkalmazási jogkivonat használatával.

Csak alkalmazás-hozzáférési jogkivonat beszerzéséhez kövesse az [alkalmazás csak az Azure ad-beli hozzáférési token Beszerzése Graph API](#app-azure-ad-graph), az erőforrás-paraméter eltérő értékének beolvasása című szakasz utasításait:

    https://management.core.windows.net/

A ASP.NET MVC minta alkalmazás [ServicePrincipalHasReadAccessToSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L110) metódusa csak alkalmazás-hozzáférési jogkivonatot kap a Azure Resource Manager a .net-Active Directory-hitelesítési tár használatával.

#### <a name="get-applications-permissions-on-subscription"></a>Alkalmazás engedélyeinek beolvasása az előfizetéshez
Ha szeretné megtekinteni, hogy az alkalmazás hozzáférhet-e az Azure-előfizetéshez, meghívhatja a [Resource Manager-engedélyek](https://docs.microsoft.com/rest/api/authorization/permissions) API-t is. Ez a megközelítés hasonló ahhoz, hogy Ön hogyan határozza meg, hogy a felhasználó rendelkezik-e hozzáférés-kezelési jogosultsággal az előfizetéshez. Ezúttal azonban hívja meg az engedélyek API-t az előző lépésben kapott alkalmazás-hozzáférési jogkivonattal.

A ASP.NET MVC minta alkalmazás [ServicePrincipalHasReadAccessToSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L110) metódusa implementálja ezt a hívást.

## <a name="manage-connected-subscriptions"></a>Csatlakoztatott előfizetések kezelése
Ha a megfelelő RBAC-szerepkör hozzá van rendelve az alkalmazás egyszerű szolgáltatásához az előfizetésben, az alkalmazás csak az Azure Resource Manager alkalmazáshoz tartozó hozzáférési jogkivonatok használatával tudja figyelni/felügyelni.

Ha egy előfizetés tulajdonosa eltávolítja az alkalmazás szerepkör-hozzárendelését a portál vagy a parancssori eszközök használatával, az alkalmazás már nem fér hozzá az előfizetéshez. Ebben az esetben tájékoztatnia kell a felhasználót arról, hogy az előfizetéssel létesített kapcsolat az alkalmazáson kívülről lett kiválasztva, és lehetőséget biztosít számukra a kapcsolat javítására. A "Repair" utasítás újból létrehozza a kapcsolat nélküli módban törölt szerepkör-hozzárendelést.

Ugyanúgy, ahogy engedélyezte a felhasználónak az alkalmazáshoz való csatlakozást, engedélyeznie kell a felhasználó számára az előfizetések leválasztását is. A hozzáférés-kezelési szempontból a kapcsolat bontása azt jelenti, hogy eltávolítja a szerepkör-hozzárendelést, amelyet az alkalmazás az előfizetéshez tartozó szolgáltatásnév tartalmaz. Előfordulhat, hogy az előfizetéshez tartozó alkalmazás bármely állapota is el lesz távolítva.
Csak az előfizetésen keresztüli hozzáférés-kezelési engedéllyel rendelkező felhasználók választhatják le az előfizetést.

A ASP.net MVC minta alkalmazás [RevokeRoleFromServicePrincipalOnSubscription metódusa](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L200) implementálja ezt a hívást.

Ez – a felhasználók most már könnyedén csatlakozhatnak és kezelhetik az Azure-előfizetéseit az alkalmazással.
