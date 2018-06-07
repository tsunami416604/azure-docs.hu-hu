---
title: 'Az Azure AD szolgáltatások közötti hitelesítés használatával OAuth2.0 meghatározta a-nevében-: |} Microsoft Docs'
description: Ez a cikk ismerteti a HTTP-üzenetek használata a szolgáltatások közötti hitelesítés használata a OAuth2.0 a-meghatalmazásos folyamat végrehajtásához.
services: active-directory
documentationcenter: .net
author: navyasric
manager: mtillman
editor: ''
ms.assetid: 09f6f318-e88b-4024-9ee1-e7f09fb19a82
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2017
ms.author: celested
ms.reviewer: hirsin; nacanuma
ms.custom: aaddev
ms.openlocfilehash: 2bb0d10fee04c4ee48344695769fa7768b0f3a85
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/06/2018
ms.locfileid: "34823866"
---
# <a name="service-to-service-calls-using-delegated-user-identity-in-the-on-behalf-of-flow"></a>Szolgáltatás hívásokon szolgáltatás meghatalmazott az On-meghatalmazásos folyamat-beli felhasználói identitás
Az OAuth 2.0 On-Behalf-Of (OBO) folyamat szolgál a használati eset, ahol egy alkalmazás elindítja egy szolgáltatás vagy webes API, amely pedig meg kell hívni egy másik szolgáltatás vagy webes API-t. A lényege való terjesztése, a felhasználó delegált identitása és az engedélyek a kérelem lánc keresztül. A középső rétegbeli szolgáltatás hitelesített kéréseket küld az alárendelt szolgáltatás kell biztonságos hozzáférési tokent az Azure Active Directory (Azure AD), a felhasználó nevében.

> [!IMPORTANT]
> Nyilvános használó ügyfelek számára a [OAuth 2.0 típusú implicit grant](active-directory-dev-understanding-oauth2-implicit-grant.md) a OBO folyamat nem használható. Ezek az ügyfelek egy középső rétegbeli bizalmas ügyfél OBO adatfolyamok végrehajtásához meg kell felelnie a hozzáférési jogkivonat. További információk arról, hogy mely ügyfelek OBO hívások hajthat végre: [ügyfél korlátozások](#client-limitations).

## <a name="on-behalf-of-flow-diagram"></a>A-nevében-: folyamatábrája
Tegyük fel, hogy a felhasználó hitelesítése egy alkalmazást, amely a a [OAuth 2.0 hitelesítési kódot adjon folyamat](active-directory-protocols-oauth-code.md). Ezen a ponton az alkalmazás rendelkezik olyan hozzáférési jogkivonatot (lexikális elem A) a felhasználói jogcímek és beleegyezése a középső rétegbeli webes API-k (API-A) eléréséhez. Most API A kell hitelesített kéréseknél az alsóbb rétegbeli webes API (API-B).

A következő lépések az On-meghatalmazásos folyamat jelent, és segítségével. a következő ábra ismerteti.

![OAuth2.0 a-meghatalmazásos folyamat](media/active-directory-protocols-oauth-on-behalf-of/active-directory-protocols-oauth-on-behalf-of-flow.png)


1. Az ügyfélalkalmazás egy kérést küld API A a jogkivonatok azonosítójához.
2. API-t A az Azure AD hitelesítési karakterlánc-kiállítási végpont hitelesíti, és API-b eléréséhez jogkivonat-kérelmek
3. Az Azure AD hitelesítési karakterlánc-kiállítási végpont ellenőrzi az API A jogkivonat A hitelesítő adatokat, és API b (lexikális elem B) állít ki a hozzáférési jogkivonat.
4. A token B API a b kiszolgálóra a kérés hitelesítési fejlécéhez van megadva
5. API B. által visszaadott adatok a védett erőforrás

## <a name="register-the-application-and-service-in-azure-ad"></a>Az alkalmazás és szolgáltatás regisztrálása az Azure ad-ben
Az ügyfélalkalmazás mind a középső rétegbeli szolgáltatás regisztrálása az Azure ad-ben.
### <a name="register-the-middle-tier-service"></a>A középső rétegbeli szolgáltatás regisztrálása
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A felső eszköztáron kattintson a fiókhoz, majd a a **Directory** menüben válassza ki az Active Directory-bérlőt, ahová be szeretné-e az alkalmazás regisztrálásához.
3. Kattintson a **több szolgáltatások** a bal oldali navigációs válassza **Azure Active Directory**.
4. Kattintson a **App regisztrációk** válassza **új alkalmazás regisztrációja**.
5. Adjon egy rövid nevet az alkalmazáshoz, és jelölje ki az alkalmazás. A alapján alkalmazás típusa a bejelentkezési URL-cím vagy átirányítási URL-CÍMÉT az alap URL-címet. Kattintson a **létrehozása** az alkalmazás létrehozására.
6. Miközben továbbra is az Azure portálon, válassza ki az alkalmazást, majd kattintson a **beállítások**. A beállítások menüből **kulcsok** és kulcs hozzáadása – 1 év vagy a 2 év kulcs időtartam kiválasztása. Ha mentése ezen a lapon, a kulcs értéke jelenik meg, másolja ki és mentse a érték biztonságos helyen – ezt a kulcsot az alkalmazás beállításainak megadtak a megvalósítás - olyan később szüksége lesz a kulcs értéke nem lesz újra megjelenített, sem lekérhető bármilyen más módon , ezért kérjük, jegyezze fel, amint az Azure portálról látható.

### <a name="register-the-client-application"></a>Az ügyfélalkalmazás regisztrálása
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A felső eszköztáron kattintson a fiókhoz, majd a a **Directory** menüben válassza ki az Active Directory-bérlőt, ahová be szeretné-e az alkalmazás regisztrálásához.
3. Kattintson a **több szolgáltatások** a bal oldali navigációs válassza **Azure Active Directory**.
4. Kattintson a **App regisztrációk** válassza **új alkalmazás regisztrációja**.
5. Adjon egy rövid nevet az alkalmazáshoz, és jelölje ki az alkalmazás. A alapján alkalmazás típusa a bejelentkezési URL-cím vagy átirányítási URL-CÍMÉT az alap URL-címet. Kattintson a **létrehozása** az alkalmazás létrehozására.
6. Engedélyek konfigurálása az alkalmazás - a beállítási menüben, válassza ki a **szükséges engedélyek** területen kattintson a **hozzáadása**, majd **API kiválasztása**, és írja be nevét a középső rétegbeli szolgáltatás a szövegmezőben. Kattintson a **Select engedélyeket** válassza ki "hozzáférési *szolgáltatásnév*".

### <a name="configure-known-client-applications"></a>Ismert ügyfélalkalmazások konfigurálása
Ebben az esetben a középső rétegbeli szolgáltatás tartozik az beszerzése a felhasználói hozzájárulás az alsóbb rétegbeli API eléréséhez felhasználói beavatkozásra. Ezért is hozzáférést biztosítson az alsóbb rétegbeli API-t kell bemutatni előzetes megfizetése esetén, egy részét a hozzájárulásukat adják. lépés: a hitelesítés során.
Ennek érdekében az alábbi lépésekkel explicit módon kötni az ügyfélalkalmazás regisztrációs regisztrációját a középső rétegbeli szolgáltatást, amely egyesíti a hozzájárulási szükséges az ügyfél és a középső rétegbeli egyetlen párbeszédpanel az Azure AD-ben.
1. Keresse meg a középső rétegű regisztrációját, és kattintson a **Manifest** a jegyzékfájl-szerkesztő megnyitásához.
2. A jegyzékben, keresse meg a `knownClientApplications` tömb tulajdonság, majd adja meg az ügyfél-Azonosítót, az ügyfélalkalmazás elemeként.
3. A jegyzékfájl mentése a Mentés gombra kattintva gombra.

## <a name="service-to-service-access-token-request"></a>Szolgáltatáskérés hozzáférési jogkivonat szolgáltatás
Olyan hozzáférési jogkivonatot kérni, a bérlő-specifikus HTTP POST ellenőrizze az Azure AD-végpont a következő paraméterekkel.

```
https://login.microsoftonline.com/<tenant>/oauth2/token
```
Attól függően, hogy az ügyfélalkalmazás úgy dönt, hogy egy közös titkot, vagy a tanúsítvány védi két esetben van.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Először. eset: egy közös titkot a hozzáférési token kérelem
Egy közös titkos kulcs használata esetén a szolgáltatás hozzáférési kérelmek tartalmazza a következő paraméterekkel:

| Paraméter |  | Leírás |
| --- | --- | --- |
| grant_type |szükséges | A token kérelem típusa. A kérelmek jwt-t használ, az értéknek kell lennie **urn: ietf:params:oauth:grant-típus: jwt-tulajdonosi**. |
| assertion |szükséges | A jogkivonatot, amelyet a kérés értéke. |
| client_id |szükséges | Az Azure ad-vel a regisztráció során a hívó szolgáltatáshoz hozzárendelt Alkalmazásazonosító. Az alkalmazás azonosítója az Azure felügyeleti portálon található, kattintson a **Active Directory**, és kattintson arra a címtárra, majd kattintson az alkalmazás nevét. |
| client_secret |szükséges | A kulcs a hívó szolgáltatás regisztrálva az Azure ad-ben. Ez az érték rendelkezik lett jegyezni a regisztráció során. |
| erőforrás |szükséges | A fogadó szolgáltatást (védett erőforrás) App ID URI. Az Azure felügyeleti portálon App ID URI megkereséséhez kattintson **Active Directory**kattintson arra a címtárra, kattintson az alkalmazás nevét, kattintson **összes beállítás** , majd **tulajdonságai**. |
| requested_token_use |szükséges | Itt adhatja meg, hogyan kell feldolgozni a kérelmet. Az On-meghatalmazásos folyamat, az érték lehet **on_behalf_of**. |
| scope |szükséges | Egy szóközzel elválasztott a jogkivonatkérelem hatókört. Az OpenID Connect, a hatókör **openid** meg kell adni.|

#### <a name="example"></a>Példa
A következő HTTP POST kérések számára egy jogkivonatot a https://graph.windows.net webes API-t. A `client_id` azonosítja a szolgáltatást, amelyet a hozzáférési jogkivonat igényel.

```
// line breaks for legibility only

POST /oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_secret=0Y1W%2BY3yYb3d9N8vSjvm8WrGzVZaAaHbHHcGbcgG%2BoI%3D
&resource=https%3A%2F%2Fgraph.windows.net
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2Rkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tLzE5MjNmODYyLWU2ZGMtNDFhMy04MWRhLTgwMmJhZTAwYWY2ZCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzI2MDM5Y2NlLTQ4OWQtNDAwMi04MjkzLTViMGM1MTM0ZWFjYi8iLCJpYXQiOjE0OTM0MjMxNTIsIm5iZiI6MTQ5MzQyMzE1MiwiZXhwIjoxNDkzNDY2NjUyLCJhY3IiOiIxIiwiYWlvIjoiWTJaZ1lCRFF2aTlVZEc0LzM0L3dpQndqbjhYeVp4YmR1TFhmVE1QeG8yYlN2elgreHBVQSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiJiMzE1MDA3OS03YmViLTQxN2YtYTA2YS0zZmRjNzhjMzI1NDUiLCJhcHBpZGFjciI6IjAiLCJlX2V4cCI6MzAyNDAwLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidmVyIjoiMS4wIn0.R-Ke-XO7lK0r5uLwxB8g5CrcPAwRln5SccJCfEjU6IUqpqcjWcDzeDdNOySiVPDU_ZU5knJmzRCF8fcjFtPsaA4R7vdIEbDuOur15FXSvE8FvVSjP_49OH6hBYqoSUAslN3FMfbO6Z8YfCIY4tSOB2I6ahQ_x4ZWFWglC3w5mK-_4iX81bqi95eV4RUKefUuHhQDXtWhrSgIEC0YiluMvA4TnaJdLq_tWXIc4_Tq_KfpkvI004ONKgU7EAMEr1wZ4aDcJV2yf22gQ1sCSig6EGSTmmzDuEPsYiyd4NhidRZJP4HiiQh-hePBQsgcSgYGvz9wC6n57ufYKh2wm_Ti3Q
&requested_token_use=on_behalf_of
&scope=openid
```

### <a name="second-case-access-token-request-with-a-certificate"></a>A második esetben: hozzáférési jogkivonat kérelem tanúsítvánnyal
Szolgáltatás hozzáférési kérelmek tanúsítvánnyal tartalmazza a következő paraméterekkel:

| Paraméter |  | Leírás |
| --- | --- | --- |
| grant_type |szükséges | A token kérelem típusa. A kérelmek jwt-t használ, az értéknek kell lennie **urn: ietf:params:oauth:grant-típus: jwt-tulajdonosi**. |
| assertion |szükséges | A jogkivonatot, amelyet a kérés értéke. |
| client_id |szükséges | Az Azure ad-vel a regisztráció során a hívó szolgáltatáshoz hozzárendelt Alkalmazásazonosító. Az alkalmazás azonosítója az Azure felügyeleti portálon található, kattintson a **Active Directory**, és kattintson arra a címtárra, majd kattintson az alkalmazás nevét. |
| client_assertion_type |szükséges |Az értéknek kell lennie `urn:ietf:params:oauth:client-assertion-type:jwt-bearer` |
| client_assertion |szükséges | Egy helyességi feltétel (egy JSON Web Token) hozzon létre, és írja alá a tanúsítványt igénylő regisztrálta hitelesítő adatként az alkalmazáshoz. További információ a [tanúsítvány a hitelesítő adatok](active-directory-certificate-credentials.md) megtudhatja, hogyan kell regisztrálni a tanúsítványt, és a helyességi feltétel formátuma.|
| erőforrás |szükséges | A fogadó szolgáltatást (védett erőforrás) App ID URI. Az Azure felügyeleti portálon App ID URI megkereséséhez kattintson **Active Directory**kattintson arra a címtárra, kattintson az alkalmazás nevét, kattintson **összes beállítás** , majd **tulajdonságai**. |
| requested_token_use |szükséges | Itt adhatja meg, hogyan kell feldolgozni a kérelmet. Az On-meghatalmazásos folyamat, az érték lehet **on_behalf_of**. |
| scope |szükséges | Egy szóközzel elválasztott a jogkivonatkérelem hatókört. Az OpenID Connect, a hatókör **openid** meg kell adni.|

Figyelje meg, hogy a paraméterek megegyeznek-szinte közös titkos kulcs kérése gazdabuszadaptereken azzal a különbséggel, hogy a client_secret paraméter helyébe két paramétert: egy client_assertion_type és client_assertion.

#### <a name="example"></a>Példa
A következő HTTP POST kérések számára egy jogkivonatot a https://graph.windows.net webes API-t a tanúsítvánnyal. A `client_id` azonosítja a szolgáltatást, amelyet a hozzáférési jogkivonat igényel.

```
// line breaks for legibility only

POST /oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&resource=https%3A%2F%2Fgraph.windows.net
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2Rkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tLzE5MjNmODYyLWU2ZGMtNDFhMy04MWRhLTgwMmJhZTAwYWY2ZCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzI2MDM5Y2NlLTQ4OWQtNDAwMi04MjkzLTViMGM1MTM0ZWFjYi8iLCJpYXQiOjE0OTM0MjMxNTIsIm5iZiI6MTQ5MzQyMzE1MiwiZXhwIjoxNDkzNDY2NjUyLCJhY3IiOiIxIiwiYWlvIjoiWTJaZ1lCRFF2aTlVZEc0LzM0L3dpQndqbjhYeVp4YmR1TFhmVE1QeG8yYlN2elgreHBVQSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiJiMzE1MDA3OS03YmViLTQxN2YtYTA2YS0zZmRjNzhjMzI1NDUiLCJhcHBpZGFjciI6IjAiLCJlX2V4cCI6MzAyNDAwLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidmVyIjoiMS4wIn0.R-Ke-XO7lK0r5uLwxB8g5CrcPAwRln5SccJCfEjU6IUqpqcjWcDzeDdNOySiVPDU_ZU5knJmzRCF8fcjFtPsaA4R7vdIEbDuOur15FXSvE8FvVSjP_49OH6hBYqoSUAslN3FMfbO6Z8YfCIY4tSOB2I6ahQ_x4ZWFWglC3w5mK-_4iX81bqi95eV4RUKefUuHhQDXtWhrSgIEC0YiluMvA4TnaJdLq_tWXIc4_Tq_KfpkvI004ONKgU7EAMEr1wZ4aDcJV2yf22gQ1sCSig6EGSTmmzDuEPsYiyd4NhidRZJP4HiiQh-hePBQsgcSgYGvz9wC6n57ufYKh2wm_Ti3Q
&requested_token_use=on_behalf_of
&scope=openid
```

## <a name="service-to-service-access-token-response"></a>Szolgáltatás-hozzáférési jogkivonat válasz szolgáltatás
Sikerességi válasz egy JSON OAuth 2.0 válasz a következő paraméterekkel.

| Paraméter | Leírás |
| --- | --- |
| token_type |A jogkivonat típusa értékét jelöli. Az egyetlen típus, amely az Azure AD által támogatott **tulajdonosi**. Tulajdonosi jogkivonatok kapcsolatos további információkért tekintse meg a [OAuth 2.0 hitelesítési keretrendszer: tulajdonosi jogkivonat használati (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt). |
| scope |Hozzáférést biztosít a jogkivonat körét. |
| expires_in |Mennyi ideig a hozzáférési jogkivonat érvénytelen (másodpercben). |
| expires_on |A hozzáférési jogkivonat lejárati idejének. A dátum jelzi másodpercben a 1970-01-01T0:0:0Z UTC, amíg az elévülési időt. Ezt az értéket a gyorsítótárazott jogkivonatok élettartama meghatározására szolgál. |
| erőforrás |A fogadó szolgáltatást (védett erőforrás) App ID URI. |
| access_token |A kért hozzáférési jogkivonat. A hívó szolgáltatás a fogadó szolgáltatással való hitelesítésre szolgáló használhatja a token. |
| id_token |A megadott azonosítóhoz jogkivonat. A hívó szolgáltatás ezzel a felhasználó személyazonosságát, és a felhasználói munkamenet elindításához. |
| refresh_token |A kért hozzáférési jogkivonat frissítési jogkivonat. A hívó szolgáltatás egy új hozzáférési jogkivonat lekérni az aktuális jogkivonat lejárata után is használhatja a token. |

### <a name="success-response-example"></a>Sikerességi válasz – példa
A következő példa bemutatja a sikeres válasz egy hozzáférési kérelmet a jogkivonat a https://graph.windows.net webes API-t.

```
{
    "token_type":"Bearer",
    "scope":"User.Read",
    "expires_in":"43482",
    "ext_expires_in":"302683",
    "expires_on":"1493466951",
    "not_before":"1493423168",
    "resource":"https://graph.windows.net",
    "access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRvd3MubmV0IiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiLyIsImlhdCI6MTQ5MzQyMzE2OCwibmJmIjoxNDkzNDIzMTY4LCJleHAiOjE0OTM0NjY5NTEsImFjciI6IjEiLCJhaW8iOiJBU1FBMi84REFBQUE1NnZGVmp0WlNjNWdBVWwrY1Z0VFpyM0VvV2NvZEoveWV1S2ZqcTZRdC9NPSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJhcHBpZGFjciI6IjEiLCJlX2V4cCI6MzAyNjgzLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJwdWlkIjoiMTAwMzNGRkZBMTJFRDdGRSIsInNjcCI6IlVzZXIuUmVhZCIsInN1YiI6IjNKTUlaSWJlYTc1R2hfWHdDN2ZzX0JDc3kxa1l1ekZKLTUyVm1Zd0JuM3ciLCJ0aWQiOiIyNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IiLCJ1bmlxdWVfbmFtZSI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXBuIjoibmF2eWFAZGRvYmFsaWFub3V0bG9vay5vbm1pY3Jvc29mdC5jb20iLCJ1dGkiOiJ4Q3dmemhhLVAwV0pRT0x4Q0dnS0FBIiwidmVyIjoiMS4wIn0.cqmUVjfVbqWsxJLUI1Z4FRx1mNQAHP-L0F4EMN09r8FY9bIKeO-0q1eTdP11Nkj_k4BmtaZsTcK_mUygdMqEp9AfyVyA1HYvokcgGCW_Z6DMlVGqlIU4ssEkL9abgl1REHElPhpwBFFBBenOk9iHddD1GddTn6vJbKC3qAaNM5VarjSPu50bVvCrqKNvFixTb5bbdnSz-Qr6n6ACiEimiI1aNOPR2DeKUyWBPaQcU5EAK0ef5IsVJC1yaYDlAcUYIILMDLCD9ebjsy0t9pj_7lvjzUSrbMdSCCdzCqez_MSNxrk1Nu9AecugkBYp3UVUZOIyythVrj6-sVvLZKUutQ",
    "refresh_token":"AQABAAAAAABnfiG-mA6NTae7CdWW7QfdjKGu9-t1scy_TDEmLi4eLQMjJGt_nAoVu6A4oSu1KsRiz8XyQIPKQxSGfbf2FoSK-hm2K8TYzbJuswYusQpJaHUQnSqEvdaCeFuqXHBv84wjFhuanzF9dQZB_Ng5za9xKlUENrNtlq9XuLNVKzxEyeUM7JyxzdY7JiEphWImwgOYf6II316d0Z6-H3oYsFezf4Xsjz-MOBYEov0P64UaB5nJMvDyApV-NWpgklLASfNoSPGb67Bc02aFRZrm4kLk-xTl6eKE6hSo0XU2z2t70stFJDxvNQobnvNHrAmBaHWPAcC3FGwFnBOojpZB2tzG1gLEbmdROVDp8kHEYAwnRK947Py12fJNKExUdN0njmXrKxNZ_fEM33LHW1Tf4kMX_GvNmbWHtBnIyG0w5emb-b54ef5AwV5_tGUeivTCCysgucEc-S7G8Cz0xNJ_BOiM_4bAv9iFmrm9STkltpz0-Tftg8WKmaJiC0xXj6uTf4ZkX79mJJIuuM7XP4ARIcLpkktyg2Iym9jcZqymRkGH2Rm9sxBwC4eeZXM7M5a7TJ-5CqOdfuE3sBPq40RdEWMFLcrAzFvP0VDR8NKHIrPR1AcUruat9DETmTNJukdlJN3O41nWdZOVoJM-uKN3uz2wQ2Ld1z0Mb9_6YfMox9KTJNzRzcL52r4V_y3kB6ekaOZ9wQ3HxGBQ4zFt-2U0mSszIAA",
    "id_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC8yNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IvIiwiaWF0IjoxNDkzNDIzMTY4LCJuYmYiOjE0OTM0MjMxNjgsImV4cCI6MTQ5MzQ2Njk1MSwiYW1yIjpbInB3ZCJdLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXRpIjoieEN3ZnpoYS1QMFdKUU9MeENHZ0tBQSIsInZlciI6IjEuMCJ9."
}
```

### <a name="error-response-example"></a>Hiba válasz – példa
Egy hiba történt egy válasz próbál szerezni hozzáférési tokent az alsóbb rétegbeli API-hoz, például a többtényezős hitelesítés beállítása a feltételes hozzáférési szabályzatot az alsóbb rétegbeli API-e az Azure AD-token végpontja érték érkezett vissza. A középső rétegbeli szolgáltatás kell surface Ez a hiba az ügyfélalkalmazás, így az ügyfélalkalmazás biztosítani tudja teljesíteni a feltételes hozzáférési házirend a felhasználói beavatkozást.

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

## <a name="use-the-access-token-to-access-the-secured-resource"></a>A védett erőforrások eléréséhez használja a hozzáférési jogkivonat
Most a középső rétegbeli szolgáltatás használhatja a hitelesített kérelmek legyen az alsóbb rétegbeli webes API-t úgy, hogy a jogkivonat fenti szerzett jogkivonat a `Authorization` fejléc.

### <a name="example"></a>Példa
```
GET /me?api-version=2013-11-08 HTTP/1.1
Host: graph.windows.net
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRvd3MubmV0IiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiLyIsImlhdCI6MTQ5MzQyMzE2OCwibmJmIjoxNDkzNDIzMTY4LCJleHAiOjE0OTM0NjY5NTEsImFjciI6IjEiLCJhaW8iOiJBU1FBMi84REFBQUE1NnZGVmp0WlNjNWdBVWwrY1Z0VFpyM0VvV2NvZEoveWV1S2ZqcTZRdC9NPSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJhcHBpZGFjciI6IjEiLCJlX2V4cCI6MzAyNjgzLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJwdWlkIjoiMTAwMzNGRkZBMTJFRDdGRSIsInNjcCI6IlVzZXIuUmVhZCIsInN1YiI6IjNKTUlaSWJlYTc1R2hfWHdDN2ZzX0JDc3kxa1l1ekZKLTUyVm1Zd0JuM3ciLCJ0aWQiOiIyNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IiLCJ1bmlxdWVfbmFtZSI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXBuIjoibmF2eWFAZGRvYmFsaWFub3V0bG9vay5vbm1pY3Jvc29mdC5jb20iLCJ1dGkiOiJ4Q3dmemhhLVAwV0pRT0x4Q0dnS0FBIiwidmVyIjoiMS4wIn0.cqmUVjfVbqWsxJLUI1Z4FRx1mNQAHP-L0F4EMN09r8FY9bIKeO-0q1eTdP11Nkj_k4BmtaZsTcK_mUygdMqEp9AfyVyA1HYvokcgGCW_Z6DMlVGqlIU4ssEkL9abgl1REHElPhpwBFFBBenOk9iHddD1GddTn6vJbKC3qAaNM5VarjSPu50bVvCrqKNvFixTb5bbdnSz-Qr6n6ACiEimiI1aNOPR2DeKUyWBPaQcU5EAK0ef5IsVJC1yaYDlAcUYIILMDLCD9ebjsy0t9pj_7lvjzUSrbMdSCCdzCqez_MSNxrk1Nu9AecugkBYp3UVUZOIyythVrj6-sVvLZKUutQ
```
## <a name="client-limitations"></a>A Client korlátozásai
A helyettesítő karakteres válasz URL-címek nyilvános ügyfelek nem használhatják az `id_token` OBO folyamatok. Bizalmas ügyfél azonban továbbra is beválthatja hozzáférési jogkivonatok az implicit grant flow keresztül beszerzett, még akkor is, ha a nyilvános ügyfél van egy helyettesítő karakteres átirányítási URI regisztrálva.

## <a name="next-steps"></a>További lépések
További információk az OAuth 2.0 protokollt, és úgy is hajtsa végre a szolgáltatások közötti hitelesítési ügyfél hitelesítő adataival.
* [OAuth 2.0 ügyfél hitelesítő adatok megadása az Azure AD használatával szolgáltatás hitelesítési szolgáltatás](active-directory-protocols-oauth-service-to-service.md)
* [OAuth 2.0 Azure AD-ben](active-directory-protocols-oauth-code.md)
