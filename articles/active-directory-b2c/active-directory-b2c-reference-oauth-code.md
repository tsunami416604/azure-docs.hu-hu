---
title: Az engedélyezési kód folyamata az Azure Active Directory B2C |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre webalkalmazásokat az Azure AD B2C-vel és az OpenID Connect hitelesítési protokoll használatával.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: c6d976869f2a068c393a643bb97cae2f7ac1a470
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52843189"
---
# <a name="azure-active-directory-b2c-oauth-20-authorization-code-flow"></a>Az Azure Active Directory B2C: OAuth 2.0 hitelesítési kódfolyamat
Használhatja az OAuth 2.0 hitelesítési kódmegadás az eszközön telepített alkalmazások a védett erőforrások, például a webes API-k eléréséhez. Az Azure Active Directory B2C használatával (Azure AD B2C-vel) megvalósítása az OAuth 2.0, adhat hozzá regisztrációt, bejelentkezést és egyéb identitáskezelési feladatokat a mobil- és asztali alkalmazásokhoz. Ez a cikk nyelvtől független. A cikkben azt ismertetjük, hogyan küldhet és fogadhat, HTTP-üzenetek bármely nyílt forráskódú könyvtáraink használata nélkül.

Az OAuth 2.0 hitelesítési kódfolyamat leírt [, az OAuth 2.0 ismertetőjének 4.1 szakaszában](https://tools.ietf.org/html/rfc6749). Hitelesítés és engedélyezés a legtöbb használhatja [alkalmazástípusok](active-directory-b2c-apps.md), beleértve a webes alkalmazások és a natívan telepített alkalmazásokat. Az OAuth 2.0 hitelesítésikód-folyamata segítségével biztonságos hozzáférési tokenek beszerzése és frissítési jogkivonatok az alkalmazásokhoz, amely által védett erőforrások eléréséhez használható egy [az engedélyezési kiszolgáló](active-directory-b2c-reference-protocols.md).  A frissítési jogkivonat lehetővé teszi az ügyfél a jogkivonatok új hozzáférést szerezni (és frissítés) Ha a hozzáférési jogkivonat lejár, általában egy óra múlva.

Ez a cikk elsősorban a **nyilvános ügyfelek** OAuth 2.0 hitelesítési kódfolyamat. Nyilvános ügyfél, akkor bármelyik ügyfélalkalmazás, amely nem megbízható, biztonságos a titkos kód a jelszavak biztonságának fenntartása érdekében. Ez magában foglalja a mobile apps, asztali alkalmazások és lényegében, ha bármely alkalmazás, amely egy eszközön fut, és hozzáférési kell. 

> [!NOTE]
> Identitáskezelés az Azure AD B2C használatával ad hozzá egy webalkalmazást, használja a [OpenID Connect](active-directory-b2c-reference-oidc.md) OAuth 2.0-s helyett.

Az Azure AD B2C kiterjeszti a szabványos OAuth 2.0 elkezdenek beérkezni a hatékonyabb, mint az egyszerű hitelesítés és engedélyezés. Azt mutatja be a [felhasználói folyamat paraméter](active-directory-b2c-reference-policies.md). Az felhasználókövetési adatai, az OAuth 2.0 használatával adja hozzá például a felhasználói élményt az alkalmazás-előfizetési, bejelentkezést és profilkezelést. Ebben a cikkben bemutatjuk, hogyan használható az OAuth 2.0 és a felhasználói folyamatok megvalósítása ezek a tapasztalatok mindegyike a natív alkalmazások az. Azt is bemutatják, hozzáférési tokenek beszerzése a webes API-k eléréséhez.

Az ebben a cikkben példa HTTP-kérések, használjuk a mintául szolgáló Azure AD B2C-címtár **fabrikamb2c.onmicrosoft.com**. A minta- és felhasználói folyamatokat is használjuk. Próbálja meg a kérések saját maga az ezeket az értékeket, vagy is le kell cserélni a saját értékeire.
Ismerje meg, hogyan [első saját Azure AD B2C directory, az alkalmazás és a felhasználói folyamatokat](#use-your-own-azure-ad-b2c-directory).

## <a name="1-get-an-authorization-code"></a>1. Hozzáférési kód beszerzése
A hitelesítési kódfolyamat irányítja a felhasználót, hogy az ügyfél kezdődik a `/authorize` végpont. Ez része a interaktív a folyamatot, ha a felhasználó hajt végre műveletet. A kéréshez, az ügyfél azt jelzi, hogy az a `scope` paraméter szerzi be a felhasználó szükséges engedélyeket. Az a `p` paraméter azt jelzi, a felhasználói folyamat végrehajtásához. Az alábbi három példák (a sortörések az olvashatóság érdekében) minden egyes használja egy másik felhasználói folyamatot.

### <a name="use-a-sign-in-user-flow"></a>Felhasználói bejelentkezési folyamat használata
```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_sign_in
```

### <a name="use-a-sign-up-user-flow"></a>Regisztráció felhasználói folyamat használata
```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_sign_up
```

### <a name="use-an-edit-profile-user-flow"></a>Egy profil szerkesztése felhasználói folyamat használata
```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_edit_profile
```

| Paraméter | Kötelező? | Leírás |
| --- | --- | --- |
| client_id |Szükséges |Az Alkalmazásazonosítót az alkalmazáshoz a hozzárendelt a [az Azure portal](https://portal.azure.com). |
| response_type |Szükséges |A válasz típusát, amelynek tartalmaznia kell `code` az engedélyezési kód folyamata. |
| redirect_uri |Szükséges |Az átirányítási URI-ját az alkalmazás, ahol a hitelesítési válaszokat küldött és az alkalmazás által fogadott. Ez pontosan egyeznie kell az átirányítási URI-k, a portál regisztrált egyik azzal a különbséggel, hogy az URL-kódolású kell lennie. |
| scope |Szükséges |Hatókörök szóközzel elválasztott listáját. Hatókör érték azt jelzi, az Azure Active Directory (Azure AD) mindkét engedélyeket, hogy a kérés érkezik. Az ügyfél-azonosító, a hatókör azt jelzi, hogy az alkalmazás kell egy hozzáférési jogkivonatot, amelyek felhasználhatók saját szolgáltatás vagy a webes API-k révén képviseli az azonos ügyfél-azonosítóját.  A `offline_access` hatókör azt jelzi, hogy kell-e az alkalmazás egy frissítési jogkivonat hosszú élettartamú erőforrások eléréséhez. Használhatja még a `openid` hatókör-azonosító jogkivonat kérése az Azure AD B2C-ből. |
| response_mode |Ajánlott |A módszer, amellyel az eredményül kapott engedélyezési kód küldi vissza az alkalmazást. Ez lehet `query`, `form_post`, vagy `fragment`. |
| state |Ajánlott |Olyan érték, amely egy karakterlánc, amely a használni kívánt tartalom lehet a kérésben. Általában egy véletlenszerűen generált egyedi érték szolgál, a webhelyközi kérések hamisításának megakadályozása támadások megelőzése érdekében. Az állapot is szolgál az alkalmazás a felhasználói állapot információt kódolása előtt a hitelesítési kérelmet. Például a felhasználó nem található az oldal, vagy a folyamatban végrehajtott felhasználói folyamatot. |
| p |Szükséges |A végrehajtott felhasználói folyamatot. Azure AD B2C-címtárát a létrehozott felhasználói folyamat neve. A felhasználói folyamat nevének értékét kell kezdődnie **b2c\_1\_**. Felhasználói folyamatok kapcsolatos további információkért lásd: [Azure AD B2C felhasználói folyamatok](active-directory-b2c-reference-policies.md). |
| parancssor |Optional |A felhasználói beavatkozás szükséges típusa. Jelenleg az egyetlen érvényes érték el `login`, amely arra kényszeríti a felhasználónak meg kell adnia a hitelesítő adataik adott kérelem. Egyszeri bejelentkezés nem lépnek érvénybe. |

Ezen a ponton a felhasználó kéri a felhasználói folyamat munkafolyamat végrehajtásához. Ez a felhasználó megadja a felhasználónevüket és jelszavukat, bejelentkezés egy közösségi identitás való regisztráláskor a címtárban, vagy bármely más több lépést is járhat. Felhasználói műveletek attól függ, hogyan van definiálva a felhasználói folyamatot.

Miután a felhasználó a felhasználói folyamat befejeződött, az Azure AD választ az alkalmazáshoz használt értéke a `redirect_uri`. A megadott metódust használ a `response_mode` paraméter. A válasz a pontosan megegyezik az egyes felhasználói művelet forgatókönyv, függetlenül a végrehajtott felhasználói folyamatot.

A sikeres válasz által használt `response_mode=query` kell kinéznie:

```
GET urn:ietf:wg:oauth:2.0:oob?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...        // the authorization_code, truncated
&state=arbitrary_data_you_can_receive_in_the_response                // the value provided in the request
```

| Paraméter | Leírás |
| --- | --- |
| Kód |Az engedélyezési kód, amely az alkalmazás kéri. Az alkalmazás az engedélyezési kód használatával egy cél-erőforrásra vonatkozó hozzáférési jogkivonat kérése. Engedélyezési kódokat is nagyon rövid ideig tartó. Ezek általában körülbelül 10 perc múlva lejár. |
| state |Tekintse meg a teljes leírást a táblázatban az előző szakaszban. Ha egy `state` paraméter tartalmazza a kérés ugyanazt az értéket meg kell jelennie a választ. Az alkalmazás kell ellenőrizze, hogy a `state` a kérés- és az értékek megegyeznek. |

Hibaválaszok is lehet küldeni az átirányítási URI-t, hogy az alkalmazás képes kezelni őket megfelelően:

```
GET urn:ietf:wg:oauth:2.0:oob?
error=access_denied
&error_description=The+user+has+cancelled+entering+self-asserted+information
&state=arbitrary_data_you_can_receive_in_the_response
```

| Paraméter | Leírás |
| --- | --- |
| error |Egy hibakód karakterláncát a bekövetkező hibák típusú besorolására használható. A karakterlánc reagálni hibákat is használja. |
| error_description |Egy adott hibaüzenet, amelyek segítségével hitelesítési hiba kiváltó okának azonosításához. |
| state |Lásd az előző táblázatban a teljes leírását. Ha egy `state` paraméter tartalmazza a kérés ugyanazt az értéket meg kell jelennie a választ. Az alkalmazás kell ellenőrizze, hogy a `state` a kérés- és az értékek megegyeznek. |

## <a name="2-get-a-token"></a>2. Egy token beszerzése
Most, hogy az engedélyezési kódot, melyeket beszerezett, beválthatja a `code` egy POST kérelmet küld az importálni kívánt erőforrás jogkivonat a `/token` végpont. Az Azure AD B2C-t kérheti a szükséges jogkivonat csak erőforrás az alkalmazás saját webes API-t. Az egyezmény, amely saját magának egy jogkivonat kérése szolgál, hogy az alkalmazás ügyfél-Azonosítóját használja, a hatókör:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://fabrikamb2c.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob

```

| Paraméter | Kötelező? | Leírás |
| --- | --- | --- |
| p |Szükséges |A felhasználói folyamat, amellyel az engedélyezési kód beszerzése. A kérelem nem használhat egy másik felhasználói folyamatot. Vegye figyelembe, hogy felveheti ezt a paramétert, hogy a *lekérdezési karakterlánc*, nem pedig a bejegyzés törzse. |
| client_id |Szükséges |Az Alkalmazásazonosítót az alkalmazáshoz a hozzárendelt a [az Azure portal](https://portal.azure.com). |
| grant_type |Szükséges |Engedélyezés típusa. Az engedélyezési kód folyamata az engedélyezési típus kell `authorization_code`. |
| scope |Ajánlott |Hatókörök szóközzel elválasztott listáját. Hatókör érték azt jelzi, az Azure AD mindkét engedélyeket, hogy a kérés érkezik. Az ügyfél-azonosító, a hatókör azt jelzi, hogy az alkalmazás kell egy hozzáférési jogkivonatot, amelyek felhasználhatók saját szolgáltatás vagy a webes API-k révén képviseli az azonos ügyfél-azonosítóját.  A `offline_access` hatókör azt jelzi, hogy kell-e az alkalmazás egy frissítési jogkivonat hosszú élettartamú erőforrások eléréséhez.  Használhatja még a `openid` hatókör-azonosító jogkivonat kérése az Azure AD B2C-ből. |
| Kód |Szükséges |A folyamat első szakasza beszerzett engedélyezési kód. |
| redirect_uri |Szükséges |Az átirányítási URI-ját az alkalmazás, ahol kapott engedélyezési kód. |

Token sikeres válasz a következőhöz hasonló:

```
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```
| Paraméter | Leírás |
| --- | --- |
| not_before |Az az időpont, amikor a jogkivonat érvényes, alapidőpont szerint. |
| token_type |A token típusú értékké. Az egyetlen, amely támogatja az Azure ad-ben típus tulajdonosi. |
| access_token |Az aláírt JSON webes jogkivonat (JWT), amely a kért. |
| scope |A hatókörök, amely a token érvényes. Emellett használhatja szerepet, amelyet a gyorsítótár-jogkivonatok későbbi használatra. |
| expires_in |Az, hogy mennyi ideig a token érvényességét (másodpercben). |
| refresh_token |Az OAuth 2.0-s frissítési jogkivonatot. Az alkalmazás használhatja ezt a jogkivonatot beszerezni a kiegészítő jogkivonatok, a jelenlegi token lejárata után. Frissítési jogkivonatok olyan hosszú élettartamú. Erőforrásokhoz való hozzáférés megőrzése a hosszabb ideig használhatja őket. További információkért lásd: a [Azure AD B2C-jogkivonatok referenciájából](active-directory-b2c-reference-tokens.md). |

Hibaválaszok néznek ki:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Paraméter | Leírás |
| --- | --- |
| error |Egy hibakód karakterláncát a bekövetkező hibák típusú besorolására használható. A karakterlánc reagálni hibákat is használja. |
| error_description |Egy adott hibaüzenet, amelyek segítségével hitelesítési hiba kiváltó okának azonosításához. |

## <a name="3-use-the-token"></a>3. A jogkivonat használatával
Most, hogy a hozzáférési jogkivonat beszerzése sikerült, használhatja a kérések a jogkivonatot a háttér-webes API-k által, beleértve a `Authorization` fejléc:

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="4-refresh-the-token"></a>4. A jogkivonat frissítése
Hozzáférési jogkivonatok és azonosító jogkivonatok olyan rövid élettartamú. Után jár le, frissítenie kell őket, hogy az erőforrások eléréséhez. Ehhez egy másik, a POST-kérés elküldése a `/token` végpont. Ebben az esetben adja meg a `refresh_token` helyett a `code`:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://fabrikamb2c.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&client_secret=JqQX2PNo9bpM0uEihUPzyrh&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Paraméter | Kötelező? | Leírás |
| --- | --- | --- |
| p |Szükséges |A felhasználói folyamatot, amely az eredeti frissítési jogkivonat beszerzéséhez használt. A kérelem nem használhat egy másik felhasználói folyamatot. Vegye figyelembe, hogy felveheti ezt a paramétert, hogy a *lekérdezési karakterlánc*, nem pedig a bejegyzés törzse. |
| client_id |Szükséges |Az Alkalmazásazonosítót az alkalmazáshoz a hozzárendelt a [az Azure portal](https://portal.azure.com). |
| client_secret |Szükséges |A titkos ügyfélkódot az client_id a társított a [az Azure portal](https://portal.azure.com). |
| grant_type |Szükséges |Engedélyezés típusa. Ez a hitelesítési kódfolyamat alapját képező, az engedélyezési típus kell `refresh_token`. |
| scope |Ajánlott |Hatókörök szóközzel elválasztott listáját. Hatókör érték azt jelzi, az Azure AD mindkét engedélyeket, hogy a kérés érkezik. Az ügyfél-azonosító, a hatókör azt jelzi, hogy az alkalmazás kell egy hozzáférési jogkivonatot, amelyek felhasználhatók saját szolgáltatás vagy a webes API-k révén képviseli az azonos ügyfél-azonosítóját.  A `offline_access` hatókör azt jelzi, hogy az alkalmazás egy frissítési jogkivonat szükséges hosszú élettartamú erőforrásokhoz való hozzáférés.  Használhatja még a `openid` hatókör-azonosító jogkivonat kérése az Azure AD B2C-ből. |
| redirect_uri |Optional |Az átirányítási URI-ját az alkalmazás, ahol kapott engedélyezési kód. |
| refresh_token |Szükséges |Az eredeti frissítési token az a folyamat második alapját képező beszerzett. |

Token sikeres válasz a következőhöz hasonló:

```
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```
| Paraméter | Leírás |
| --- | --- |
| not_before |Az az időpont, amikor a jogkivonat érvényes, alapidőpont szerint. |
| token_type |A token típusú értékké. Az egyetlen, amely támogatja az Azure ad-ben típus tulajdonosi. |
| access_token |Az Ön által kért aláírt JWT. |
| scope |A hatókörök, amely a token érvényes. Is használhatja a gyorsítótár-jogkivonatokat a hatókörök későbbi használatra. |
| expires_in |Az, hogy mennyi ideig a token érvényességét (másodpercben). |
| refresh_token |Az OAuth 2.0-s frissítési jogkivonatot. Az alkalmazás használhatja ezt a jogkivonatot beszerezni a kiegészítő jogkivonatok, a jelenlegi token lejárata után. Frissítési jogkivonatok hosszú élettartamú, és az erőforrásokhoz való hozzáférés megőrzése hosszabb ideig is használható. További információkért lásd: a [Azure AD B2C-jogkivonatok referenciájából](active-directory-b2c-reference-tokens.md). |

Hibaválaszok néznek ki:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Paraméter | Leírás |
| --- | --- |
| error |Egy hibakód karakterláncát, amely segítségével a fellépő hibákat besorolása. A karakterlánc reagálni hibákat is használja. |
| error_description |Egy adott hibaüzenet, amelyek segítségével hitelesítési hiba kiváltó okának azonosításához. |

## <a name="use-your-own-azure-ad-b2c-directory"></a>A saját Azure AD B2C-címtár használata
Szeretné kipróbálni ezeket a kérelmeket saját magának, kövesse az alábbi lépéseket. Cserélje le a példában szereplő értékeket, ez a cikk a saját értékeire használjuk.

1. [Az Azure AD B2C-címtár létrehozása](active-directory-b2c-get-started.md). Annak a könyvtárnak a nevét használja a kérelmeket.
2. [Hozzon létre egy alkalmazást](active-directory-b2c-app-registration.md) beszerzése az alkalmazás Azonosítóját és átirányítási URI-t. Egy natív ügyfél belefoglalása az alkalmazásban.
3. [A felhasználói folyamatok létrehozása](active-directory-b2c-reference-policies.md) beszerzése a felhasználói folyamat nevét.

