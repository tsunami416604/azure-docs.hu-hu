---
title: Kérelem-hozzáférési jogkivonata – Azure Active Directory B2C |} A Microsoft Docs
description: Útmutató az Azure Active Directory B2C hozzáférési jogkivonat kérése.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 5670d8b3c97cc1f9f6d149e8eadaa60d527e45f5
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59683936"
---
# <a name="request-an-access-token-in-azure-active-directory-b2c"></a>Az Azure Active Directory B2C hozzáférési jogkivonat kérése

Egy *hozzáférési jogkivonat* használható az Azure Active Directory (Azure AD) B2C segítségével azonosítja az API-k a megadott engedélyek jogcímeket tartalmaz. Erőforrás-kiszolgáló meghívásakor egy hozzáférési jogkivonatot a HTTP-kérelem jelen kell lennie. Hozzáférési jogkivonat jelölése **access_token** a Azure AD B2C-ből a válaszokat. 

Ez a cikk bemutatja, hogyan egy webalkalmazás és webes API hozzáférési jogkivonat kérése. Jogkivonatok az Azure AD B2C-vel kapcsolatos további információkért lásd: a [áttekintése az Azure Active Directory B2C a tokenek](active-directory-b2c-reference-tokens.md).

> [!NOTE]
> **Webes API-láncok (a – meghatalmazásos) nem támogatja az Azure AD B2C-t.** -Számos architektúrában szerepelnek olyan webes API-t, amelyek egy másik alsóbb rétegbeli webes API egyaránt az Azure AD B2C által védett. Ez a forgatókönyv gyakori az ügyfelekről, amelyek egy webes API-k háttérrendszer meghívja a egy másik szolgáltatás, amely. Ez a láncolatba fűzött webes API-forgatókönyv az OAuth 2.0 JWT tulajdonosi hitelesítő adatok biztosítása, más néven az On-meghatalmazásos folyamat használatával támogatható. Azonban az On-meghatalmazásos folyamat még nem implementáltuk az Azure AD B2C-ben.

## <a name="prerequisites"></a>Előfeltételek

- [Felhasználói folyamat létrehozása](tutorial-create-user-flows.md) regisztráljon, és jelentkezzen be az alkalmazást használók.
- Ha ezt még nem tette meg, [adjon hozzá egy webes API-alkalmazás az Azure Active Directory B2C-bérlő](add-web-application.md).

## <a name="scopes"></a>Hatókörök

Hatókörök adja meg a védett erőforrások kezeléséhez. Amikor egy hozzáférési jogkivonatot kér, az ügyfélalkalmazás kell adja meg a kívánt engedélyeket a a **hatókör** paraméter a kérelem. Adja meg például a **hatókör értéke** , `read` az API-hoz, amely rendelkezik a **Alkalmazásazonosító URI-t** , `https://contoso.onmicrosoft.com/api`, a hatókör lenne `https://contoso.onmicrosoft.com/api/read`.

A hatóköröket a webes API a hatóköralapú hozzáférés-vezérlés megvalósításához használja. A webes API-k bizonyos felhasználói például rendelkezhetnek olvasási és írási hozzáféréssel is, míg mások csak olvasási hozzáféréssel. A kérésben több engedély beszerzéséhez, adhat hozzá több bejegyzés az egyetlen **hatókör** paramétert a kérés, szóközzel elválasztva.

Az alábbi példa bemutatja egy URL-CÍMBEN szereplő dekódolni hatókörök:

```
scope=https://contoso.onmicrosoft.com/api/read openid offline_access
```

Az alábbi példa bemutatja az URL-kódolású hatókörök:

```
scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fapi%2Fread%20openid%20offline_access
```

Ha több hatókört, mit kap az ügyfélalkalmazás számára, mint kér, a hívás sikeres lesz, abban az esetben, ha legalább egy engedélyt kapnak. A **scp** jogcím az eredményül kapott hozzáférési jogkivonatot a rendszer csak a sikeresen megadott engedélyek kitölti. Az OpenID Connect standard számos speciális hatókör értékeinek megadása A következő hatókörök mutatják be a profil hozzáférési engedélyt:

- **openid** -kérelmek egy azonosító jogkivonat.
- **offline_access** -kérelmek, a frissítési jogkivonat használatával [hitelesítési kód folyamatok](active-directory-b2c-reference-oauth-code.md).

Ha a **response_type** paramétert egy `/authorize` kérelemben `token`, a **hatókör** paraméternek tartalmaznia kell legalább egy erőforrás-hatókör nem `openid` és `offline_access`fog kapni. Ellenkező esetben a `/authorize` kérelem meghiúsul.

## <a name="request-a-token"></a>A jogkivonat kérése

Egy hozzáférési jogkivonatot kér, az engedélyezési kódot kell. Az alábbi példában a kérést, a `/authorize` végpontja egy engedélyezési kód. Egyéni tartományok használata nem támogatott a hozzáférési jogkivonatok segítségével. A kérelem URL-CÍMÉT a bérlő-name.onmicrosoft.com tartományához használható.

A következő példában cserélje le ezeket az értékeket:

- `<tenant-name>` -A az Azure AD B2C-bérlő neve.
- `<policy-name>` – Az egyéni szabályzat vagy felhasználói folyamat nevét.
- `<application-ID>` -A webalkalmazást, amely a felhasználói folyamat támogatásához regisztrált alkalmazás azonosítóját.
- `<redirect-uri>` -A **átirányítási URI-t** ügyfélalkalmazás regisztrációja során megadott.

```
GET https://<tenant-name>.b2clogin.com/tfp/<tenant-name>.onmicrosoft.com/<policy-name>/oauth2/v2.0/authorize?
client_id=<application-ID>
&nonce=anyRandomValue
&redirect_uri=https://jwt.ms
&scope=https://tenant-name>.onmicrosoft.com/api/read
&response_type=code 
```

A válasz az engedélyezési kód ebben a példában hasonló lesz:

```
https://jwt.ms/?code=eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMC...
```

Miután sikeresen kapott engedélyezési kód, a hozzáférési jogkivonat kérése használhatja azt:

```
POST <tenant-name>.onmicrosoft.com/oauth2/v2.0/token?p=<policy-name> HTTP/1.1
Host: https://<tenant-name>.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code
&client_id=<application-ID>
&scope=https://<tenant-name>.onmicrosoft.com/api/read
&code=eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMC...
&redirect_uri=https://jwt.ms
&client_secret=2hMG2-_:y12n10vwH...
```

A következő választ hasonló kell megjelennie:

```
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrN...",
    "token_type": "Bearer",
    "not_before": 1549647431,
    "expires_in": 3600,
    "expires_on": 1549651031,
    "resource": "f2a76e08-93f2-4350-833c-965c02483b11",
    "profile_info": "eyJ2ZXIiOiIxLjAiLCJ0aWQiOiJjNjRhNGY3ZC0zMDkxLTRjNzMtYTcyMi1hM2YwNjk0Z..."
}
```

Használata esetén https://jwt.ms a hozzáférési jogkivonat által eredményül adott vizsgálatához kell megjelennie a következő példához hasonló:

```
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dl..."
}.{
  "iss": "https://contoso0926tenant.b2clogin.com/c64a4f7d-3091-4c73-a7.../v2.0/",
  "exp": 1549651031,
  "nbf": 1549647431,
  "aud": "f2a76e08-93f2-4350-833c-965...",
  "oid": "1558f87f-452b-4757-bcd1-883...",
  "sub": "1558f87f-452b-4757-bcd1-883...",
  "name": "David",
  "tfp": "B2C_1_signupsignin1",
  "nonce": "anyRandomValue",
  "scp": "read",
  "azp": "38307aee-303c-4fff-8087-d8d2...",
  "ver": "1.0",
  "iat": 1549647431
}.[Signature]
```

## <a name="next-steps"></a>További lépések

- Hogyan [jogkivonatok konfigurálása az Azure AD B2C-ben](configure-tokens.md)
