---
title: Microsoft identity platform és OAuth2.0 A folyamat nevében | Azure
description: Ez a cikk azt ismerteti, hogy miként valósítható meg a HTTP-üzenetek a szolgáltatás hitelesítéséhez az OAuth2.0 a folyamat nevében történő megvalósításához.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 1/3/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 7a91f61302b5944e69f71c3cfee2f41cd87b809f
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309381"
---
# <a name="microsoft-identity-platform-and-oauth-20-on-behalf-of-flow"></a>Microsoft identity platform és OAuth 2.0 A folyamat nevében


Az OAuth 2.0 A folyamat nevében (OBO) szolgál a használati eset, amikor egy alkalmazás meghívja a szolgáltatás/web API-t, amely viszont meg kell hívnia egy másik szolgáltatás/webes API-t. Az ötlet a delegált felhasználói identitás és engedélyek propagálása a kérelemláncon keresztül. Ahhoz, hogy a középső rétegbeli szolgáltatás hitelesített kérelmeket küldjön az alsóbb rétegbeli szolgáltatásnak, a felhasználó nevében biztosítania kell egy hozzáférési jogkivonatot a Microsoft identitásplatformról.

Ez a cikk azt ismerteti, hogy miként programozhat közvetlenül az alkalmazásban lévő protokoll ellen.  Ha lehetséges, azt javasoljuk, hogy a támogatott Microsoft Authentication Libraries (MSAL) helyett [a jogkivonatok beszerzéséhez és a biztonságos webes API-k hívásához](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)használja.  Is vessen egy pillantást a [minta alkalmazások at MSAL](sample-v2-code.md).

> [!NOTE]
>
> - A Microsoft identity platform végpontja nem támogatja az összes forgatókönyvet és szolgáltatást. Annak megállapításához, hogy a Microsoft identity platform végpontját kell-e használnia, olvassa el a [Microsoft identity platform korlátait.](active-directory-v2-limitations.md) 
> - 2018 májusátként néhány implicit `id_token` áramlásszármazott nem használható obo-folyamathoz. Az egyoldalas alkalmazásoknak (SpA-knak) **hozzáférési** jogkivonatot kell átadniuk egy középső rétegbeli bizalmas ügyfélnek, hogy helyette obo-folyamatokat hajtsanak végre. Ha többet szeretne tudni arról, hogy mely ügyfelek végezhetnek OBO-hívásokat, olvassa el a [korlátozásokat.](#client-limitations)

## <a name="protocol-diagram"></a>Protokolldiagram

Tegyük fel, hogy a felhasználó hitelesített egy alkalmazáson az [OAuth 2.0 engedélyezési kód engedélyezési folyamat](v2-oauth2-auth-code-flow.md) vagy más bejelentkezési folyamat használatával. Ezen a ponton az alkalmazás rendelkezik egy hozzáférési jogkivonatot *api A* (A jogkivonat) a felhasználó jogcímek és a hozzájárulás a középső rétegbeli webes API (API A) eléréséhez. Most az API A kell, hogy egy hitelesített kérelmet az alsóbb rétegbeli webes API (Api B).

Az alábbi lépések alkotják az OBO-folyamatot, és a következő ábra segítségével kerülnek magyarázatra.

![Az OAuth2.0 a folyamat nevében](./media/v2-oauth2-on-behalf-of-flow/protocols-oauth-on-behalf-of-flow.png)

1. Az ügyfélalkalmazás az A jogkivonattal (API `aud` A jogcímekkel) az API A-ra irányuló kérelmet küld.
1. Az A API hitelesíti magát a Microsoft identity platform tokenkiállítási végpontján, és jogkivonatot kér a B API eléréséhez.
1. A Microsoft identity platform token kiállítási végpont érvényesíti az API A hitelesítő adatait az A jogkivonattal együtt, és kiadja a b API (B token) hozzáférési jogkivonatát az API A számára.
1. A B jogkivonatot az API A állítja be a B API-nak küldött kérelem engedélyezési fejlécében.
1. A biztonságos erőforrásból származó adatokat az API B adja vissza az A API-nak, és onnan az ügyfélnek.

> [!NOTE]
> Ebben a forgatókönyvben a középső rétegbeli szolgáltatás nem rendelkezik felhasználói beavatkozással a felhasználó beleegyezését az alsóbb rétegbeli API eléréséhez. Ezért a lehetőséget, hogy hozzáférést biztosítson az alsóbb rétegbeli API-t a hitelesítés során a hozzájárulási lépés részeként jelenik meg. Ha tudni szeretné, hogyan állíthatja be ezt az alkalmazáshoz, olvassa el [A középső rétegbeli alkalmazás hozzájárulásának megszerzése](#gaining-consent-for-the-middle-tier-application)című témakört.

## <a name="service-to-service-access-token-request"></a>Szolgáltatás-szolgáltatás hozzáférési jogkivonat-kérelem

Hozzáférési jogkivonat kéréséhez készítsen HTTP-postát a bérlő-specifikus Microsoft-identitásplatform-token végpontjára a következő paraméterekkel.

```
https://login.microsoftonline.com/<tenant>/oauth2/v2.0/token
```

Két eset attól függően, hogy az ügyfélalkalmazás úgy dönt, hogy egy közös titkos kulcsot vagy egy tanúsítványt.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Első eset: Hozzáférési jogkivonat-kérelem megosztott titkos titokkal

Megosztott titkos kulcs használata esetén a szolgáltatás-szolgáltatás hozzáférési jogkivonat-kérelem a következő paramétereket tartalmazza:

| Paraméter |  | Leírás |
| --- | --- | --- |
| `grant_type` | Kötelező | A tokenkérelem típusa. JWT-t használó kérelmek esetén az `urn:ietf:params:oauth:grant-type:jwt-bearer`értéknek . |
| `client_id` | Kötelező | Az alkalmazás (ügyfél) azonosítója, amely [az Azure Portal – App regisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) lap az alkalmazáshoz rendelt. |
| `client_secret` | Kötelező | Az alkalmazáshoz az Azure Portalon létrehozott ügyféltitok – Alkalmazásregisztrációk lap. |
| `assertion` | Kötelező | A kérelemben használt jogkivonat értéke.  Ennek a jogkivonatnak rendelkeznie kell az OBO-kérelmet `client-id` készítő alkalmazás közönségével (a mezőáltal jelölt alkalmazással). |
| `scope` | Kötelező | A jogkivonat-kérelem hatóköreinek szóközt választott listája. További információt a hatókörök című [témakörben talál.](v2-permissions-and-consent.md) |
| `requested_token_use` | Kötelező | Itt adható meg, hogyan kell a kérelmet feldolgozni. Az OBO-folyamatban az értéket a `on_behalf_of`értékértékre kell állítani. |

#### <a name="example"></a>Példa

A következő HTTP POST hozzáférési jogkivonatot kér, és frissíti a `user.read` https://graph.microsoft.com webes API hatókörét.

```
//line breaks for legibility only

POST /oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn:ietf:params:oauth:grant-type:jwt-bearer
&client_id=2846f71b-a7a4-4987-bab3-760035b2f389
&client_secret=BYyVnAt56JpLwUcyo47XODd
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiIyODQ2ZjcxYi1hN2E0LTQ5ODctYmFiMy03NjAwMzViMmYzODkiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE0OTM5MjA5MTYsIm5iZiI6MTQ5MzkyMDkxNiwiZXhwIjoxNDkzOTI0ODE2LCJhaW8iOiJBU1FBMi84REFBQUFnZm8vNk9CR0NaaFV2NjJ6MFFYSEZKR0VVYUIwRUlIV3NhcGducndMMnVrPSIsIm5hbWUiOiJOYXZ5YSBDYW51bWFsbGEiLCJvaWQiOiJkNWU5NzljNy0zZDJkLTQyYWYtOGYzMC03MjdkZDRjMmQzODMiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJuYWNhbnVtYUBtaWNyb3NvZnQuY29tIiwic3ViIjoiZ1Q5a1FMN2hXRUpUUGg1OWJlX1l5dVZNRDFOTEdiREJFWFRhbEQzU3FZYyIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInV0aSI6IjN5U3F4UHJweUVPd0ZsTWFFMU1PQUEiLCJ2ZXIiOiIyLjAifQ.TPPJSvpNCSCyUeIiKQoLMixN1-M-Y5U0QxtxVkpepjyoWNG0i49YFAJC6ADdCs5nJXr6f-ozIRuaiPzy29yRUOdSz_8KqG42luCyC1c951HyeDgqUJSz91Ku150D9kP5B9-2R-jgCerD_VVuxXUdkuPFEl3VEADC_1qkGBiIg0AyLLbz7DTMp5DvmbC09DhrQQiouHQGFSk2TPmksqHm3-b3RgeNM1rJmpLThis2ZWBEIPx662pjxL6NJDmV08cPVIcGX4KkFo54Z3rfwiYg4YssiUc4w-w3NJUBQhnzfTl4_Mtq2d7cVlul9uDzras091vFy32tWkrpa970UvdVfQ
&scope=https://graph.microsoft.com/user.read+offline_access
&requested_token_use=on_behalf_of
```

### <a name="second-case-access-token-request-with-a-certificate"></a>Második eset: Hozzáférési jogkivonat-kérelem tanúsítvánnyal

A szolgáltatás-szolgáltatás hozzáférési jogkivonat-kérelem tanúsítvánnyal a következő paramétereket tartalmazza:

| Paraméter |  | Leírás |
| --- | --- | --- |
| `grant_type` | Kötelező | A jogkivonat-kérelem típusa. JWT-t használó kérelmek esetén az `urn:ietf:params:oauth:grant-type:jwt-bearer`értéknek . |
| `client_id` | Kötelező |  Az alkalmazás (ügyfél) azonosítója, amely [az Azure Portal – App regisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) lap az alkalmazáshoz rendelt. |
| `client_assertion_type` | Kötelező | Az értéknek `urn:ietf:params:oauth:client-assertion-type:jwt-bearer`. |
| `client_assertion` | Kötelező | Egy állítás (egy JSON-webtoken), amelyet létre kell hoznia, és alá kell írnia az alkalmazás hitelesítő adataiként regisztrált tanúsítvánnyal. A tanúsítvány regisztrálásáról és a feltétel formátumáról a tanúsítvány hitelesítő adatai című [témakörben](active-directory-certificate-credentials.md)olvashat. |
| `assertion` | Kötelező | A kérelemben használt jogkivonat értéke. |
| `requested_token_use` | Kötelező | Itt adható meg, hogyan kell a kérelmet feldolgozni. Az OBO-folyamatban az értéket a `on_behalf_of`értékértékre kell állítani. |
| `scope` | Kötelező | A jogkivonat-kérelem hatóköreinek térelválasztott listája. További információt a hatókörök című [témakörben talál.](v2-permissions-and-consent.md)|

Figyelje meg, hogy a paraméterek majdnem ugyanazok, mint a `client_secret` kérelem megosztott titok, azzal a `client_assertion_type` `client_assertion`kivételével, hogy a paraméter helyébe két paraméter: a és .

#### <a name="example"></a>Példa

A következő HTTP POST kér `user.read` egy https://graph.microsoft.com hozzáférési jogkivonatot a webes API hatókörét egy tanúsítvánnyal.

```
// line breaks for legibility only

POST /oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2Rkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tLzE5MjNmODYyLWU2ZGMtNDFhMy04MWRhLTgwMmJhZTAwYWY2ZCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzI2MDM5Y2NlLTQ4OWQtNDAwMi04MjkzLTViMGM1MTM0ZWFjYi8iLCJpYXQiOjE0OTM0MjMxNTIsIm5iZiI6MTQ5MzQyMzE1MiwiZXhwIjoxNDkzNDY2NjUyLCJhY3IiOiIxIiwiYWlvIjoiWTJaZ1lCRFF2aTlVZEc0LzM0L3dpQndqbjhYeVp4YmR1TFhmVE1QeG8yYlN2elgreHBVQSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiJiMzE1MDA3OS03YmViLTQxN2YtYTA2YS0zZmRjNzhjMzI1NDUiLCJhcHBpZGFjciI6IjAiLCJlX2V4cCI6MzAyNDAwLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidmVyIjoiMS4wIn0.R-Ke-XO7lK0r5uLwxB8g5CrcPAwRln5SccJCfEjU6IUqpqcjWcDzeDdNOySiVPDU_ZU5knJmzRCF8fcjFtPsaA4R7vdIEbDuOur15FXSvE8FvVSjP_49OH6hBYqoSUAslN3FMfbO6Z8YfCIY4tSOB2I6ahQ_x4ZWFWglC3w5mK-_4iX81bqi95eV4RUKefUuHhQDXtWhrSgIEC0YiluMvA4TnaJdLq_tWXIc4_Tq_KfpkvI004ONKgU7EAMEr1wZ4aDcJV2yf22gQ1sCSig6EGSTmmzDuEPsYiyd4NhidRZJP4HiiQh-hePBQsgcSgYGvz9wC6n57ufYKh2wm_Ti3Q
&requested_token_use=on_behalf_of
&scope=https://graph.microsoft.com/user.read+offline_access
```

## <a name="service-to-service-access-token-response"></a>Szolgáltatás szolgáltatás-hozzáférési jogkivonat válasza

A sikeres válasz egy JSON OAuth 2.0 válasz a következő paraméterekkel.

| Paraméter | Leírás |
| --- | --- |
| `token_type` | A token típusának értékét jelzi. A Microsoft identity platform csak `Bearer`a programot támogatja. A tulajdonosi jogkivonatokról további információt az [OAuth 2.0 engedélyezési keretrendszerében talál: tulajdonosi token használata (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |
| `scope` | A jogkivonatban megadott hozzáférési tartomány. |
| `expires_in` | A hozzáférési jogkivonat érvényességi ideje másodpercben. |
| `access_token` | A kért hozzáférési jogkivonat. A hívó szolgáltatás ezt a jogkivonatot használhatja a fogadó szolgáltatás hitelesítéséhez. |
| `refresh_token` | A kért hozzáférési jogkivonat frissítési jogkivonata. A hívó szolgáltatás használhatja ezt a jogkivonatot, hogy kérjen egy másik hozzáférési jogkivonatot az aktuális hozzáférési jogkivonat lejárta után. A frissítési jogkivonat `offline_access` csak akkor érhető el, ha a hatókört kérték. |

### <a name="success-response-example"></a>Példa sikerválaszra

A következő példa sikeres választ mutat a https://graph.microsoft.com webes API-hoz egy hozzáférési jogkivonatra vonatkozó kérelemre.

```
{
  "token_type": "Bearer",
  "scope": "https://graph.microsoft.com/user.read",
  "expires_in": 3269,
  "ext_expires_in": 0,
  "access_token": "eyJ0eXAiOiJKV1QiLCJub25jZSI6IkFRQUJBQUFBQUFCbmZpRy1tQTZOVGFlN0NkV1c3UWZkQ0NDYy0tY0hGa18wZE50MVEtc2loVzRMd2RwQVZISGpnTVdQZ0tQeVJIaGlDbUN2NkdyMEpmYmRfY1RmMUFxU21TcFJkVXVydVJqX3Nqd0JoN211eHlBQSIsImFsZyI6IlJTMjU2IiwieDV0IjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIiwia2lkIjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIn0.eyJhdWQiOiJodHRwczovL2dyYXBoLm1pY3Jvc29mdC5jb20iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaWF0IjoxNDkzOTMwMzA1LCJuYmYiOjE0OTM5MzAzMDUsImV4cCI6MTQ5MzkzMzg3NSwiYWNyIjoiMCIsImFpbyI6IkFTUUEyLzhEQUFBQU9KYnFFWlRNTnEyZFcxYXpKN1RZMDlYeDdOT29EMkJEUlRWMXJ3b2ZRc1k9IiwiYW1yIjpbInB3ZCJdLCJhcHBfZGlzcGxheW5hbWUiOiJUb2RvRG90bmV0T2JvIiwiYXBwaWQiOiIyODQ2ZjcxYi1hN2E0LTQ5ODctYmFiMy03NjAwMzViMmYzODkiLCJhcHBpZGFjciI6IjEiLCJmYW1pbHlfbmFtZSI6IkNhbnVtYWxsYSIsImdpdmVuX25hbWUiOiJOYXZ5YSIsImlwYWRkciI6IjE2Ny4yMjAuMC4xOTkiLCJuYW1lIjoiTmF2eWEgQ2FudW1hbGxhIiwib2lkIjoiZDVlOTc5YzctM2QyZC00MmFmLThmMzAtNzI3ZGQ0YzJkMzgzIiwib25wcmVtX3NpZCI6IlMtMS01LTIxLTIxMjc1MjExODQtMTYwNDAxMjkyMC0xODg3OTI3NTI3LTI2MTE4NDg0IiwicGxhdGYiOiIxNCIsInB1aWQiOiIxMDAzM0ZGRkEwNkQxN0M5Iiwic2NwIjoiVXNlci5SZWFkIiwic3ViIjoibWtMMHBiLXlpMXQ1ckRGd2JTZ1JvTWxrZE52b3UzSjNWNm84UFE3alVCRSIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInVuaXF1ZV9uYW1lIjoibmFjYW51bWFAbWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hY2FudW1hQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJWR1ItdmtEZlBFQ2M1dWFDaENRSkFBIiwidmVyIjoiMS4wIn0.cubh1L2VtruiiwF8ut1m9uNBmnUJeYx4x0G30F7CqSpzHj1Sv5DCgNZXyUz3pEiz77G8IfOF0_U5A_02k-xzwdYvtJUYGH3bFISzdqymiEGmdfCIRKl9KMeoo2llGv0ScCniIhr2U1yxTIkIpp092xcdaDt-2_2q_ql1Ha_HtjvTV1f9XR3t7_Id9bR5BqwVX5zPO7JMYDVhUZRx08eqZcC-F3wi0xd_5ND_mavMuxe2wrpF-EZviO3yg0QVRr59tE3AoWl8lSGpVc97vvRCnp4WVRk26jJhYXFPsdk4yWqOKZqzr3IFGyD08WizD_vPSrXcCPbZP3XWaoTUKZSNJg",
  "refresh_token": "OAQABAAAAAABnfiG-mA6NTae7CdWW7QfdAALzDWjw6qSn4GUDfxWzJDZ6lk9qRw4AnqPnvFqnzS3GiikHr5wBM1bV1YyjH3nUeIhKhqJWGwqJFRqs2sE_rqUfz7__3J92JDpi6gDdCZNNaXgreQsH89kLCVNYZeN6kGuFGZrjwxp1wS2JYc97E_3reXBxkHrA09K5aR-WsSKCEjf6WI23FhZMTLhk_ZKOe_nWvcvLj13FyvSrTMZV2cmzyCZDqEHtPVLJgSoASuQlD2NXrfmtcmgWfc3uJSrWLIDSn4FEmVDA63X6EikNp9cllH3Gp7Vzapjlnws1NQ1_Ff5QrmBHp_LKEIwfzVKnLLrQXN0EzP8f6AX6fdVTaeKzm7iw6nH0vkPRpUeLc3q_aNsPzqcTOnFfgng7t2CXUsMAGH5wclAyFCAwL_Cds7KnyDLL7kzOS5AVZ3Mqk2tsPlqopAiHijZaJumdTILDudwKYCFAMpUeUwEf9JmyFjl2eIWPmlbwU7cHKWNvuRCOYVqbsTTpJthwh4PvsL5ov5CawH_TaV8omG_tV6RkziHG9urk9yp2PH9gl7Cv9ATa3Vt3PJWUS8LszjRIAJmyw_EhgHBfYCvEZ8U9PYarvgqrtweLcnlO7BfnnXYEC18z_u5wemAzNBFUje2ttpGtRmRic4AzZ708tBHva2ePJWGX6pgQbiWF8esOrvWjfrrlfOvEn1h6YiBW291M022undMdXzum6t1Y1huwxHPHjCAA"
}
```

> [!NOTE]
> A fenti hozzáférési jogkivonat egy v1.0-formátumú jogkivonat. Ennek az az oka, hogy a jogkivonat az elérésalatt álló **erőforrás** alapján érhető el. A Microsoft Graph úgy van beállítva, hogy elfogadja a v1.0-s jogkivonatokat, így a Microsoft identity platform v1.0-s hozzáférési jogkivonatokat hoz létre, amikor egy ügyfél jogkivonatokat kér a Microsoft Graph számára. Csak az alkalmazások nak kell megtekinteniük a hozzáférési jogkivonatokat. Az ügyfelek **nem vizsgálhatják** meg őket.

### <a name="error-response-example"></a>Példa hibaválaszra

Egy hibaválaszt ad vissza a jogkivonat-végpont, amikor megpróbál megszerezni egy hozzáférési jogkivonatot az alsóbb rétegbeli API-t, ha az alsóbb rétegbeli API feltételes hozzáférési szabályzat (például többtényezős hitelesítés) van beállítva. A középső rétegbeli szolgáltatásnak felszínre kell írnia ezt a hibát az ügyfélalkalmazásban, hogy az ügyfélalkalmazás biztosítani tudja a felhasználói beavatkozást a feltételes hozzáférési házirend kielégítéséhez.

```
{
    "error":"interaction_required",
    "error_description":"AADSTS50079: Due to a configuration change made by your administrator, or because you moved to a new location, you must enroll in multi-factor authentication to access 'bf8d80f9-9098-4972-b203-500f535113b1'.\r\nTrace ID: b72a68c3-0926-4b8e-bc35-3150069c2800\r\nCorrelation ID: 73d656cf-54b1-4eb2-b429-26d8165a52d7\r\nTimestamp: 2017-05-01 22:43:20Z",
    "error_codes":[50079],
    "timestamp":"2017-05-01 22:43:20Z",
    "trace_id":"b72a68c3-0926-4b8e-bc35-3150069c2800",
    "correlation_id":"73d656cf-54b1-4eb2-b429-26d8165a52d7",
    "claims":"{\"access_token\":{\"polids\":{\"essential\":true,\"values\":[\"9ab03e19-ed42-4168-b6b7-7001fb3e933a\"]}}}"
}
```

## <a name="use-the-access-token-to-access-the-secured-resource"></a>A hozzáférési jogkivonat használata a védett erőforrás eléréséhez

Most a középső rétegbeli szolgáltatás használhatja a fent beszerzett jogkivonatot, hogy hitelesített `Authorization` kérelmeket az alsóbb rétegbeli webes API-t, a jogkivonat a fejlécben beállításával.

### <a name="example"></a>Példa

```
GET /v1.0/me HTTP/1.1
Host: graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJub25jZSI6IkFRQUJBQUFBQUFCbmZpRy1tQTZOVGFlN0NkV1c3UWZkSzdNN0RyNXlvUUdLNmFEc19vdDF3cEQyZjNqRkxiNlVrcm9PcXA2cXBJclAxZVV0QktzMHEza29HN3RzXzJpSkYtQjY1UV8zVGgzSnktUHZsMjkxaFNBQSIsImFsZyI6IlJTMjU2IiwieDV0IjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIiwia2lkIjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIn0.eyJhdWQiOiJodHRwczovL2dyYXBoLm1pY3Jvc29mdC5jb20iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaWF0IjoxNDkzOTMwMDE2LCJuYmYiOjE0OTM5MzAwMTYsImV4cCI6MTQ5MzkzMzg3NSwiYWNyIjoiMCIsImFpbyI6IkFTUUEyLzhEQUFBQUlzQjN5ZUljNkZ1aEhkd1YxckoxS1dlbzJPckZOUUQwN2FENTVjUVRtems9IiwiYW1yIjpbInB3ZCJdLCJhcHBfZGlzcGxheW5hbWUiOiJUb2RvRG90bmV0T2JvIiwiYXBwaWQiOiIyODQ2ZjcxYi1hN2E0LTQ5ODctYmFiMy03NjAwMzViMmYzODkiLCJhcHBpZGFjciI6IjEiLCJmYW1pbHlfbmFtZSI6IkNhbnVtYWxsYSIsImdpdmVuX25hbWUiOiJOYXZ5YSIsImlwYWRkciI6IjE2Ny4yMjAuMC4xOTkiLCJuYW1lIjoiTmF2eWEgQ2FudW1hbGxhIiwib2lkIjoiZDVlOTc5YzctM2QyZC00MmFmLThmMzAtNzI3ZGQ0YzJkMzgzIiwib25wcmVtX3NpZCI6IlMtMS01LTIxLTIxMjc1MjExODQtMTYwNDAxMjkyMC0xODg3OTI3NTI3LTI2MTE4NDg0IiwicGxhdGYiOiIxNCIsInB1aWQiOiIxMDAzM0ZGRkEwNkQxN0M5Iiwic2NwIjoiVXNlci5SZWFkIiwic3ViIjoibWtMMHBiLXlpMXQ1ckRGd2JTZ1JvTWxrZE52b3UzSjNWNm84UFE3alVCRSIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInVuaXF1ZV9uYW1lIjoibmFjYW51bWFAbWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hY2FudW1hQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJzUVlVekYxdUVVS0NQS0dRTVFVRkFBIiwidmVyIjoiMS4wIn0.Hrn__RGi-HMAzYRyCqX3kBGb6OS7z7y49XPVPpwK_7rJ6nik9E4s6PNY4XkIamJYn7tphpmsHdfM9lQ1gqeeFvFGhweIACsNBWhJ9Nx4dvQnGRkqZ17KnF_wf_QLcyOrOWpUxdSD_oPKcPS-Qr5AFkjw0t7GOKLY-Xw3QLJhzeKmYuuOkmMDJDAl0eNDbH0HiCh3g189a176BfyaR0MgK8wrXI_6MTnFSVfBePqklQeLhcr50YTBfWg3Svgl6MuK_g1hOuaO-XpjUxpdv5dZ0SvI47fAuVDdpCE48igCX5VMj4KUVytDIf6T78aIXMkYHGgW3-xAmuSyYH_Fr0yVAQ
```

## <a name="gaining-consent-for-the-middle-tier-application"></a>Hozzájárulás megszerzése a középszintű alkalmazáshoz

Az alkalmazás architektúrájától vagy használatától függően különböző stratégiákat is figyelembe vehet annak biztosítására, hogy az OBO-folyamat sikeres legyen. A végső cél minden esetben a megfelelő hozzájárulás biztosítása, hogy az ügyfélalkalmazás megtudja hívni a középső rétegbeli alkalmazást, és a középső rétegbeli alkalmazás jogosult a háttér-erőforrás hívására. 

> [!NOTE]
> Korábban a Microsoft-fiókrendszer (személyes fiókok) nem támogatta az "Ismert ügyfélalkalmazás" mezőt, és nem tudta megjeleníteni az egyesített hozzájárulást.  Ez lett hozzáadva, és a Microsoft identity platform összes alkalmazása használhatja az ismert ügyfélalkalmazás-megközelítést az OBO-hívások hoz. 

### <a name="default-and-combined-consent"></a>/.alapértelmezett és kombinált hozzájárulás

A középső rétegbeli alkalmazás hozzáadja az ügyfelet az ismert ügyfélalkalmazások listájához a jegyzékfájlban, majd az ügyfél kombinált jóváhagyási folyamatot indíthat el saját maga és a középső rétegbeli alkalmazás számára is. A Microsoft identity platform végpontján ez [ `/.default` ](v2-permissions-and-consent.md#the-default-scope)a hatókör használatával történik. Amikor egy hozzájárulási képernyőt indít `/.default`el az ismert ügyfélalkalmazások használatával, és a jóváhagyási képernyőn megjelennek **az** ügyfél engedélyei a középső réteg API-hoz, és a középső réteg API-hoz szükséges engedélyeket is kérnek. A felhasználó mindkét alkalmazáshoz beleegyezést ad, majd az OBO-folyamat működik.

### <a name="pre-authorized-applications"></a>Előre engedélyezett alkalmazások

Az erőforrások jelezhetik, hogy egy adott alkalmazás mindig rendelkezik engedéllyel bizonyos hatókörök fogadására. Ez elsősorban akkor hasznos, ha az előtér-ügyfél és a háttér-erőforrás közötti kapcsolatok zökkenőmentesebbé teszik. Egy erőforrás deklarálhat több előre engedélyezett alkalmazást – bármely ilyen alkalmazás kérheti ezeket az engedélyeket egy OBO-folyamatban, és a felhasználó beleegyezése nélkül fogadhatja őket.

### <a name="admin-consent"></a>Rendszergazdai jóváhagyás

A bérlői rendszergazda garantálhatja, hogy az alkalmazások engedélyt kapnak a szükséges API-k hívásához a középső rétegbeli alkalmazás rendszergazdai hozzájárulásának megadásával. Ehhez a rendszergazda megtalálhatja a középső rétegbeli alkalmazást a bérlőben, megnyithatja a szükséges engedélyeket tartalmazó lapot, és engedélyt adhat az alkalmazásnak. Ha többet szeretne megtudni a rendszergazdai jóváhagyásról, olvassa el a [hozzájárulási és engedélydokumentációt.](v2-permissions-and-consent.md)

### <a name="use-of-a-single-application"></a>Egyetlen alkalmazás használata

Bizonyos esetekben előfordulhat, hogy csak egy párosítás a középső rétegbeli és az előtér-ügyfél. Ebben a forgatókönyvben előfordulhat, hogy könnyebb, hogy ez egy alkalmazás, a középső rétegbeli alkalmazás teljes körű szükségességét. Az előtér és a webes API közötti hitelesítéshez használhat cookie-kat, id_token vagy hozzáférési jogkivonatot, amelyet magához az alkalmazáshoz kell kérni. Ezután kérje a jóváhagyást az egyetlen alkalmazás a háttér-erőforrás.

## <a name="client-limitations"></a>Ügyfél korlátozások

Ha egy ügyfél az implicit folyamat ot használja egy id_token lekérni, és az ügyfél helyettesítő karaktereket is használ a válasz URL-címében, a id_token nem használható obo-folyamathoz.  Az implicit támogatási folyamaton keresztül beszerzett hozzáférési jogkivonatokat azonban egy bizalmas ügyfél akkor is beválthatja, ha a fogadó ügyfél rendelkezik regisztrált helyettesítő karakteres válasz URL-címmel.

## <a name="next-steps"></a>További lépések

További információ az OAuth 2.0 protokollról, valamint az ügyfél hitelesítő adataival történő szolgáltatásszolgáltatás végrehajtásának másik módja.

* [OAuth 2.0 ügyfél hitelesítő adatok megadása a Microsoft identitásplatformján](v2-oauth2-client-creds-grant-flow.md)
* [OAuth 2.0-s kódáramlás a Microsoft identitásplatformján](v2-oauth2-auth-code-flow.md)
* [A `/.default` hatókör használata](v2-permissions-and-consent.md#the-default-scope)
