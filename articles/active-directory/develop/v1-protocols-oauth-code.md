---
title: Az OAuth 2.0 hitelesítési kódfolyamat megismerheti az Azure ad-ben
description: Ez a cikk ismerteti, hogyan használható a HTTP-üzenetek webalkalmazásokhoz és webes API-k a bérlő Azure Active Directory és az OAuth 2.0 használatával való hozzáférés engedélyezésére.
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 19199d25b960d768f844d725616220fb78e7d983
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2019
ms.locfileid: "55094053"
---
# <a name="authorize-access-to-azure-active-directory-web-applications-using-the-oauth-20-code-grant-flow"></a>Az OAuth 2.0 kód engedélyezési folyamatával használatával az Azure Active Directory webes alkalmazásokhoz való hozzáférés engedélyezése

Az Azure Active Directory (Azure AD) az OAuth 2.0 használatával lehetővé teszi, hogy engedélyezze a hozzáférést a webalkalmazásokhoz és webes API-k az Azure AD-bérlőben. Ez az útmutató nyelvektől független, és hogyan küldhetők és fogadhatók HTTP-üzenetek bármelyikének használata nélkül ismerteti meg [nyílt forráskódú könyvtáraink](active-directory-authentication-libraries.md).

Az OAuth 2.0 hitelesítési kódfolyamat leírt [, az OAuth 2.0 ismertetőjének 4.1 szakaszában](https://tools.ietf.org/html/rfc6749#section-4.1). Hitelesítési és engedélyezési végezhető a legtöbb alkalmazás típusok, többek között a web apps és a natív módon telepített alkalmazásokat.

[!INCLUDE [active-directory-protocols-getting-started](../../../includes/active-directory-protocols-getting-started.md)]

## <a name="oauth-20-authorization-flow"></a>OAuth 2.0 engedélyezési folyamat

Magas szinten a teljes engedélyezési folyamatot egy alkalmazáshoz egy kicsit nézhet ki:

![OAuth hitelesítési Kódfolyamat](./media/v1-protocols-oauth-code/active-directory-oauth-code-flow-native-app.png)

## <a name="request-an-authorization-code"></a>Hozzáférési kód kérése

A hitelesítési kódfolyamat irányítja a felhasználót, hogy az ügyfél kezdődik a `/authorize` végpont. A kéréshez az ügyfél azt jelzi, hogy az engedélyeket kell beszerezni a felhasználó elől. Megtekintheti az OAuth 2.0 engedélyezési végpont a bérlőhöz tartozó kiválasztásával **alkalmazásregisztrációk > végpontok** az Azure Portalon.

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
| bérlő |szükséges |A `{tenant}` szabályozza, ki az alkalmazás be tud jelentkezni az értéket a kérelem elérési használható. A megengedett értékek: bérlő azonosítókat, például `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` vagy `contoso.onmicrosoft.com` vagy `common` bérlői független jogkivonatokat |
| client_id |szükséges |Az Alkalmazásazonosítót az alkalmazáshoz rendelt Azure AD-vel való regisztrációja. Az Azure Portalon találja. Kattintson a **Azure Active Directory** a szolgáltatások oldalsávon kattintson **alkalmazásregisztrációk**, és válassza ki az alkalmazást. |
| response_type |szükséges |Tartalmaznia kell `code` az engedélyezési kód folyamata. |
| redirect_uri |Ajánlott |Az alkalmazás, ahol küldött és az alkalmazás által fogadott a hitelesítési válaszokat redirect_uri tulajdonsága. Pontosan egyeznie kell a redirect_uris regisztrálta a portálon, kivéve azt az URL-kódolású kell lennie. A natív és mobil alkalmazások esetén az alapértelmezett értéket használjon `urn:ietf:wg:oauth:2.0:oob`. |
| response_mode |választható |Meghatározza a létrejövő jogkivonat vissza küldhet az alkalmazáshoz használandó módszert. Lehet `query`, `fragment`, vagy `form_post`. `query` a kódot biztosít az átirányítási URI-t a lekérdezési sztring paramétereként. Ha Ön a kért egy azonosító jogkivonat, használja az implicit folyamatot, nem használhatja `query` meghatározott a [OpenID specifikációja](https://openid.net/specs/oauth-v2-multiple-response-types-1_0.html#Combinations). Ha csak a kódot kért, `query`, `fragment`, vagy `form_post`. `form_post` az átirányítási URI-t a kódot egy HOZZÁSZÓLÁSRA hajtja végre. Az alapértelmezett érték `query` egy kódot folyamathoz.  |
| state |Ajánlott |A kérésben is a token válaszban visszaadott érték. Egy véletlenszerűen generált egyedi érték jellemzően a [webhelyközi kérések hamisításának megakadályozása támadások](https://tools.ietf.org/html/rfc6749#section-10.12). Az állapot az alkalmazás a felhasználói állapot információt kódolásához, előtt a hitelesítési kérelmet, például az oldal vagy voltak a nézet is szolgál. |
| erőforrás | Ajánlott |Az Alkalmazásazonosító URI-t a cél a webes API (védett erőforrás). Az Alkalmazásazonosító URI-t, az Azure Portalon kattintson **Azure Active Directory**, kattintson a **alkalmazást az alkalmazásregisztrációk**, nyissa meg az alkalmazás **beállítások** lapon, majd kattintson a  **Tulajdonságok**. Például egy külső erőforrás is lehet `https://graph.microsoft.com`. Ez azért szükséges, egy engedélyezési vagy jogkivonat-kérelmeket. Annak biztosítása érdekében kevesebb hitelesítési kérések helyezze el az engedélyezési kérésben, ha biztosítani szeretné, jóváhagyás a felhasználó. |
| scope | **figyelmen kívül hagyva** | V1 az Azure AD-alkalmazásokhoz, hatókörök statikusan konfigurálni kell lennie az alkalmazások az Azure portál **beállítások**, **szükséges engedélyek**. |
| parancssor |választható |A felhasználói beavatkozás szükséges típusát jelzi.<p> Érvényes értékek a következők: <p> *Bejelentkezési*: A felhasználó a rendszer kéri, hogy hitelesítse magát újra. <p> *select_account*: A kéri felhasználót, hogy válassza ki a fiókot, az egyszeri bejelentkezés megszakítása. A felhasználó előfordulhat, hogy válasszon egy meglévő bejelentkezett fiókot, adja meg a hitelesítő adataikat a korábban megjegyzett fiók vagy teljesen használt egy másik fiókot. <p> *Hozzájárulás*: Felhasználói beleegyezés kapott, de frissíteni kell. A felhasználó jóváhagyást kell kérni. <p> *admin_consent*: A rendszergazda a rendszer kéri a szervezetben lévő összes felhasználó nevében jóváhagyást |
| login_hint |választható |Segítségével előre töltse ki a felhasználónév, e-mail-cím mező a bejelentkezési oldal a felhasználó számára, ha ismeri a kívánt időben felhasználóneve. Gyakran alkalmazások újrahitelesítés, hogy már kinyert a felhasználónevet egy előző jelentkezzen be az során használja ezt a paramétert a `preferred_username` jogcím. |
| domain_hint |választható |A bérlő és a tartományhoz, amely a felhasználónak kell használnia, hogy jelentkezzen be a mutatót. A domain_hint értéke a bérlő regisztrált tartományhoz. Ha a bérlő összevonást használ, egy helyszíni címtár, az adott bérlő összevonási kiszolgáló átirányítja a aad-ben. |
| code_challenge_method | Ajánlott    | Kódolás használt módszer a `code_verifier` számára a `code_challenge` paraméter. Lehetnek `plain` vagy `S256`. Ha ki van zárva, `code_challenge` adatforrásmérete egyszerű szöveges Ha `code_challenge` részét képezi. Az Azure AAD 1.0-s verziója is támogatja `plain` és `S256`. További információkért lásd: a [PKCE RFC](https://tools.ietf.org/html/rfc7636). |
| code_challenge        | Ajánlott    | Használt kód engedélyezések koncepció kulcs használatával a kód Exchange (PKCE) egy nyilvános vagy natív ügyfél. Kötelező, ha `code_challenge_method` részét képezi. További információkért lásd: a [PKCE RFC](https://tools.ietf.org/html/rfc7636). |

> [!NOTE]
> Ha a felhasználó egy szervezet része, a szervezet rendszergazdája hozzájárulás megadása vagy elutasítása a felhasználó nevében, vagy lehetővé teszik a felhasználó beleegyezését. A felhasználó a beállítás csak akkor, ha a rendszergazda lehetővé teszi, hogy jóváhagyást kap.
>
>

Ezen a ponton a felhasználó kéri, adja meg a hitelesítő adataikat, és hozzájárul az Azure Portalon az alkalmazás által kért engedélyeket. A felhasználó végzi a hitelesítést, és engedélyezi a jóváhagyás után az Azure AD az alkalmazáshoz, választ küld a `redirect_uri` címe a kérelemben a kóddal.

### <a name="successful-response"></a>A sikeres válasz
A sikeres válasz nézhet ki:

```
GET  HTTP/1.1 302 Found
Location: http://localhost:12345/?code= AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrqqf_ZT_p5uEAEJJ_nZ3UmphWygRNy2C3jJ239gV_DBnZ2syeg95Ki-374WHUP-i3yIhv5i-7KU2CEoPXwURQp6IVYMw-DjAOzn7C3JCu5wpngXmbZKtJdWmiBzHpcO2aICJPu1KvJrDLDP20chJBXzVYJtkfjviLNNW7l7Y3ydcHDsBRKZc3GuMQanmcghXPyoDg41g8XbwPudVh7uCmUponBQpIhbuffFP_tbV8SNzsPoFz9CLpBCZagJVXeqWoYMPe2dSsPiLO9Alf_YIe5zpi-zY4C3aLw5g9at35eZTfNd0gBRpR5ojkMIcZZ6IgAA&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF&state=D79E5777-702E-4260-9A62-37F75FF22CCE
```

| Paraméter | Leírás |
| --- | --- |
| admin_consent |Az értéke igaz, ha a rendszergazda által jóváhagyott kérelem jóváhagyásukat kéri az. |
| Kód |Az engedélyezési kódot, amely az alkalmazás kéri. Az alkalmazás az engedélyezési kód használatával a célként megadott erőforrás-hozzáférési jogkivonat kérése. |
| session_state |A jelenlegi felhasználói munkamenetet azonosító egyedi értéket. Ez az érték egy GUID Azonosítót, de vizsgálat nélkül átadott átlátszatlan érték kell kezelni. |
| state |Ha a kérelem tartalmazza a state paraméterben, ugyanazt az értéket meg kell jelennie a választ. Ajánlott az alkalmazás győződjön meg arról, hogy a kérés- és állapot értékei megegyeznek a válasz használata előtt. Ez segít észlelni [többhelyes kérelem hamisítására (CSRF) támadások](https://tools.ietf.org/html/rfc6749#section-10.12) szemben az ügyfél. |

### <a name="error-response"></a>Hiba történt a válasz
Hibaválaszok is elküldheti az `redirect_uri` úgy, hogy az alkalmazás kezelni tudja őket megfelelően.

```
GET http://localhost:12345/?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Paraméter | Leírás |
| --- | --- |
| error |Egy hiba-kód szakaszban az 5.2 meghatározott értékét a [OAuth 2.0 engedélyezési keretrendszer](https://tools.ietf.org/html/rfc6749). A következő táblázat a hibakódok, melyek az Azure AD adja vissza. |
| error_description |A hiba részletes leírását. Ez az üzenet nem célja, hogy lehet végfelhasználói rövid. |
| state |Az állapot értéke nem újra felhasználhatók egy véletlenszerűen generált érték, amely a kérés küldése és webhelyközi kérések hamisításának megakadályozása (CSRF) támadások megelőzése érdekében a válaszban visszaadott. |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Hitelesítési végpont hibák hibakódok
A következő táblázat ismerteti a különböző visszaadható hibakódok a `error` válaszként küldött hibaüzenetben paraméterében.

| Hibakód | Leírás | Ügyfélművelet |
| --- | --- | --- |
| invalid_request |Protokollhiba történt, például egy hiányzó kötelező paraméter. |Javítsa ki, és küldje el újra a kérelmet. Ez fejlesztési hiba, és a kezdeti tesztelés során általában történt. |
| unauthorized_client |Az ügyfélalkalmazás számára nem engedélyezett az engedélyezési kódot kér. |Ez általában akkor fordul elő, ha az ügyfélalkalmazás nincs regisztrálva az Azure ad-ben, vagy a felhasználó Azure AD-bérlő nem kerül. Az alkalmazás megkérheti a felhasználót az utasítást az alkalmazás telepítése és az Azure ad-hez adásával. |
| access_denied |Erőforrás tulajdonosának jóváhagyás elutasítva |Az ügyfélalkalmazás is értesíti a felhasználót, hogy azt nem lehet folytatni, kivéve, ha a felhasználó. |
| unsupported_response_type |Az engedélyezési kiszolgáló nem támogatja a kód választípusához a kérésben. |Javítsa ki, és küldje el újra a kérelmet. Ez fejlesztési hiba, és a kezdeti tesztelés során általában történt. |
| server_error |A kiszolgáló váratlan hibát észlelt. |Ismételje meg a kérelmet. Ezeket a hibákat okozhat az ideiglenes feltételek. Az ügyfélalkalmazás megmagyarázhatják a felhasználót, hogy a válasz egy átmeneti hiba miatt késik. |
| temporarily_unavailable |A kiszolgáló nem túlságosan elfoglalt a kérelem kezelése. |Ismételje meg a kérelmet. Az ügyfélalkalmazás megmagyarázhatják a felhasználót, hogy a válasz egy ideiglenes állapot miatt késik. |
| invalid_resource |A célként megadott erőforrás érvénytelen, mert nem létezik, az Azure AD nem található, vagy azt nem megfelelően van konfigurálva. |Ez azt jelzi, hogy az erőforrást, ha létezik, nincs konfigurálva a bérlőben. Az alkalmazás megkérheti a felhasználót az utasítást az alkalmazás telepítése és az Azure ad-hez adásával. |

## <a name="use-the-authorization-code-to-request-an-access-token"></a>Az engedélyezési kód használatával hozzáférési jogkivonat kérése
Most, hogy az engedélyezési kód beszerzése és engedéllyel rendelkezik a felhasználó által, a kód egy hozzáférési jogkivonatot a kívánt erőforrást beválthatja egy POST kérelmet küld a `/token` végpont:

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
| bérlő |szükséges |A `{tenant}` szabályozza, ki az alkalmazás be tud jelentkezni az értéket a kérelem elérési használható. A megengedett értékek: bérlő azonosítókat, például `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` vagy `contoso.onmicrosoft.com` vagy `common` bérlői független jogkivonatokat |
| client_id |szükséges |Az alkalmazásazonosítót az alkalmazáshoz rendelt Azure AD-vel való regisztrációja. Az Azure Portalon találja. Az alkalmazásazonosító az alkalmazás regisztrációját a beállításait jelenik meg. |
| grant_type |szükséges |Meg kell `authorization_code` az engedélyezési kód folyamata. |
| Kód |szükséges |A `authorization_code` az előző szakaszban beszerzett |
| redirect_uri |szükséges |Azonos `redirect_uri` beszerzéséhez használt értékkel a `authorization_code`. |
| client_secret |a web apps esetében nem engedélyezett a nyilvános ügyfelek szükséges |Az alkalmazás titkos, amelyet az alkalmazás mellett az Azure Portalon létrehozott **kulcsok**. Ez nem használható egy natív alkalmazást (nyilvános ügyfél), mert client_secrets megbízhatóan nem tárolható az eszközökön. Webalkalmazások és webes API-kat (az összes bizalmas ügyfeleknek), amelyek történő tárolásának lehetőségét szükséges a `client_secret` biztonságosan a kiszolgálói oldalon. A titkos ügyfélkódot kell URL-kódolású elküldése előtt. |
| erőforrás | Ajánlott |Az Alkalmazásazonosító URI-t a cél a webes API (védett erőforrás). Az Alkalmazásazonosító URI-t, az Azure Portalon kattintson **Azure Active Directory**, kattintson a **alkalmazást az alkalmazásregisztrációk**, nyissa meg az alkalmazás **beállítások** lapon, majd kattintson a  **Tulajdonságok**. Például egy külső erőforrás is lehet `https://graph.microsoft.com`. Ez azért szükséges, egy engedélyezési vagy jogkivonat-kérelmeket. Annak biztosítása érdekében kevesebb hitelesítési kérések helyezze el az engedélyezési kérésben, ha biztosítani szeretné, jóváhagyás a felhasználó. Ha az engedélyezési kérésben, mind a jogkivonat kérése az erőforrás "paramétereknek egyezniük kell. | 
| code_verifier | választható | Az azonos code_verifier a authorization_code beszerzéséhez használt. Szükséges, ha az engedélyezési kód engedélyezési kérésben PKCE használt. További információkért lásd: a [PKCE RFC](https://tools.ietf.org/html/rfc7636)   |

Az Alkalmazásazonosító URI-t, az Azure Portalon kattintson **Azure Active Directory**, kattintson a **alkalmazást az alkalmazásregisztrációk**, nyissa meg az alkalmazás **beállítások** lapon, majd kattintson a  **Tulajdonságok**.

### <a name="successful-response"></a>A sikeres válasz
Az Azure AD vissza egy [hozzáférési jogkivonat](access-tokens.md) sikeres válasz alapján. Hálózati hívások az ügyfélalkalmazás és azok kapcsolódó késés minimalizálása érdekében az ügyfélalkalmazásnak kell hozzáférési jogkivonatok gyorsítótárazása az OAuth 2.0-válaszban megadott jogkivonat élettartama. Annak megállapításához, a jogkivonat élettartamát, vagy használja a `expires_in` vagy `expires_on` paraméterértékeket.

Ha egy webes API-erőforrás adja vissza egy `invalid_token` hibakód, ez azt jelentheti, hogy az erőforrás megállapította, hogy a jogkivonat lejárt. Ha az ügyfél- és erőforrás óra alkalommal különböző (vagyis egy "időeltérés"), az erőforrás érdemes lehet a token lejárt előtt a jogkivonatot az ügyfél gyorsítótárában törlődik. Ha ez történik, akkor is, ha számított élettartamuk belül törölje a jogkivonatot a gyorsítótárból.

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
| access_token |A kért [hozzáférési jogkivonat](access-tokens.md) , egy aláírt JSON webes jogkivonat (JWT). Az alkalmazás a jogkivonat használatával hitelesítik magukat a védett erőforráshoz, például a webes API-t. |
| token_type |Typ tokenu értékét jelöli. Az egyetlen, amely támogatja az Azure ad-ben típus tulajdonosi. További információ a tulajdonosi jogkivonatokat: [OAuth2.0 engedélyezési keretrendszer: Tulajdonosi jogkivonat-használat (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt) |
| expires_in |Mennyi ideig a hozzáférési jogkivonat érvénytelen (másodpercben). |
| expires_on |A hozzáférési jogkivonat lejáratának időpontja. A dátum jelenik meg a másodpercek számát, 1970-01-01T0:0:0Z UTC a lejárati időpontig. Ez az érték a gyorsítótárazott jogkivonatok élettartama meghatározására szolgál. |
| erőforrás |Az Alkalmazásazonosító URI a webes API (védett erőforrás). |
| scope |Az ügyfélalkalmazás számára biztosított megszemélyesítési engedélyeket. Az alapértelmezett engedély `user_impersonation`. A védett erőforrás tulajdonosa további értékek regisztrálhat az Azure ad-ben. |
| refresh_token |Az OAuth 2.0-s frissítési jogkivonatot. Az alkalmazás a jogkivonat használatával szerzi be a további hozzáférési jogkivonatokat a jelenlegi hozzáférési jogkivonat lejárata után. Frissítési jogkivonatok hosszú élettartamú, és az erőforrásokhoz való hozzáférés megőrzése hosszabb ideig is használható. |
| id_token |Az előjel nélküli JSON webes jogkivonat (JWT) jelölő egy [azonosító jogkivonat](id-tokens.md). Az alkalmazás is base64Url dekódolni a szegmensek a token a bejelentkezett felhasználóval kapcsolatos információkat. Az alkalmazás gyorsítótárazzák az értékeket, és megjelenítheti őket, de azt nem igazolható azokat bármilyen engedélyezési és biztonsági határokat. |

JSON webes jogkivonatainak kapcsolatos további információkért lásd: a [JWT IETF draft specifikáció](https://go.microsoft.com/fwlink/?LinkId=392344).   Tudjon meg többet a `id_tokens`, tekintse meg a [folyamat OpenID Connect 1.0-s verziójú](v1-protocols-openid-connect-code.md).

### <a name="error-response"></a>Hiba történt a válasz
Kiállítási végpont olyan HTTP-hibakódok, mert az ügyfél közvetlenül a kiállítási végpont hívja. A HTTP-állapotkódot mellett az Azure AD-kiállítási végpont is objektumok, amelyek a hiba a JSON-dokumentumok adja vissza.

Hiba történt egy mintaválasz nézhet ki:

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
| error |Egy hibakód karakterláncát típusú előforduló hibák besorolására használható, és reagálhat a hibák használható. |
| error_description |Egy adott hibaüzenet, amelyek segítségével a fejlesztők hitelesítési hiba kiváltó okának azonosításához. |
| error_codes |A diagnosztikát segítő STS-specifikus hibakódok listáját. |
| időbélyeg |Az idő, a hiba történt. |
| trace_id |A kérelem, amelyek segítik a diagnosztikai egyedi azonosítója. |
| correlation_id |A különböző összetevők a diagnosztikát segítő kérelem egyedi azonosítója. |

#### <a name="http-status-codes"></a>HTTP-állapotkódok
Az alábbi táblázat a kiállítási végpont által visszaadott HTTP-állapotkódok. Néhány esetben elegendő ahhoz, hogy a válasz ismertetik a hibakód, de ha nincsenek hibák, szeretné-e a hozzájuk tartozó JSON-dokumentum elemzése, és vizsgálja meg a hibakódot.

| HTTP-kód | Leírás |
| --- | --- |
| 400 |Alapértelmezett HTTP-kód. A legtöbb esetben használja, és általában az oka, hogy hibás kérés. Javítsa ki, és küldje el újra a kérelmet. |
| 401 |A hitelesítés sikertelen volt. Ha például a kérelemből hiányzik a titkos ügyfélkódot paramétert. |
| 403 |A hitelesítés sikertelen. Például a felhasználónak nincs engedélye az erőforrás elérésére. |
| 500 |Belső hiba történt a szolgáltatás. Ismételje meg a kérelmet. |

#### <a name="error-codes-for-token-endpoint-errors"></a>A jogkivonat-végpont hibákat hibakódok
| Hibakód | Leírás | Ügyfélművelet |
| --- | --- | --- |
| invalid_request |Protokollhiba történt, például egy hiányzó kötelező paraméter. |Javítsa ki és küldje el újra a kérelmet |
| invalid_grant |Az engedélyezési kód érvénytelen vagy lejárt. |Próbálkozzon egy új kérelmet a `/authorize` végpont |
| unauthorized_client |A hitelesített ügyfél nem jogosult használni a engedélyezés engedélyezési típusa. |Ez általában akkor fordul elő, ha az ügyfélalkalmazás nincs regisztrálva az Azure ad-ben, vagy a felhasználó Azure AD-bérlő nem kerül. Az alkalmazás megkérheti a felhasználót az utasítást az alkalmazás telepítése és az Azure ad-hez adásával. |
| invalid_client |Ügyfél-hitelesítés nem sikerült. |Az ügyfél-hitelesítő adatok nem érvényesek. Meg az alkalmazás-rendszergazda hitelesítő adatokat frissíti. |
| unsupported_grant_type |Az engedélyezési kiszolgáló nem támogatja az engedélyezés engedélyezési típusa. |A kérelem az engedélyezési típus módosítása Ezen hibatípus csak a fejlesztés során megtörténik, és a kezdeti tesztelés során észlelt. |
| invalid_resource |A célként megadott erőforrás érvénytelen, mert nem létezik, az Azure AD nem található, vagy azt nem megfelelően van konfigurálva. |Ez azt jelzi, hogy az erőforrást, ha létezik, nincs konfigurálva a bérlőben. Az alkalmazás megkérheti a felhasználót az utasítást az alkalmazás telepítése és az Azure ad-hez adásával. |
| interaction_required |A kérelem felhasználói beavatkozást igényel. Ha például egy további hitelesítési lépésre szükség. | Helyett a nem interaktív kéréseket próbálkozzon újra egy interaktív engedélyezési kérést ugyanarra az erőforrásra vonatkozó. |
| temporarily_unavailable |A kiszolgáló nem túlságosan elfoglalt a kérelem kezelése. |Ismételje meg a kérelmet. Az ügyfélalkalmazás megmagyarázhatják a felhasználót, hogy a válasz egy ideiglenes állapot miatt késik. |

## <a name="use-the-access-token-to-access-the-resource"></a>A hozzáférési jogkivonat használata az erőforrás eléréséhez
Most, hogy sikeresen szerezte- `access_token`, használhatja a jogkivonatot a kéréseket a webes API-k, többek között a a `Authorization` fejléc. A [RFC 6750](https://www.rfc-editor.org/rfc/rfc6750.txt) specifikáció ismerteti a HTTP-kérések tulajdonosi jogkivonatok segítségével védett erőforrások eléréséhez.

### <a name="sample-request"></a>Mintakérelem
```
GET /data HTTP/1.1
Host: service.contoso.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ
```

### <a name="error-response"></a>Hiba történt a válasz
RFC 6750 probléma HTTP-állapotkódok megvalósító védett erőforrásokhoz. Ha a kérelem nem tartalmaz a hitelesítő adatok, vagy a token hiányzik, a válasz tartalmazza egy `WWW-Authenticate` fejléc. Amikor egy kérelem meghiúsul, az erőforrás-kiszolgáló válaszol a HTTP-állapotkódot és a egy hibakódot.

A következő egy sikertelen választ például amikor az ügyfél kérése nem tartalmazza a tulajdonosi jogkivonatot:

```
HTTP/1.1 401 Unauthorized
WWW-Authenticate: Bearer authorization_uri="https://login.microsoftonline.com/contoso.com/oauth2/authorize",  error="invalid_token",  error_description="The access token is missing.",
```

#### <a name="error-parameters"></a>Hiba paramétereit
| Paraméter | Leírás |
| --- | --- |
| authorization_uri |Az URI (fizikai végpont), az engedélyezési kiszolgálón. Ez az érték is szolgál keresési kulcsként részletes tájékoztatást nyújt a kiszolgáló egyes felderítési végpontokból. <p><p> Az ügyfélnek ellenőriznie kell, hogy az engedélyezési kiszolgáló nem megbízható. Az erőforrások védelme az Azure AD, ha elegendő ahhoz, hogy ellenőrizze, hogy az URL-cím kezdete https://login.microsoftonline.com vagy másik gazdagépnevet, amely támogatja az Azure ad-ben. A bérlő-specifikus erőforrás mindig egy bérlő-specifikus engedélyezési URI-t adja vissza. |
| error |Egy hiba-kód szakaszban az 5.2 meghatározott értékét a [OAuth 2.0 engedélyezési keretrendszer](https://tools.ietf.org/html/rfc6749). |
| error_description |A hiba részletes leírását. Ez az üzenet nem célja, hogy lehet végfelhasználói rövid. |
| resource_id |Az erőforrás egyedi azonosítójának beolvasása. Az ügyfélalkalmazás használható ez az azonosító értéket a `resource` paramétert az erőforrás-jogkivonat kérés. <p><p> Lényeges, hogy az ügyfélalkalmazás ellenőrizheti ezt az értéket, előfordulhat, hogy egy rosszindulatú szolgáltatás lehet tudni idéz elő egy **jogosultságszint-az-jogosultságokkal** támadás <p><p> A javasolt stratégiával és a támadás megakadályozására, hogy ellenőrizze, hogy a `resource_id` megegyezik az alapszintű, a webes API URL-címe, amely hozzáfér. Például ha https://service.contoso.com/data hozzáférnek, a `resource_id` htttps://service.contoso.com/ is lehet. Az ügyfélalkalmazás elutasítása kell egy `resource_id` , amely nem ezzel kezdődik az alap URL-címet, ha nincs alternatív megbízhatóan az azonosító ellenőrzése. |

#### <a name="bearer-scheme-error-codes"></a>Tulajdonosi séma hibakódok
Az RFC 6750 specifikáció határozza meg a következő hibák a válaszban a WWW-Authenticate fejléc és tulajdonosi sémát használó erőforrásokhoz.

| HTTP-állapotkód | Hibakód | Leírás | Ügyfélművelet |
| --- | --- | --- | --- |
| 400 |invalid_request |A kérés formátuma nem megfelelő. Például akkor lehet, hogy hiányzik egy paraméter vagy kétszer használja ugyanezt a paramétert. |Javítsa a hibát, és ismételje meg a kérelmet. Ezen hibatípus csak a fejlesztés során megtörténik, és a kezdeti tesztelés észlelhető. |
| 401 |invalid_token |A hozzáférési jogkivonat hiányzó, érvénytelen, vagy vissza lett vonva. A error_description paraméter értékét a további részleteket biztosít. |Egy új jogkivonatot kér az engedélyezési kiszolgálón. Ha az új jogkivonat nem sikerül, egy váratlan hiba történt. A felhasználó, és próbálkozzon újra egy hibaüzenet küldése után véletlenszerű késéseket. |
| 403 |insufficient_scope |A hozzáférési jogkivonat nem tartalmazza a megszemélyesítési az erőforrás eléréséhez szükséges engedéllyel. |Új engedélyezési kérést küldhet az engedélyezési végponton. Ha a válasz a hatókör-paramétert tartalmaz, használja a hatókör értéke az erőforrás a kérelem. |
| 403 |insufficient_access |A jogkivonat a tulajdonos nem rendelkezik az erőforrás eléréséhez szükséges engedélyekkel. |Kéri a felhasználót egy másik fiók használata vagy a megadott erőforráshoz engedélyek kéréséhez. |

## <a name="refreshing-the-access-tokens"></a>A hozzáférési jogkivonatok frissítése

Hozzáférési jogkivonatok rövid életű, és folytatja az erőforrások elérése után frissíteni kell. Frissítheti a `access_token` Ha elküldi egy másik `POST` kérelmet a `/token` végpont, de ez idő kezeléséről a `refresh_token` helyett a `code`.  Frissítési jogkivonatok érvényesek az összes erőforrást, amely az ügyfél már rendelkezésre beleegyezik abba, hogy hozzáférés -, a frissítési jogkivonatok vonatkozó kérés kiadott `resource=https://graph.microsoft.com` segítségével egy új hozzáférési jogkivonat a kérelem `resource=https://contoso.com/api`. 

Frissítési jogkivonatok nem rendelkezik megadott élettartam. A frissítési biztonsági jogkivonat élettartamának jellemzően viszonylag hosszú. Azonban bizonyos esetekben frissítési biztonsági jogkivonat lejár, vissza lenne vonva, vagy nem rendelkezik megfelelő jogosultsággal a kívánt műveletet. Az alkalmazás várható és megfelelően a kiállítási végpont által visszaadott hibák kezelni kell.

Frissítési jogkivonat hiba választ kap, amikor elveti a jelenlegi frissítési jogkivonat, és kérjen új engedélyezési kódot, vagy hozzáférési tokent. Különösen, ha frissítés használatával tokent a az engedélyezési kód folyamatot, ha tartalmazó választ kap a `interaction_required` vagy `invalid_grant` hibakódok, elveti a frissítési jogkivonatot, és új engedélyezési kódot kér.

Minta kérést a **bérlőspecifikus** végpont (is használhatja a **közös** végpont), egy új hozzáférési token használatával a frissítési jogkivonatok néz ki:

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
Token sikeres válasz fog kinézni:

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
| expires_in |A token fennmaradó élettartam (másodperc). Egy tipikus értéke 3600 (1 óra). |
| expires_on |A dátum és idő, amikor a jogkivonat lejár. A dátum jelenik meg a másodpercek számát, 1970-01-01T0:0:0Z UTC a lejárati időpontig. |
| erőforrás |Azonosítja a védett erőforrás, amely a hozzáférési jogkivonat elérésére használta. |
| scope |A natív ügyfélalkalmazás rendelt megszemélyesítési engedélyeket. Az alapértelmezett engedély **user_impersonation**. A célként megadott erőforrás tulajdonosának alternatív értékek regisztrálhat az Azure ad-ben. |
| access_token |Az új hozzáférési jogkivonatot, amely a kért. |
| refresh_token |Egy új OAuth 2.0 refresh_token, amely új hozzáférési jogkivonatok kérése, amikor lejár egy, a válasz is használható. |

### <a name="error-response"></a>Hiba történt a válasz
Hiba történt egy mintaválasz nézhet ki:

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
| error |Egy hibakód karakterláncát típusú előforduló hibák besorolására használható, és reagálhat a hibák használható. |
| error_description |Egy adott hibaüzenet, amelyek segítségével a fejlesztők hitelesítési hiba kiváltó okának azonosításához. |
| error_codes |A diagnosztikát segítő STS-specifikus hibakódok listáját. |
| időbélyeg |Az idő, a hiba történt. |
| trace_id |A kérelem, amelyek segítik a diagnosztikai egyedi azonosítója. |
| correlation_id |A különböző összetevők a diagnosztikát segítő kérelem egyedi azonosítója. |

A hibakódok és a javasolt művelet leírását, [hibakódok a jogkivonat-végpont hibákat](#error-codes-for-token-endpoint-errors).
