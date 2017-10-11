---
title: "Hitelesítésikód-folyamata – az Azure AD B2C |} Microsoft Docs"
description: "Ismerje meg, hogyan hozhat létre a webalkalmazásokkal az Azure AD B2C és az OpenID Connect hitelesítési protokoll használatával."
services: active-directory-b2c
documentationcenter: 
author: saeedakhter-msft
manager: krassk
editor: parakhj
ms.assetid: c371aaab-813a-4317-97df-b62e2f53d865
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2017
ms.author: saeedakhter-msft
ms.openlocfilehash: dfc4f2e84704307ccbea6141c0dbc8d089733b22
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="azure-active-directory-b2c-oauth-20-authorization-code-flow"></a>Az Azure Active Directory B2C: OAuth 2.0 hitelesítésikód-folyamata
Az OAuth 2.0 hitelesítésengedélyezési kódot az eszközön telepített alkalmazások segítségével védett erőforrások, például webes API-k eléréséhez. Az Azure Active Directory B2C segítségével OAuth 2.0 (az Azure AD B2C) végrehajtásával, adhat hozzá a regisztráció, bejelentkezés és egyéb identitás-felügyeleti feladatokat a mobil- és asztali alkalmazásokhoz való. Ez a cikk nyelvfüggetlen. A cikk azt ismerteti, hogyan küldésére és fogadására a HTTP-üzenetek bármely nyílt forráskódú kódtárai használata nélkül.

<!-- TODO: Need link to libraries -->

Az OAuth 2.0 hitelesítésikód-folyamata ismertetett [az OAuth 2.0-s szabvány 4.1 szakasz](http://tools.ietf.org/html/rfc6749). Használhatja a hitelesítéshez és engedélyezéshez a legtöbb alkalmazás típusok, beleértve a [webalkalmazások](active-directory-b2c-apps.md#web-apps) és [natívan telepített alkalmazásokat](active-directory-b2c-apps.md#mobile-and-native-apps). Az OAuth 2.0 hitelesítésikód-folyamata segítségével biztonságosan megszerzése *hozzáférési jogkivonatok* az alkalmazásokat, amelyek használhatók által védett erőforrások eléréséhez az [engedélyezési server](active-directory-b2c-reference-protocols.md#the-basics).

Ez a cikk összpontosít a **nyilvános ügyfelek** OAuth 2.0 hitelesítésikód-folyamata. Egy nyilvános ügyfél bármilyen ügyfélalkalmazást, amely nem lehet biztonságosan egységének fenntartására szolgáló módszert a titkos jelszó megbízható. Ez magában foglalja a mobilalkalmazások, az asztali alkalmazások és a tulajdonképpen bármely olyan alkalmazás a eszközön fut, és meg kell kapnia a hozzáférési jogkivonatok. 

> [!NOTE]
> Az Azure AD B2C segítségével adhat hozzá egy webalkalmazást az Identitáskezelés, [OpenID Connect](active-directory-b2c-reference-oidc.md) OAuth 2.0 helyett.

Az Azure AD B2C bővíti a szabványos OAuth 2.0 tehet még többet, mint az egyszerű hitelesítés és engedélyezés zajlik. Okozna a [házirend paraméter](active-directory-b2c-reference-policies.md). A beépített házirendek segítségével OAuth 2.0 felhasználói élményt hozzáadása az alkalmazáshoz, például a regisztráció, bejelentkezés és profilok kezelése. Ez a cikk azt mutatja be OAuth 2.0-s és a házirendek használatával valósít meg minden olyan ezeket a szolgáltatásokat a natív alkalmazások. Azt is bemutatják a hozzáférési jogkivonatok lekérésére, webes API-k eléréséhez.

A minta Azure AD B2C-címtár használjuk a ebben a cikkben példa HTTP-kérelmeket, **fabrikamb2c.onmicrosoft.com**. Is használhatja a mintaalkalmazás és a házirendeket. Megpróbálhatja a kérelmek saját kezűleg ezen értékek használatával, vagy azokat lecserélheti a saját értékeit.
Megtudhatja, hogyan [beolvasása a saját Azure AD B2C directory, az alkalmazás és a házirendek](#use-your-own-azure-ad-b2c-directory).

## <a name="1-get-an-authorization-code"></a>1. Az engedélyezési kód beolvasása
A hitelesítésikód-folyamata kezdődik irányítja a felhasználót, hogy az ügyfél a `/authorize` végpont. Ez része az interaktív a folyamatot, ha a felhasználó végrehajtja a műveletet. A kérelem, az ügyfél azt jelzi, az a `scope` paraméter szerzi be a felhasználó szükséges engedélyeket. Az a `p` paraméter azt jelzi, a házirend végrehajtásához. Az alábbi három példák (olvashatóság érdekében sortöréssel) minden használja egy másik házirendet.

### <a name="use-a-sign-in-policy"></a>A bejelentkezési házirend
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_sign_in
```

### <a name="use-a-sign-up-policy"></a>A regisztrációs szabályzatban használata
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_sign_up
```

### <a name="use-an-edit-profile-policy"></a>Egy házirend-profil szerkesztése
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_edit_profile
```

| Paraméter | Kötelező megadni? | Leírás |
| --- | --- | --- |
| client_id |Szükséges |Az alkalmazást a hozzárendelt Alkalmazásazonosító a [Azure-portálon](https://portal.azure.com). |
| response_type |Szükséges |A válasz típusa, amely tartalmazza `code` a a hitelesítésikód-folyamata. |
| redirect_uri |Szükséges |Az átirányítási URI-t, az alkalmazás, ahol hitelesítési válaszokat küldött és az alkalmazás által fogadott. Az pontosan egyeznie kell az átirányítási URI-k, a portál regisztrált azzal a különbséggel, hogy az URL-kódolású kell lennie. |
| Hatókör |Szükséges |Hatókörök szóközökkel elválasztott listája. Egy hatókör érték azt jelzi, az Azure Active Directory (Azure AD) mindkét kérnek engedély. Használja az ügyfél-azonosító, a hatókör, az azt jelzi, hogy az alkalmazás egy hozzáférési jogkivonatot, amely használható a saját szolgáltatás vagy webes API-t kell-e, képviseli az azonos ügyfél-azonosítót.  A `offline_access` hatókör azt jelzi, hogy kell-e az alkalmazást egy frissítési jogkivonat hosszú élettartamú erőforrások elérése érdekében. Is használhatja a `openid` egy azonosító jogkivonatot kérhet az Azure AD B2C hatókör. |
| response_mode |Ajánlott |A módszer, amelynek használatával az eredményül kapott engedélyezési kód küldi vissza az alkalmazást. Ez lehet a `query`, `form_post`, vagy `fragment`. |
| state |Ajánlott |A token válaszként visszaadott a kérelemben szereplő érték. Bármely, a használni kívánt tartalmat karakterlánc lehet. Általában egy véletlenszerűen generált egyedi érték használata esetén webhelyközi kérések hamisításának megakadályozása támadások megelőzése érdekében. Az állapot is kódolásához használatos a felhasználói állapot az alkalmazás információkat előtt a hitelesítési kérelmet. Például az volt a felhasználó oldalon, vagy a házirendet, amely a végrehajtása. |
| P |Szükséges |A házirendet, amely végrehajtja a rendszer. Egy Azure AD B2C-címtárban létrehozott házirend nevét. A házirend nevének értékét kell kezdődnie **b2c\_1\_**. A házirendekkel kapcsolatos további információkért lásd: [beépített házirendek az Azure AD B2C](active-directory-b2c-reference-policies.md). |
| parancssor |Optional |A típus a felhasználói beavatkozás szükséges. Az egyetlen érvényes érték jelenleg `login`, amely előírja, hogy a felhasználó megadja hitelesítő adataikkal, hogy kérésre. Egyszeri bejelentkezés nem lépnek érvénybe. |

Ezen a ponton a felhasználónak kapcsolatba a házirend-munkafolyamat végrehajtásához. Ez lehet, hogy magában a írja be a felhasználónevét és jelszavát, felhasználó bejelentkezik egy közösségi identitás regisztrál a könyvtár, vagy több lépésből áll. Felhasználói műveletek attól függ, hogyan van definiálva a házirendet.

Miután a felhasználó teljesítette a szabályzat, az Azure AD az alkalmazásban használt értéke a választ ad `redirect_uri`. A megadott metódust használja a `response_mode` paraméter. A válasz megegyezik pontosan az egyes felhasználói művelet forgatókönyv, függetlenül a házirend, hogy végre lett hajtva.

A sikeres válasz használó `response_mode=query` dolgozunk:

```
GET urn:ietf:wg:oauth:2.0:oob?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...        // the authorization_code, truncated
&state=arbitrary_data_you_can_receive_in_the_response                // the value provided in the request
```

| Paraméter | Leírás |
| --- | --- |
| Kód |Az engedélyezési kód, amely az alkalmazás kéri. Az alkalmazás az engedélyezési kód segítségével olyan hozzáférési jogkivonatot cél erőforrás igényelhetnek. Hitelesítési kódok nagyon rövid élettartamú. Ezek általában körülbelül 10 perc múlva lejár. |
| state |Tekintse meg a tábla az előző szakaszban leírt teljes leírását. Ha egy `state` paraméter szerepel a kérést, ugyanazt az értéket meg kell jelennie a válaszban. Az alkalmazás győződjön meg arról, hogy a `state` a kérelem és a válaszban szereplő értékek azonosak. |

Hibaválaszok is küldhetők az átirányítási URI-t, hogy az alkalmazás képes kezelni őket megfelelően:

```
GET urn:ietf:wg:oauth:2.0:oob?
error=access_denied
&error_description=The+user+has+cancelled+entering+self-asserted+information
&state=arbitrary_data_you_can_receive_in_the_response
```

| Paraméter | Leírás |
| --- | --- |
| error |Egy hiba kód karakterlánc jelentkező hibákról típusú besorolására használható. A karakterlánc hibák reagálni is használja. |
| error_description |Egy adott hibaüzenet, melyek segíthetnek hitelesítési hiba okának azonosításához. |
| state |Lásd az előző táblázatban a teljes leírását. Ha egy `state` paraméter szerepel a kérést, ugyanazt az értéket meg kell jelennie a válaszban. Az alkalmazás győződjön meg arról, hogy a `state` a kérelem és a válaszban szereplő értékek azonosak. |

## <a name="2-get-a-token"></a>2. A jogkivonat beolvasása
Most, hogy az engedélyezési kód jut hozzá, akkor is beválthatja a `code` úgy, hogy a POST kérést küld a kívánt erőforrás jogkivonat a `/token` végpont. Az Azure AD B2C-ben a jogkivonatot kérhet csak erőforrás az alkalmazás saját webes API-t. Az a magához jogkivonat kérésével használt egyezmény, hogy az alkalmazás ügyfél-Azonosítóját használja a hatóköreként:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob

```

| Paraméter | Kötelező megadni? | Leírás |
| --- | --- | --- |
| P |Szükséges |A házirendet, amely szerepel az engedélyezési kód megszerzésére. A kérelem nem használhatja másik szabályt. Vegye figyelembe, hogy hozzáadta a paraméter a *lekérdezési karakterlánc*, és nem a FELADÁS egy vagy több szervezet. |
| client_id |Szükséges |Az alkalmazást a hozzárendelt Alkalmazásazonosító a [Azure-portálon](https://portal.azure.com). |
| grant_type |Szükséges |A támogatás típusát. A hitelesítésikód-folyamata, a megadott típus kell `authorization_code`. |
| Hatókör |Ajánlott |Hatókörök szóközökkel elválasztott listája. Egy hatókör érték azt jelzi, az Azure AD mindkét kérnek engedély. Használja az ügyfél-azonosító, a hatókör, az azt jelzi, hogy az alkalmazás egy hozzáférési jogkivonatot, amely használható a saját szolgáltatás vagy webes API-t kell-e, képviseli az azonos ügyfél-azonosítót.  A `offline_access` hatókör azt jelzi, hogy kell-e az alkalmazást egy frissítési jogkivonat hosszú élettartamú erőforrások elérése érdekében.  Is használhatja a `openid` egy azonosító jogkivonatot kérhet az Azure AD B2C hatókör. |
| Kód |Szükséges |A folyamat első szakasza beszerzett engedélyezési kódot. |
| redirect_uri |Szükséges |Az átirányítási URI-t, az alkalmazás, ahol kapott engedélyezési kódot. |

A sikeres token válasz így néz ki:

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
| not_before |Az az idő, amelynél a jogkivonat érvényes, időbeli kortartományon. |
| token_type |A jogkivonat típusa érték. A csak az Azure AD támogató típus tulajdonosi. |
| access_token |Az aláírt JSON webes jogkivonat (JWT) az Ön által kért. |
| Hatókör |A hatókörök, amely a token érvényes. Is használhatja szerepet, amelyet a gyorsítótár jogkivonatok későbbi használatra. |
| expires_in |Mennyi ideig, amely a token érvényes (másodpercben). |
| refresh_token |Az OAuth 2.0-s frissítési jogkivonat. Az alkalmazás a jogkivonat segítségével szerezzen be további jogkivonatok a jelenlegi jogkivonat lejárata után is. Frissítési jogkivonatok hosszú élettartamú. Erőforrásokhoz való hozzáférés megőrzése huzamosabb ideig használhatja őket. További információkért lásd: a [Azure AD B2C-jogkivonatok referenciájából](active-directory-b2c-reference-tokens.md). |

Hibaválaszok néznek ki:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Paraméter | Leírás |
| --- | --- |
| error |Egy hiba kód karakterlánc jelentkező hibákról típusú besorolására használható. A karakterlánc hibák reagálni is használja. |
| error_description |Egy adott hibaüzenet, melyek segíthetnek hitelesítési hiba okának azonosításához. |

## <a name="3-use-the-token"></a>3. A jogkivonat
Most, hogy olyan hozzáférési jogkivonatot már sikeresen szerezte be, használhatja a token kérelmek számára a háttérben futó webes API-khoz azzal, hogy belefoglalja azt a a `Authorization` fejléc:

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="4-refresh-the-token"></a>4. A token frissítése
Hozzáférési jogkivonatok és azonosító-jogkivonatokat, amelyek rövid élettartamú. Miután járnak, frissítenie kell a további erőforrások eléréséhez. Ehhez az szükséges, küldje el a POST kérelmet egy másik a `/token` végpont. Most, adja meg a `refresh_token` ahelyett, hogy a `code`:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Paraméter | Kötelező megadni? | Leírás |
| --- | --- | --- |
| P |Szükséges |A házirendet, amely az eredeti frissítési jogkivonat használatával. A kérelem nem használhatja másik szabályt. Vegye figyelembe, hogy hozzáadta a paraméter a *lekérdezési karakterlánc*, és nem a FELADÁS egy vagy több szervezet. |
| client_id |Ajánlott |Az alkalmazást a hozzárendelt Alkalmazásazonosító a [Azure-portálon](https://portal.azure.com). |
| grant_type |Szükséges |A támogatás típusát. A hitelesítésikód-folyamata ezen szakasza az engedélytípus kell `refresh_token`. |
| Hatókör |Ajánlott |Hatókörök szóközökkel elválasztott listája. Egy hatókör érték azt jelzi, az Azure AD mindkét kérnek engedély. Használja az ügyfél-azonosító, a hatókör, az azt jelzi, hogy az alkalmazás egy hozzáférési jogkivonatot, amely használható a saját szolgáltatás vagy webes API-t kell-e, képviseli az azonos ügyfél-azonosítót.  A `offline_access` hatókör azt jelzi, hogy az alkalmazás egy frissítési jogkivonat kell hosszú élettartamú erőforrások elérése érdekében.  Is használhatja a `openid` egy azonosító jogkivonatot kérhet az Azure AD B2C hatókör. |
| redirect_uri |Optional |Az átirányítási URI-t, az alkalmazás, ahol kapott engedélyezési kódot. |
| refresh_token |Szükséges |Az eredeti frissítési jogkivonat a folyamat második szakasza beszerzett. |

A sikeres token válasz így néz ki:

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
| not_before |Az az idő, amelynél a jogkivonat érvényes, időbeli kortartományon. |
| token_type |A jogkivonat típusa érték. A csak az Azure AD támogató típus tulajdonosi. |
| access_token |Az aláírt jwt-t az Ön által kért. |
| Hatókör |A hatókörök, amely a token érvényes. Is használhatja a szerepet, amelyet a gyorsítótár jogkivonatok későbbi használatra. |
| expires_in |Mennyi ideig, amely a token érvényes (másodpercben). |
| refresh_token |Az OAuth 2.0-s frissítési jogkivonat. Az alkalmazás a jogkivonat segítségével szerezzen be további jogkivonatok a jelenlegi jogkivonat lejárata után is. Frissítési jogkivonatok hosszú élettartamú, és erőforrásokhoz való hozzáférés megőrzése huzamosabb ideig használható. További információkért lásd: a [Azure AD B2C-jogkivonatok referenciájából](active-directory-b2c-reference-tokens.md). |

Hibaválaszok néznek ki:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Paraméter | Leírás |
| --- | --- |
| error |Egy hiba kód karakterlánc típusú előforduló hibák besorolására használható. A karakterlánc hibák reagálni is használja. |
| error_description |Egy adott hibaüzenet, melyek segíthetnek hitelesítési hiba okának azonosításához. |

## <a name="use-your-own-azure-ad-b2c-directory"></a>Használja a saját Azure AD B2C-címtár
Próbálja meg ezek a kérelmek saját magának, végezze el a következő lépéseket. Cserélje a példaértékeket a saját értékekkel cikkben használtuk.

1. [Az Azure AD B2C címtár létrehozása](active-directory-b2c-get-started.md). Annak a könyvtárnak a nevét használja a kérelmeket.
2. [Hozzon létre egy alkalmazást](active-directory-b2c-app-registration.md) a Azonosítóját és egy átirányítási URI-t. Egy natív ügyfél tartalmazza az alkalmazásban.
3. [A házirendek létrehozása](active-directory-b2c-reference-policies.md) beszerzése a házirend nevét.

