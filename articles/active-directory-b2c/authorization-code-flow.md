---
title: Engedélyezési kód folyamata - Azure Active Directory B2C | Microsoft dokumentumok
description: Ismerje meg, hogyan hozhat létre webalkalmazásokat az Azure AD B2C és az OpenID Connect hitelesítési protokoll használatával.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 8248ca0abb1d633786b09b894bcd6b1089ab2d8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260890"
---
# <a name="oauth-20-authorization-code-flow-in-azure-active-directory-b2c"></a>OAuth 2.0 engedélyezési kód folyamat az Azure Active Directory B2C

Az OAuth 2.0 engedélyezési kód támogatás az eszközre telepített alkalmazásokban a védett erőforrások, például a webes API-k eléréséhez. Az OAuth 2.0 Azure Active Directory B2C (Azure AD B2C) implementációjával regisztrációs, bejelentkezési és egyéb identitáskezelési feladatokat adhat hozzá a mobil- és asztali alkalmazásokhoz. Ez a cikk nyelvfüggetlen. A cikkben bemutatjuk, hogyan küldhet és fogadhat HTTP-üzeneteket nyílt forráskódú tárak használata nélkül.

Az OAuth 2.0 engedélyezési kódfolyamatát [az OAuth 2.0 specifikáció 4.1 szakasza](https://tools.ietf.org/html/rfc6749)ismerteti. A legtöbb [alkalmazástípusban](application-types.md)használhatja hitelesítésre és engedélyezésre, beleértve a webalkalmazásokat és a natívan telepített alkalmazásokat is. Az OAuth 2.0 engedélyezési kódfolyamat segítségével biztonságosan szerezhet hozzáférési jogkivonatokat és frissítési jogkivonatokat az alkalmazásokhoz, amelyek az [engedélyezési kiszolgáló](protocols-overview.md)által védett erőforrások elérésére használhatók.  A frissítési jogkivonat lehetővé teszi, hogy az ügyfél új hozzáférési (és frissítési) jogkivonatokat szerezzen be, miután a hozzáférési jogkivonat lejár, általában egy óra elteltével.

Ez a cikk az OAuth 2.0 engedélyezési kód **folyamatnyilvános ügyfeleire** összpontosít. A nyilvános ügyfél minden olyan ügyfélalkalmazás, amely nem megbízható a titkos jelszó integritásának biztonságos fenntartása érdekében. Ez magában foglalja a mobilalkalmazásokat, asztali alkalmazásokat, és lényegében minden olyan alkalmazást, amely egy eszközön fut, és hozzáférési jogkivonatokat kell beszereznie.

> [!NOTE]
> Ha identitáskezelést szeretne hozzáadni egy webalkalmazáshoz az Azure AD B2C használatával, használja az [OpenID Connectet](openid-connect.md) az OAuth 2.0 helyett.

Az Azure AD B2C kiterjeszti a szabványos OAuth 2.0-s folyamatokat, hogy ne csak egyszerű hitelesítést és engedélyezést tegyen. Ez bevezeti a [felhasználói folyamat](user-flow-overview.md). A felhasználói folyamatok segítségével az OAuth 2.0 segítségével felhasználói élményt adhat hozzá az alkalmazáshoz, például a regisztrációhoz, a bejelentkezéshez és a profilkezeléshez. Az OAuth 2.0 protokollt használó identitásszolgáltatók közé tartozik az [Amazon,](identity-provider-amazon.md)az [Azure Active Directory, a](identity-provider-azure-ad-single-tenant.md) [Facebook,](identity-provider-facebook.md) [a GitHub](identity-provider-github.md), a [Google](identity-provider-google.md)és a [LinkedIn](identity-provider-linkedin.md).

A cikkben szereplő HTTP-kérések kipróbálása:

1. Cserélje `{tenant}` le az Azure AD B2C-bérlő nevét.
1. Cserélje `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` le az Azure AD B2C-bérlőben korábban regisztrált alkalmazás alkalmazásazonosítóját.
1. Cserélje `{policy}` le például `b2c_1_sign_in`a bérlőben létrehozott házirend nevére.

## <a name="1-get-an-authorization-code"></a>1. Engedélyezési kód beszerezni
Az engedélyezési kód folyamata azzal kezdődik, `/authorize` hogy az ügyfél a végpontra irányítja a felhasználót. Ez a folyamat interaktív része, ahol a felhasználó műveleteket tesz. Ebben a kérésben az ügyfél `scope` a paraméterben jelzi a felhasználótól beszerezni kívánt engedélyeket. A következő három példa (az olvashatóság érdekében sortörésekkel) különböző felhasználói folyamatot használ.


```HTTP
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
```


| Paraméter | Kötelező? | Leírás |
| --- | --- | --- |
|{bérlő}| Kötelező | Az Azure AD B2C-bérlő neve|
| {házirend} | Kötelező | A futtatandó felhasználói folyamat. Adja meg az Azure AD B2C-bérlőben létrehozott felhasználói folyamat nevét. Például: `b2c_1_sign_in` `b2c_1_sign_up`, `b2c_1_edit_profile`vagy . |
| client_id |Kötelező |Az alkalmazáshoz rendelt alkalmazásazonosító az [Azure Portalon.](https://portal.azure.com) |
| response_type |Kötelező |A választípus, amelynek tartalmaznia `code` kell az engedélyezési kód folyamatához. |
| redirect_uri |Kötelező |Az alkalmazás átirányítási URI-ja, ahol az alkalmazás hitelesítési válaszokat küld és fogad. Pontosan meg kell egyeznie a portálon regisztrált átirányítási URI-k egyikével, azzal a különbséggel, hogy URL-kódolásúnak kell lennie. |
| scope |Kötelező |A hatókörök térben elválasztott listája. Egyetlen hatókör-érték jelzi az Azure Active Directory (Azure AD) mindkét kért engedélyeket. Az ügyfélazonosító használata hatókörként azt jelzi, hogy az alkalmazásnak szüksége van egy hozzáférési jogkivonatra, amely használható a saját szolgáltatásán vagy webes API-ján, amelyet ugyanaz az ügyfélazonosító jelöl.  A `offline_access` hatókör azt jelzi, hogy az alkalmazásnak szüksége van egy frissítési jogkivonatra az erőforrások hosszú élettartamú eléréséhez. A hatókör használatával `openid` is kérhet azonosító jogkivonatot az Azure AD B2C-től. |
| response_mode |Ajánlott |Az eredményül kapott engedélyezési kód nak az alkalmazásba történő visszaküldéséhez használt módszer. Ez lehet `query` `form_post`, `fragment`vagy . |
| state |Ajánlott |A kérelemben szereplő érték bármilyen használni kívánt tartalom karakterlánca lehet. Általában egy véletlenszerűen generált egyedi értéket használ, hogy megakadályozza a helyek közötti kérelem hamisítási támadások. Az állapot is a felhasználó állapotára vonatkozó információk kódolására szolgál az alkalmazásban a hitelesítési kérelem bekövetkezése előtt. Például az oldal, amelyen a felhasználó volt, vagy a végrehajtás alatt lévő felhasználói folyamat. |
| Gyors |Optional |A szükséges felhasználói beavatkozás típusa. Jelenleg az egyetlen érvényes `login`érték , amely arra kényszeríti a felhasználót, hogy adja meg a hitelesítő adatait a kéréshez. Az egyszeri bejelentkezés nem lép érvénybe. |

Ezen a ponton a felhasználónak be kell fejeznie a felhasználói folyamat munkafolyamatát. Ez magában foglalhatja a felhasználó megadását a felhasználónevét és jelszavát, a közösségi identitással való bejelentkezést, a címtárra való feliratkozást vagy bármely más lépést. A felhasználói műveletek a felhasználói folyamat definiálásától függenek.

Miután a felhasználó befejezte a felhasználói folyamatot, az Azure AD `redirect_uri`a használt értékkel válaszol az alkalmazásra. A `response_mode` paraméterben megadott módszert használja. A válasz pontosan ugyanaz az egyes felhasználói művelet forgatókönyvek, függetlenül a felhasználói folyamat, amely végrehajtotta.

A sikeres válasz, amely a `response_mode=query` következőképpen néz ki:

```HTTP
GET urn:ietf:wg:oauth:2.0:oob?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...        // the authorization_code, truncated
&state=arbitrary_data_you_can_receive_in_the_response                // the value provided in the request
```

| Paraméter | Leírás |
| --- | --- |
| code |Az alkalmazás által kért engedélyezési kód. Az alkalmazás az engedélyezési kód segítségével hozzáférési jogkivonatot kérhet egy célerőforráshoz. Az engedélyezési kódok nagyon rövid életűek. Általában körülbelül 10 perc után járnak le. |
| state |Lásd a teljes leírást az előző szakasz táblázatában. Ha `state` egy paraméter szerepel a kérelemben, akkor ugyanazt az értéket kell megjelennie a válaszban. Az alkalmazásnak ellenőriznie kell, hogy a `state` kérelemben és a válaszban lévő értékek azonosak-e. |

A hibaválaszok is elküldhetők az átirányítási URI-ba, hogy az alkalmazás megfelelően tudja kezelni őket:

```HTTP
GET urn:ietf:wg:oauth:2.0:oob?
error=access_denied
&error_description=The+user+has+cancelled+entering+self-asserted+information
&state=arbitrary_data_you_can_receive_in_the_response
```

| Paraméter | Leírás |
| --- | --- |
| error |Hibakód-karakterlánc, amely a előforduló hibatípusok osztályozására használható. A karakterlánc segítségével is reagálhat a hibákra. |
| error_description |Egy adott hibaüzenet, amely segíthet a hitelesítési hiba kiváltó okának azonosításában. |
| state |Lásd a teljes leírást az előző táblázatban. Ha `state` egy paraméter szerepel a kérelemben, akkor ugyanazt az értéket kell megjelennie a válaszban. Az alkalmazásnak ellenőriznie kell, hogy a `state` kérelemben és a válaszban lévő értékek azonosak-e. |

## <a name="2-get-a-token"></a>2. Kap egy token
Most, hogy megszerezte az engedélyezési kódot, beválthatja a `code` jogkivonatot a tervezett erőforrásra, ha postakérést küld a `/token` végpontnak. Az Azure AD B2C-ben [hozzáférési jogkivonatokat kérhet más API-khoz](access-tokens.md#request-a-token) a szokásos módon, a kérelemben azok hatókörének megadásával.

Az alkalmazás saját háttérwebes API-jához hozzáférési jogkivonatot is kérhet, ha az alkalmazás ügyfélazonosítóját használja a kért hatókörként (ami egy hozzáférési jogkivonatot eredményez, amelyben az ügyfélazonosító "közönség"):

```HTTP
POST https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1

Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob

```

| Paraméter | Kötelező? | Leírás |
| --- | --- | --- |
|{bérlő}| Kötelező | Az Azure AD B2C-bérlő neve|
|{házirend}| Kötelező| Az engedélyezési kód beszerzéséhez használt felhasználói folyamat. Ebben a kérésben nem használhat másik felhasználói folyamatot. |
| client_id |Kötelező |Az alkalmazáshoz rendelt alkalmazásazonosító az [Azure Portalon.](https://portal.azure.com)|
| client_secret | Igen, a Web Apps alkalmazásban | Az [Azure Portalon](https://portal.azure.com/)létrehozott alkalmazástitok. Az ügyféltitkos kulcsok ebben a folyamatban webalkalmazás-forgatókönyvekben használatosak, ahol az ügyfél biztonságosan tárolhat egy ügyféltitkot. Natív alkalmazás (nyilvános ügyfél) forgatókönyvek esetén az ügyféltitkos kulcsok nem tárolhatók biztonságosan, ezért nem használhatók ebben a hívásban. Ha ügyféltitkot használ, kérjük, rendszeresen módosítsa azt. |
| grant_type |Kötelező |A támogatás típusa. Az engedélyezési kód folyamatához a `authorization_code`támogatás típusának . |
| scope |Ajánlott |A hatókörök térben elválasztott listája. Egyetlen hatókör értéke jelzi az Azure AD mindkét kért engedélyeket. Az ügyfélazonosító használata hatókörként azt jelzi, hogy az alkalmazásnak szüksége van egy hozzáférési jogkivonatra, amely használható a saját szolgáltatásán vagy webes API-ján, amelyet ugyanaz az ügyfélazonosító jelöl.  A `offline_access` hatókör azt jelzi, hogy az alkalmazásnak szüksége van egy frissítési jogkivonatra az erőforrások hosszú élettartamú eléréséhez.  A hatókör használatával `openid` is kérhet azonosító jogkivonatot az Azure AD B2C-től. |
| code |Kötelező |A folyamat első szakaszában beszerzett engedélyezési kód. |
| redirect_uri |Kötelező |Annak az alkalmazásnak az átirányítási URI-ja, amelynek az engedélyezési kódot kapta. |

A sikeres jogkivonat-válasz így néz ki:

```JSON
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
| not_before |A token érvényességének időpontja, időszerint. |
| token_type |A token típusának értéke. Az Azure AD által támogatott egyetlen típus a bemutatóra. |
| access_token |A kért json-webtoken (JWT). |
| scope |Azok a hatókörök, amelyekre a jogkivonat érvényes. A további felhasználás érdekében a hatókörök segítségével is gyorsítótárazhatja a tokeneket. |
| expires_in |A token érvényességi ideje (másodpercben). |
| refresh_token |Egy OAuth 2.0 frissítési jogkivonat. Az alkalmazás használhatja ezt a jogkivonatot további jogkivonatok beszerzéséhez az aktuális jogkivonat lejárta után. A frissítési tokenek hosszú élettartamúak. Ezek segítségével hosszabb ideig megtarthatja az erőforrásokhoz való hozzáférést. További információ: az [Azure AD B2C token hivatkozás.](tokens-overview.md) |

A hibaválaszok a következőkre néznek ki:

```JSON
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Paraméter | Leírás |
| --- | --- |
| error |Hibakód-karakterlánc, amely a előforduló hibatípusok osztályozására használható. A karakterlánc segítségével is reagálhat a hibákra. |
| error_description |Egy adott hibaüzenet, amely segíthet a hitelesítési hiba kiváltó okának azonosításában. |

## <a name="3-use-the-token"></a>3. Használja a token
Most, hogy sikeresen beszerzett egy hozzáférési jogkivonatot, használhatja a jogkivonatot a háttérwebes API-knak a háttérwebes API-kra irányuló kérelmekben, ha a `Authorization` fejlécbe is belehelyezi:

```HTTP
GET /tasks
Host: mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="4-refresh-the-token"></a>4. Frissítse a token
A hozzáférési jogkivonatok és az azonosító jogkivonatok rövid életűek. Lejáratuk után frissítenie kell őket az erőforrások eléréséhez. Ehhez küldjön be egy másik `/token` POST-kérelmet a végpontnak. Ez úttal adja `refresh_token` meg `code`a helyett a:

```HTTP
POST https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1

Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Paraméter | Kötelező? | Leírás |
| --- | --- | --- |
|{bérlő}| Kötelező | Az Azure AD B2C-bérlő neve|
|{házirend} |Kötelező |Az eredeti frissítési jogkivonat beszerzéséhez használt felhasználói folyamat. Ebben a kérésben nem használhat másik felhasználói folyamatot. |
| client_id |Kötelező |Az alkalmazáshoz rendelt alkalmazásazonosító az [Azure Portalon.](https://portal.azure.com) |
| client_secret | Igen, a Web Apps alkalmazásban | Az [Azure Portalon](https://portal.azure.com/)létrehozott alkalmazástitok. Az ügyféltitkos kulcsok ebben a folyamatban webalkalmazás-forgatókönyvekben használatosak, ahol az ügyfél biztonságosan tárolhat egy ügyféltitkot. Natív alkalmazás (nyilvános ügyfél) forgatókönyvek esetén az ügyféltitkos kulcsok nem tárolhatók biztonságosan, ezért nem használhatók ebben a hívásban. Ha ügyféltitkot használ, kérjük, rendszeresen módosítsa azt. |
| grant_type |Kötelező |A támogatás típusa. Az engedélyezési kódfolyamat ezen szakasza esetén `refresh_token`a támogatás típusának . |
| scope |Ajánlott |A hatókörök térben elválasztott listája. Egyetlen hatókör értéke jelzi az Azure AD mindkét kért engedélyeket. Az ügyfélazonosító használata hatókörként azt jelzi, hogy az alkalmazásnak szüksége van egy hozzáférési jogkivonatra, amely használható a saját szolgáltatásán vagy webes API-ján, amelyet ugyanaz az ügyfélazonosító jelöl.  A `offline_access` hatókör azt jelzi, hogy az alkalmazásnak szüksége lesz egy frissítési jogkivonatra az erőforrások hosszú élettartamú eléréséhez.  A hatókör használatával `openid` is kérhet azonosító jogkivonatot az Azure AD B2C-től. |
| redirect_uri |Optional |Annak az alkalmazásnak az átirányítási URI-ja, amelynek az engedélyezési kódot kapta. |
| refresh_token |Kötelező |A folyamat második szakaszában beszerzett eredeti frissítési token. |

A sikeres jogkivonat-válasz így néz ki:

```JSON
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
| not_before |A token érvényességének időpontja, időszerint. |
| token_type |A token típusának értéke. Az Azure AD által támogatott egyetlen típus a bemutatóra. |
| access_token |Az aláírt JWT, amit kért. |
| scope |Azok a hatókörök, amelyekre a jogkivonat érvényes. A hatókörök segítségével gyorsítótárazhatja a tokeneket későbbi használatra. |
| expires_in |A token érvényességi ideje (másodpercben). |
| refresh_token |Egy OAuth 2.0 frissítési jogkivonat. Az alkalmazás használhatja ezt a jogkivonatot további jogkivonatok beszerzéséhez az aktuális jogkivonat lejárta után. A frissítési jogkivonatok hosszú élettartamúak, és az erőforrásokhoz való hozzáférés hosszabb ideig való megőrzésére használhatók. További információ: az [Azure AD B2C token hivatkozás.](tokens-overview.md) |

A hibaválaszok a következőkre néznek ki:

```JSON
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Paraméter | Leírás |
| --- | --- |
| error |Hibakód-karakterlánc, amely a előforduló hibatípusok osztályozására használható. A karakterlánc segítségével is reagálhat a hibákra. |
| error_description |Egy adott hibaüzenet, amely segíthet a hitelesítési hiba kiváltó okának azonosításában. |

## <a name="use-your-own-azure-ad-b2c-directory"></a>Saját Azure AD B2C-címtár használata
Ha ezeket a kéréseket saját kezűleg szeretné kipróbálni, hajtsa végre az alábbi lépéseket. Cserélje le a cikkben használt példaértékeket a saját értékeire.

1. [Hozzon létre egy Azure AD B2C könyvtárat.](tutorial-create-tenant.md) Használja a könyvtár nevét a kérelmekben.
2. [Hozzon létre egy alkalmazást](tutorial-register-applications.md) egy alkalmazásazonosító és egy átirányítási URI beszerzéséhez. Natív ügyfél felvétele az alkalmazásba.
3. [Hozza létre a felhasználói folyamatokat](user-flow-overview.md) a felhasználói folyamatnevek beszerzéséhez.
