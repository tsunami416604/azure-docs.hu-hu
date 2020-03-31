---
title: Ismerje meg az OAuth 2.0 engedélyezési kód folyamatát az Azure AD-ben
description: Ez a cikk ismerteti, hogyan http-üzenetek használatával engedélyezheti a hozzáférést a webalkalmazások és webes API-k a bérlő ben az Azure Active Directory és az OAuth 2.0 használatával.
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 3ec7cf5a45ce31cde923dce521636589cfcda786
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154457"
---
# <a name="authorize-access-to-azure-active-directory-web-applications-using-the-oauth-20-code-grant-flow"></a>Hozzáférés engedélyezése Azure Active Directory webes alkalmazásokhoz az OAuth 2.0 kódengedélyezési folyamat használatával

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

> [!NOTE]
>  Ha nem mondja meg a kiszolgálónak, hogy milyen erőforrást kíván hívni, akkor a kiszolgáló nem indítja el az adott erőforrás feltételes hozzáférési házirendjeit. Tehát annak érdekében, hogy az MFA-eseményindító, meg kell, hogy egy erőforrást az URL-t. 
>

Az Azure Active Directory (Azure AD) az OAuth 2.0 használatával teszi lehetővé a webalkalmazásokhoz és webes API-khez való hozzáférés engedélyezését az Azure AD-bérlőben. Ez az útmutató nyelvfüggetlen, és leírja, hogyan küldhet és fogadhat [HTTP-üzeneteket a nyílt forráskódú tárak](active-directory-authentication-libraries.md)használata nélkül.

Az OAuth 2.0 engedélyezési kódfolyamatát [az OAuth 2.0 specifikáció 4.1 szakasza](https://tools.ietf.org/html/rfc6749#section-4.1)ismerteti. A hitelesítés és engedélyezés a legtöbb alkalmazástípusban, beleértve a webalkalmazásokat és a natívan telepített alkalmazásokat is, hitelesítésre és engedélyezésre szolgál.

## <a name="register-your-application-with-your-ad-tenant"></a>Alkalmazás regisztrálása az AD-bérlőben
Először regisztrálja az alkalmazást az Azure Active Directory (Azure AD) bérlőjével. Ekkor kapni fog egy alkalmazásazonosítót az alkalmazáshoz, amely immár képes jogkivonatokat fogadni.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
   
1. Válassza ki az Azure AD-bérlőt a fiók kiválasztásával a lap jobb felső sarkában, majd válassza ki a **Címtár-navigáció tikkasztása,** majd válassza ki a megfelelő bérlőt. 
   - Hagyja ki ezt a lépést, ha csak egy Azure AD-bérlő van a fiókjában, vagy ha már kiválasztotta a megfelelő Azure AD-bérlőt.
   
1. Az Azure Portalon keresse meg és válassza az **Azure Active Directoryt.**
   
1. Az **Azure Active Directory** bal oldali menüjében válassza az **Alkalmazásregisztrációk**lehetőséget, majd az **Új regisztráció**lehetőséget.
   
1. Kövesse az utasításokat az új alkalmazás létrehozásához. Nem számít, ha ez egy webes alkalmazás vagy egy nyilvános kliens (mobil & asztali) alkalmazás ez a bemutató, de ha szeretne konkrét példákat webes alkalmazások vagy nyilvános kliens alkalmazások, nézd meg [a quickstarts](v1-overview.md).
   
   - A **név** az alkalmazás neve, amely a végfelhasználók számára ad leírást az alkalmazásról.
   - A **Támogatott fióktípusok csoportban**válassza a Fiókok lehetőséget **bármely szervezeti címtárban és személyes Microsoft-fiókban.**
   - Adja meg az **átirányítási URI-t**. Webes alkalmazások esetén ez az alkalmazás alap URL-címe, ahol a felhasználók bejelentkezhetnek.  Például: `http://localhost:12345`. Nyilvános ügyfél (mobil & asztali), az Azure AD használja, hogy token válaszok at. Adja meg az alkalmazáshoz tartozó értéket.  Például: `http://MyFirstAADApp`.
   <!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->  
   
1. Miután befejezte a regisztrációt, az Azure AD egyedi ügyfélazonosítót **(az alkalmazásazonosítót)** rendel hozzá az alkalmazáshoz. Erre az értékre a következő szakaszokban van szükség, ezért másolja az alkalmazáslapról.
   
1. Ha meg szeretné találni az alkalmazást az Azure Portalon, válassza **az Alkalmazásregisztrációk**lehetőséget, majd válassza **az Összes alkalmazás megtekintése**lehetőséget.

## <a name="oauth-20-authorization-flow"></a>OAuth 2.0 engedélyezési folyamat

Magas szinten az alkalmazás teljes engedélyezési folyamata egy kicsit így néz ki:

![OAuth hitelesítési kódfolyamat](./media/v1-protocols-oauth-code/active-directory-oauth-code-flow-native-app.png)

## <a name="request-an-authorization-code"></a>Engedélyezési kód kérése

Az engedélyezési kód folyamata azzal kezdődik, `/authorize` hogy az ügyfél a végpontra irányítja a felhasználót. Ebben a kérésben az ügyfél jelzi a felhasználótól beszerezni szükséges engedélyeket. Az OAuth 2.0 engedélyezési végponta a bérlő kiválasztásával **alkalmazásregisztrációk > végpontok** az Azure Portalon.

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%3A12345
&response_mode=query
&resource=https%3A%2F%2Fservice.contoso.com%2F
&state=12345
```

| Paraméter |  | Leírás |
| --- | --- | --- |
| Bérlő |kötelező |A `{tenant}` kérelem elérési útjának értéke segítségével szabályozhatja, hogy ki jelentkezhet be az alkalmazásba. Az engedélyezett értékek például bérlői `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` `contoso.onmicrosoft.com` azonosítók, vagy `common` bérlőfüggetlen jogkivonatok |
| client_id |kötelező |Az alkalmazáshoz rendelt alkalmazásazonosító, amikor regisztrálta az Azure AD-vel. Ez az Azure Portalon található. Kattintson az **Azure Active Directory** a szolgáltatások oldalsávján, kattintson az Alkalmazás **regisztrációk**, és válassza ki az alkalmazást. |
| response_type |kötelező |`code` Tartalmaznia kell az engedélyezési kód folyamat. |
| redirect_uri |Ajánlott |Az alkalmazás redirect_uri, ahol az alkalmazás elküldheti és fogadhatja a hitelesítési válaszokat. Pontosan meg kell egyeznie a portálon regisztrált redirect_uris egyikével, kivéve, hogy url-kódolni kell. Natív & mobilalkalmazások esetén a alapértelmezett `https://login.microsoftonline.com/common/oauth2/nativeclient`értéket kell használnia. |
| response_mode |választható |Megadja azt a módszert, amelyet az eredményül kapott jogkivonat nak az alkalmazásnak való visszaküldéséhez kell használni. Lehet `query`, `fragment`vagy `form_post`. `query`a kódot lekérdezési karakterlánc-paraméterként adja meg az átirányítási URI-n. Ha azonosító tokent kér az implicit folyamat használatával, `query` akkor nem használhatja az [OpenID specifikációban megadottak szerint.](https://openid.net/specs/oauth-v2-multiple-response-types-1_0.html#Combinations) Ha csak a kódot kéri, használhatja `query` `fragment`a `form_post`, vagy a . `form_post`végrehajtegy POST-et, amely tartalmazza a kódot az átirányítási URI-ba. Az alapértelmezett `query` beállítás egy kódfolyamat.  |
| state |Ajánlott |A kérelemben szereplő érték, amely a jogkivonat-válaszban is megjelenik. A véletlenszerűen generált egyedi értéket általában a [helyek közötti kérelmek hamisítási támadásainak megelőzésére](https://tools.ietf.org/html/rfc6749#section-10.12)használják. Az állapot arra is használható, hogy a hitelesítési kérelem bekövetkezése előtt kódolja a felhasználó állapotát az alkalmazásban, például azt az oldalt vagy nézetet, amelyen voltak. |
| Erőforrás | Ajánlott |A célwebes API (biztonságos erőforrás) alkalmazásazonosító-URI-ja. Az alkalmazásazonosító URI jának megkereséséhez az Azure Portalon kattintson az **Azure Active Directory**elemre, kattintson az **Alkalmazásregisztrációk**elemre, nyissa meg az alkalmazás **Beállítások lapját,** majd kattintson a **Tulajdonságok gombra.** Ez is lehet egy `https://graph.microsoft.com`külső erőforrás, mint a . Erre az egyik engedélyezési vagy jogkivonat-kérelemben van szükség. Annak érdekében, hogy kevesebb hitelesítési kérdés helyezze el az engedélyezési kérelemben annak biztosítása érdekében, hogy a felhasználó beleegyezést kapott. |
| scope | **Figyelmen kívül hagyja** | A v1 Azure AD-alkalmazások esetében a hatóköröket statikusan kell konfigurálni az Azure Portalon az alkalmazások **beállításai**, **szükséges engedélyek**alatt. |
| Gyors |választható |Adja meg a szükséges felhasználói beavatkozás típusát.<p> Az érvényes értékek a következők: <p> *bejelentkezés*: A felhasználót újra kell hitelesíteni. <p> *select_account*: A rendszer kéri a felhasználót, hogy válasszon ki egy fiókot, megszakítva az egyszeri bejelentkezést. A felhasználó kiválaszthat egy meglévő bejelentkezett fiókot, megadhatja a hitelesítő adatait egy megjegyzett fiókhoz, vagy teljesen más fiókot használhat. <p> *hozzájárulás*: A felhasználó beleegyezése meglett adva, de azt frissíteni kell. A felhasználót meg kell kérni a beleegyezésre. <p> *admin_consent*: A rendszergazdát a szervezet összes felhasználója nevében meg kell kérni a hozzájárulásra. |
| login_hint |választható |A felhasználó bejelentkezési lapjának felhasználónév/e-mail cím mezőjének előzetes kitöltésére használható, ha előre ismeri a felhasználónevét. Az alkalmazások gyakran használják ezt a paramétert az újrahitelesítés során, miután már kinyerték a felhasználónevet egy korábbi bejelentkezésből a `preferred_username` jogcím használatával. |
| domain_hint |választható |A felhasználó által a bejelentkezéshez használt bérlővel vagy tartománnyal kapcsolatos tippet tartalmaz. A domain_hint értéke a bérlő regisztrált tartománya. Ha a bérlő egy helyszíni könyvtárba van összeadva, az AAD átirányítja a megadott bérlői összevonási kiszolgálóra. |
| code_challenge_method | Ajánlott    | A `code_verifier` `code_challenge` paraméter kódolására használt módszer. Lehet az `plain` egyik `S256`vagy . Ha kizárt, `code_challenge` akkor a program `code_challenge` egyszerű szövegnek tekinti, ha szerepel. Az Azure AAD 1.0-s v1.0-s és `plain` `S256`a. További információt a [PKCE RFC című](https://tools.ietf.org/html/rfc7636)témakörben talál. |
| code_challenge        | Ajánlott    | Az engedélyezési kódtámogatások biztonságossá tétele a Kódcsere próbakulcsán (PKCE) keresztül egy natív vagy nyilvános ügyféltől. Kötelező, `code_challenge_method` ha benne van. További információt a [PKCE RFC című](https://tools.ietf.org/html/rfc7636)témakörben talál. |

> [!NOTE]
> Ha a felhasználó egy szervezet része, a szervezet rendszergazdája beleegyezhet vagy elutasíthatja a felhasználó nevében, vagy engedélyezheti a felhasználó beleegyezését. A felhasználó csak akkor kaphat beleegyezési engedélyt, ha azt a rendszergazda engedélyezi.
>
>

Ezen a ponton a felhasználó nak meg kell adnia a hitelesítő adatait, és hozzájárul az alkalmazás által kért engedélyeket az Azure Portalon. Miután a felhasználó hitelesíti magát, és megadja a jóváhagyást, az Azure AD választ küld az alkalmazásnak a `redirect_uri` kérelemben megadott címen a kóddal.

### <a name="successful-response"></a>Sikeres válasz
A sikeres válasz így nézhet ki:

```
GET  HTTP/1.1 302 Found
Location: http://localhost:12345/?code= AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrqqf_ZT_p5uEAEJJ_nZ3UmphWygRNy2C3jJ239gV_DBnZ2syeg95Ki-374WHUP-i3yIhv5i-7KU2CEoPXwURQp6IVYMw-DjAOzn7C3JCu5wpngXmbZKtJdWmiBzHpcO2aICJPu1KvJrDLDP20chJBXzVYJtkfjviLNNW7l7Y3ydcHDsBRKZc3GuMQanmcghXPyoDg41g8XbwPudVh7uCmUponBQpIhbuffFP_tbV8SNzsPoFz9CLpBCZagJVXeqWoYMPe2dSsPiLO9Alf_YIe5zpi-zY4C3aLw5g9at35eZTfNd0gBRpR5ojkMIcZZ6IgAA&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF&state=D79E5777-702E-4260-9A62-37F75FF22CCE
```

| Paraméter | Leírás |
| --- | --- |
| admin_consent |Az érték igaz, ha a rendszergazda hozzájárult a hozzájárulási kérelem kéréshez. |
| code |Az alkalmazás által kért engedélyezési kód. Az alkalmazás az engedélyezési kód segítségével hozzáférési jogkivonatot kérhet a célerőforráshoz. |
| session_state |Az aktuális felhasználói munkamenetet azonosító egyedi érték. Ez az érték guid, de vizsgálat nélkül átadott átlátszatlan értékként kell kezelni. |
| state |Ha a kérelem ben szerepel egy állapotparaméter, a válaszban ugyanannak az értéknek kell megjelennie. Ez egy jó gyakorlat az alkalmazás ellenőrizze, hogy az állapotértékek a kérelemben és a válaszban azonosak a válasz használata előtt. Ez segít észlelni a [helyek közötti kérelem hamisítás (CSRF) támadások](https://tools.ietf.org/html/rfc6749#section-10.12) az ügyfél ellen. |

### <a name="error-response"></a>Hibaválasz
Hibaválaszok is küldhető a, `redirect_uri` hogy az alkalmazás képes kezelni őket megfelelően.

```
GET http://localhost:12345/?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Paraméter | Leírás |
| --- | --- |
| error |Az [OAuth 2.0 engedélyezési keretrendszer 5.2](https://tools.ietf.org/html/rfc6749)szakaszában meghatározott hibakódérték. A következő táblázat ismerteti a hibakódokat, amelyek et az Azure AD visszaad. |
| error_description |A hiba részletesebb leírása. Ez az üzenet nem végfelhasználóbarát. |
| state |Az állapotérték egy véletlenszerűen generált, nem újrafelhasznált érték, amely a kérelemben elküldésre kerül, és a válaszban visszaadja a helyközi kérelmek hamisításának (CSRF) támadásainak megakadályozása érdekében. |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Hibakódok az engedélyezési végponthibáihoz
Az alábbi táblázat a hibaválasz `error` paraméterében visszaadható különböző hibakódokat ismerteti.

| Hibakód | Leírás | Ügyfélművelet |
| --- | --- | --- |
| invalid_request |Protokollhiba, például hiányzó szükséges paraméter. |Javítsa ki és küldje el újra a kérelmet. Ez egy fejlesztési hiba, és általában fogott a kezdeti tesztelés során. |
| unauthorized_client |Az ügyfélalkalmazás nem kérhet engedélyezési kódot. |Ez általában akkor fordul elő, ha az ügyfélalkalmazás nincs regisztrálva az Azure AD-ben, vagy nem kerül hozzá a felhasználó Azure AD-bérlőhöz. Az alkalmazás kérheti a felhasználóutasítást az alkalmazás telepítéséhez és az Azure AD-hez való hozzáadásához. |
| access_denied |Az erőforrás tulajdonosa megtagadta a hozzájárulást |Az ügyfélalkalmazás értesítheti a felhasználót, hogy nem folytatható, ha a felhasználó nem járul hozzá. |
| unsupported_response_type |Az engedélyezési kiszolgáló nem támogatja a kérelemben lévő választípust. |Javítsa ki és küldje el újra a kérelmet. Ez egy fejlesztési hiba, és általában fogott a kezdeti tesztelés során. |
| server_error |A kiszolgáló váratlan hibát észlelt. |Próbálkozzon újra a kéréssel. Ezek a hibák ideiglenes körülményekből eredhetnek. Az ügyfélalkalmazás elmagyarázhatja a felhasználónak, hogy a válasz átmeneti hiba miatt késik. |
| temporarily_unavailable |A kiszolgáló átmenetileg túl elfoglalt a kérés kezeléséhez. |Próbálkozzon újra a kéréssel. Az ügyfélalkalmazás elmagyarázhatja a felhasználónak, hogy a válasz egy ideiglenes feltétel miatt késik. |
| invalid_resource |A célerőforrás érvénytelen, mert nem létezik, az Azure AD nem találja, vagy nincs megfelelően konfigurálva. |Ez azt jelzi, hogy az erőforrás, ha létezik, nincs konfigurálva a bérlőben. Az alkalmazás kérheti a felhasználóutasítást az alkalmazás telepítéséhez és az Azure AD-hez való hozzáadásához. |

## <a name="use-the-authorization-code-to-request-an-access-token"></a>Hozzáférési jogkivonat kérése az engedélyezési kód használatával
Most, hogy megszerezte az engedélyezési kódot, és a felhasználó engedélyt adott, beválthatja a kódot egy hozzáférési jogkivonathoz `/token` a kívánt erőforráshoz, ha postakérést küld a végpontnak:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded
grant_type=authorization_code
&client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrqqf_ZT_p5uEAEJJ_nZ3UmphWygRNy2C3jJ239gV_DBnZ2syeg95Ki-374WHUP-i3yIhv5i-7KU2CEoPXwURQp6IVYMw-DjAOzn7C3JCu5wpngXmbZKtJdWmiBzHpcO2aICJPu1KvJrDLDP20chJBXzVYJtkfjviLNNW7l7Y3ydcHDsBRKZc3GuMQanmcghXPyoDg41g8XbwPudVh7uCmUponBQpIhbuffFP_tbV8SNzsPoFz9CLpBCZagJVXeqWoYMPe2dSsPiLO9Alf_YIe5zpi-zY4C3aLw5g9at35eZTfNd0gBRpR5ojkMIcZZ6IgAA
&redirect_uri=https%3A%2F%2Flocalhost%3A12345
&resource=https%3A%2F%2Fservice.contoso.com%2F
&client_secret=p@ssw0rd

//NOTE: client_secret only required for web apps
```

| Paraméter |  | Leírás |
| --- | --- | --- |
| Bérlő |kötelező |A `{tenant}` kérelem elérési útjának értéke segítségével szabályozhatja, hogy ki jelentkezhet be az alkalmazásba. Az engedélyezett értékek például bérlői `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` `contoso.onmicrosoft.com` azonosítók, vagy `common` bérlőfüggetlen jogkivonatok |
| client_id |kötelező |Az alkalmazáshoz rendelt alkalmazásazonosító, amikor regisztrálta az Azure AD-vel. Ez az Azure Portalon található. Az alkalmazásazonosító megjelenik az alkalmazásregisztráció beállításaiban. |
| grant_type |kötelező |Az `authorization_code` engedélyezési kód folyamatához kell lenni. |
| code |kötelező |Az `authorization_code` előző szakaszban beszerzett |
| redirect_uri |kötelező | Az `redirect_uri`ügyfélalkalmazásban regisztrált. |
| client_secret |a webes alkalmazásokhoz szükséges, nyilvános ügyfelek számára nem engedélyezett |Az alkalmazás titkos, amelyaz Azure Portalon az alkalmazás **keys**csoportban létrehozott. Nem használható natív alkalmazásban (nyilvános ügyfél), mert client_secrets nem lehet megbízhatóan tárolni az eszközökön. Ez szükséges a webes alkalmazások és webes API-k (minden bizalmas `client_secret` ügyfelek), amelyek képesek tárolni a biztonságosan a kiszolgálóoldalon. Az client_secret elküldés előtt URL-kódolásúnak kell lennie. |
| Erőforrás | Ajánlott |A célwebes API (biztonságos erőforrás) alkalmazásazonosító-URI-ja. Az alkalmazásazonosító URI jának megkereséséhez az Azure Portalon kattintson az **Azure Active Directory**elemre, kattintson az **Alkalmazásregisztrációk**elemre, nyissa meg az alkalmazás **Beállítások lapját,** majd kattintson a **Tulajdonságok gombra.** Ez is lehet egy `https://graph.microsoft.com`külső erőforrás, mint a . Erre az egyik engedélyezési vagy jogkivonat-kérelemben van szükség. Annak érdekében, hogy kevesebb hitelesítési kérdés helyezze el az engedélyezési kérelemben annak biztosítása érdekében, hogy a felhasználó beleegyezést kapott. Ha az engedélyezési kérelemben és a jogkivonat-kérelemben is, az erőforrás paramétereinek egyeznie kell. | 
| code_verifier | választható | Ugyanaz a code_verifier, mint a authorization_code megszerzéséhez. Kötelező, ha pkce-t használtak az engedélyezési kód engedélyezési kérelmében. További információkért lásd a [PKCE RFC](https://tools.ietf.org/html/rfc7636)   |

Az alkalmazásazonosító URI jának megkereséséhez az Azure Portalon kattintson az **Azure Active Directory**elemre, kattintson az **Alkalmazásregisztrációk**elemre, nyissa meg az alkalmazás **Beállítások lapját,** majd kattintson a **Tulajdonságok gombra.**

### <a name="successful-response"></a>Sikeres válasz
Az Azure AD egy sikeres válasz esetén [egy hozzáférési jogkivonatot](../develop/access-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) ad vissza. Az ügyfélalkalmazásból és a hozzájuk tartozó késésből érkező hálózati hívások minimalizálása érdekében az ügyfélalkalmazásnak gyorsítótáraznia kell a hozzáférési jogkivonatokat az OAuth 2.0 válaszban megadott jogkivonat-élettartamhoz. A token élettartamának meghatározásához `expires_in` `expires_on` használja a vagy a paraméterértékeket.

Ha egy webes API-erőforrás `invalid_token` hibakódot ad vissza, ez azt jelezheti, hogy az erőforrás megállapította, hogy a jogkivonat lejárt. Ha az ügyfél és az erőforrás óraideje eltérő (más néven "idődöntés"), az erőforrás úgy tekintheti, hogy a jogkivonat lejárt, mielőtt a jogkivonat törlődik az ügyfél gyorsítótárából. Ebben az esetben törölje a jogkivonatot a gyorsítótárból, még akkor is, ha az még a számított élettartamon belül van.

A sikeres válasz így nézhet ki:

```
{
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ",
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1388444763",
  "resource": "https://service.contoso.com/",
  "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4rTfgV29ghDOHRc2B-C_hHeJaJICqjZ3mY2b_YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfcUl4VBbiSHZyd1NVZG5QTIOcbObu3qnLutbpadZGAxqjIbMkQ2bQS09fTrjMBtDE3D6kSMIodpCecoANon9b0LATkpitimVCrl-NyfN3oyG4ZCWu18M9-vEou4Sq-1oMDzExgAf61noxzkNiaTecM-Ve5cq6wHqYQjfV9DOz4lbceuYCAA",
  "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
  "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83ZmU4MTQ0Ny1kYTU3LTQzODUtYmVjYi02ZGU1N2YyMTQ3N2UvIiwiaWF0IjoxMzg4NDQwODYzLCJuYmYiOjEzODg0NDA4NjMsImV4cCI6MTM4ODQ0NDc2MywidmVyIjoiMS4wIiwidGlkIjoiN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlIiwib2lkIjoiNjgzODlhZTItNjJmYS00YjE4LTkxZmUtNTNkZDEwOWQ3NGY1IiwidXBuIjoiZnJhbmttQGNvbnRvc28uY29tIiwidW5pcXVlX25hbWUiOiJmcmFua21AY29udG9zby5jb20iLCJzdWIiOiJKV3ZZZENXUGhobHBTMVpzZjd5WVV4U2hVd3RVbTV5elBtd18talgzZkhZIiwiZmFtaWx5X25hbWUiOiJNaWxsZXIiLCJnaXZlbl9uYW1lIjoiRnJhbmsifQ."
}

```

| Paraméter | Leírás |
| --- | --- |
| access_token |A kért hozzáférési jogkivonat.  Ez egy átlátszatlan karakterlánc - attól függ, hogy az erőforrás mit vár, és nem az ügyfél számára, hogy nézd meg. Az alkalmazás használhatja ezt a jogkivonatot a biztonságos erőforrás, például egy webes API hitelesítéséhez. |
| token_type |A token típusának értékét jelzi. Az Azure AD által támogatott egyetlen típus a bemutatóra. A tulajdonosi jogkivonatokról további információt az [OAuth2.0 engedélyezési keretrendszer: Tulajdonosi jogkivonat-használat (RFC 6750) című](https://www.rfc-editor.org/rfc/rfc6750.txt) témakörben talál. |
| expires_in |Mennyi ideig érvényes a hozzáférési jogkivonat (másodpercben). |
| expires_on |A hozzáférési jogkivonat lejártának időpontja. A dátum az 1970-01-01T0:0:0Z UTC és a lejárati idő között eltelt másodpercek számaként jelenik meg. Ez az érték határozza meg a gyorsítótárazott jogkivonatok élettartamát. |
| Erőforrás |A webes API (biztonságos erőforrás) alkalmazásazonosító-URI-ja. |
| scope |Az ügyfélalkalmazásnak megadott megszemélyesítési engedélyek. Az alapértelmezett `user_impersonation`engedély a . A védett erőforrás tulajdonosa további értékeket regisztrálhat az Azure AD-ben. |
| refresh_token |Egy OAuth 2.0 frissítési jogkivonat. Az alkalmazás használhatja ezt a jogkivonatot további hozzáférési jogkivonatok beszerzéséhez az aktuális hozzáférési jogkivonat lejárta után. A frissítési jogkivonatok hosszú élettartamúak, és az erőforrásokhoz való hozzáférés hosszabb ideig való megőrzésére használhatók. |
| id_token |Egy azonosító [jogkivonatot](../develop/id-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)képviselő aláíratlan JSON webtoken (JWT). Az alkalmazás base64Url dekódolni a szegmensek a jogkivonat információt kérni a felhasználó, aki bejelentkezett. Az alkalmazás gyorsítótárazhatja az értékeket, és megjelenítheti azokat, de nem támaszkodhat rájuk semmilyen engedélyezési vagy biztonsági határesetén. |

A JSON webes tokenekről a [JWT IETF tervezetspecifikációjában](https://go.microsoft.com/fwlink/?LinkId=392344)talál további információt.   További információ: `id_tokens`a [1.0-s v1.0 OpenID Connect folyamat.](v1-protocols-openid-connect-code.md)

### <a name="error-response"></a>Hibaválasz
A token kiállítási végpont hibák HTTP-hibakódok, mert az ügyfél közvetlenül hívja meg a token kiállítási végpontot. A HTTP-állapotkód mellett az Azure AD token kiállítási végpont jain egy JSON-dokumentumot is ad vissza a hibát leíró objektumokkal.

A mintahiba-válasz a következőkre néz ki:

```
{
  "error": "invalid_grant",
  "error_description": "AADSTS70002: Error validating credentials. AADSTS70008: The provided authorization code or refresh token is expired. Send a new interactive authorization request for this user and resource.\r\nTrace ID: 3939d04c-d7ba-42bf-9cb7-1e5854cdce9e\r\nCorrelation ID: a8125194-2dc8-4078-90ba-7b6592a7f231\r\nTimestamp: 2016-04-11 18:00:12Z",
  "error_codes": [
    70002,
    70008
  ],
  "timestamp": "2016-04-11 18:00:12Z",
  "trace_id": "3939d04c-d7ba-42bf-9cb7-1e5854cdce9e",
  "correlation_id": "a8125194-2dc8-4078-90ba-7b6592a7f231"
}
```
| Paraméter | Leírás |
| --- | --- |
| error |Hibakód-karakterlánc, amely a előforduló hibatípusok osztályozására használható, és a hibákra való reagálásra használható. |
| error_description |Egy adott hibaüzenet, amely segíthet a fejlesztőnek a hitelesítési hiba kiváltó okának azonosításában. |
| error_codes |Az STS-specifikus hibakódok listája, amelyek segíthetnek a diagnosztikában. |
| időbélyeg |A hiba bekövetkezésének időpontja. |
| trace_id |A kérelem egyedi azonosítója, amely segíthet a diagnosztikában. |
| correlation_id |A kérelem egyedi azonosítója, amely segíthet az összetevők közötti diagnosztikában. |

#### <a name="http-status-codes"></a>HTTP-állapotkódok
Az alábbi táblázat felsorolja azokat a HTTP-állapotkódokat, amelyeket a tokenkiállítási végpont visszaad. Bizonyos esetekben a hibakód elegendő a válasz leírásához, de ha hibák vannak, elemeznie kell a kísérő JSON-dokumentumot, és meg kell vizsgálnia annak hibakódját.

| HTTP-kód | Leírás |
| --- | --- |
| 400 |Alapértelmezett HTTP-kód. A legtöbb esetben használatos, és általában hibás kérés miatt. Javítsa ki és küldje el újra a kérelmet. |
| 401 |A hitelesítés sikertelen. Például a kérelemből hiányzik a client_secret paraméter. |
| 403 |Az engedélyezés nem sikerült. A felhasználónak például nincs engedélye az erőforrás elérésére. |
| 500 |Belső hiba történt a szolgáltatásban. Próbálkozzon újra a kéréssel. |

#### <a name="error-codes-for-token-endpoint-errors"></a>Hibakódok a tokenvégpont-hibákhoz
| Hibakód | Leírás | Ügyfélművelet |
| --- | --- | --- |
| invalid_request |Protokollhiba, például hiányzó szükséges paraméter. |A kérelem javítása és újraküldése |
| invalid_grant |Az engedélyezési kód érvénytelen vagy lejárt. |Próbálkozzon egy új `/authorize` kéréssel a végponthoz |
| unauthorized_client |A hitelesített ügyfél nem jogosult az engedélyezési engedély típusának használatára. |Ez általában akkor fordul elő, ha az ügyfélalkalmazás nincs regisztrálva az Azure AD-ben, vagy nem kerül hozzá a felhasználó Azure AD-bérlőhöz. Az alkalmazás kérheti a felhasználóutasítást az alkalmazás telepítéséhez és az Azure AD-hez való hozzáadásához. |
| invalid_client |Az ügyfélhitelesítés nem sikerült. |Az ügyfél hitelesítő adatai érvénytelenek. A javításhoz az alkalmazás rendszergazdája frissíti a hitelesítő adatokat. |
| unsupported_grant_type |Az engedélyezési kiszolgáló nem támogatja az engedélyezési engedély típusát. |Módosítsa a támogatás típusát a kérelemben. Ez a hibatípus csak a fejlesztés során fordulhat elő, és a kezdeti tesztelés során észlelhető. |
| invalid_resource |A célerőforrás érvénytelen, mert nem létezik, az Azure AD nem találja, vagy nincs megfelelően konfigurálva. |Ez azt jelzi, hogy az erőforrás, ha létezik, nincs konfigurálva a bérlőben. Az alkalmazás kérheti a felhasználóutasítást az alkalmazás telepítéséhez és az Azure AD-hez való hozzáadásához. |
| interaction_required |A kérelem felhasználói beavatkozást igényel. Például egy további hitelesítési lépés szükséges. | Nem interaktív kérelem helyett próbálkozzon újra egy interaktív engedélyezési kérelemmel ugyanahhoz az erőforráshoz. |
| temporarily_unavailable |A kiszolgáló átmenetileg túl elfoglalt a kérés kezeléséhez. |Próbálkozzon újra a kéréssel. Az ügyfélalkalmazás elmagyarázhatja a felhasználónak, hogy a válasz egy ideiglenes feltétel miatt késik. |

## <a name="use-the-access-token-to-access-the-resource"></a>Az erőforrás eléréséhez használja a hozzáférési jogkivonatot
Most, hogy sikeresen beszerzett egy, `access_token`használhatja a jogkivonatot a webes `Authorization` API-kra vonatkozó kérelmekben, a fejlécben való szerepeltetésével. Az [RFC 6750](https://www.rfc-editor.org/rfc/rfc6750.txt) specifikáció ismerteti, hogyan használhatja a tulajdonosi jogkivonatokat a HTTP-kérelmekvédett erőforrások eléréséhez.

### <a name="sample-request"></a>Mintakérelem
```
GET /data HTTP/1.1
Host: service.contoso.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ
```

### <a name="error-response"></a>Hibaválasz
Az RFC 6750-es számú http-állapotkódokat megvalósító védett erőforrások. Ha a kérelem nem tartalmaz hitelesítési hitelesítő adatokat, `WWW-Authenticate` vagy hiányzik a jogkivonat, a válasz egy fejlécet tartalmaz. Ha egy kérelem sikertelen, az erőforrás-kiszolgáló a HTTP-állapotkóddal és egy hibakóddal válaszol.

Az alábbi példa egy sikertelen válasz, ha az ügyfélkérelem nem tartalmazza a tulajdonosi jogkivonatot:

```
HTTP/1.1 401 Unauthorized
WWW-Authenticate: Bearer authorization_uri="https://login.microsoftonline.com/contoso.com/oauth2/authorize",  error="invalid_token",  error_description="The access token is missing.",
```

#### <a name="error-parameters"></a>Hibaparaméterek
| Paraméter | Leírás |
| --- | --- |
| authorization_uri |Az engedélyezési kiszolgáló URI-ja (fizikai végpontja). Ez az érték is használható a kapcsolati kulcs, hogy minél több információt a kiszolgáló egy felderítési végpont. <p><p> Az ügyfélnek ellenőriznie kell, hogy az engedélyezési kiszolgáló megbízható-e. Ha az erőforrás azure AD által védett, elegendő annak ellenőrzése, hogy az URL-cím kezdődik, `https://login.microsoftonline.com` vagy egy másik állomásnevet, amely az Azure AD támogatja. A tenant-specific resource should always return a tenant-specific authorization URI. |
| error |Az [OAuth 2.0 engedélyezési keretrendszer 5.2](https://tools.ietf.org/html/rfc6749)szakaszában meghatározott hibakódérték. |
| error_description |A hiba részletesebb leírása. Ez az üzenet nem végfelhasználóbarát. |
| resource_id |Az erőforrás egyedi azonosítóját adja vissza. Az ügyfélalkalmazás használhatja ezt az `resource` azonosítót, mint a paraméter értékét, amikor az erőforrás jogkivonatot kér. <p><p> Fontos, hogy az ügyfélalkalmazás ellenőrizze ezt az értéket, ellenkező esetben egy rosszindulatú szolgáltatás **jogosultságszint-emelési** támadást okozhat <p><p> A támadás megelőzésére ajánlott stratégia annak `resource_id` ellenőrzése, hogy a webes API-URL-cím alapja megegyezik-e az elérhető web API-url-címével. Ha például `https://service.contoso.com/data` a program hoz `resource_id` éri `https://service.contoso.com/`el a hozzáférést, akkor a lehet. Az ügyfélalkalmazásnak `resource_id` el kell utasítania egy olyan alkalmazást, amely nem az alap URL-címével kezdődik, kivéve, ha van egy megbízható alternatív módja az azonosító ellenőrzésének. |

#### <a name="bearer-scheme-error-codes"></a>Bemutatóra szóló séma hibakódjai
Az RFC 6750 specifikáció a következő hibákat határozza meg a www-hitelesítési fejlécet és a bemutatóra programot használó erőforrásokesetében.

| HTTP-állapotkód | Hibakód | Leírás | Ügyfélművelet |
| --- | --- | --- | --- |
| 400 |invalid_request |A kérés nem jól formázott. Előfordulhat például, hogy hiányzik egy paraméter, vagy kétszer használja ugyanazt a paramétert. |Javítsa ki a hibát, és próbálkozzon újra a kéréssel. Ez a hibatípus csak a fejlesztés során fordulhat elő, és a kezdeti tesztelés során észlelhető. |
| 401 |invalid_token |A hozzáférési jogkivonat hiányzik, érvénytelen vagy visszavonva van. A error_description paraméter értéke további részleteket tartalmaz. |Kérjen új jogkivonatot az engedélyezési kiszolgálótól. Ha az új jogkivonat sikertelen, váratlan hiba történt. Küldjön hibaüzenetet a felhasználónak, majd próbálkozzon újra véletlenszerű késések után. |
| 403 |insufficient_scope |A hozzáférési jogkivonat nem tartalmazza az erőforrás eléréséhez szükséges megszemélyesítési engedélyeket. |Új engedélyezési kérelem küldése az engedélyezési végpontra. Ha a válasz tartalmazza a hatókör paramétert, használja a hatókör értékét az erőforrásnak küldött kérelemben. |
| 403 |insufficient_access |A jogkivonat tárgya nem rendelkezik az erőforrás eléréséhez szükséges engedélyekkel. |Kérje meg a felhasználót, hogy használjon másik fiókot, vagy kérjen engedélyeket a megadott erőforráshoz. |

## <a name="refreshing-the-access-tokens"></a>A hozzáférési jogkivonatok frissítése

Az access tokenek rövid életűek, és lejáratuk után frissíteni kell őket az erőforrások elérésének folytatásához. Frissítheti a `access_token` másik `POST` kérelmet `/token` a végpontnak, de ezúttal `refresh_token` a helyett `code`a .  A frissítési jogkivonatok minden olyan erőforrásra érvényesek, amelyhez az ügyfél már engedélyt `resource=https://graph.microsoft.com` kapott a hozzáféréshez – `resource=https://contoso.com/api`így a kérelemre kiadott frissítési jogkivonat új hozzáférési jogkivonat kéréséhez használható. 

A frissítési jogkivonatok nem rendelkeznek megadott élettartammal. A frissítési jogkivonatok élettartama általában viszonylag hosszú. Bizonyos esetekben azonban a frissítési jogkivonatok lejárnak, visszavonásra kerülnek, vagy nem rendelkeznek megfelelő jogosultságokkal a kívánt művelethez. Az alkalmazásnak megfelelően kell várnia és kezelnie a tokenkiállítási végpont által visszaadott hibákat.

Amikor frissítési jogkivonat-hibával érkezik válasz, dobja el az aktuális frissítési jogkivonatot, és kérjen új engedélyezési kódot vagy hozzáférési jogkivonatot. Különösen, ha egy frissítési jogkivonatot használ az engedélyezési kód `interaction_required` engedélyezési `invalid_grant` folyamatában, ha választ kap a vagy hibakódokkal, dobja el a frissítési jogkivonatot, és kérjen új engedélyezési kódot.

A **bérlő-specifikus** végpont (a **közös** végpont használatával is használhatja) egy új hozzáférési jogkivonat beszerzése egy frissítési jogkivonat használatával a következőképpen néz ki:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&grant_type=refresh_token
&resource=https%3A%2F%2Fservice.contoso.com%2F
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps
```

### <a name="successful-response"></a>Sikeres válasz
A sikeres jogkivonat-válasz így fog kinézni:

```
{
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1460404526",
  "resource": "https://service.contoso.com/",
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ",
  "refresh_token": "AwABAAAAv YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfcUl4VBbiSHZyd1NVZG5QTIOcbObu3qnLutbpadZGAxqjIbMkQ2bQS09fTrjMBtDE3D6kSMIodpCecoANon9b0LATkpitimVCrl PM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4rTfgV29ghDOHRc2B-C_hHeJaJICqjZ3mY2b_YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfmVCrl-NyfN3oyG4ZCWu18M9-vEou4Sq-1oMDzExgAf61noxzkNiaTecM-Ve5cq6wHqYQjfV9DOz4lbceuYCAA"
}
```
| Paraméter | Leírás |
| --- | --- |
| token_type |A token típusa. Az egyetlen támogatott érték a **bemutatóra .** |
| expires_in |A token hátralévő élettartama másodpercben. Egy tipikus érték 3600 (egy óra). |
| expires_on |A jogkivonat lejáratának dátuma és időpontja. A dátum az 1970-01-01T0:0:0Z UTC és a lejárati idő között eltelt másodpercek számaként jelenik meg. |
| Erőforrás |Azonosítja a védett erőforrást, amelyhez a hozzáférési jogkivonat használható. |
| scope |A natív ügyfélalkalmazásnak megadott megszemélyesítési engedélyek. Az alapértelmezett engedély **user_impersonation**. . A célerőforrás tulajdonosa alternatív értékeket regisztrálhat az Azure AD-ben. |
| access_token |A kért új hozzáférési jogkivonat. |
| refresh_token |Egy új OAuth 2.0 refresh_token, amely új hozzáférési jogkivonatok kérésére használható, amikor a válaszban szereplő jogkivonat lejár. |

### <a name="error-response"></a>Hibaválasz
A mintahiba-válasz a következőkre néz ki:

```
{
  "error": "invalid_resource",
  "error_description": "AADSTS50001: The application named https://foo.microsoft.com/mail.read was not found in the tenant named 295e01fc-0c56-4ac3-ac57-5d0ed568f872. This can happen if the application has not been installed by the administrator of the tenant or consented to by any user in the tenant. You might have sent your authentication request to the wrong tenant.\r\nTrace ID: ef1f89f6-a14f-49de-9868-61bd4072f0a9\r\nCorrelation ID: b6908274-2c58-4e91-aea9-1f6b9c99347c\r\nTimestamp: 2016-04-11 18:59:01Z",
  "error_codes": [
    50001
  ],
  "timestamp": "2016-04-11 18:59:01Z",
  "trace_id": "ef1f89f6-a14f-49de-9868-61bd4072f0a9",
  "correlation_id": "b6908274-2c58-4e91-aea9-1f6b9c99347c"
}
```

| Paraméter | Leírás |
| --- | --- |
| error |Hibakód-karakterlánc, amely a előforduló hibatípusok osztályozására használható, és a hibákra való reagálásra használható. |
| error_description |Egy adott hibaüzenet, amely segíthet a fejlesztőnek a hitelesítési hiba kiváltó okának azonosításában. |
| error_codes |Az STS-specifikus hibakódok listája, amelyek segíthetnek a diagnosztikában. |
| időbélyeg |A hiba bekövetkezésének időpontja. |
| trace_id |A kérelem egyedi azonosítója, amely segíthet a diagnosztikában. |
| correlation_id |A kérelem egyedi azonosítója, amely segíthet az összetevők közötti diagnosztikában. |

A hibakódok és az ajánlott ügyfélművelet leírását a [tokenvégpont-hibák hibakódjai című témakörben tartalmazza.](#error-codes-for-token-endpoint-errors)

## <a name="next-steps"></a>További lépések
Ha többet szeretne megtudni az Azure AD v1.0-s végpontjáról, valamint arról, hogyan adhat hozzá hitelesítést és engedélyezést a webalkalmazásokhoz és a webes API-khoz, olvassa el [a mintaalkalmazások című témakört.](sample-v1-code.md)
