---
title: Az Azure Active Directory service-to-service-hitelesítés a OAuth2.0--meghatalmazásos draft specifikáció használó |} A Microsoft Docs
description: Ez a cikk ismerteti a HTTP-üzenetek használata a szolgáltatások közötti hitelesítés a a OAuth2.0--meghatalmazásos folyamat végrehajtásához.
services: active-directory
documentationcenter: .net
author: navyasric
manager: mtillman
editor: ''
ms.assetid: 09f6f318-e88b-4024-9ee1-e7f09fb19a82
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2017
ms.author: celested
ms.reviewer: hirsin, nacanuma
ms.custom: aaddev
ms.openlocfilehash: 3c2953d44587d72517c6f619ee9c9f05aabff186
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2019
ms.locfileid: "55094376"
---
# <a name="service-to-service-calls-that-use-delegated-user-identity-in-the-on-behalf-of-flow"></a>Szolgáltatások közötti hívások használat meghatalmazott felhasználói identitás az On-meghatalmazásos folyamat

[!INCLUDE [active-directory-develop-applies-v1](../../../includes/active-directory-develop-applies-v1.md)]

Az OAuth 2.0-alapú meghatalmazásos (OBO) a folyamat lehetővé teszi, hogy egy alkalmazás, amely egy szolgáltatás vagy a webes API-t adja át a felhasználói hitelesítés és egy másik szolgáltatás vagy a webes API-t hív meg. OBO folyamat propagálása zajlik, a meghatalmazott felhasználó identitása és a kérelem láncot engedélyeket. A középső rétegű szolgáltatás hitelesített kéréseket küld az alárendelt szolgáltatás azt kell biztonságos hozzáférési jogkivonatot az Azure Active Directory (Azure AD) a felhasználó nevében.

> [!IMPORTANT]
> 2018 május egy `id_token` az On-meghatalmazásos folyamat nem használható.  Egylapos alkalmazások (gyógyfürdők) át kell adnia egy hozzáférési jogkivonatot egy középső rétegű bizalmas ügyfél OBO folyamatok végrehajtásához. Az ügyfelek által végrehajtható műveleteket a alapú meghatalmazásos hívások kapcsolatos további részletekért lásd: [korlátozások](#client-limitations).

## <a name="on-behalf-of-flow-diagram"></a>A alapú meghatalmazásos folyamat diagramja

OBO folyamat elindul, a felhasználó hitelesítését az alkalmazást a [OAuth 2.0 hitelesítési kód adja meg a folyamat](v1-protocols-oauth-code.md). Ezen a ponton az alkalmazás által a középső rétegbeli webes API-hoz (API-t A) a felhasználói jogcímek és jóváhagyás API A. eléréséhez tartalmazó hozzáférési token (jogkivonat A) Ezután API egy kérést egy hitelesített az alsóbb rétegbeli webes API-hoz (API-t, B).

Ezeket a lépéseket az On-meghatalmazásos folyamat alkotják: ![OAuth2.0--meghatalmazásos folyamat](./media/v1-oauth2-on-behalf-of-flow/active-directory-protocols-oauth-on-behalf-of-flow.png)

1. Az ügyfélalkalmazás kérést küld egy API-t a jogkivonattal A.
1. API-t A hitelesíti magát az Azure AD-kiállítási végpont, és b API eléréséhez tokent kér
1. Az Azure AD-kiállítási végpont érvényesíti az API A jogkivonat A hitelesítő adatokat, és problémák a hozzáférési jogkivonat (token B) API-t a b.
1. A kérelem API-b token B az engedélyezési fejléc tartalmazza.
1. API-B a védett erőforrás adatait adja vissza.

>[!NOTE]
>A célközönség jogcím a hozzáférési jogkivonat kérése egy tokent egy alsóbb rétegbeli szolgáltatáshoz használt OBO kérvényező szolgáltatás Azonosítójának kell lennie. A jogkivonat is kell aláírni az Azure Active Directory globális aláírási kulcs (amely az alapértelmezett érték, az alkalmazások keresztül regisztrált **alkalmazásregisztrációk** a portálon).

## <a name="register-the-application-and-service-in-azure-ad"></a>Az alkalmazás és szolgáltatás regisztrálása az Azure ad-ben

A középső rétegű szolgáltatás és az ügyfélalkalmazás regisztrálja az Azure ad-ben.

### <a name="register-the-middle-tier-service"></a>Regisztrálja a középső rétegű szolgáltatást

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Az a felső menüsávon válassza ki a fiókját, és keresse meg a **Directory** listát használva jelölje ki az Active Directory-bérlő, az alkalmazás.
1. Válassza ki **további szolgáltatások** a bal oldali panelen, majd **Azure Active Directory**.
1. Válassza ki **alkalmazásregisztrációk** , majd **új alkalmazásregisztráció**.
1. Adjon egy rövid nevet az alkalmazáshoz, és válassza ki az alkalmazás típusaként.
    1. Attól függően, hogy az alkalmazás típusát, állítsa be a bejelentkezési URL-cím vagy átirányítási URL-CÍMÉT az alap URL-címre.
    1. Válassza ki **létrehozás** hozhat létre az alkalmazást.
1. Az Azure Portalon való kilépés előtt hozzon létre egy ügyfélkulcsot.
    1. Az Azure Portalon válassza az alkalmazást, majd válasszon **beállítások**.
    1. Válassza ki **kulcsok** a beállítások menüben, és adjon hozzá egy kulcsot az vagy egy vagy két év a kulcs időtartam.
    1. Ez a lap mentésekor az Azure Portalon a kulcs értékét jeleníti meg. Másolja és mentse a kulcs értékét egy biztonságos helyre.

    > [!IMPORTANT]
    > Szüksége lesz a kulcs az Alkalmazásbeállítások konfigurálása a végrehajtása során. A kulcs értéke nem jelenik meg újra, és lekérhető más módon nem. Jegyezze fel, amint az Azure Portalon látható.

### <a name="register-the-client-application"></a>Az ügyféloldali alkalmazás regisztrálása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Az a felső menüsávon válassza ki a fiókját, és keresse meg a **Directory** listát használva jelölje ki az Active Directory-bérlő, az alkalmazás.
1. Válassza ki **további szolgáltatások** a bal oldali panelen, majd **Azure Active Directory**.
1. Válassza ki **alkalmazásregisztrációk** , majd **új alkalmazásregisztráció**.
1. Adjon egy rövid nevet az alkalmazáshoz, és válassza ki az alkalmazás típusaként.
   1. Attól függően, hogy az alkalmazás típusát, állítsa be a bejelentkezési URL-cím vagy átirányítási URL-CÍMÉT az alap URL-címre.
   1. Válassza ki **létrehozás** hozhat létre az alkalmazást.
1. Az alkalmazás-engedélyek konfigurálása.
   1. A beállítások menüben válassza ki a **szükséges engedélyek** szakaszt, és válassza ki **Hozzáadás** és **API kiválasztása**.
   1. A szövegmezőbe írja be a középső rétegű szolgáltatás nevét.
   1. Válasszon **engedélyek kiválasztása** , majd **hozzáférés szolgáltatásnév**.

### <a name="configure-known-client-applications"></a>Ismert ügyfélalkalmazások konfigurálása

Ebben a forgatókönyvben a középső rétegű szolgáltatást kell beszerezni a felhasználó beleegyezésével hozzáférni az alsóbb rétegbeli API-hoz felhasználói interakció nélkül. Az alsóbb rétegbeli API-hoz való hozzáférést biztosít arra, hitelesítés során a hozzájárulási lépés részeként előtér fel kell bemutatni.

Kövesse az alábbi lépéseket az Azure ad-ben a középső rétegű szolgáltatást a regisztráció az ügyfél alkalmazásregisztráció explicit módon kötődni. Ez a művelet a jóváhagyás szükséges, egy egyetlen párbeszédpanel az ügyfél és a középső rétegbeli egyesíti.

1. Nyissa meg a középső rétegű szolgáltatás regisztrációs, és válassza ki **Manifest** megnyitásához az alkalmazásjegyzék-szerkesztőben.
1. Keresse meg a `knownClientApplications` tulajdonság tömböt, és adja hozzá az ügyfél-azonosító, az ügyfélalkalmazás elemként.
1. A jegyzékfájl mentése kiválasztásával **mentése**.

## <a name="service-to-service-access-token-request"></a>Szolgáltatások közötti hozzáférési jogkivonat kérése

Egy hozzáférési jogkivonatot kér, győződjön meg arról, egy HTTP POST a bérlő-specifikus az Azure AD-végpont a következő paraméterekkel:

```
https://login.microsoftonline.com/<tenant>/oauth2/token
```

Az ügyfélalkalmazás egy közös titkos kulcsot vagy a tanúsítvány biztonságát.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Első eset: A közös titkos kulcsot a hozzáférési jogkivonat kérése

A közös titkos kulcsot használja, amikor egy szolgáltatások közötti hozzáférési jogkivonat kérése a következő paramétereket tartalmaz:

| Paraméter |  | Leírás |
| --- | --- | --- |
| grant_type |szükséges | A jogkivonat kérése típusa. OBO kérelmet egy JSON webes jogkivonat (JWT) használja, így az az érték lehet **urn: ietf:params:oauth:grant-típus: jwt-tulajdonosi**. |
| assertion |szükséges | A hozzáférési jogkivonatot, amelyet a kérés értékét. |
| client_id |szükséges | Az Azure AD-regisztrációja során a hívó szolgáltatáshoz rendelt alkalmazás azonosítója. Az Azure Portalon az alkalmazás Azonosítójának megkereséséhez válassza ki a **Active Directory**, jelölje ki azt a könyvtárat, és válassza ki az alkalmazás nevét. |
| client_secret |szükséges | A kulcsot az Azure AD-ben regisztrált a hívó szolgáltatás. Ez az érték rendelkezik lett jegyezni a regisztrációs idején. |
| erőforrás |szükséges | Az app ID URI-ját a fogadó szolgáltatást (védett erőforrás). Az Azure Portalon az alkalmazás Alkalmazásazonosító URI megkereséséhez válassza ki a **Active Directory** , és jelölje ki azt a könyvtárat. Válassza ki az alkalmazás nevét, válassza a **minden beállítás**, majd válassza ki **tulajdonságok**. |
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

### <a name="second-case-access-token-request-with-a-certificate"></a>Második eset: Hozzáférési jogkivonat kérése tanúsítvánnyal

A service to service hozzáférési jogkivonat kérése tanúsítvánnyal az alábbi paramétereket tartalmazza:

| Paraméter |  | Leírás |
| --- | --- | --- |
| grant_type |szükséges | A jogkivonat kérése típusa. OBO kérelmet a JWT-hozzáférési tokent használ, így a értéke nem lehet **urn: ietf:params:oauth:grant-típus: jwt-tulajdonosi**. |
| assertion |szükséges | A kérésben használt token értékét. |
| client_id |szükséges | Az Azure AD-regisztrációja során a hívó szolgáltatáshoz rendelt alkalmazás azonosítója. Az Azure Portalon az alkalmazás Azonosítójának megkereséséhez válassza ki a **Active Directory**, jelölje ki azt a könyvtárat, és válassza ki az alkalmazás nevét. |
| client_assertion_type |szükséges |Az értéknek kell lennie `urn:ietf:params:oauth:client-assertion-type:jwt-bearer` |
| client_assertion |szükséges | A JSON Web Token létrehozott és a tanúsítvány aláírására regisztrált hitelesítő adatként az alkalmazáshoz. Lásd: [hitelesítő tanúsítvány](active-directory-certificate-credentials.md) további helyességi feltétel formátum és a tanúsítvány regisztrálása ismerteti.|
| erőforrás |szükséges | Az app ID URI-ját a fogadó szolgáltatást (védett erőforrás). Az Azure Portalon az alkalmazás Alkalmazásazonosító URI megkereséséhez válassza ki a **Active Directory** , és jelölje ki azt a könyvtárat. Válassza ki az alkalmazás nevét, válassza a **minden beállítás**, majd válassza ki **tulajdonságok**. |
| requested_token_use |szükséges | Itt adhatja meg, hogyan kell feldolgozni a kérelmet. Az On-meghatalmazásos folyamat, az értéke nem lehet **on_behalf_of**. |
| scope |szükséges | Szóközzel elválasztott a jogkivonat kérése hatókörök listája. Az OpenID Connect, a hatókör **openid** meg kell adni.|

Ezeket a paramétereket szinte teljesen megegyezik a kérés által kivételével, amelyek a közös titkos kulcsot a rendszer a `client_secret parameter` helyébe a két paramétert: `client_assertion_type` és `client_assertion`.

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

## <a name="service-to-service-access-token-response"></a>Szolgáltatások – hozzáférési token válasz

Sikerességi válasz az JSON OAuth 2.0 választ az alábbi paraméterekkel:

| Paraméter | Leírás |
| --- | --- |
| token_type |Typ tokenu értékét jelöli. Az egyetlen típus, amely az Azure AD által támogatott **tulajdonosi**. További információ a tulajdonosi jogkivonatokat: a [OAuth 2.0 engedélyezési keretrendszer: Tulajdonosi jogkivonat-használat (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |
| scope |Megadja a hozzáférést a jogkivonat hatókörét. |
| expires_in |Mennyi ideig a hozzáférési jogkivonat érvénytelen (másodpercben). |
| expires_on |A hozzáférési jogkivonat lejáratának időpontja. A dátum jelenik meg a másodpercek számát, 1970-01-01T0:0:0Z UTC a lejárati időpontig. Ez az érték a gyorsítótárazott jogkivonatok élettartama meghatározására szolgál. |
| erőforrás |Az app ID URI-ját a fogadó szolgáltatást (védett erőforrás). |
| access_token |A kért hozzáférési jogkivonatot. A hívó szolgáltatás használhatja ezt a jogkivonatot a fogadó szolgáltatással való hitelesítésre. |
| id_token |A kért azonosító jogkivonat. A hívó szolgáltatás a jogkivonat segítségével ellenőrizze a felhasználó identitását, és megkezdheti a felhasználói munkamenetet. |
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

Az Azure AD-jogkivonat végpontra hibaüzenetet ad vissza, való hozzáférési jogkivonat beszerzése az alsóbb rétegbeli API-hoz, hogy a feltételes hozzáférési szabályzat (például a többtényezős hitelesítés) van beállítva. A középső rétegű szolgáltatást kell surface ezt a hibát az ügyfélalkalmazás, úgy, hogy az ügyfélalkalmazás tud biztosítani a felhasználói beavatkozás a feltételes hozzáférési szabályzat kielégítéséhez.

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

A középső rétegű szolgáltatást beszerezni a hozzáférési jogkivonat segítségével, hogy hitelesített kéréseket az alsóbb rétegbeli webes API a jogkivonat beállításával a `Authorization` fejléc.

### <a name="example"></a>Példa

```
GET /me?api-version=2013-11-08 HTTP/1.1
Host: graph.windows.net
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRvd3MubmV0IiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiLyIsImlhdCI6MTQ5MzQyMzE2OCwibmJmIjoxNDkzNDIzMTY4LCJleHAiOjE0OTM0NjY5NTEsImFjciI6IjEiLCJhaW8iOiJBU1FBMi84REFBQUE1NnZGVmp0WlNjNWdBVWwrY1Z0VFpyM0VvV2NvZEoveWV1S2ZqcTZRdC9NPSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJhcHBpZGFjciI6IjEiLCJlX2V4cCI6MzAyNjgzLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJwdWlkIjoiMTAwMzNGRkZBMTJFRDdGRSIsInNjcCI6IlVzZXIuUmVhZCIsInN1YiI6IjNKTUlaSWJlYTc1R2hfWHdDN2ZzX0JDc3kxa1l1ekZKLTUyVm1Zd0JuM3ciLCJ0aWQiOiIyNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IiLCJ1bmlxdWVfbmFtZSI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXBuIjoibmF2eWFAZGRvYmFsaWFub3V0bG9vay5vbm1pY3Jvc29mdC5jb20iLCJ1dGkiOiJ4Q3dmemhhLVAwV0pRT0x4Q0dnS0FBIiwidmVyIjoiMS4wIn0.cqmUVjfVbqWsxJLUI1Z4FRx1mNQAHP-L0F4EMN09r8FY9bIKeO-0q1eTdP11Nkj_k4BmtaZsTcK_mUygdMqEp9AfyVyA1HYvokcgGCW_Z6DMlVGqlIU4ssEkL9abgl1REHElPhpwBFFBBenOk9iHddD1GddTn6vJbKC3qAaNM5VarjSPu50bVvCrqKNvFixTb5bbdnSz-Qr6n6ACiEimiI1aNOPR2DeKUyWBPaQcU5EAK0ef5IsVJC1yaYDlAcUYIILMDLCD9ebjsy0t9pj_7lvjzUSrbMdSCCdzCqez_MSNxrk1Nu9AecugkBYp3UVUZOIyythVrj6-sVvLZKUutQ
```

## <a name="saml-assertions-obtained-with-an-oauth20-obo-flow"></a>Egy OAuth2.0 OBO flow szolgáltatás használatában szerzett SAML helyességi feltételek

Néhány OAuth-alapú webszolgáltatások kell más webes szolgáltatás, amely fogadja el a SAML helyességi feltételek nem interaktív folyamatokban API-k eléréséhez. Az Azure Active Directory-On – meghatalmazásos folyamat, amely egy SAML-alapú webszolgáltatás használja, mint a célerőforrás válaszul egy SAML-előfeltétel biztosít.

>[!NOTE]
>Ez az egy nem szabványos bővítmény, amely az OAuth 2.0-alapú meghatalmazásos folyamat, amely lehetővé teszi az OAuth2-alapú webszolgáltatás API-végpontok, amelyek SAML-jogkivonatokat használják eléréséhez.

> [!TIP]
> Amikor egy SAML-védelemmel ellátott webes szolgáltatás hívása egy előtér-webalkalmazást, csak az API-t és a felhasználói munkamenet normál interaktív hitelesítési folyamat elindításához. Csak ki kell egy OBO folyamat használja, ha egy szolgáltatások közötti meghívásához szükséges egy SAML-jogkivonat felhasználói környezetet biztosít.

### <a name="obtain-a-saml-token-by-using-an-obo-request-with-a-shared-secret"></a>SAML-jogkivonat beszerzése egy közös titkos kulcsot egy OBO-kérelem használatával

SAML-előfeltétel-szolgáltatások kérelmet a következő paramétereket tartalmazza:

| Paraméter |  | Leírás |
| --- | --- | --- |
| grant_type |szükséges | A jogkivonat kérése típusa. Jwt-t használó kérelmek, az értéknek kell lennie **urn: ietf:params:oauth:grant-típus: jwt-tulajdonosi**. |
| assertion |szükséges | A hozzáférési jogkivonatot, amelyet a kérés értékét.|
| client_id |szükséges | Az Azure AD-regisztrációja során a hívó szolgáltatáshoz rendelt alkalmazás azonosítója. Az Azure Portalon az alkalmazás Azonosítójának megkereséséhez válassza ki a **Active Directory**, jelölje ki azt a könyvtárat, és válassza ki az alkalmazás nevét. |
| client_secret |szükséges | A kulcsot az Azure AD-ben regisztrált a hívó szolgáltatás. Ez az érték rendelkezik lett jegyezni a regisztrációs idején. |
| erőforrás |szükséges | Az app ID URI-ját a fogadó szolgáltatást (védett erőforrás). Ez az a erőforrás, amely az SAML-jogkivonat célközönség lesz. Az Azure Portalon az alkalmazás Alkalmazásazonosító URI megkereséséhez válassza ki a **Active Directory** , és jelölje ki azt a könyvtárat. Válassza ki az alkalmazás nevét, válassza a **minden beállítás**, majd válassza ki **tulajdonságok**. |
| requested_token_use |szükséges | Itt adhatja meg, hogyan kell feldolgozni a kérelmet. Az On-meghatalmazásos folyamat, az értéke nem lehet **on_behalf_of**. |
| requested_token_type | szükséges | Határozza meg a kért jogkivonatot. Az érték lehet **urn: ietf:params:oauth:token-típus: egy saml2** vagy **urn: ietf:params:oauth:token-típus: saml1** az elért erőforrás követelményeitől függően. |

A válasz egy SAML-jogkivonatban kódolt UTF8 és Base64url tartalmaz.

- **Az SAML-előfeltétel SubjectConfirmationData származási hely egy OBO hívás**: Ha a célalkalmazásnak egy címzett értéket igényel **SubjectConfirmationData**, majd az érték lehet egy nem helyettesítő válasz URL-cím, az erőforrás-alkalmazás konfigurációjában.
- **A SubjectConfirmationData csomópont**: A csomópont nem tartalmazhat egy **InResponseTo** attribútumot, mert nem része egy SAML-válasz. Az alkalmazás fogad a SAML-jogkivonat fogadja el a SAML helyességi feltétel alkalmazása nélkül képesnek kell lennie egy **InResponseTo** attribútum.

- **Hozzájárulás**: Jóváhagyás előre meg kell adni egy OAuth folyamata a felhasználói adatokat tartalmazó SAML-jogkivonat fogadásához. Beszerzését rendszergazdai jóváhagyást az engedélyekkel kapcsolatos információkért lásd: [engedélyek és jóváhagyás az Azure Active Directory 1.0-s verziójú végpontján az](https://docs.microsoft.com/azure/active-directory/develop/v1-permissions-and-consent).

### <a name="response-with-saml-assertion"></a>Az SAML-előfeltétel válasz

| Paraméter | Leírás |
| --- | --- |
| token_type |Typ tokenu értékét jelöli. Az egyetlen típus, amely az Azure AD által támogatott **tulajdonosi**. További információ a tulajdonosi jogkivonatokat: [OAuth 2.0 engedélyezési keretrendszer: Tulajdonosi jogkivonat-használat (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |
| scope |Megadja a hozzáférést a jogkivonat hatókörét. |
| expires_in |Mennyi ideig a hozzáférési jogkivonat érvénytelen (másodpercben). |
| expires_on |A hozzáférési jogkivonat lejáratának időpontja. A dátum jelenik meg a másodpercek számát, 1970-01-01T0:0:0Z UTC a lejárati időpontig. Ez az érték a gyorsítótárazott jogkivonatok élettartama meghatározására szolgál. |
| erőforrás |Az app ID URI-ját a fogadó szolgáltatást (védett erőforrás). |
| access_token |A paraméter, amely visszaadja az SAML-előfeltétel. |
| refresh_token |A frissítési jogkivonatot. A hívó szolgáltatás a jogkivonat használatával új hozzáférési jogkivonat kérése az aktuális SAML helyességi feltétel lejárta után is. |

- token_type: Tulajdonosi
- expires_in: 3296
- ext_expires_in: 0
- expires_on: 1529627844
- erőforrás: `https://api.contoso.com`
- access_token: \<SAML-előfeltétel\>
- issued_token_type: urn:ietf:params:oauth:token-type:saml2
- refresh_token: \<Jogkivonat frissítése\>

## <a name="client-limitations"></a>Ügyfél-korlátozások

Helyettesítő karaktert tartalmazó válasz URL-címekkel rendelkező nyilvános ügyfelek nem használhatják egy `id_token` OBO folyamatokhoz. Azonban továbbra is beválthatja bizalmas ügyfél **hozzáférés** az implicit engedélyezési folyamat keresztül beszerzett, még akkor is, ha a nyilvános ügyfél rendelkezik egy helyettesítő karaktert tartalmazó jogkivonatok átirányítási URI-t regisztrált.

## <a name="next-steps"></a>További lépések

További információ az OAuth 2.0 protokollt és a egy másik módja a által használt ügyfél-hitelesítő adatok szolgáltatások közötti hitelesítés végrehajtásához:

* [OAuth 2.0 ügyfélhitelesítő adatok az Azure AD használatával szolgáltatás hitelesítési szolgáltatás](v1-oauth2-client-creds-grant-flow.md)
* [OAuth 2.0, Azure AD-ben](v1-protocols-oauth-code.md)
