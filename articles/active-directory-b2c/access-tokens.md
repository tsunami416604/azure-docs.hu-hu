---
title: Hozzáférési jogkivonat kérése – Azure Active Directory B2C | Microsoft Docs
description: Megtudhatja, hogyan kérhet hozzáférési jogkivonatot a Azure Active Directory B2Cból.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 8358d3378ea892ebeef653bcb51243c9f1aa0b8d
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79259772"
---
# <a name="request-an-access-token-in-azure-active-directory-b2c"></a>Hozzáférési jogkivonat igénylése Azure Active Directory B2C

A *hozzáférési jogkivonatok* olyan jogcímeket tartalmaznak, amelyeket Azure Active Directory B2C (Azure ad B2C) az API-khoz megadott engedélyek azonosítására használhat. Egy erőforrás-kiszolgáló meghívásakor a hozzáférési tokennek jelen kell lennie a HTTP-kérelemben. A hozzáférési jogkivonatot a Azure AD B2C válaszai **access_tokenként** jelöli meg.

Ez a cikk bemutatja, hogyan kérhet hozzáférési jogkivonatot egy webalkalmazáshoz és webes API-hoz. A Azure AD B2C jogkivonatokkal kapcsolatos további információkért tekintse meg a [Azure Active Directory B2C jogkivonatok áttekintése](tokens-overview.md)című témakört.

> [!NOTE]
> **Az Azure AD B2C nem támogatja a webes API-Láncok használatát.** – Számos architektúra tartalmaz egy webes API-t, amelynek egy másik, Azure AD B2C által védett webes API-t kell meghívnia. Ez a forgatókönyv gyakori a webes API-háttérrel rendelkező ügyfeleknél, ami viszont egy másik szolgáltatást hív meg. Ez a láncolt webes API-forgatókönyv a OAuth 2,0 JWT tulajdonosi hitelesítő adatok megadásával, más néven a meghatalmazott folyamattal támogatott. A folyamatban lévő folyamat azonban jelenleg nem Azure AD B2Cban van implementálva.

## <a name="prerequisites"></a>Előfeltételek

- [Hozzon létre egy felhasználói folyamatot](tutorial-create-user-flows.md) , amely lehetővé teszi a felhasználók számára az alkalmazásba való regisztrációt és bejelentkezést.
- Ha még nem tette meg, [adjon hozzá egy webes API-alkalmazást a Azure Active Directory B2C-bérlőhöz](add-web-application.md).

## <a name="scopes"></a>Hatókörök

A hatókörök lehetővé teszik a védett erőforrásokra vonatkozó engedélyek kezelését. Hozzáférési jogkivonat kérése esetén az ügyfélalkalmazás a kérelem **hatókör** -paraméterében meg kell adnia a kívánt engedélyeket. Ha például meg szeretné adni a `read` **hatókörének értékét** azon API számára, amelynek `https://contoso.onmicrosoft.com/api`az **alkalmazás-azonosító URI-ja** , a hatókör `https://contoso.onmicrosoft.com/api/read`.

A hatóköröket a webes API a hatóköralapú hozzáférés-vezérlés megvalósításához használja. A webes API-k bizonyos felhasználói például rendelkezhetnek olvasási és írási hozzáféréssel is, míg mások csak olvasási hozzáféréssel. Ha több engedélyt szeretne beszerezni ugyanabban a kérésben, több bejegyzést is hozzáadhat a kérelem egyetlen **hatókörű** paraméteréhez, szóközzel elválasztva.

Az alábbi példa egy URL-címben dekódolt hatóköröket mutatja be:

```
scope=https://contoso.onmicrosoft.com/api/read openid offline_access
```

Az alábbi példában egy URL-címben kódolt hatókörök láthatók:

```
scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fapi%2Fread%20openid%20offline_access
```

Ha több hatókört kér az ügyfélalkalmazás számára, akkor a hívás sikeres lesz, ha legalább egy engedélyt megadtak. Az eredményül kapott hozzáférési jogkivonatban lévő **SCP** -jogcím csak a sikeresen megadott engedélyekkel van feltöltve. Az OpenID Connect standard több speciális hatókör-értéket ad meg. A következő hatókörök a felhasználó profiljához való hozzáférés engedélyét jelentik:

- **OpenID** – azonosító tokent kér.
- **offline_access** – frissítési tokent kér az [Auth kód folyamatainak](authorization-code-flow.md)használatával.

Ha egy `/authorize`-kérelemben szereplő **response_type** paraméter `token`tartalmaz, a **hatókör** -paraméternek tartalmaznia kell legalább egy olyan erőforrás-hatókört, amely nem `openid`, és `offline_access`, amelyet meg fog adni. Ellenkező esetben a `/authorize` kérelem meghiúsul.

## <a name="request-a-token"></a>Jogkivonat igénylése

Hozzáférési jogkivonat igényléséhez egy engedélyezési kódnak kell lennie. Az alábbi példa egy engedélyezési kód `/authorize`-végpontra irányuló kérést mutat be. A hozzáférési jogkivonatokkal való használat nem támogatja az egyéni tartományokat. Használja a tenant-name.onmicrosoft.com tartományt a kérelem URL-címében.

Az alábbi példában a következő értékeket kell lecserélnie:

- `<tenant-name>` – a Azure AD B2C bérlő neve.
- `<policy-name>` – az egyéni házirend vagy felhasználói folyamat neve.
- `<application-ID>` – a felhasználói folyamat támogatásához regisztrált webalkalmazás alkalmazás-azonosítója.
- `<redirect-uri>` – az ügyfélalkalmazás regisztrálásakor megadott **átirányítási URI** .

```HTTP
GET https://<tenant-name>.b2clogin.com/tfp/<tenant-name>.onmicrosoft.com/<policy-name>/oauth2/v2.0/authorize?
client_id=<application-ID>
&nonce=anyRandomValue
&redirect_uri=https://jwt.ms
&scope=https://<tenant-name>.onmicrosoft.com/api/read
&response_type=code
```

Az engedélyezési kóddal kapcsolatos válasznak a következő példához hasonlónak kell lennie:

```
https://jwt.ms/?code=eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMC...
```

Az engedélyezési kód sikeres kézhezvételét követően a segítségével hozzáférési tokent igényelhet:

```HTTP
POST <tenant-name>.onmicrosoft.com/oauth2/v2.0/token?p=<policy-name> HTTP/1.1
Host: <tenant-name>.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code
&client_id=<application-ID>
&scope=https://<tenant-name>.onmicrosoft.com/api/read
&code=eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMC...
&redirect_uri=https://jwt.ms
&client_secret=2hMG2-_:y12n10vwH...
```

A következőhöz hasonló válasznak kell megjelennie:

```JSON
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

Ha a https://jwt.ms használatával vizsgálja meg a visszaadott hozzáférési tokent, akkor az alábbi példához hasonlóan kell megjelennie:

```JSON
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

## <a name="next-steps"></a>Következő lépések

- Tudnivalók a [jogkivonatok konfigurálásáról a Azure ad B2C](configure-tokens.md)
