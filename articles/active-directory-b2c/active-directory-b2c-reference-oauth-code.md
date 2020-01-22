---
title: Engedélyezési kód folyamatábrája – Azure Active Directory B2C | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre webalkalmazásokat a Azure AD B2C és az OpenID Connect hitelesítési protokoll használatával.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: marsma
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: e813a16416343954ea92813cb53bbd81f1977320
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/21/2020
ms.locfileid: "76290121"
---
# <a name="oauth-20-authorization-code-flow-in-azure-active-directory-b2c"></a>OAuth 2,0 engedélyezési kód folyamata Azure Active Directory B2C

Az eszközön telepített alkalmazásokban a OAuth 2,0-as engedélyezési kód segítségével hozzáférhet a védett erőforrásokhoz, például a webes API-khoz. A OAuth 2,0 Azure Active Directory B2C (Azure AD B2C) implementációjának használatával a mobil-és asztali alkalmazásokhoz regisztrációt, bejelentkezést és egyéb Identitáskezelés-felügyeleti feladatokat adhat hozzá. Ez a cikk nyelvtől független. A cikkben bemutatjuk, hogyan küldhet és fogadhat HTTP-üzeneteket a nyílt forráskódú kódtárak használata nélkül.

Az OAuth 2,0 engedélyezési kód folyamatát a [OAuth 2,0 specifikáció 4,1. szakasza](https://tools.ietf.org/html/rfc6749)ismerteti. Használhatja hitelesítésre és engedélyezésre a legtöbb alkalmazás- [típusban](active-directory-b2c-apps.md), beleértve a webalkalmazásokat és a natív módon telepített alkalmazásokat is. Az OAuth 2,0 engedélyezési kód folyamatával biztonságosan kaphat hozzáférési jogkivonatokat, és frissítheti az alkalmazásaihoz tartozó jogkivonatokat, amelyek az [engedélyezési kiszolgáló](active-directory-b2c-reference-protocols.md)által védett erőforrások elérésére használhatók.  A frissítési jogkivonat lehetővé teszi, hogy az ügyfél új hozzáférési (és frissítési) jogkivonatokat szerezzen be a hozzáférési jogkivonat lejárta után, általában egy óra elteltével.

Ez a cikk a **nyilvános ügyfelek** OAuth 2,0 engedélyezési kód folyamatát ismerteti. A nyilvános ügyfél olyan ügyfélalkalmazás, amely nem megbízható a titkos jelszó integritásának biztonságos fenntartásához. Ez magában foglalja a Mobile apps, az asztali alkalmazások és az eszközön futó alkalmazások, valamint a hozzáférési jogkivonatok beszerzését.

> [!NOTE]
> Ha Azure AD B2C használatával szeretne identitás-kezelést hozzáadni egy webalkalmazáshoz, használja az [OpenID connectet](active-directory-b2c-reference-oidc.md) a OAuth 2,0 helyett.

Azure AD B2C kiterjeszti a standard OAuth 2,0-as folyamatait, hogy több, mint egyszerű hitelesítést és engedélyezést végezzenek. Bevezeti a [felhasználói folyamatot](active-directory-b2c-reference-policies.md). A felhasználói folyamatok esetében a OAuth 2,0 használatával felhasználói élményeket adhat hozzá az alkalmazáshoz, például a regisztráláshoz, a bejelentkezéshez és a profilok kezeléséhez. Az OAuth 2,0 protokollt használó azonosítók közé tartozik az [Amazon](active-directory-b2c-setup-amzn-app.md), a [Azure Active Directory](active-directory-b2c-setup-oidc-azure-active-directory.md), a [Facebook](active-directory-b2c-setup-fb-app.md), a [GitHub](active-directory-b2c-setup-github-app.md), a [Google](active-directory-b2c-setup-goog-app.md)és a [LinkedIn](active-directory-b2c-setup-li-app.md).

A cikkben szereplő HTTP-kérelmek kipróbálásához:

1. Cserélje le a `{tenant}`t a Azure AD B2C bérlő nevére.
1. Cserélje le a `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6`t egy korábban a Azure AD B2C-bérlőben regisztrált alkalmazáshoz tartozó alkalmazás-AZONOSÍTÓra.
1. Cserélje le a `{policy}`t a bérlőben létrehozott szabályzat nevére, például `b2c_1_sign_in`.

## <a name="1-get-an-authorization-code"></a>1. engedélyezési kód beszerzése
Az engedélyezési kód folyamata azzal kezdődik, hogy az ügyfél átirányítja a felhasználót a `/authorize` végpontra. Ez a folyamat interaktív része, ahol a felhasználó végrehajtja a műveletet. Ebben a kérelemben az ügyfél a `scope` paraméterben jelzi, hogy milyen engedélyek szükségesek a felhasználótól való beolvasáshoz. A következő három példa (az olvashatóság érdekében sortöréssel) minden más felhasználói folyamatot használ.


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
|Bérlő| Szükséges | A Azure AD B2C bérlő neve|
| politika | Szükséges | A futtatandó felhasználói folyamat. Adja meg a Azure AD B2C bérlőben létrehozott felhasználói folyamat nevét. Például: `b2c_1_sign_in`, `b2c_1_sign_up`vagy `b2c_1_edit_profile`. |
| client_id |Szükséges |Az alkalmazáshoz a [Azure Portal](https://portal.azure.com)hozzárendelt alkalmazás-azonosító. |
| response_type |Szükséges |A válasz típusa, amelynek tartalmaznia kell `code` az engedélyezési kód folyamatát. |
| redirect_uri |Szükséges |Az alkalmazás átirányítási URI-ja, ahol az alkalmazás elküldi és fogadja a hitelesítési válaszokat. Pontosan meg kell egyeznie a portálon regisztrált átirányítási URI-k egyikével, azzal a különbséggel, hogy az URL-kódolásnak kell lennie. |
| scope |Szükséges |A hatókörök szóközzel tagolt listája. Egyetlen hatóköri érték azt jelzi, hogy Azure Active Directory (Azure AD) a kért engedélyek mindegyikét. Az ügyfél-azonosító hatókörének használata azt jelzi, hogy az alkalmazásnak olyan hozzáférési jogkivonatra van szüksége, amely a saját szolgáltatásán vagy webes API-ban használható, ugyanazokat az ügyfél-azonosítót jelképezve.  A `offline_access` hatókör azt jelzi, hogy az alkalmazásnak frissítési jogkivonatra van szüksége az erőforrásokhoz való hosszú élettartamú hozzáféréshez. Az `openid` hatókörrel is elvégezheti az azonosító token igénylését Azure AD B2Cból. |
| response_mode |Ajánlott |Az eredményül kapott engedélyezési kód az alkalmazásba való visszaküldéséhez használt módszer. Ez lehet `query`, `form_post`vagy `fragment`. |
| state |Ajánlott |A kérelemben szereplő érték, amely a használni kívánt tartalom karakterlánca lehet. A rendszer általában véletlenszerűen generált egyedi értéket használ, hogy megakadályozza a helyek közötti kérelmek hamisításának támadásait. Az állapot az alkalmazásban a felhasználó állapotára vonatkozó információk kódolására is használatos, mielőtt a hitelesítési kérelem bekövetkezett volna. Például a felhasználó lapja vagy a végrehajtás alatt álló felhasználói folyamat. |
| gyors |Választható |A kötelező felhasználói beavatkozás típusa. Jelenleg az egyetlen érvényes érték `login`, amely arra kényszeríti a felhasználót, hogy adja meg hitelesítő adatait a kérésen. Az egyszeri bejelentkezés nem lép érvénybe. |

Ekkor a rendszer megkéri a felhasználót, hogy fejezze be a felhasználói folyamat munkafolyamatát. Ez magában foglalhatja a felhasználónevet és a jelszót, illetve a közösségi identitással való bejelentkezést, a címtárra való regisztrációt, illetve az egyéb lépések számát is. A felhasználói műveletek attól függnek, hogy a felhasználói folyamat hogyan van definiálva.

Miután a felhasználó befejezte a felhasználói folyamatot, az Azure AD egy választ küld az alkalmazásnak a `redirect_uri`használt értékre. A `response_mode` paraméterben megadott metódust használja. A válasz pontosan ugyanaz, mint a felhasználói műveletek egyes forgatókönyvei esetében, a végrehajtott felhasználói folyamattól függetlenül.

Az `response_mode=query`t használó sikeres válasz a következőképpen néz ki:

```HTTP
GET urn:ietf:wg:oauth:2.0:oob?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...        // the authorization_code, truncated
&state=arbitrary_data_you_can_receive_in_the_response                // the value provided in the request
```

| Paraméter | Leírás |
| --- | --- |
| kód |Az alkalmazás által kért engedélyezési kód. Az alkalmazás az engedélyezési kóddal kérhet hozzáférési tokent a cél erőforráshoz. Az engedélyezési kódok nagyon rövid életűek. Általában körülbelül 10 perc elteltével lejárnak. |
| state |Az előző szakaszban található táblázatban tekintse meg a teljes leírást. Ha egy `state` paraméter szerepel a kérelemben, akkor a válaszban ugyanazt az értéket kell megjelennie. Az alkalmazásnak ellenőriznie kell, hogy a kérelemben és a válaszban szereplő `state` értékek azonosak-e. |

A rendszer a hibaüzeneteket is elküldheti az átirányítási URI-nak, hogy az alkalmazás megfelelően tudja kezelni őket:

```HTTP
GET urn:ietf:wg:oauth:2.0:oob?
error=access_denied
&error_description=The+user+has+cancelled+entering+self-asserted+information
&state=arbitrary_data_you_can_receive_in_the_response
```

| Paraméter | Leírás |
| --- | --- |
| error |Hibakód-karakterlánc, amely a felmerülő hibák típusának besorolására használható. A karakterláncot is használhatja a hibákra való reagálásra. |
| error_description |Egy adott hibaüzenet, amely segítséget nyújt a hitelesítési hiba kiváltó okának azonosításában. |
| state |Tekintse meg az előző táblázatban szereplő teljes leírást. Ha egy `state` paraméter szerepel a kérelemben, akkor a válaszban ugyanazt az értéket kell megjelennie. Az alkalmazásnak ellenőriznie kell, hogy a kérelemben és a válaszban szereplő `state` értékek azonosak-e. |

## <a name="2-get-a-token"></a>2. jogkivonat beolvasása
Most, hogy beszerzett egy engedélyezési kódot, beválthatja a tokenhez tartozó `code` a kívánt erőforráshoz egy POST-kérelem küldésével a `/token` végpontnak. A Azure AD B2Cban a [más API-k számára a szokásos módon kérhet hozzáférési jogkivonatokat](active-directory-b2c-access-tokens.md#request-a-token) a kérelemben szereplő hatókör (ek) megadásával.

Az alkalmazás saját háttérbeli webes API-hoz is kérhet hozzáférési tokent, ha az alkalmazás ügyfél-AZONOSÍTÓját használja a kért hatókörként (amely az ügyfél-AZONOSÍTÓhoz tartozó hozzáférési jogkivonatot eredményezi a "célközönség"):

```HTTP
POST https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1

Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob

```

| Paraméter | Kötelező? | Leírás |
| --- | --- | --- |
|Bérlő| Szükséges | A Azure AD B2C bérlő neve|
|politika| Szükséges| Az engedélyezési kód beszerzéséhez használt felhasználói folyamat. Ebben a kérelemben nem használhat másik felhasználói folyamatot. |
| client_id |Szükséges |Az alkalmazáshoz a [Azure Portal](https://portal.azure.com)hozzárendelt alkalmazás-azonosító.|
| client_secret | Igen, Web Apps | Az [Azure Portalban](https://portal.azure.com/)létrehozott alkalmazás titka. A folyamat az ügyfél titkos kulcsait használja a webalkalmazási forgatókönyvek esetében, ahol az ügyfél biztonságosan tárolhat egy ügyfél titkát. A natív alkalmazások (nyilvános ügyfelek) esetében az ügyfél titkos kulcsai nem tárolhatók biztonságosan, ezért nem használhatók ebben a hívásban. Ha ügyfél-titkos kulcsot használ, rendszeres időközönként módosítsa azt. |
| grant_type |Szükséges |A támogatás típusa. Az engedélyezési kód folyamatához `authorization_code`kell lennie. |
| scope |Ajánlott |A hatókörök szóközzel tagolt listája. Egyetlen hatóköri érték azt jelzi, hogy az Azure AD-t mind a kért engedélyek jelentik. Az ügyfél-azonosító hatókörének használata azt jelzi, hogy az alkalmazásnak olyan hozzáférési jogkivonatra van szüksége, amely a saját szolgáltatásán vagy webes API-ban használható, ugyanazokat az ügyfél-azonosítót jelképezve.  A `offline_access` hatókör azt jelzi, hogy az alkalmazásnak frissítési jogkivonatra van szüksége az erőforrásokhoz való hosszú élettartamú hozzáféréshez.  Az `openid` hatókörrel is elvégezheti az azonosító token igénylését Azure AD B2Cból. |
| kód |Szükséges |A folyamat első szakaszában beszerzett engedélyezési kód. |
| redirect_uri |Szükséges |Annak az alkalmazásnak az átirányítási URI azonosítója, amelyen az engedélyezési kódot megkapta. |

Egy sikeres jogkivonat-válasz a következőképpen néz ki:

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
| not_before |Az az időpont, amikor a jogkivonat érvényesnek minősül, a kor időpontjában. |
| token_type |A jogkivonat típusának értéke. Az Azure AD által támogatott egyetlen típus a tulajdonos. |
| access_token |A kért aláírt JSON Web Token (JWT). |
| scope |Azok a hatókörök, amelyekhez a jogkivonat érvényes. Hatóköröket is használhat a tokenek későbbi használatra való gyorsítótárazásához. |
| expires_in |Az az időtartam, ameddig a jogkivonat érvényes (másodpercben). |
| refresh_token |Egy OAuth 2,0 frissítési token. Az alkalmazás az aktuális jogkivonat lejárta után további jogkivonatok beszerzésére használhatja ezt a tokent. A frissítési tokenek hosszú életűek. A segítségével hosszabb ideig megőrizheti az erőforrásokhoz való hozzáférést. További információ: [Azure ad B2C jogkivonat-hivatkozás](active-directory-b2c-reference-tokens.md). |

A hibaüzenetek így néznek ki:

```JSON
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Paraméter | Leírás |
| --- | --- |
| error |Hibakód-karakterlánc, amely a felmerülő hibák típusának besorolására használható. A karakterláncot is használhatja a hibákra való reagálásra. |
| error_description |Egy adott hibaüzenet, amely segítséget nyújt a hitelesítési hiba kiváltó okának azonosításában. |

## <a name="3-use-the-token"></a>3. a jogkivonat használata
Most, hogy sikeresen beszerzett egy hozzáférési jogkivonatot, a jogkivonatot a háttérben lévő webes API-khoz a kérelmekben a `Authorization` fejlécében is használhatja:

```HTTP
GET /tasks
Host: mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="4-refresh-the-token"></a>4. a jogkivonat frissítése
A hozzáférési tokenek és az azonosító tokenek rövid életűek. A lejárat után frissítenie kell őket, hogy továbbra is hozzáférhessenek az erőforrásokhoz. Ehhez küldje el egy másik POST-kérelmet az `/token`-végpontnak. Ezúttal a `code`helyett adja meg a `refresh_token`:

```HTTP
POST https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1

Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Paraméter | Kötelező? | Leírás |
| --- | --- | --- |
|Bérlő| Szükséges | A Azure AD B2C bérlő neve|
|politika |Szükséges |Az eredeti frissítési jogkivonat beszerzéséhez használt felhasználói folyamat. Ebben a kérelemben nem használhat másik felhasználói folyamatot. |
| client_id |Szükséges |Az alkalmazáshoz a [Azure Portal](https://portal.azure.com)hozzárendelt alkalmazás-azonosító. |
| client_secret | Igen, Web Apps | Az [Azure Portalban](https://portal.azure.com/)létrehozott alkalmazás titka. A folyamat az ügyfél titkos kulcsait használja a webalkalmazási forgatókönyvek esetében, ahol az ügyfél biztonságosan tárolhat egy ügyfél titkát. A natív alkalmazások (nyilvános ügyfelek) esetében az ügyfél titkos kulcsai nem tárolhatók biztonságosan, ezért nem használhatók ebben a hívásban. Ha ügyfél-titkos kulcsot használ, rendszeres időközönként módosítsa azt. |
| grant_type |Szükséges |A támogatás típusa. Az engedélyezési kód folyamatának ezen szakasza esetében a Grant típusnak `refresh_token`nak kell lennie. |
| scope |Ajánlott |A hatókörök szóközzel tagolt listája. Egyetlen hatóköri érték azt jelzi, hogy az Azure AD-t mind a kért engedélyek jelentik. Az ügyfél-azonosító hatókörének használata azt jelzi, hogy az alkalmazásnak olyan hozzáférési jogkivonatra van szüksége, amely a saját szolgáltatásán vagy webes API-ban használható, ugyanazokat az ügyfél-azonosítót jelképezve.  A `offline_access` hatókör azt jelzi, hogy az alkalmazásnak frissítési jogkivonatra lesz szüksége az erőforrásokhoz való hosszú élettartamú hozzáféréshez.  Az `openid` hatókörrel is elvégezheti az azonosító token igénylését Azure AD B2Cból. |
| redirect_uri |Választható |Annak az alkalmazásnak az átirányítási URI azonosítója, amelyen az engedélyezési kódot megkapta. |
| refresh_token |Szükséges |A folyamat második szakaszában beszerzett eredeti frissítési jogkivonat. |

Egy sikeres jogkivonat-válasz a következőképpen néz ki:

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
| not_before |Az az időpont, amikor a jogkivonat érvényesnek minősül, a kor időpontjában. |
| token_type |A jogkivonat típusának értéke. Az Azure AD által támogatott egyetlen típus a tulajdonos. |
| access_token |A kért aláírt JWT. |
| scope |Azok a hatókörök, amelyekhez a jogkivonat érvényes. A hatóköröket használhatja a tokenek későbbi használatra való gyorsítótárazásához is. |
| expires_in |Az az időtartam, ameddig a jogkivonat érvényes (másodpercben). |
| refresh_token |Egy OAuth 2,0 frissítési token. Az alkalmazás az aktuális jogkivonat lejárta után további jogkivonatok beszerzésére használhatja ezt a tokent. A frissítési tokenek hosszú életűek, és az erőforrásokhoz való hozzáférés hosszabb ideig is használhatók. További információ: [Azure ad B2C jogkivonat-hivatkozás](active-directory-b2c-reference-tokens.md). |

A hibaüzenetek így néznek ki:

```JSON
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Paraméter | Leírás |
| --- | --- |
| error |Hibakód-karakterlánc, amely a hibák típusának besorolására használható. A karakterláncot is használhatja a hibákra való reagálásra. |
| error_description |Egy adott hibaüzenet, amely segítséget nyújt a hitelesítési hiba kiváltó okának azonosításában. |

## <a name="use-your-own-azure-ad-b2c-directory"></a>Saját Azure AD B2C címtár használata
Ha szeretné kipróbálni ezeket a kéréseket, hajtsa végre az alábbi lépéseket. Cserélje le a cikkben használt példa értékeket a saját értékeire.

1. [Hozzon létre egy Azure ad B2C könyvtárat](active-directory-b2c-get-started.md). Használja a címtár nevét a kérelmekben.
2. [Hozzon létre egy alkalmazást](active-directory-b2c-app-registration.md) egy alkalmazás-azonosító és egy ÁTirányítási URI beszerzéséhez. Adjon meg egy natív ügyfelet az alkalmazásban.
3. [Hozza létre a felhasználói folyamatokat](active-directory-b2c-reference-policies.md) a felhasználói folyamatok nevének beszerzéséhez.
