---
title: Az Azure AD v2.0--meghatalmazásos folyamat OAuth2.0 |} A Microsoft Docs
description: Ez a cikk ismerteti, hogyan használható a HTTP-üzenetek megvalósításához a szolgáltatások közötti hitelesítés a OAuth2.0--meghatalmazásos folyamat használatával.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 09f6f318-e88b-4024-9ee1-e7f09fb19a82
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 0fc81a75e79d7f570bd55c9c30a464e5bbb9ad1c
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2018
ms.locfileid: "52423432"
---
# <a name="azure-active-directory-v20-and-oauth-20-on-behalf-of-flow"></a>Az Azure Active Directory 2.0-s verzió és az OAuth 2.0-alapú meghatalmazásos folyamat

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Az OAuth 2.0-alapú meghatalmazásos folyamat szolgál a használati eset, ahol az alkalmazás meghívja a szolgáltatás/webes API-kat, amelynek be kell meghívni egy másik szolgáltatás/webes API-t. A cél pedig propagálása a delegált felhasználó identitása és a kérelem láncot engedélyeket. A középső rétegű szolgáltatás hitelesített kéréseket küld az alárendelt szolgáltatás kell biztonságossá tételéhez egy hozzáférési jogkivonatot az Azure Active Directory (Azure AD), a felhasználó nevében.

> [!NOTE]
> A v2.0-végpont nem támogatja az összes Azure Active Directory-forgatókönyvek és funkciók. Annak megállapításához, hogy használjon a v2.0-végpont, olvassa el [v2.0 korlátozások](active-directory-v2-limitations.md).
>

> [!IMPORTANT]
> 2018 május egy `id_token` nem használható az On-meghatalmazásos folyamat - gyógyfürdők meg kell felelnie egy **hozzáférés** tokent egy középső rétegű bizalmas ügyfél számára, hogy OBO folyamatok. Lásd: [korlátozások](#client-limitations) , amelyen az ügyfelek a alapú meghatalmazásos hívásokat hajthat végre további részletekért.

## <a name="protocol-diagram"></a>Protokoll diagramja
Tegyük fel, hogy a felhasználó hitelesítése egy alkalmazást a a a [OAuth 2.0 hitelesítési kód adja meg a folyamat](v2-oauth2-auth-code-flow.md). Ezen a ponton az alkalmazás rendelkezik-e a hozzáférési jogkivonat *az API-t A* (token A) a felhasználói jogcímek és a középső rétegű elérésére engedélyt a webes API-t (API-t A). Most API-t A kell egy hitelesített kéréseknél az alsóbb rétegbeli webes API-hoz (API-t, B).

A következő lépések az On-meghatalmazásos folyamat jelent, és az alábbi ábra segítségével vannak írva.

![OAuth2.0--meghatalmazásos folyamat](./media/v1-oauth2-on-behalf-of-flow/active-directory-protocols-oauth-on-behalf-of-flow.png)


1. Az ügyfélalkalmazás kérést küld egy API-t A jogkivonattal (az egy `aud` API a jogcím).
2. API-t A hitelesíti magát az Azure AD-kiállítási végpont, és b API eléréséhez tokent kér
3. Az Azure AD-kiállítási végpont érvényesíti az API A jogkivonat A hitelesítő adatokat, és problémák a hozzáférési jogkivonat (token B) API-t a b.
4. A jogkivonat B API b. a kérelem az engedélyezési fejléc értéke
5. B API által visszaadott adatok a biztonságos erőforrás

> [!NOTE]
> Ebben a forgatókönyvben a középső rétegű szolgáltatás rendelkezik beszerzése a felhasználói beleegyezés az alsóbb rétegbeli API eléréséhez nincs szükség felhasználói beavatkozásra. Ezért az alsóbb rétegbeli API-hoz való hozzáférést biztosít arra, akkor jelenik meg előre, a hitelesítés során. lépés: a hozzájárulási részét.
>

## <a name="service-to-service-access-token-request"></a>A szolgáltatás a szolgáltatás-hozzáférési jogkivonat kérése
Egy hozzáférési jogkivonatot kér, győződjön meg arról, egy HTTP POST a bérlő-specifikus, az Azure AD v2.0-végpont a következő paraméterekkel.

```
https://login.microsoftonline.com/<tenant>/oauth2/v2.0/token
```

Nincsenek két esetben attól függően, hogy az ügyfélalkalmazás úgy dönt, hogy egy közös titkos kulcsot, vagy a tanúsítvány védi.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Először. eset: egy közös titkos kulcsot a hozzáférési jogkivonat kérése
A közös titkos kulcsot használja, amikor egy szolgáltatások közötti hozzáférési jogkivonat kérése a következő paramétereket tartalmaz:

| Paraméter |  | Leírás |
| --- | --- | --- |
| grant_type |szükséges | A jogkivonat kérése típusa. A kérést a jwt-t, az értéknek kell lennie **urn: ietf:params:oauth:grant-típus: jwt-tulajdonosi**. |
| client_id |szükséges | Az alkalmazás AZONOSÍTÓJÁT, amely a [alkalmazásregisztrációs portálon](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) az alkalmazáshoz rendelt. |
| client_secret |szükséges | Az alkalmazás titkos az alkalmazását az alkalmazásregisztrációs portálon a létrehozott. |
| assertion |szükséges | A kérésben használt token értékét. |
| scope |szükséges | Szóközzel elválasztott a jogkivonat kérése hatókörök listája. További információkért lásd: [hatókörök](v2-permissions-and-consent.md).|
| requested_token_use |szükséges | Itt adhatja meg, hogyan kell feldolgozni a kérelmet. Az On-meghatalmazásos folyamat, az értéke nem lehet **on_behalf_of**. |

#### <a name="example"></a>Példa
A következő HTTP POST kéréseket egy hozzáférési jogkivonatot, és a frissítési jogkivonatot `user.read` a hatóköre a https://graph.microsoft.com webes API-t.

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

### <a name="second-case-access-token-request-with-a-certificate"></a>A második eset: a tanúsítványhoz a hozzáférési jogkivonat kérése
A service to service hozzáférési jogkivonat kérése tanúsítvánnyal az alábbi paramétereket tartalmazza:

| Paraméter |  | Leírás |
| --- | --- | --- |
| grant_type |szükséges | A jogkivonat kérése típusa. A kérést a jwt-t, az értéknek kell lennie **urn: ietf:params:oauth:grant-típus: jwt-tulajdonosi**. |
| client_id |szükséges | Az alkalmazás AZONOSÍTÓJÁT, amely a [alkalmazásregisztrációs portálon](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) az alkalmazáshoz rendelt. |
| client_assertion_type |szükséges |Az értéknek kell lennie `urn:ietf:params:oauth:client-assertion-type:jwt-bearer` |
| client_assertion |szükséges | Egy helyességi feltétel (egy JSON Web Token) létrehozására és aláírására a tanúsítványt igénylő regisztrált hitelesítő adatként az alkalmazáshoz. További információ [hitelesítő tanúsítvány](active-directory-certificate-credentials.md) megtudhatja, hogyan regisztrálhat a tanúsítvány és a helyességi feltétel formátumát.|
| assertion |szükséges | A kérésben használt token értékét. |
| requested_token_use |szükséges | Itt adhatja meg, hogyan kell feldolgozni a kérelmet. Az On-meghatalmazásos folyamat, az értéke nem lehet **on_behalf_of**. |
| scope |szükséges | Szóközzel elválasztott a jogkivonat kérése hatókörök listája. További információkért lásd: [hatókörök](v2-permissions-and-consent.md).|

Figyelje meg, hogy paraméterei szinte teljesen megegyezik a kérés által közös titkos kulcsot is azzal a különbséggel, hogy a titkos ügyfélkódot paraméter váltotta fel két paramétert: egy client_assertion_type és client_assertion.

#### <a name="example"></a>Példa
A következő HTTP POST kéréseket a hozzáférési jogkivonat `user.read` a hatóköre a https://graph.microsoft.com webes API-tanúsítvánnyal.

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

## <a name="service-to-service-access-token-response"></a>Szolgáltatás-hozzáférési token válasz szolgáltatás
Sikerességi válasz egy JSON OAuth 2.0-válaszban az alábbi paraméterekkel.

| Paraméter | Leírás |
| --- | --- |
| token_type |Typ tokenu értékét jelöli. Az egyetlen típus, amely az Azure AD által támogatott **tulajdonosi**. További információ a tulajdonosi jogkivonatokat: a [OAuth 2.0 engedélyezési keretrendszer: tulajdonosi jogkivonat-használat (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |
| scope |Megadja a hozzáférést a jogkivonat hatókörét. |
| expires_in |Mennyi ideig a hozzáférési jogkivonat érvénytelen (másodpercben). |
| access_token |A kért hozzáférési jogkivonatot. A hívó szolgáltatás használhatja ezt a jogkivonatot a fogadó szolgáltatással való hitelesítésre. |
| refresh_token |A frissítési jogkivonat a kért hozzáférési jogkivonat. A hívó szolgáltatás a jogkivonat használatával új hozzáférési jogkivonat kérése a jelenlegi hozzáférési jogkivonat lejárata után. A frissítési jogkivonat csak akkor biztosított, ha a `offline_access` a kért hatókörhöz.|

### <a name="success-response-example"></a>Példa a sikeres válasz
Az alábbi példa bemutatja egy hozzáférési kérelem sikeres válasz token a https://graph.microsoft.com webes API-t.

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
> Figyelje meg, hogy a fenti hozzáférési jogkivonatot V1-formátumú jogkivonatot. Ez azért, mert a jogkivonat van megadva az erőforrás-hozzáférésének alapján. A Microsoft Graph kéri a V1-jogkivonatokat, így az Azure AD V1 hozzáférési jogkivonatokat állít elő, amikor egy ügyfél jogkivonatok igényel a Microsoft Graph. Csak az alkalmazások hozzáférési jogkivonatok tekintse - ügyfeleknek nem kell megvizsgálni őket. 


### <a name="error-response-example"></a>Hiba történt a válasz példa
Egy hibaválasz jogkivonat-végpont az Azure AD által visszaadott közben hozzáférési jogkivonat beszerzése az alsóbb rétegbeli API-hoz, például többtényezős hitelesítést, állítsa be rajta a feltételes hozzáférési szabályzat az alsóbb rétegbeli API-e. A középső rétegű szolgáltatást kell surface ezt a hibát az ügyfélalkalmazás, úgy, hogy az ügyfélalkalmazás tud biztosítani a felhasználói beavatkozás a feltételes hozzáférési szabályzat kielégítéséhez.

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

## <a name="use-the-access-token-to-access-the-secured-resource"></a>A hozzáférési jogkivonat használata a védett erőforrások eléréséhez
Most, hogy hitelesített kéréseket az alsóbb rétegbeli webes API-t, a jogkivonat beállít a fenti megszerzett jogkivonat segítségével a középső rétegű szolgáltatást a `Authorization` fejléc.

### <a name="example"></a>Példa
```
GET /v1.0/me HTTP/1.1
Host: graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJub25jZSI6IkFRQUJBQUFBQUFCbmZpRy1tQTZOVGFlN0NkV1c3UWZkSzdNN0RyNXlvUUdLNmFEc19vdDF3cEQyZjNqRkxiNlVrcm9PcXA2cXBJclAxZVV0QktzMHEza29HN3RzXzJpSkYtQjY1UV8zVGgzSnktUHZsMjkxaFNBQSIsImFsZyI6IlJTMjU2IiwieDV0IjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIiwia2lkIjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIn0.eyJhdWQiOiJodHRwczovL2dyYXBoLm1pY3Jvc29mdC5jb20iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaWF0IjoxNDkzOTMwMDE2LCJuYmYiOjE0OTM5MzAwMTYsImV4cCI6MTQ5MzkzMzg3NSwiYWNyIjoiMCIsImFpbyI6IkFTUUEyLzhEQUFBQUlzQjN5ZUljNkZ1aEhkd1YxckoxS1dlbzJPckZOUUQwN2FENTVjUVRtems9IiwiYW1yIjpbInB3ZCJdLCJhcHBfZGlzcGxheW5hbWUiOiJUb2RvRG90bmV0T2JvIiwiYXBwaWQiOiIyODQ2ZjcxYi1hN2E0LTQ5ODctYmFiMy03NjAwMzViMmYzODkiLCJhcHBpZGFjciI6IjEiLCJmYW1pbHlfbmFtZSI6IkNhbnVtYWxsYSIsImdpdmVuX25hbWUiOiJOYXZ5YSIsImlwYWRkciI6IjE2Ny4yMjAuMC4xOTkiLCJuYW1lIjoiTmF2eWEgQ2FudW1hbGxhIiwib2lkIjoiZDVlOTc5YzctM2QyZC00MmFmLThmMzAtNzI3ZGQ0YzJkMzgzIiwib25wcmVtX3NpZCI6IlMtMS01LTIxLTIxMjc1MjExODQtMTYwNDAxMjkyMC0xODg3OTI3NTI3LTI2MTE4NDg0IiwicGxhdGYiOiIxNCIsInB1aWQiOiIxMDAzM0ZGRkEwNkQxN0M5Iiwic2NwIjoiVXNlci5SZWFkIiwic3ViIjoibWtMMHBiLXlpMXQ1ckRGd2JTZ1JvTWxrZE52b3UzSjNWNm84UFE3alVCRSIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInVuaXF1ZV9uYW1lIjoibmFjYW51bWFAbWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hY2FudW1hQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJzUVlVekYxdUVVS0NQS0dRTVFVRkFBIiwidmVyIjoiMS4wIn0.Hrn__RGi-HMAzYRyCqX3kBGb6OS7z7y49XPVPpwK_7rJ6nik9E4s6PNY4XkIamJYn7tphpmsHdfM9lQ1gqeeFvFGhweIACsNBWhJ9Nx4dvQnGRkqZ17KnF_wf_QLcyOrOWpUxdSD_oPKcPS-Qr5AFkjw0t7GOKLY-Xw3QLJhzeKmYuuOkmMDJDAl0eNDbH0HiCh3g189a176BfyaR0MgK8wrXI_6MTnFSVfBePqklQeLhcr50YTBfWg3Svgl6MuK_g1hOuaO-XpjUxpdv5dZ0SvI47fAuVDdpCE48igCX5VMj4KUVytDIf6T78aIXMkYHGgW3-xAmuSyYH_Fr0yVAQ
```

## <a name="client-limitations"></a>Ügyfél-korlátozások
Ha egy ügyfél az implicit folyamat segítségével kéri le a id_token, és a egy válasz URL-cím is tartalmaz helyettesítő karaktereket, hogy az ügyfél, a id_token egy OBO folyamat nem használható.  Azonban az implicit engedélyezési folyamat keresztül beszerzett hozzáférési jogkivonatok továbbra is váltható bizalmas ügyfelek akkor is, ha a kezdeményező ügyfélnek van regisztrálva helyettesítő karaktert tartalmazó válasz-URL. 

## <a name="next-steps"></a>További lépések
További információ az OAuth 2.0 protokollt és a szolgáltatások közötti hitelesítés ügyfél-hitelesítő adatok használatával egy másik módszerét.
* [OAuth 2.0 ügyfél hitelesítő adatainak megadása az Azure AD v2.0](v2-oauth2-client-creds-grant-flow.md)
* [Az OAuth 2.0 az Azure AD v2.0](v2-oauth2-auth-code-flow.md)
