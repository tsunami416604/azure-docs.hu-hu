---
title: Azure AD-OAuth2.0--meghatalmazásos draft specifikáció használatával szolgáltatások közötti hitelesítés |} A Microsoft Docs
description: Ez a cikk ismerteti, hogyan használható a HTTP-üzenetek megvalósításához a szolgáltatások közötti hitelesítés a OAuth2.0--meghatalmazásos folyamat használatával.
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
ms.reviewer: hirsin, nacanuma
ms.custom: aaddev
ms.openlocfilehash: ce29c6a9df49721ca23f84da3f1c97bcc83ab4a7
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39581465"
---
# <a name="service-to-service-calls-using-delegated-user-identity-in-the-on-behalf-of-flow"></a>Szolgáltatások közötti hívások használatával delegált felhasználói identitás az On-meghatalmazásos folyamat
Az OAuth 2.0-s alapú meghatalmazásos (OBO) folyamat szolgál a használati eset, ahol az alkalmazás meghívja a szolgáltatás/webes API-kat, amelynek be kell meghívni egy másik szolgáltatás/webes API-t. A cél pedig propagálása a delegált felhasználó identitása és a kérelem láncot engedélyeket. A középső rétegű szolgáltatás hitelesített kéréseket küld az alárendelt szolgáltatás kell biztonságossá tételéhez egy hozzáférési jogkivonatot az Azure Active Directory (Azure AD), a felhasználó nevében.

> [!IMPORTANT]
> Nyilvános ügyfelek, amelyek a [OAuth 2.0 típusú implicit engedélyezés](v1-oauth2-implicit-grant-flow.md) OBO folyamat nem használható. Ezek az ügyfelek kell továbbítani a hozzáférési jogkivonat egy középső rétegű bizalmas ügyfél OBO folyamatok végrehajtásához. További információ arról, hogy mely ügyfelek OBO hívásokat hajthat végre: [ügyfél korlátozások](#client-limitations).

## <a name="on-behalf-of-flow-diagram"></a>A alapú meghatalmazásos folyamat diagramja
Tegyük fel, hogy a felhasználó hitelesítése egy alkalmazást a a a [OAuth 2.0 hitelesítési kód adja meg a folyamat](v1-protocols-oauth-code.md). Ezen a ponton az alkalmazás-hozzáférési token (jogkivonat A) a felhasználói jogcímek és az beleegyezése eléréséhez a középső rétegbeli webes API-k (API-t A) rendelkezik. Most API-t A kell egy hitelesített kéréseknél az alsóbb rétegbeli webes API-hoz (API-t, B).

A következő lépések az On-meghatalmazásos folyamat jelent, és az alábbi ábra segítségével vannak írva.

![OAuth2.0--meghatalmazásos folyamat](./media/v1-oauth2-on-behalf-of-flow/active-directory-protocols-oauth-on-behalf-of-flow.png)


1. Az ügyfélalkalmazás kérést küld egy API-t a jogkivonattal A.
2. API-t A hitelesíti magát az Azure AD-kiállítási végpont, és b API eléréséhez tokent kér
3. Az Azure AD-kiállítási végpont érvényesíti az API A jogkivonat A hitelesítő adatokat, és problémák a hozzáférési jogkivonat (token B) API-t a b.
4. A jogkivonat B API b. a kérelem az engedélyezési fejléc értéke
5. B API által visszaadott adatok a biztonságos erőforrás

## <a name="register-the-application-and-service-in-azure-ad"></a>Az alkalmazás és szolgáltatás regisztrálása az Azure ad-ben
Az ügyfélalkalmazás és a középső rétegű szolgáltatást is regisztrálni az Azure ad-ben.
### <a name="register-the-middle-tier-service"></a>Regisztrálja a középső rétegű szolgáltatást
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A fiókjában, majd a kattintson a felső sávon a **Directory** menüben válassza ki az Active Directory-bérlőt, az alkalmazásokat regisztrálni szeretne.
3. Kattintson a **további szolgáltatások** a bal oldali navigációs, és válassza a **Azure Active Directory**.
4. Kattintson a **alkalmazásregisztrációk** válassza **új alkalmazásregisztráció**.
5. Adjon egy rövid nevet az alkalmazáshoz, és válassza ki az alkalmazás típusaként. Az alkalmazás típusa-készlet alapján a bejelentkezési URL-cím vagy átirányítási URL-CÍMÉT az alap URL-címre. Kattintson a **létrehozás** hozhat létre az alkalmazást.
6. Miközben továbbra is az Azure Portalon, válassza ki az alkalmazást, majd kattintson a **beállítások**. A beállítások menüből **kulcsok** és kulcs hozzáadása – adja meg, akár 1 vagy 2 év kulcs időtartam. Mentése ezen a lapon, a kulcs értéke jelenik meg, másolja és mentse az értéket egy biztonságos helyre – ezt a kulcsot később, az alkalmazás beállításainak konfigurálása a megvalósítás - kell ezt a kulcsértéket nem lesz újból megjelenített, sem lekérhető más módon , ezért jegyezze fel azt, amint az Azure Portalon látható.

### <a name="register-the-client-application"></a>Az ügyféloldali alkalmazás regisztrálása
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A fiókjában, majd a kattintson a felső sávon a **Directory** menüben válassza ki az Active Directory-bérlőt, az alkalmazásokat regisztrálni szeretne.
3. Kattintson a **további szolgáltatások** a bal oldali navigációs, és válassza a **Azure Active Directory**.
4. Kattintson a **alkalmazásregisztrációk** válassza **új alkalmazásregisztráció**.
5. Adjon egy rövid nevet az alkalmazáshoz, és válassza ki az alkalmazás típusaként. Az alkalmazás típusa-készlet alapján a bejelentkezési URL-cím vagy átirányítási URL-CÍMÉT az alap URL-címre. Kattintson a **létrehozás** hozhat létre az alkalmazást.
6. Engedélyek konfigurálása az alkalmazás - Beállítások menüjében, válassza a **szükséges engedélyek** területén kattintson a **Hozzáadás**, majd **API kiválasztása**, és adja meg a a középső rétegű szolgáltatás be a szövegmezőbe. Ezután kattintson a **engedélyek kiválasztása** , és válassza ki a(z) hozzáférés *szolgáltatásnév*".

### <a name="configure-known-client-applications"></a>Ismert ügyfélalkalmazások konfigurálása
Ebben a forgatókönyvben a középső rétegű szolgáltatás rendelkezik beszerzése a felhasználói beleegyezés az alsóbb rétegbeli API eléréséhez nincs szükség felhasználói beavatkozásra. Ezért az alsóbb rétegbeli API-hoz való hozzáférést biztosít lehetőséget kell bemutatni előre, a hitelesítés során. lépés: a hozzájárulási részét.
Ennek érdekében kövesse az alábbi lépéseket az Azure ad-ben a középső rétegű szolgáltatást, amely egyesíti a jóváhagyás szükséges, egy egyetlen párbeszédpanel az ügyfél és a középső rétegbeli regisztrációját az ügyfél alkalmazásregisztráció explicit módon kötődni.
1. Keresse meg a középső rétegű szolgáltatás regisztrációjához, és kattintson a **Manifest** megnyitásához az alkalmazásjegyzék-szerkesztőben.
2. A jegyzékfájlban, keresse meg a `knownClientApplications` tulajdonság tömböt, és adja hozzá az ügyfél-azonosító, az ügyfélalkalmazás egy elemet.
3. A jegyzékfájl mentéséhez kattintson a Mentés gombra.

## <a name="service-to-service-access-token-request"></a>A szolgáltatás a szolgáltatás-hozzáférési jogkivonat kérése
Egy hozzáférési jogkivonatot kér, győződjön meg arról, egy HTTP POST a bérlő-specifikus az Azure AD-végpont a következő paraméterekkel.

```
https://login.microsoftonline.com/<tenant>/oauth2/token
```
Nincsenek két esetben attól függően, hogy az ügyfélalkalmazás úgy dönt, hogy egy közös titkos kulcsot, vagy a tanúsítvány védi.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Először. eset: egy közös titkos kulcsot a hozzáférési jogkivonat kérése
A közös titkos kulcsot használja, amikor egy szolgáltatások közötti hozzáférési jogkivonat kérése a következő paramétereket tartalmaz:

| Paraméter |  | Leírás |
| --- | --- | --- |
| grant_type |szükséges | A jogkivonat kérése típusa. A kérést a jwt-t, az értéknek kell lennie **urn: ietf:params:oauth:grant-típus: jwt-tulajdonosi**. |
| assertion |szükséges | A kérésben használt token értékét. |
| client_id |szükséges | Az Azure AD-regisztrációja során a hívó szolgáltatáshoz rendelt alkalmazás azonosítója. Az Azure felügyeleti portálján az alkalmazás Azonosítójának megkereséséhez kattintson **Active Directory**, és kattintson arra a címtárra, majd kattintson az alkalmazás nevét. |
| client_secret |szükséges | A kulcsot az Azure AD-ben regisztrált a hívó szolgáltatás. Ez az érték rendelkezik lett jegyezni a regisztrációs idején. |
| erőforrás |szükséges | Az Alkalmazásazonosító URI-t a fogadó szolgáltatás (védett erőforrás). Az Alkalmazásazonosító URI-t, az Azure felügyeleti portálján találja, kattintson a **Active Directory**kattintson arra a címtárra, kattintson az alkalmazás nevét, kattintson **minden beállítás** majd **tulajdonságai**. |
| requested_token_use |szükséges | Itt adhatja meg, hogyan kell feldolgozni a kérelmet. Az On-meghatalmazásos folyamat, az értéke nem lehet **on_behalf_of**. |
| scope |szükséges | Szóközzel elválasztott a jogkivonat kérése hatókörök listája. Az OpenID Connect, a hatókör **openid** meg kell adni.|

#### <a name="example"></a>Példa
A következő HTTP POST kéréseket a hozzáférési jogkivonatot a https://graph.windows.net webes API-t. A `client_id` azonosítja a szolgáltatást, amely a hozzáférési jogkivonatot kér.

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

### <a name="second-case-access-token-request-with-a-certificate"></a>A második eset: a tanúsítványhoz a hozzáférési jogkivonat kérése
A service to service hozzáférési jogkivonat kérése tanúsítvánnyal az alábbi paramétereket tartalmazza:

| Paraméter |  | Leírás |
| --- | --- | --- |
| grant_type |szükséges | A jogkivonat kérése típusa. A kérést a jwt-t, az értéknek kell lennie **urn: ietf:params:oauth:grant-típus: jwt-tulajdonosi**. |
| assertion |szükséges | A kérésben használt token értékét. |
| client_id |szükséges | Az Azure AD-regisztrációja során a hívó szolgáltatáshoz rendelt alkalmazás azonosítója. Az Azure felügyeleti portálján az alkalmazás Azonosítójának megkereséséhez kattintson **Active Directory**, és kattintson arra a címtárra, majd kattintson az alkalmazás nevét. |
| client_assertion_type |szükséges |Az értéknek kell lennie `urn:ietf:params:oauth:client-assertion-type:jwt-bearer` |
| client_assertion |szükséges | Egy helyességi feltétel (egy JSON Web Token) létrehozására és aláírására a tanúsítványt igénylő regisztrált hitelesítő adatként az alkalmazáshoz. További információ [hitelesítő tanúsítvány](active-directory-certificate-credentials.md) megtudhatja, hogyan regisztrálhat a tanúsítvány és a helyességi feltétel formátumát.|
| erőforrás |szükséges | Az Alkalmazásazonosító URI-t a fogadó szolgáltatás (védett erőforrás). Az Alkalmazásazonosító URI-t, az Azure felügyeleti portálján találja, kattintson a **Active Directory**kattintson arra a címtárra, kattintson az alkalmazás nevét, kattintson **minden beállítás** majd **tulajdonságai**. |
| requested_token_use |szükséges | Itt adhatja meg, hogyan kell feldolgozni a kérelmet. Az On-meghatalmazásos folyamat, az értéke nem lehet **on_behalf_of**. |
| scope |szükséges | Szóközzel elválasztott a jogkivonat kérése hatókörök listája. Az OpenID Connect, a hatókör **openid** meg kell adni.|

Figyelje meg, hogy paraméterei szinte teljesen megegyezik a kérés által közös titkos kulcsot is azzal a különbséggel, hogy a titkos ügyfélkódot paraméter váltotta fel két paramétert: egy client_assertion_type és client_assertion.

#### <a name="example"></a>Példa
A következő HTTP POST kéréseket a hozzáférési jogkivonatot a https://graph.windows.net webes API-tanúsítvánnyal. A `client_id` azonosítja a szolgáltatást, amely a hozzáférési jogkivonatot kér.

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

## <a name="service-to-service-access-token-response"></a>Szolgáltatás-hozzáférési token válasz szolgáltatás
Sikerességi válasz egy JSON OAuth 2.0-válaszban az alábbi paraméterekkel.

| Paraméter | Leírás |
| --- | --- |
| token_type |Typ tokenu értékét jelöli. Az egyetlen típus, amely az Azure AD által támogatott **tulajdonosi**. További információ a tulajdonosi jogkivonatokat: a [OAuth 2.0 engedélyezési keretrendszer: tulajdonosi jogkivonat-használat (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt). |
| scope |Megadja a hozzáférést a jogkivonat hatókörét. |
| expires_in |Mennyi ideig a hozzáférési jogkivonat érvénytelen (másodpercben). |
| expires_on |A hozzáférési jogkivonat lejáratának időpontja. A dátum jelenik meg a másodpercek számát, 1970-01-01T0:0:0Z UTC a lejárati időpontig. Ez az érték a gyorsítótárazott jogkivonatok élettartama meghatározására szolgál. |
| erőforrás |Az Alkalmazásazonosító URI-t a fogadó szolgáltatás (védett erőforrás). |
| access_token |A kért hozzáférési jogkivonatot. A hívó szolgáltatás használhatja ezt a jogkivonatot a fogadó szolgáltatással való hitelesítésre. |
| id_token |A kért azonosító jogkivonat. A hívó szolgáltatás ezzel a a felhasználó identitását, és megkezdheti a felhasználói munkamenetet. |
| refresh_token |A frissítési jogkivonat a kért hozzáférési jogkivonat. A hívó szolgáltatás a jogkivonat használatával új hozzáférési jogkivonat kérése a jelenlegi hozzáférési jogkivonat lejárata után. |

### <a name="success-response-example"></a>Példa a sikeres válasz
Az alábbi példa bemutatja egy hozzáférési kérelem sikeres válasz token a https://graph.windows.net webes API-t.

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
GET /me?api-version=2013-11-08 HTTP/1.1
Host: graph.windows.net
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRvd3MubmV0IiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiLyIsImlhdCI6MTQ5MzQyMzE2OCwibmJmIjoxNDkzNDIzMTY4LCJleHAiOjE0OTM0NjY5NTEsImFjciI6IjEiLCJhaW8iOiJBU1FBMi84REFBQUE1NnZGVmp0WlNjNWdBVWwrY1Z0VFpyM0VvV2NvZEoveWV1S2ZqcTZRdC9NPSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJhcHBpZGFjciI6IjEiLCJlX2V4cCI6MzAyNjgzLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJwdWlkIjoiMTAwMzNGRkZBMTJFRDdGRSIsInNjcCI6IlVzZXIuUmVhZCIsInN1YiI6IjNKTUlaSWJlYTc1R2hfWHdDN2ZzX0JDc3kxa1l1ekZKLTUyVm1Zd0JuM3ciLCJ0aWQiOiIyNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IiLCJ1bmlxdWVfbmFtZSI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXBuIjoibmF2eWFAZGRvYmFsaWFub3V0bG9vay5vbm1pY3Jvc29mdC5jb20iLCJ1dGkiOiJ4Q3dmemhhLVAwV0pRT0x4Q0dnS0FBIiwidmVyIjoiMS4wIn0.cqmUVjfVbqWsxJLUI1Z4FRx1mNQAHP-L0F4EMN09r8FY9bIKeO-0q1eTdP11Nkj_k4BmtaZsTcK_mUygdMqEp9AfyVyA1HYvokcgGCW_Z6DMlVGqlIU4ssEkL9abgl1REHElPhpwBFFBBenOk9iHddD1GddTn6vJbKC3qAaNM5VarjSPu50bVvCrqKNvFixTb5bbdnSz-Qr6n6ACiEimiI1aNOPR2DeKUyWBPaQcU5EAK0ef5IsVJC1yaYDlAcUYIILMDLCD9ebjsy0t9pj_7lvjzUSrbMdSCCdzCqez_MSNxrk1Nu9AecugkBYp3UVUZOIyythVrj6-sVvLZKUutQ
```
## <a name="client-limitations"></a>Ügyfél-korlátozások
Helyettesítő karaktert tartalmazó válasz URL-címekkel rendelkező nyilvános ügyfelek nem használhatják egy `id_token` OBO folyamatokhoz. Bizalmas ügyfél azonban továbbra is beválthatja hozzáférési jogkivonatok az implicit engedélyezési folyamat keresztül beszerzett, még akkor is, ha a nyilvános ügyfél rendelkezik egy helyettesítő karaktert tartalmazó átirányítási URI-t regisztrált.

## <a name="next-steps"></a>További lépések
További információ az OAuth 2.0 protokollt és a szolgáltatások közötti hitelesítés ügyfél-hitelesítő adatok használatával egy másik módszerét.
* [OAuth 2.0 ügyfélhitelesítő adatok az Azure AD használatával service hitelesítési szolgáltatás](v1-oauth2-client-creds-grant-flow.md)
* [OAuth 2.0, Azure AD-ben](v1-protocols-oauth-code.md)
