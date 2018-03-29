---
title: Az OAuth 2.0 hitelesítésikód-folyamata megismerése az Azure ad-ben
description: A cikkből megtudhatja, hogyan használható a HTTP-üzenetek webalkalmazások és webes API-k használata az Azure Active Directory és az OAuth 2.0-bérlőben hozzáférés hitelesítése.
services: active-directory
documentationcenter: .net
author: hpsin
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2018
ms.author: hirsin
ms.custom: aaddev
ms.openlocfilehash: 241f872b3069a58a35df7104f3335964298c7a20
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2018
---
# <a name="authorize-access-to-web-applications-using-oauth-20-and-azure-active-directory"></a>Hozzáférés engedélyezése webalkalmazásoknak OAuth 2.0 és az Azure Active Directory használatával
Az Azure Active Directory (Azure AD) által használt OAuth 2.0 ahhoz, hogy engedélyezi a hozzáférést a webalkalmazások és webes API-knak az Azure AD-bérlőben. Ez az útmutató nyelvfüggetlen, és ismerteti, hogyan lehet üzeneteket küldjön és fogadjon HTTP a nyílt forráskódú kódtárai bármelyikét használata nélkül.

Az OAuth 2.0 hitelesítésikód-folyamata ismertetett [az OAuth 2.0-s szabvány 4.1 szakasz](https://tools.ietf.org/html/rfc6749#section-4.1). A legtöbb alkalmazás típusok, beleértve a webalkalmazások hitelesítési és engedélyezési végezhetők, és natív módon telepített alkalmazásokat.

[!INCLUDE [active-directory-protocols-getting-started](../../../includes/active-directory-protocols-getting-started.md)]

## <a name="oauth-20-authorization-flow"></a>OAuth 2.0 hitelesítési folyamata
Magas szinten az alkalmazás teljes engedélyezési folyamata néz ki egy kicsit:

![OAuth hitelesítési folyamata](media/active-directory-protocols-oauth-code/active-directory-oauth-code-flow-native-app.png)

## <a name="request-an-authorization-code"></a>Az engedélyezési kód kérése
A hitelesítésikód-folyamata kezdődik irányítja a felhasználót, hogy az ügyfél a `/authorize` végpont. A kérelem az ügyfél azt jelzi, kell szerzi be a felhasználói engedélyek. Kaphat az OAuth 2.0 végpont a bérlő kiválasztásával **alkalmazás-regisztráció > végpontok** az Azure portálon.

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&resource=https%3A%2F%2Fservice.contoso.com%2F
&state=12345
```

| Paraméter |  | Leírás |
| --- | --- | --- |
| bérlő |Szükséges |A `{tenant}` személyek is jelentkezzen be az alkalmazás a kérelem elérési útjában szereplő érték is használható.  Az engedélyezett értékek a következők bérlői azonosítók, például `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` vagy `contoso.onmicrosoft.com` vagy `common` bérlői független jogkivonatokat |
| client_id |Szükséges |Az Azure ad-vel regisztrált az alkalmazáshoz hozzárendelt Alkalmazásazonosító. Ez az Azure portálon találja meg. Kattintson a **Active Directory**, kattintson arra a címtárra, válassza ki az alkalmazást, és kattintson a **konfigurálása** |
| response_type |Szükséges |Tartalmaznia kell `code` a a hitelesítésikód-folyamata. |
| redirect_uri |Ajánlott |Az alkalmazás, ahol küldött és az alkalmazás által fogadott a hitelesítési válaszok redirect_uri.  Ez pontosan egyeznie kell a redirect_uris regisztrálta a portálon, kivéve az url-kódolású kell lennie.  Natív & mobileszköz-alkalmazások esetén az alapértelmezett értéket használjon `urn:ietf:wg:oauth:2.0:oob`. |
| response_mode |Ajánlott |Megadja azt a módszert, amelynek használatával az eredményül kapott jogkivonat vissza küldése az alkalmazásnak.  A következők egyike lehet: `query` vagy `form_post`. |
| state |Ajánlott |A kérelem is a biztonságijogkivonat-válaszban visszaadott szerepel érték. Egy véletlenszerűen generált egyedi érték jellemzően a [webhelyközi kérések hamisításának megakadályozása támadások megelőzése](http://tools.ietf.org/html/rfc6749#section-10.12).  Az állapot az alkalmazás a felhasználói állapot információt kódolásához, előtt a hitelesítési kérést, például az oldal vagy nézet, amilyenek korábban voltak a is használatos. |
| erőforrás |választható |A webes API-k (védett erőforrás) App ID URI. App ID URI-azonosítója a webes API-t, az Azure portálon található, kattintson a **Active Directory**, kattintson arra a címtárra, kattintson az alkalmazás majd **konfigurálása**. |
| parancssor |választható |Jelzi a felhasználói beavatkozás szükséges.<p> Érvényes értékek a következők: <p> *bejelentkezési*: A felhasználó a rendszer kéri újból hitelesítésre. <p> *hozzájárulás*: felhasználói hozzájárulás rendelkezik, de frissíteni kell. A felhasználó beleegyezését kell kérni. <p> *admin_consent*: A rendszergazda a rendszer kéri a szervezetben lévő összes felhasználó nevében hozzájárulás |
| login_hint |választható |Segítségével előre töltse ki a bejelentkezési oldal a felhasználó a felhasználónév vagy e-mail cím mező, ha tudja, hogy időben a felhasználónevét.  Gyakran alkalmazások újrahitelesítés, hogy már kivont a felhasználónév egy korábbi bejelentkezési használatával során használja ezt a paramétert a `preferred_username` jogcímek. |
| domain_hint |választható |A bérlői és a tartományhoz, amely a felhasználó által használandó jelentkezzen be a mutatót. A domain_hint értéke egy regisztrált tartományt a bérlő számára. A bérlő helyszíni Directory össze van vonva, ha a megadott tenantot összevonási kiszolgáló átirányítja a aad-ben. |
| code_challenge_method | választható    | Kódolja használt módszer a `code_verifier` a a `code_challenge` paraméter. Egyike lehet `plain` vagy `S256`.  Ha ki van zárva, `code_challenge` adottnak egyszerű szöveges Ha `code_challenge` tartalmazza.  Az Azure AAD v2.0 egyaránt támogat `plain` és `S256`. További információkért lásd: a [PKCE RFC](https://tools.ietf.org/html/rfc7636). |
| code_challenge        | választható    | Egy natív ügyfél a kód Exchange (PKCE) engedélyezési kód biztosít igazolása kulccsal védelméhez használt. Kötelező, ha `code_challenge_method` tartalmazza.  További információkért lásd: a [PKCE RFC](https://tools.ietf.org/html/rfc7636). |

> [!NOTE]
> Ha a felhasználó egy szervezet része, a szervezet rendszergazdája hozzájárulás vagy elutasítja a felhasználó nevében, vagy a felhasználó beleegyezését engedélyezése. A felhasználó beleegyezését csak akkor, ha a rendszergazda engedélyezi azt a lehetőséget kap.
>
>

Ezen a ponton a felhasználónak kapcsolatba kell adnia a hitelesítő adatait, és hozzájárul az engedélyek szerepelnek a `scope` lekérdezési paraméter. Miután a felhasználó hitelesíti és engedélyezi a hozzájárulási, az Azure AD az alkalmazást választ küld a `redirect_uri` a kérés címe.

### <a name="successful-response"></a>A sikeres válasz
A sikeres válasz nézhet ki:

```
GET  HTTP/1.1 302 Found
Location: http://localhost/myapp/?code= AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrqqf_ZT_p5uEAEJJ_nZ3UmphWygRNy2C3jJ239gV_DBnZ2syeg95Ki-374WHUP-i3yIhv5i-7KU2CEoPXwURQp6IVYMw-DjAOzn7C3JCu5wpngXmbZKtJdWmiBzHpcO2aICJPu1KvJrDLDP20chJBXzVYJtkfjviLNNW7l7Y3ydcHDsBRKZc3GuMQanmcghXPyoDg41g8XbwPudVh7uCmUponBQpIhbuffFP_tbV8SNzsPoFz9CLpBCZagJVXeqWoYMPe2dSsPiLO9Alf_YIe5zpi-zY4C3aLw5g9at35eZTfNd0gBRpR5ojkMIcZZ6IgAA&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF&state=D79E5777-702E-4260-9A62-37F75FF22CCE
```

| Paraméter | Leírás |
| --- | --- |
| admin_consent |Az értéke True, ha a rendszergazda átadni kívánt hozzájárult e a jóváhagyási kérelem kérése. |
| Kód |Az engedélyezési kód, amely az alkalmazás kéri. Az alkalmazás az engedélyezési kód segítségével olyan hozzáférési jogkivonatot célerőforrás igényelhetnek. |
| session_state |A jelenlegi felhasználói munkamenetet azonosító egyedi érték. Ez az érték egy GUID, de egy vizsgálat nélkül átadott nem átlátszó értéket kell kezelni. |
| state |Ha a kérelem egy állapot paramétert tartalmaz, ugyanazt az értéket meg kell jelennie a válasz. Célszerű ellenőrizni, hogy a kérés- és állapot értékei megegyeznek a válasz használata előtt az alkalmazáshoz. Ez segít észleléséhez [többhelyes kérelem hamisítására (CSRF) támadások](https://tools.ietf.org/html/rfc6749#section-10.12) az ügyfél ellen. |

### <a name="error-response"></a>Hibaválaszba
Hibaválaszok is elküldheti a `redirect_uri` , hogy az alkalmazás kezeli őket megfelelően.

```
GET http://localhost:12345/?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Paraméter | Leírás |
| --- | --- |
| error |Egy hiba kód értékét, az 5.2. szakaszban meghatározott a [OAuth 2.0 hitelesítési keretrendszer](http://tools.ietf.org/html/rfc6749). A következő táblázat a hibakódok az Azure AD eredményül. |
| error_description |A hiba részletes leírását. Ez az üzenet nem lehet olyan végfelhasználói leíró. |
| state |Az állapot értéke nem használja fel újra egy véletlenszerűen generált érték, amely a kérelemben küldött és az eredmény abban a webhelyközi kérések hamisítására (CSRF) támadások megelőzése érdekében. |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Engedélyezési végpont hibái hibakódok
A következő táblázat ismerteti a különböző hibakódok a visszaadható a `error` paramétere a hibaüzenetet.

| Hibakód | Leírás | Ügyfélművelet |
| --- | --- | --- |
| invalid_request |Protokollhiba történt, például a hiányzó kötelező paraméter. |Javítsa ki, és küldje el újra a kérelmet. A fejlesztési hiba, és a kezdeti tesztelés során általában kiszűri. |
| unauthorized_client |Az ügyfélalkalmazás az engedélyezési kód kérése nem engedélyezett. |Ez általában akkor fordul elő, amikor az ügyfél-alkalmazás nincs regisztrálva az Azure ad-ben, vagy nem kerül be a felhasználó Azure AD-bérlő. Az alkalmazás kérheti a felhasználót az alkalmazás telepítése és az Azure AD hozzáadni utasítás. |
| access_denied |Erőforrás tulajdonosa hozzájárulási megtagadva |Az ügyfélalkalmazás is értesíti a felhasználót, hogy azt nem folytatható, kivéve, ha a felhasználó. |
| unsupported_response_type |Az engedélyezési kiszolgáló nem támogatja a kérelem a válasz típusa. |Javítsa ki, és küldje el újra a kérelmet. A fejlesztési hiba, és a kezdeti tesztelés során általában kiszűri. |
| server_error |A kiszolgáló váratlan hibát észlelt. |Próbálkozzon újra a kéréssel. Ezeket a hibákat okozhat az átmeneti állapotot. Az ügyfélalkalmazás elmagyarázhatja a felhasználót, hogy egy ideiglenes hiba miatt késik a válaszában. |
| temporarily_unavailable |A kiszolgáló ideiglenesen jelenleg túl elfoglalt a kérelem kezelésére. |Próbálkozzon újra a kéréssel. Az ügyfélalkalmazás elmagyarázhatja a felhasználót, hogy egy ideiglenes állapot miatt késik a válaszában. |
| invalid_resource |A cél erőforráson érvénytelen, mert nem létezik, az Azure AD a fájl nem található vagy nincs megfelelően konfigurálva. |Ez azt jelzi, hogy az erőforrás, ha létezik, nincs konfigurálva a bérlőben. Az alkalmazás kérheti a felhasználót az alkalmazás telepítése és az Azure AD hozzáadni utasítás. |

## <a name="use-the-authorization-code-to-request-an-access-token"></a>Az engedélyezési kód segítségével olyan hozzáférési jogkivonatot kérése
Most, hogy az engedélyezési kód jut hozzá, és a felhasználó a engedélyt kaptak, olyan hozzáférési jogkivonatot a kívánt erőforráshoz tartozó kód is beváltja úgy, hogy a POST kérést küld a `/token` végpont:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded
grant_type=authorization_code
&client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrqqf_ZT_p5uEAEJJ_nZ3UmphWygRNy2C3jJ239gV_DBnZ2syeg95Ki-374WHUP-i3yIhv5i-7KU2CEoPXwURQp6IVYMw-DjAOzn7C3JCu5wpngXmbZKtJdWmiBzHpcO2aICJPu1KvJrDLDP20chJBXzVYJtkfjviLNNW7l7Y3ydcHDsBRKZc3GuMQanmcghXPyoDg41g8XbwPudVh7uCmUponBQpIhbuffFP_tbV8SNzsPoFz9CLpBCZagJVXeqWoYMPe2dSsPiLO9Alf_YIe5zpi-zY4C3aLw5g9at35eZTfNd0gBRpR5ojkMIcZZ6IgAA
&redirect_uri=https%3A%2F%2Flocalhost%2Fmyapp%2F
&resource=https%3A%2F%2Fservice.contoso.com%2F
&client_secret=p@ssw0rd

//NOTE: client_secret only required for web apps
```

| Paraméter |  | Leírás |
| --- | --- | --- |
| bérlő |Szükséges |A `{tenant}` személyek is jelentkezzen be az alkalmazás a kérelem elérési útjában szereplő érték is használható.  Az engedélyezett értékek a következők bérlői azonosítók, például `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` vagy `contoso.onmicrosoft.com` vagy `common` bérlői független jogkivonatokat |
| client_id |Szükséges |Az Azure ad-vel regisztrált az alkalmazáshoz hozzárendelt alkalmazásazonosító. Ez az Azure portálon találja meg. Az alkalmazásazonosító az alkalmazás-regisztrációs beállításai jelenik meg.  |
| grant_type |Szükséges |Kell `authorization_code` a a hitelesítésikód-folyamata. |
| Kód |Szükséges |A `authorization_code` az előző szakaszban beszerzett |
| redirect_uri |Szükséges |Azonos `redirect_uri` megszerzésére használt érték a `authorization_code`. |
| client_secret |a web Apps, nem engedélyezett a következő nyilvános ügyfelek szükséges |Az alkalmazás az app-regisztrációs portálon létrehozott alkalmazáskulcsot.  Ez nem használható natív alkalmazásban (nyilvános ügyfél), mert client_secrets megbízhatóan nem tárolható az eszközökön.  Szükséges, hogy az webalkalmazások és webes API-k (összes bizalmas ügyfelek), amelyek tárolása a `client_secret` biztonságos helyen a kiszolgálói oldalon. |
| erőforrás |szükséges, ha a kérelemben megadott engedélyezési kódot, ellenkező esetben nem kötelező |A webes API-k (védett erőforrás) App ID URI. |
| code_verifier | választható              | Az beszerzése a authorization_code használt azonos code_verifier.  Szükséges, ha az engedélyezési kód támogatási kérelmet PKCE használta.  További információkért lásd: a [PKCE RFC](https://tools.ietf.org/html/rfc7636)   |

Az Azure felügyeleti portálon App ID URI megkereséséhez kattintson **Active Directory**, kattintson arra a címtárra, kattintson az alkalmazást, majd **konfigurálása**.

### <a name="successful-response"></a>A sikeres válasz
Az Azure AD, a sikeres válasz hozzáférési jogkivonatot ad vissza. Az ügyfélalkalmazás és azok kapcsolódó késés hálózati hívásait minimalizálása érdekében az ügyfélalkalmazás kell gyorsítótárba hozzáférési jogkivonatok a jogkivonat élettartamát az OAuth 2.0 válaszban megadott. Használja a jogkivonat élettartamát határozza meg, hogy a `expires_in` vagy `expires_on` paraméterértékeket.

Ha a webes API-erőforrás adja vissza egy `invalid_token` hibakód, ez azt jelentheti, hogy az erőforrás megállapította, hogy a jogkivonat lejárt. Ha az ügyfél- és erőforrás óra alkalommal különböző (ismert egy "eltérésére alkalommal"), az erőforrás érdemes lehet a jogkivonat lejárt, mielőtt a jogkivonat nincs bejelölve, az ügyfél-gyorsítótárból. Ha ez történik, törölje a jogkivonatot a gyorsítótárból, akkor is, ha továbbra is számított élettartamuk belül.

A sikeres válasz nézhet ki:

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
| access_token |A kért hozzáférési jogkivonat, egy aláírt JSON webes jogkivonat (JWT). Az alkalmazás a token használatával hitelesítik magukat a védett erőforrások, például egy webes API. |
| token_type |A jogkivonat típusa értékét jelöli. A csak az Azure AD támogató típus tulajdonosi. Tulajdonosi jogkivonatok kapcsolatos további információkért lásd: [OAuth2.0 hitelesítési keretrendszer: tulajdonosi jogkivonat használati (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt) |
| expires_in |Mennyi ideig a hozzáférési jogkivonat érvénytelen (másodpercben). |
| expires_on |A hozzáférési jogkivonat lejárati idejének. A dátum jelzi másodpercben a 1970-01-01T0:0:0Z UTC, amíg az elévülési időt. Ezt az értéket a gyorsítótárazott jogkivonatok élettartama meghatározására szolgál. |
| erőforrás |A webes API-k (védett erőforrás) App ID URI. |
| Hatókör |Az ügyfélalkalmazás számára megadott megszemélyesítési engedélyeket. Az alapértelmezett engedély `user_impersonation`. A védett erőforrás tulajdonosa további értékeket regisztrálhatja az Azure AD-ben. |
| refresh_token |Az OAuth 2.0-s frissítési jogkivonat. Az alkalmazás a jogkivonat segítségével szerezzen be további hozzáférési jogkivonatok az aktuális jogkivonat lejárata után is.  Frissítési jogkivonatok hosszú élettartamú, és erőforrásokhoz való hozzáférés megőrzése huzamosabb ideig használható. |
| id_token |Az aláírás nélküli JSON webes jogkivonat (JWT). Az alkalmazás is base64Url dekódolni a bejelentkezett felhasználóval kapcsolatos információkat a token szegmensek. Az alkalmazás gyorsítótárazása az értékeket, és a megjelenítésükhöz, de azt nem igazolható a azokat bármilyen engedélyezési vagy a biztonsági határokat. |

### <a name="jwt-token-claims"></a>JWT jogkivonat jogcímek
A JWT jogkivonat értékének a `id_token` paraméter a következő jogcímeket is dekódolható:

```
{
 "typ": "JWT",
 "alg": "none"
}.
{
 "aud": "2d4d11a2-f814-46a7-890a-274a72a7309e",
 "iss": "https://sts.windows.net/7fe81447-da57-4385-becb-6de57f21477e/",
 "iat": 1388440863,
 "nbf": 1388440863,
 "exp": 1388444763,
 "ver": "1.0",
 "tid": "7fe81447-da57-4385-becb-6de57f21477e",
 "oid": "68389ae2-62fa-4b18-91fe-53dd109d74f5",
 "upn": "frank@contoso.com",
 "unique_name": "frank@contoso.com",
 "sub": "JWvYdCWPhhlpS1Zsf7yYUxShUwtUm5yzPmw_-jX3fHY",
 "family_name": "Miller",
 "given_name": "Frank"
}.
```

JSON webes jogkivonatainak kapcsolatos további információkért tekintse meg a [JWT IETF-vázlat specifikáció](http://go.microsoft.com/fwlink/?LinkId=392344). A jogkivonat-típusok és a jogcímek kapcsolatos további információkért olvassa el a [támogatott jogkivonat és jogcímtípusok](active-directory-token-and-claims.md)

A `id_token` paraméter a következő jogcímtípust tartalmazza:

| Jogcím típusa | Leírás |
| --- | --- |
| és |A jogkivonat célközönség. Amikor a jogkivonat ügyfél alkalmazás, a célközönségét a `client_id` az ügyfél. |
| Exp |Lejárati idő. A jogkivonat lejárati idejének. A jogkivonat érvényes, az aktuális dátumra/időre lehet kisebb vagy egyenlő, mint a `exp` érték. Az idő másodpercben 1970. január 1. a ki (1970-01-01T0:0:0Z) UTC, amíg a token érvényességi lejárati idejének.|
| family_name |Felhasználó Vezetéknév vagy családnév. Az alkalmazás meg tudja jeleníteni ezt az értéket. |
| given_name |Felhasználó első nevét. Az alkalmazás meg tudja jeleníteni ezt az értéket. |
| IAT |Kiadott időpontban. Az az idő, amikor a jwt-t adta ki. Az idő másodpercben 1970. január 1. a ki (1970-01-01T0:0:0Z) UTC, amíg a token lett kiállítva. |
| iss |A jogkivonat-kibocsátó azonosítja. |
| nbf |Nem korábbi. Az az idő, amikor a jogkivonat érvénybe lép. A jogkivonat érvényes az aktuális dátum/idő nagyobb vagy egyenlő a Nbf értéknek kell lennie. Az idő másodpercben 1970. január 1. a ki (1970-01-01T0:0:0Z) UTC, amíg a token lett kiállítva. |
| oid |Objektumazonosító (ID) a felhasználó objektum az az Azure ad-ben. |
| Sub |Token tulajdonos azonosítója. Ez az a felhasználót, hogy a jogkivonat ismerteti egy állandó, és nem módosítható azonosítót. Használja ezt az értéket logika gyorsítótárazását. |
| TID |Bérlő azonosítója (ID) az Azure AD-bérlő a jogkivonatot kibocsátó. |
| unique_name |Egyedi azonosítója, amely a felhasználó jeleníthetőek meg. Ez általában az egyszerű felhasználónév (UPN). |
| egyszerű felhasználónév |A felhasználó egyszerű felhasználóneve. |
| ver |Verzió. A JWT jogkivonat, általában az 1.0-s verzióját. |

### <a name="error-response"></a>Hibaválaszba
A jogkivonat kibocsátási végpont olyan HTTP hibakódok, mert az ügyfél meghívja a hitelesítési karakterláncok kiállításához végpont közvetlenül. Mellett a HTTP-állapotkód: az Azure AD hitelesítési karakterlánc-kiállítási végpont is egy JSON-dokumentum objektumok, amelyek ismertetik a hibát adja vissza.

Egy minta hibaüzenetet nézhet ki:

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
| error |Egy hiba kód karakterlánc, amely segítségével besorolni a felmerülő hibákat, és reagálni hibákat is használható. |
| error_description |Egy adott hibaüzenet, amelyek segítségével a fejlesztők hitelesítési hiba okának azonosításához. |
| error_codes |STS-specifikus hibakódok, amelyek segítik a diagnosztika listáját. |
| időbélyeg |Az az idő, a hiba történt. |
| trace_id |A kérelem, amelyek segítik a diagnosztika egyedi azonosítója. |
| correlation_id |A kérelem, amelyek segítik a diagnosztika összetevői között egyedi azonosítója. |

#### <a name="http-status-codes"></a>HTTP-állapotkódok
A következő táblázat felsorolja a HTTP-állapotkódok, amely a token kiállítási végpont adja vissza. Bizonyos esetekben hibakód elegendő ahhoz, hogy a válasz írják le, de ha nincsenek hibák, kell elemezni, a hozzá tartozó JSON-dokumentum, és vizsgálja meg a hiba kódja.

| HTTP-kód | Leírás |
| --- | --- |
| 400 |Alapértelmezett HTTP-kód. A legtöbb esetben használja, és általában az az oka, hogy hibás kérés. Javítsa ki, és küldje el újra a kérelmet. |
| 401 |A hitelesítés sikertelen volt. Például a kérelemből hiányzik a client_secret paraméter. |
| 403 |Nem sikerült engedélyezése. Például a felhasználónak nincs engedélye az erőforrás elérésére. |
| 500 |Belső hiba történt: a szolgáltatás. Próbálkozzon újra a kéréssel. |

#### <a name="error-codes-for-token-endpoint-errors"></a>A token-végpont hibákat hibakódok
| Hibakód | Leírás | Ügyfélművelet |
| --- | --- | --- |
| invalid_request |Protokollhiba történt, például a hiányzó kötelező paraméter. |Javítsa ki, és küldje el újra a kérelmet |
| invalid_grant |Az engedélyezési kód érvénytelen vagy lejárt. |Próbálja meg egy új kérelmet a `/authorize` végpont |
| unauthorized_client |A hitelesített ügyfél nem jogosult a engedélyezési grant típus használatára. |Ez általában akkor fordul elő, amikor az ügyfél-alkalmazás nincs regisztrálva az Azure ad-ben, vagy nem kerül be a felhasználó Azure AD-bérlő. Az alkalmazás kérheti a felhasználót az alkalmazás telepítése és az Azure AD hozzáadni utasítás. |
| invalid_client |Nem sikerült ügyfél-hitelesítéshez. |Az ügyfél hitelesítő adatok érvénytelenek. Javítsa ki, az alkalmazás-rendszergazda frissítések a hitelesítő adatokat. |
| unsupported_grant_type |A hitelesítési kiszolgáló nem támogatja az engedélyezési grant típusát. |A kérelem grant típusának módosítása. Hiba az ilyen típusú csak a fejlesztés során megtörténik, és a kezdeti tesztelés során észlelt. |
| invalid_resource |A cél erőforráson érvénytelen, mert nem létezik, az Azure AD a fájl nem található vagy nincs megfelelően konfigurálva. |Ez azt jelzi, hogy az erőforrás, ha létezik, nincs konfigurálva a bérlőben. Az alkalmazás kérheti a felhasználót az alkalmazás telepítése és az Azure AD hozzáadni utasítás. |
| interaction_required |A kérelem felhasználói beavatkozást igényel. Például egy további hitelesítési lépésre szükség. | Helyett a nem interaktív kérelmet próbálkozzon újra az azonos erőforrás interaktív hitelesítési kérelmet. |
| temporarily_unavailable |A kiszolgáló ideiglenesen jelenleg túl elfoglalt a kérelem kezelésére. |Próbálkozzon újra a kéréssel. Az ügyfélalkalmazás elmagyarázhatja a felhasználót, hogy egy ideiglenes állapot miatt késik a válaszában. |

## <a name="use-the-access-token-to-access-the-resource"></a>Az erőforrás eléréséhez használja a hozzáférési jogkivonat
Most, hogy sikeresen jut hozzá egy `access_token`, azzal, hogy belefoglalja azt a webes API-k, intézett kérésekben a token is használhatja a `Authorization` fejléc. A [RFC 6750](http://www.rfc-editor.org/rfc/rfc6750.txt) megadását ismerteti a HTTP-kérelmek tulajdonosi jogkivonatok segítségével védett erőforrások eléréséhez.

### <a name="sample-request"></a>Mintakérelem
```
GET /data HTTP/1.1
Host: service.contoso.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ
```

### <a name="error-response"></a>Hibaválaszba
Védett erőforrások, amelyek megvalósítják az RFC 6750 probléma HTTP-állapotkódok. Ha a kérelem nem tartalmazza a hitelesítő adatok, vagy a token hiányzik, a válasz tartalmazza egy `WWW-Authenticate` fejléc. Egy kérelem sikertelen lesz, amikor az erőforrás-kiszolgáló válaszol a HTTP-állapotkód és egy hibakódot.

A következő példája egy sikertelen választ, ha az ügyfél kérelem nem tartalmazza a tulajdonosi jogkivonatot:

```
HTTP/1.1 401 Unauthorized
WWW-Authenticate: Bearer authorization_uri="https://login.microsoftonline.com/contoso.com/oauth2/authorize",  error="invalid_token",  error_description="The access token is missing.",
```

#### <a name="error-parameters"></a>Hiba paramétereit
| Paraméter | Leírás |
| --- | --- |
| authorization_uri |Az URI (fizikai végpont) a hitelesítési kiszolgáló. Ezt az értéket is tájékozódhat a kiszolgáló lekérése a discovery endpoint használja keresési kulcsként. <p><p> Az ügyfélnek ellenőrizni kell, hogy a hitelesítési kiszolgáló nem megbízható. Amikor az erőforrás Azure AD által védett, nem elegendő győződjön meg arról, hogy az URL-cím kezdődik https://login.microsoftonline.com vagy egy másik állomásnév, amely támogatja az Azure AD. A bérlő-specifikus erőforrás mindig kell visszaadnia egy bérlő vonatkozó engedélyezési URI Azonosítót. |
| error |Egy hiba kód értékét, az 5.2. szakaszban meghatározott a [OAuth 2.0 hitelesítési keretrendszer](http://tools.ietf.org/html/rfc6749). |
| error_description |A hiba részletes leírását. Ez az üzenet nem lehet olyan végfelhasználói leíró. |
| resource_id |Az erőforrás egyedi azonosítóját adja vissza. Az ügyfélalkalmazás Ez az azonosító értékét használhatja a `resource` paraméter az erőforráshoz tartozó jogkivonat kérelem során. <p><p> Fontos az ügyfélalkalmazás ellenőrizheti ezt az értéket a, ellenkező esetben a rosszindulatú szolgáltatás valószínűleg idéz elő egy **utasítással történő jogosultságszint-az-jogosultságokkal** támadás <p><p> Ajánlott stratégiát a támadás megakadályozza, hogy ellenőrizze, hogy a `resource_id` megegyezik a Web API URL-CÍMÉT a következő is hozzáférnek. Például ha https://service.contoso.com/data is hozzáférnek, a `resource_id` htttps://service.contoso.com/ lehet. Az ügyfélalkalmazás elutasítása kell egy `resource_id` , nem kezdődik az alap URL-cím kivéve, ha megbízható alternatív módot ellenőrizze a azonosítóját. |

#### <a name="bearer-scheme-error-codes"></a>Tulajdonosi séma hibakódok
Az RFC 6750 specifikációt a válaszban a WWW-Authenticate fejléc és a tulajdonosi sémát használó erőforrásokhoz hibák a következők határozza meg.

| HTTP-állapotkód | Hibakód | Leírás | Ügyfélművelet |
| --- | --- | --- | --- |
| 400 |invalid_request |A kérelem formátuma helytelen. Például akkor lehet, hogy hiányzik egy paraméter vagy kétszer azonos paraméter használatával. |Javítsa ki a hibát, és próbálkozzon újra a kéréssel. Hiba az ilyen típusú csak a fejlesztés során megtörténik, és a kezdeti tesztelése észlelhető. |
| 401 |invalid_token |A hozzáférési jogkivonat hiányzó, érvénytelen, vagy vissza lett vonva. A error_description paraméter értékének további részletesen ismerteti. |A hitelesítési kiszolgáló egy új jogkivonatot kérhet. Ha az új jogkivonatot sikertelen, hiba történt. A felhasználó, és próbálkozzon újra egy hibaüzenet küldése után véletlenszerű késleltetése. |
| 403 |insufficient_scope |A hozzáférési jogkivonat nem tartalmaz megszemélyesítési jogosultságot az erőforrás elérésére. |Új engedélyezési kérelmet küld az engedélyezési végpont. Ha a válasz a hatókör-paramétert tartalmaz, hatókörében lévő érték legyen használva a kérelmet az erőforráshoz. |
| 403 |insufficient_access |A tárgy, a jogkivonat nem rendelkezik az erőforrás eléréséhez szükséges engedélyeket. |Kéri a felhasználót, használjon más fiókot vagy kérjen engedélyeket a megadott erőforrás. |

## <a name="refreshing-the-access-tokens"></a>A hozzáférési jogkivonatok frissítése
Hozzáférési jogkivonatok rövid életű, és folytatja az erőforrások elérése után frissíteni kell. Is frissítheti a `access_token` szerint egy másik `POST` elküldeni a kérelmet a `/token` végpont, de ez idő biztosítása a `refresh_token` ahelyett, hogy a `code`.

Frissítési jogkivonatok nem rendelkezik megadott élettartama. A frissítési jogkivonatokat élettartamának jellemzően viszonylag hosszú. Azonban bizonyos esetekben a frissítési jogkivonatokat lejár, vissza lenne vonva, vagy nem rendelkezik megfelelő jogosultsággal a kívánt műveletet. Az alkalmazás a várt, és megfelelően a jogkivonat kibocsátási végpont által visszaadott hibák kezelésének kell.

Ha kapott választ egy frissítési jogkivonat hibával, elveti a jelenlegi frissítési jogkivonat és egy új hitelesítési kód kérése vagy hozzáférési tokent. Különösen akkor, amikor frissítés használatával lexikális elem: az engedélyezési Code Grant flow Ha tartalmazó választ kap a `interaction_required` vagy `invalid_grant` hibakódok, a frissítési jogkivonat elvetése és az új hitelesítési kódot kér.

Minta kérelem a **bérlői-specifikus** végpont (is használhatja a **közös** végpont) egy új férhetnek hozzá a frissítési jogkivonat használatával token néz ki:

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

### <a name="successful-response"></a>A sikeres válasz
A sikeres token válasz hasonlóan fog kinézni:

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
| token_type |A jogkivonat típusa. Az egyetlen támogatott érték **tulajdonosi**. |
| expires_in |A token fennmaradó élettartam (másodperc). Jellemző értéke 3600 (egy óra). |
| expires_on |A dátum és idő, amikor a jogkivonat lejár. A dátum jelzi másodpercben a 1970-01-01T0:0:0Z UTC, amíg az elévülési időt. |
| erőforrás |Azonosítja a védett erőforrást, amely a hozzáférési jogkivonat eléréséhez használt. |
| Hatókör |A natív ügyfélalkalmazás adott megszemélyesítési engedélyek. Az alapértelmezett engedély **user_impersonation**. A célként megadott erőforrás tulajdonosa választható érték regisztrálhatja az Azure AD-ben. |
| access_token |Az új jogkivonat kért. |
| refresh_token |Egy új OAuth 2.0 refresh_token kérjen új jogkivonatot, ezt a választ a lejártakor használható. |

### <a name="error-response"></a>Hibaválaszba
Egy minta hibaüzenetet nézhet ki:

```
{
  "error": "invalid_resource",
  "error_description": "AADSTS50001: The application named https://foo.microsoft.com/mail.read was not found in the tenant named 295e01fc-0c56-4ac3-ac57-5d0ed568f872.  This can happen if the application has not been installed by the administrator of the tenant or consented to by any user in the tenant.  You might have sent your authentication request to the wrong tenant.\r\nTrace ID: ef1f89f6-a14f-49de-9868-61bd4072f0a9\r\nCorrelation ID: b6908274-2c58-4e91-aea9-1f6b9c99347c\r\nTimestamp: 2016-04-11 18:59:01Z",
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
| error |Egy hiba kód karakterlánc, amely segítségével besorolni a felmerülő hibákat, és reagálni hibákat is használható. |
| error_description |Egy adott hibaüzenet, amelyek segítségével a fejlesztők hitelesítési hiba okának azonosításához. |
| error_codes |STS-specifikus hibakódok, amelyek segítik a diagnosztika listáját. |
| időbélyeg |Az az idő, a hiba történt. |
| trace_id |A kérelem, amelyek segítik a diagnosztika egyedi azonosítója. |
| correlation_id |A kérelem, amelyek segítik a diagnosztika összetevői között egyedi azonosítója. |

A hibakódok és a javasolt művelet leírását, [token-végpont hibák hibakódok](#error-codes-for-token-endpoint-errors).
