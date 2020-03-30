---
title: Hozzáférési jogkivonat kérése – Azure Active Directory B2C | Microsoft dokumentumok
description: Megtudhatja, hogyan kérhet egy hozzáférési jogkivonatot az Azure Active Directory B2C-ből.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259772"
---
# <a name="request-an-access-token-in-azure-active-directory-b2c"></a>Hozzáférési jogkivonat kérése az Azure Active Directory B2C-ben

A hozzáférési jogkivonat olyan *jogcímeket* tartalmaz, amelyek et az Azure Active Directory B2C (Azure AD B2C) az API-k hoz megadott engedélyek azonosítására használhatja. Erőforrás-kiszolgáló hívásakor egy hozzáférési jogkivonatnak jelen kell lennie a HTTP-kérelemben. A hozzáférési jogkivonat ot **access_token** jelöli az Azure AD B2C válaszai.

Ez a cikk bemutatja, hogyan kérhet egy hozzáférési jogkivonatot egy webalkalmazáshoz és egy webes API-hoz. Az Azure AD B2C tokenjeiről az [Azure Active Directory B2C tokenjeiről szóló témakörben olvashat bővebben.](tokens-overview.md)

> [!NOTE]
> **A webes API-láncokat (a nevében) az Azure AD B2C nem támogatja.** - Számos architektúra tartalmaz egy webes API-t, amelynek meg kell hívnia egy másik alsóbb rétegbeli webes API-t, mindkettő az Azure AD B2C által védett. Ez a forgatókönyv gyakori az ügyfelek, amelyek egy webes API-háttér-, amely viszont meghívja egy másik szolgáltatás. Ez a láncolt webes API-forgatókönyv az OAuth 2.0 JWT tulajdonosi hitelesítő adatok megadásával, más néven a folyamat nevében érhető el. Azonban a nevében folyamat jelenleg nincs megvalósítva az Azure AD B2C.

## <a name="prerequisites"></a>Előfeltételek

- [Hozzon létre egy felhasználói folyamatot,](tutorial-create-user-flows.md) amely lehetővé teszi a felhasználók számára, hogy regisztráljanak és jelentkezzenek be az alkalmazásba.
- Ha még nem tette meg, [adjon hozzá egy webes API-alkalmazást az Azure Active Directory B2C-bérlőhöz.](add-web-application.md)

## <a name="scopes"></a>Hatókörök

A hatókörök lekínálják a védett erőforrások engedélyeinek kezelését. Hozzáférési jogkivonat kérésekor az ügyfélalkalmazásnak meg kell adnia a kívánt engedélyeket a kérelem **hatóköri** paraméterében. Ha például az **alkalmazásazonosító URI-val** rendelkező `https://contoso.onmicrosoft.com/api`API **hatókörértékét** `read` szeretné megadni, a hatókör a . `https://contoso.onmicrosoft.com/api/read`

A hatóköröket a webes API a hatóköralapú hozzáférés-vezérlés megvalósításához használja. A webes API-k bizonyos felhasználói például rendelkezhetnek olvasási és írási hozzáféréssel is, míg mások csak olvasási hozzáféréssel. Ha ugyanabban a kérelemben több engedélyt szeretne beszerezni, több bejegyzést is hozzáadhat a kérelem egyetlen **hatókörparaméteréhez,** szóközökkel elválasztva.

A következő példa az URL-ben dekódolt hatóköröket mutatja be:

```
scope=https://contoso.onmicrosoft.com/api/read openid offline_access
```

A következő példa egy URL-címbe kódolt hatóköröket mutat be:

```
scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fapi%2Fread%20openid%20offline_access
```

Ha az ügyfélalkalmazáshoz megadottnál több hatókört kér, a hívás sikeres, ha legalább egy engedélyt megad. Az **eredményül** kapott hozzáférési jogkivonat scp-jogcíme csak a sikeresen megadott engedélyekkel van feltöltve. Az OpenID Connect szabvány több speciális hatókörértéket határoz meg. A következő hatókörök a felhasználó profiljának elérésére irányuló engedélyt jelölik:

- **openid** - azonosító jogkivonatot kér.
- **offline_access** – Frissítési jogkivonatot kér [az Auth Kódfolyamatok](authorization-code-flow.md)használatával.

Ha a `/authorize` **kérelemben** szereplő `token`response_type paraméter tartalmazza a hatókörparamétert, `openid` `offline_access` legalább egy erőforráshatókört tartalmaznia kell, kivéve, ha meg lesz adva. **scope** Ellenkező esetben `/authorize` a kérelem sikertelen lesz.

## <a name="request-a-token"></a>Token kérése

Hozzáférési jogkivonat kéréséhez engedélyezési kódra van szükség. Az alábbi példa egy kérelem `/authorize` a végpont egy engedélyezési kódot. Egyéni tartományok nem támogatottak hozzáférési jogkivonatokkal való használatra. Használja a tenant-name.onmicrosoft.com tartományt a kérelem URL-címében.

A következő példában lecseréli ezeket az értékeket:

- `<tenant-name>`- Az Azure AD B2C-bérlő neve.
- `<policy-name>`- Az egyéni házirend vagy a felhasználói folyamat neve.
- `<application-ID>`- A felhasználói folyamat támogatásához regisztrált webalkalmazás alkalmazásazonosítója.
- `<redirect-uri>`- Az ügyfélalkalmazás regisztrálásakor megadott **átirányítási URI.**

```HTTP
GET https://<tenant-name>.b2clogin.com/tfp/<tenant-name>.onmicrosoft.com/<policy-name>/oauth2/v2.0/authorize?
client_id=<application-ID>
&nonce=anyRandomValue
&redirect_uri=https://jwt.ms
&scope=https://<tenant-name>.onmicrosoft.com/api/read
&response_type=code
```

Az engedélyezési kóddal rendelkező válasznak hasonlónak kell lennie ehhez a példához:

```
https://jwt.ms/?code=eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMC...
```

Az engedélyezési kód sikeres fogadása után hozzáférési jogkivonat ot kérhet vele:

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

A következő höz hasonló választ kell látnia:

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

https://jwt.ms A visszaadott hozzáférési jogkivonat vizsgálatakor a következő példához hasonló példát kell látnia:

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

## <a name="next-steps"></a>További lépések

- A [jogkivonatok konfigurálása az Azure AD B2C-ben](configure-tokens.md)
