---
title: 'Az Azure AD szolgáltatás és szolgáltatás közötti hitelesítés OAuth 2.0-s verziójának előtagja: spec | Microsoft Docs'
description: Ez a cikk azt ismerteti, hogyan használhatók a HTTP-üzenetek a szolgáltatás és a szolgáltatás közötti hitelesítés megvalósításához a OAuth 2.0-s verziójával.
services: active-directory
documentationcenter: .net
author: navyasric
manager: CelesteDG
editor: ''
ms.assetid: 09f6f318-e88b-4024-9ee1-e7f09fb19a82
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: ryanwi
ms.reviewer: hirsin, nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: accd14446ab8f4a70336e3bd6787cbd8c93ff21d
ms.sourcegitcommit: a3a40ad60b8ecd8dbaf7f756091a419b1fe3208e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/21/2019
ms.locfileid: "69891519"
---
# <a name="service-to-service-calls-that-use-delegated-user-identity-in-the-on-behalf-of-flow"></a>Szolgáltatások közötti hívások, amelyek delegált felhasználói identitást használnak a következő folyamat során:

[!INCLUDE [active-directory-develop-applies-v1](../../../includes/active-directory-develop-applies-v1.md)]

Az OAuth 2,0-alapú (OBO) folyamat lehetővé teszi egy olyan alkalmazás számára, amely egy szolgáltatást vagy webes API-t hív meg, hogy átadja a felhasználói hitelesítést egy másik szolgáltatásnak vagy webes API-nak. Az OBO-folyamat propagálja a delegált felhasználói identitást és engedélyeket a kérési láncon keresztül. Ahhoz, hogy a középső szintű szolgáltatás hitelesített kéréseket továbbítson az alárendelt szolgáltatásnak, a felhasználónak kell védenie Azure Active Directory (Azure AD) hozzáférési jogkivonatát.

> [!IMPORTANT]
> A 2018 `id_token` . májustól kezdve nem használható a folyamaton kívüli folyamathoz.  Az egylapos alkalmazások (Gyógyfürdők) hozzáférési jogkivonatot kell átadni egy közepes szintű bizalmas ügyfélnek az OBO-folyamatok végrehajtásához. További információk az elvégezhető hívásokat végrehajtó ügyfelekről: [korlátozások](#client-limitations).

## <a name="on-behalf-of-flow-diagram"></a>Folyamaton kívüli diagram

Az OBO-folyamat akkor indul el, amikor a felhasználó hitelesítése megtörtént egy olyan alkalmazáson, amely a [OAuth 2,0 engedélyezési kódot](v1-protocols-oauth-code.md)használja. Ezen a ponton az alkalmazás egy hozzáférési jogkivonatot (a token A jogkivonatot) küld a felhasználói jogcímeket tartalmazó középső rétegbeli webes API-ra (a API-ra), amely az A API-hoz való hozzáférést tartalmazza. Ezt követően a API A hitelesített kérelmet küld az alárendelt webes API-nak (B API).

Ezek a lépések a következő folyamatokat alkotják: ![A OAuth 2.0-ban, a folyamaton belüli folyamat lépéseinek megjelenítése](./media/v1-oauth2-on-behalf-of-flow/active-directory-protocols-oauth-on-behalf-of-flow.png)

1. Az ügyfélalkalmazás egy kérelmet küld az a API-nak az a jogkivonattal.
1. Az API A hitelesíti az Azure AD jogkivonat-kiállítási végpontot, és jogkivonatot kér a B API eléréséhez.
1. Az Azure AD jogkivonat-kiállítási végpont ellenőrzi az a API hitelesítő adatait az A jogkivonattal, és kiadja a B API (token B) hozzáférési jogkivonatát.
1. A B API-ra irányuló kérelem a B tokent tartalmazza az engedélyezési fejlécben.
1. A B API a biztonságos erőforrásból származó adatokkal tér vissza.

>[!NOTE]
>Egy alsóbb rétegbeli szolgáltatás jogkivonatának igényléséhez használt hozzáférési jogkivonatban szereplő célközönség-jogcímnek az OBO-kérelmet készítő szolgáltatás AZONOSÍTÓjának kell lennie. A tokent is alá kell írni a Azure Active Directory globális aláíró kulccsal (ez az alapértelmezett beállítás a portálon **Alkalmazásregisztrációk** használatával regisztrált alkalmazások esetében).

## <a name="register-the-application-and-service-in-azure-ad"></a>Az alkalmazás és a szolgáltatás regisztrálása az Azure AD-ben

Regisztrálja a középső rétegbeli szolgáltatást és az ügyfélalkalmazás az Azure AD-ben.

### <a name="register-the-middle-tier-service"></a>A középső rétegbeli szolgáltatás regisztrálása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A felső sávon válassza ki a fiókját, és a **címtár** listában válassza ki a Active Directory bérlőt az alkalmazáshoz.
1. A bal oldali ablaktáblán válassza a **További szolgáltatások** lehetőséget, és válassza a **Azure Active Directory**lehetőséget.
1. Válassza a **Alkalmazásregisztrációk** , majd az **új regisztráció**lehetőséget.
1. Adjon egy rövid nevet az alkalmazásnak, és válassza ki az alkalmazás típusát.
1. A **támogatott fiókok típusai**területen válassza a **fiókok lehetőséget bármely szervezeti címtárban és személyes Microsoft-fiókban**.
1. Állítsa be az átirányítási URI-t az alap URL-címre.
1. Válassza a **Regisztráció** elemet az alkalmazás létrehozásához.
1. Az Azure Portal bezárása előtt állítson be egy ügyfél-titkot.
1. A Azure Portal válassza ki az alkalmazást, és válassza a **tanúsítványok & titkok**lehetőséget.
1. Válassza az **új ügyfél titkot** , és adjon hozzá egy vagy két év időtartamú titkot.
1. A lap mentésekor a Azure Portal megjeleníti a titkos értéket. Másolja és mentse a titkos értéket egy biztonságos helyen.

> [!IMPORTANT]
> A saját implementációjában az Alkalmazásbeállítások konfigurálásához szükség van a titkos kulcsra. Ez a titkos érték nem jelenik meg újra, és semmilyen más módon nem kérhető le. Jegyezze fel, amint a Azure Portal látható.

### <a name="register-the-client-application"></a>Az ügyfélalkalmazás regisztrálása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A felső sávon válassza ki a fiókját, és a **címtár** listában válassza ki a Active Directory bérlőt az alkalmazáshoz.
1. A bal oldali ablaktáblán válassza a **További szolgáltatások** lehetőséget, és válassza a **Azure Active Directory**lehetőséget.
1. Válassza a **Alkalmazásregisztrációk** , majd az **új regisztráció**lehetőséget.
1. Adjon egy rövid nevet az alkalmazásnak, és válassza ki az alkalmazás típusát.
1. A **támogatott fiókok típusai**területen válassza a **fiókok lehetőséget bármely szervezeti címtárban és személyes Microsoft-fiókban**.
1. Állítsa be az átirányítási URI-t az alap URL-címre.
1. Válassza a **Regisztráció** elemet az alkalmazás létrehozásához.
1. Konfigurálja az alkalmazás engedélyeit. Az **API-engedélyek**területen válassza az **engedély hozzáadása** , majd **az API**-k elemet.
1. Írja be a középső rétegbeli szolgáltatás nevét a szövegmezőbe.
1. Válassza az **engedélyek kiválasztása** , majd **a \<hozzáférési szolgáltatás neve >** elemet.

### <a name="configure-known-client-applications"></a>Ismert ügyfélalkalmazások konfigurálása

Ebben az esetben a középső rétegbeli szolgáltatásnak be kell szereznie a felhasználó beleegyezik az alsóbb rétegbeli API eléréséhez felhasználói beavatkozás nélkül. Az alárendelt API-hoz való hozzáférés engedélyezésének lehetőségét a hitelesítés során a jóváhagyás lépésének részeként be kell mutatni.

Kövesse az alábbi lépéseket az ügyfélalkalmazás regisztrációjának explicit módon való kötéséhez az Azure AD-ben a középső rétegbeli szolgáltatás regisztrálásával. A művelet egyesíti az ügyfél és a középső réteg által megkövetelt beleegyezett egyetlen párbeszédablakba.

1. Lépjen a középső rétegbeli szolgáltatás regisztrációja elemre, és válassza a **jegyzékfájl** elemet a jegyzékfájl-szerkesztő megnyitásához.
1. Keresse meg `knownClientApplications` a Array tulajdonságot, és adja hozzá az ügyfélalkalmazás ügyfél-azonosítóját elemként.
1. Mentse a jegyzékfájlt a **Mentés gombra**kattintva.

## <a name="service-to-service-access-token-request"></a>Szolgáltatás-szolgáltatás hozzáférési jogkivonat kérése

Hozzáférési jogkivonat igényléséhez tegyen közzé egy HTTP-bejegyzést a bérlői specifikus Azure AD-végponton a következő paraméterekkel:

```
https://login.microsoftonline.com/<tenant>/oauth2/token
```

Az ügyfélalkalmazás védelmét egy közös titok vagy egy tanúsítvány védi.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Első eset: Hozzáférési jogkivonat-kérelem közös titokkal

Közös titkos kulcs használata esetén a szolgáltatás-szolgáltatás hozzáférési jogkivonat-kérelem a következő paramétereket tartalmazza:

| Paraméter |  | Leírás |
| --- | --- | --- |
| grant_type |szükséges | A jogkivonat-kérelem típusa. Egy OBO-kérelem egy JSON Web Token (JWT) használ, ezért az értéknek a következőnek kell lennie: **urn: IETF: params: OAuth: Grant-Type: JWT-tulajdonos**. |
| assertion |szükséges | A kérelemben használt hozzáférési jogkivonat értéke. |
| client_id |szükséges | Az Azure AD-vel való regisztráció során a hívó szolgáltatáshoz rendelt alkalmazás-azonosító. Ha meg szeretné keresni az alkalmazás AZONOSÍTÓját a Azure Portalban, válassza a **Active Directory**lehetőséget, válassza ki a könyvtárat, majd válassza ki az alkalmazás nevét. |
| client_secret |szükséges | Az Azure AD-ben a hívó szolgáltatáshoz regisztrált kulcs. Ezt az értéket a regisztráció időpontjában fel kell jegyezni. |
| resource |szükséges | A fogadó szolgáltatás alkalmazás-AZONOSÍTÓjának URI azonosítója (biztonságos erőforrás). Az alkalmazás AZONOSÍTÓjának URI azonosítójának megkereséséhez a Azure Portal válassza a **Active Directory** lehetőséget, majd válassza ki a könyvtárat. Válassza ki az alkalmazás nevét, válassza a **minden beállítás**lehetőséget, majd válassza a **Tulajdonságok**lehetőséget. |
| requested_token_use |szükséges | Megadja a kérelem feldolgozásának módját. A folyamaton belüli folyamatban az értéknek **on_behalf_of**kell lennie. |
| scope |szükséges | A jogkivonat-kérelem hatókörének szóközzel tagolt listája. Az OpenID Connect esetében meg kell adni az **OpenID** hatókört.|

#### <a name="example"></a>Példa

A következő http-bejegyzés egy hozzáférési jogkivonatot https://graph.windows.net kér a webes API-hoz. Az `client_id` azonosítja azt a szolgáltatást, amely a hozzáférési jogkivonatot kéri.

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

### <a name="second-case-access-token-request-with-a-certificate"></a>Második eset: Hozzáférési jogkivonat-kérelem tanúsítvánnyal

Egy tanúsítványhoz tartozó szolgáltatás-szolgáltatás hozzáférési jogkivonat-kérelem a következő paramétereket tartalmazza:

| Paraméter |  | Leírás |
| --- | --- | --- |
| grant_type |szükséges | A jogkivonat-kérelem típusa. Egy OBO-kérelem JWT-hozzáférési tokent használ, így az érték csak az **urn: IETF: params: OAuth: Grant-Type: JWT-tulajdonos**lehet. |
| assertion |szükséges | A kérelemben használt jogkivonat értéke. |
| client_id |szükséges | Az Azure AD-vel való regisztráció során a hívó szolgáltatáshoz rendelt alkalmazás-azonosító. Ha meg szeretné keresni az alkalmazás AZONOSÍTÓját a Azure Portalban, válassza a **Active Directory**lehetőséget, válassza ki a könyvtárat, majd válassza ki az alkalmazás nevét. |
| client_assertion_type |szükséges |Az értéknek meg kell felelnie`urn:ietf:params:oauth:client-assertion-type:jwt-bearer` |
| client_assertion |szükséges | Az Ön által létrehozott és az alkalmazáshoz hitelesítő adatként regisztrált tanúsítvánnyal rendelkező JSON Web Token. Tekintse meg a [tanúsítvány hitelesítő adatait](active-directory-certificate-credentials.md) , és Ismerje meg a tanúsítvány regisztrálásának módját.|
| resource |szükséges | A fogadó szolgáltatás alkalmazás-AZONOSÍTÓjának URI azonosítója (biztonságos erőforrás). Az alkalmazás AZONOSÍTÓjának URI azonosítójának megkereséséhez a Azure Portal válassza a **Active Directory** lehetőséget, majd válassza ki a könyvtárat. Válassza ki az alkalmazás nevét, válassza a **minden beállítás**lehetőséget, majd válassza a **Tulajdonságok**lehetőséget. |
| requested_token_use |szükséges | Megadja a kérelem feldolgozásának módját. A folyamaton belüli folyamatban az értéknek **on_behalf_of**kell lennie. |
| scope |szükséges | A jogkivonat-kérelem hatókörének szóközzel tagolt listája. Az OpenID Connect esetében meg kell adni az **OpenID** hatókört.|

Ezek a paraméterek majdnem ugyanazok, mint a közös titok kérelme, kivéve, hogy `client_secret parameter` a két paraméterrel van lecserélve: `client_assertion_type` és `client_assertion`.

#### <a name="example"></a>Példa

A következő http post hozzáférési jogkivonatot kér a https://graph.windows.net webes API-hoz egy tanúsítvánnyal. Az `client_id` azonosítja azt a szolgáltatást, amely a hozzáférési jogkivonatot kéri.

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

## <a name="service-to-service-access-token-response"></a>A szolgáltatás és a szolgáltatás közötti hozzáférési jogkivonat válasza

A sikeres válasz egy JSON-OAuth 2,0-válasz a következő paraméterekkel:

| Paraméter | Leírás |
| --- | --- |
| token_type |Megadja a jogkivonat típusának értékét. Az Azure AD által támogatott egyetlen típus a **tulajdonos**. A tulajdonosi jogkivonatokkal kapcsolatos további információkért tekintse [meg a OAuth 2,0 engedélyezési keretrendszert: Tulajdonosi jogkivonat használata (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |
| scope |A jogkivonatban megadott hozzáférési hatókör. |
| expires_in |A hozzáférési jogkivonat érvényességének időtartama (másodpercben). |
| expires_on |A hozzáférési jogkivonat lejáratának időpontja. A dátum az 1970-01-01T0:0: 0Z UTC számú másodperc, a lejárati időpontig. Ez az érték a gyorsítótárazott tokenek élettartamának meghatározására szolgál. |
| resource |A fogadó szolgáltatás alkalmazás-AZONOSÍTÓjának URI azonosítója (biztonságos erőforrás). |
| access_token |A kért hozzáférési jogkivonat. A hívó szolgáltatás ezt a tokent használhatja a fogadó szolgáltatásban való hitelesítéshez. |
| id_token |A kért azonosító jogkivonat. A hívó szolgáltatás a token használatával ellenőrizheti a felhasználó identitását, és megkezdheti a munkamenetet a felhasználóval. |
| refresh_token |A kért hozzáférési jogkivonat frissítési jogkivonata. A hívó szolgáltatás ezzel a jogkivonattal kérhet egy másik hozzáférési tokent az aktuális hozzáférési jogkivonat lejárta után. |

### <a name="success-response-example"></a>Sikeres válasz – példa

Az alábbi példa egy, a https://graph.windows.net webes API hozzáférési jogkivonatára vonatkozó kérelemre adott sikeres választ mutat be.

```json
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

### <a name="error-response-example"></a>Hiba-válasz példa

Az Azure AD jogkivonat-végpont olyan hibaüzenetet ad vissza, amely egy feltételes hozzáférési házirenddel (például többtényezős hitelesítéssel) beállított alsóbb rétegbeli API hozzáférési jogkivonatának beszerzésére tesz kísérletet. A középső rétegbeli szolgáltatásnak ezt a hibát fel kell vennie az ügyfélalkalmazás számára, hogy az ügyfélalkalmazás meg tudja adni a felhasználói beavatkozást a feltételes hozzáférési szabályzat teljesítése érdekében.

```json
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

## <a name="use-the-access-token-to-access-the-secured-resource"></a>A biztonságos erőforrás eléréséhez használja a hozzáférési jogkivonatot.

A középső rétegbeli szolgáltatás a beszerzett hozzáférési jogkivonattal hitelesített kéréseket végez az alárendelt webes API-hoz, ha a tokent a `Authorization` fejlécben állítja be.

### <a name="example"></a>Példa

```
GET /me?api-version=2013-11-08 HTTP/1.1
Host: graph.windows.net
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRvd3MubmV0IiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiLyIsImlhdCI6MTQ5MzQyMzE2OCwibmJmIjoxNDkzNDIzMTY4LCJleHAiOjE0OTM0NjY5NTEsImFjciI6IjEiLCJhaW8iOiJBU1FBMi84REFBQUE1NnZGVmp0WlNjNWdBVWwrY1Z0VFpyM0VvV2NvZEoveWV1S2ZqcTZRdC9NPSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJhcHBpZGFjciI6IjEiLCJlX2V4cCI6MzAyNjgzLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJwdWlkIjoiMTAwMzNGRkZBMTJFRDdGRSIsInNjcCI6IlVzZXIuUmVhZCIsInN1YiI6IjNKTUlaSWJlYTc1R2hfWHdDN2ZzX0JDc3kxa1l1ekZKLTUyVm1Zd0JuM3ciLCJ0aWQiOiIyNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IiLCJ1bmlxdWVfbmFtZSI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXBuIjoibmF2eWFAZGRvYmFsaWFub3V0bG9vay5vbm1pY3Jvc29mdC5jb20iLCJ1dGkiOiJ4Q3dmemhhLVAwV0pRT0x4Q0dnS0FBIiwidmVyIjoiMS4wIn0.cqmUVjfVbqWsxJLUI1Z4FRx1mNQAHP-L0F4EMN09r8FY9bIKeO-0q1eTdP11Nkj_k4BmtaZsTcK_mUygdMqEp9AfyVyA1HYvokcgGCW_Z6DMlVGqlIU4ssEkL9abgl1REHElPhpwBFFBBenOk9iHddD1GddTn6vJbKC3qAaNM5VarjSPu50bVvCrqKNvFixTb5bbdnSz-Qr6n6ACiEimiI1aNOPR2DeKUyWBPaQcU5EAK0ef5IsVJC1yaYDlAcUYIILMDLCD9ebjsy0t9pj_7lvjzUSrbMdSCCdzCqez_MSNxrk1Nu9AecugkBYp3UVUZOIyythVrj6-sVvLZKUutQ
```

## <a name="saml-assertions-obtained-with-an-oauth20-obo-flow"></a>OAuth 2.0 OBO-flow-val kapott SAML-kijelentések

Néhány OAuth-alapú webszolgáltatásnak hozzá kell férnie más webszolgáltatási API-khoz, amelyek nem interaktív folyamatokban fogadják el az SAML-kijelentéseket. A Azure Active Directory az SAML-alapú webszolgáltatás forrásként való megadására irányuló, az általa létrehozott folyamatra adott válaszként SAML-jogcímet adhat meg.

>[!NOTE]
>Ez egy nem szabványos bővítmény a OAuth 2,0-hez, amely lehetővé teszi, hogy egy OAuth2-alapú alkalmazás hozzáférhessen az SAML-jogkivonatokat használó webszolgáltatás API-végpontokhoz.

> [!TIP]
> Ha SAML-védelemmel ellátott webszolgáltatást hív meg egy előtér-webalkalmazásból, egyszerűen hívja meg az API-t, és kezdeményezzen egy normál, interaktív hitelesítési folyamatot a felhasználó meglévő munkamenetével. Csak egy OBO-folyamatot kell használnia, ha egy szolgáltatás-szolgáltatás hívásához SAML-jogkivonat szükséges a felhasználói környezet biztosításához.

### <a name="obtain-a-saml-token-by-using-an-obo-request-with-a-shared-secret"></a>SAML-token beszerzése egy megosztott titkos kulccsal rendelkező OBO-kérelem használatával

Az SAML-állítások szolgáltatás-szolgáltatásra irányuló kérelme a következő paramétereket tartalmazza:

| Paraméter |  | Leírás |
| --- | --- | --- |
| grant_type |szükséges | A jogkivonat-kérelem típusa. A JWT használó kérések esetében az értéknek **urn: IETF: params: OAuth: Grant-Type: JWT-tulajdonos**értékűnek kell lennie. |
| assertion |szükséges | A kérelemben használt hozzáférési jogkivonat értéke.|
| client_id |szükséges | Az Azure AD-vel való regisztráció során a hívó szolgáltatáshoz rendelt alkalmazás-azonosító. Ha meg szeretné keresni az alkalmazás AZONOSÍTÓját a Azure Portalban, válassza a **Active Directory**lehetőséget, válassza ki a könyvtárat, majd válassza ki az alkalmazás nevét. |
| client_secret |szükséges | Az Azure AD-ben a hívó szolgáltatáshoz regisztrált kulcs. Ezt az értéket a regisztráció időpontjában fel kell jegyezni. |
| resource |szükséges | A fogadó szolgáltatás alkalmazás-AZONOSÍTÓjának URI azonosítója (biztonságos erőforrás). Ez az az erőforrás, amely az SAML-jogkivonat célközönsége lesz. Az alkalmazás AZONOSÍTÓjának URI azonosítójának megkereséséhez a Azure Portal válassza a **Active Directory** lehetőséget, majd válassza ki a könyvtárat. Válassza ki az alkalmazás nevét, válassza a **minden beállítás**lehetőséget, majd válassza a **Tulajdonságok**lehetőséget. |
| requested_token_use |szükséges | Megadja a kérelem feldolgozásának módját. A folyamaton belüli folyamatban az értéknek **on_behalf_of**kell lennie. |
| requested_token_type | szükséges | Megadja a kért jogkivonat típusát. Az érték a következő lehet: **urn: IETF: params: OAuth: token-Type: egy saml2** vagy **urn: IETF: param: OAuth: token-Type: saml1** a hozzáfért erőforrás követelményeitől függően. |

A válasz az UTF8 és a Base64url kódolású SAML-tokent tartalmaz.

- **SubjectConfirmationData egy OBO-hívásból származó SAML-** kijelentéshez: Ha a célalkalmazás a **SubjectConfirmationData**egy címzett értéket igényel, akkor az értéknek nem helyettesítő karakterből álló URL-címnek kell lennie az erőforrás-alkalmazás konfigurációjában.
- **A SubjectConfirmationData csomópont**: A csomópont nem tartalmazhat **InResponseTo** attribútumot, mert nem része SAML-válasznak. Az SAML-tokent fogadó alkalmazásnak képesnek kell lennie arra, hogy elfogadja az SAML-jogkivonatot **InResponseTo** attribútum nélkül.

- **Beleegyezik**: Meg kell adni egy olyan SAML-tokent, amely egy OAuth-folyamaton felhasználói adatmennyiséget tartalmaz. Az engedélyekkel és a rendszergazdai jogosultság beszerzésével kapcsolatos információkért tekintse meg [a Azure Active Directory 1.0-s verziójának engedélyeit és](https://docs.microsoft.com/azure/active-directory/develop/v1-permissions-and-consent)a hozzájuk tartozó hozzájárulásukat.

### <a name="response-with-saml-assertion"></a>SAML-kijelentéssel kapcsolatos válasz

| Paraméter | Leírás |
| --- | --- |
| token_type |Megadja a jogkivonat típusának értékét. Az Azure AD által támogatott egyetlen típus a **tulajdonos**. A tulajdonosi jogkivonatokkal kapcsolatos további információkért lásd [: OAuth 2,0 engedélyezési keretrendszer: Tulajdonosi jogkivonat használata (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |
| scope |A jogkivonatban megadott hozzáférési hatókör. |
| expires_in |A hozzáférési jogkivonat érvényességének időtartama (másodpercben). |
| expires_on |A hozzáférési jogkivonat lejáratának időpontja. A dátum az 1970-01-01T0:0: 0Z UTC számú másodperc, a lejárati időpontig. Ez az érték a gyorsítótárazott tokenek élettartamának meghatározására szolgál. |
| resource |A fogadó szolgáltatás alkalmazás-AZONOSÍTÓjának URI azonosítója (biztonságos erőforrás). |
| access_token |Az SAML-kijelentést visszaadó paraméter. |
| refresh_token |A frissítési jogkivonat. A hívó szolgáltatás a jelenlegi SAML-állítás lejárta után is kérheti a tokent egy másik hozzáférési jogkivonat igénylésére. |

- token_type: Tulajdonosi
- expires_in: 3296
- ext_expires_in: 0
- expires_on: 1529627844
- erőforrás`https://api.contoso.com`
- access_token: \<SAML-kijelentés\>
- issued_token_type: urn: IETF: paraméterek: OAuth: token-Type: egy saml2
- refresh_token: \<Jogkivonat frissítése\>

## <a name="client-limitations"></a>Ügyfél korlátozásai

A helyettesítő karakteres válasz URL-címekkel `id_token` rendelkező nyilvános ügyfelek nem használhatják az OBO-folyamatokat. A bizalmas ügyfél azonban továbbra is beválthatja az implicit engedélyezési folyamat során beszerzett **hozzáférési** jogkivonatokat, még akkor is, ha a nyilvános ügyfélben van regisztrálva egy helyettesítő karakteres átirányítási URI.

## <a name="next-steps"></a>További lépések

További információ a OAuth 2,0 protokollról és egy másik módszer az ügyfél-hitelesítő adatokat használó szolgáltatás-szolgáltatás hitelesítés végrehajtásához:

* [Szolgáltatás-szolgáltatás hitelesítése OAuth 2,0 ügyfél-hitelesítő adatokkal az Azure AD-ben](v1-oauth2-client-creds-grant-flow.md)
* [OAuth 2,0 az Azure AD-ben](v1-protocols-oauth-code.md)
