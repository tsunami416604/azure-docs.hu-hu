---
title: Szolgáltatás-szolgáltatás hitelesítés az OAuth2.0-val a folyamat nevében | Microsoft dokumentumok
description: Ez a cikk azt ismerteti, hogy miként valósítható meg a HTTP-üzenetek szolgáltatás-szolgáltatás hitelesítés megvalósítása az OAuth2.0 a folyamat nevében.
services: active-directory
documentationcenter: .net
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: ryanwi
ms.reviewer: hirsin, nacanuma
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: a301029f30a77f4e62ad3529aac488a81c12566e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154525"
---
# <a name="service-to-service-calls-that-use-delegated-user-identity-in-the-on-behalf-of-flow"></a>Delegált felhasználói identitást használó szolgáltatás-szolgáltatás hívások a Nevében folyamatban

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Az OAuth 2.0 On-Behalf-Of (OBO) folyamat lehetővé teszi, hogy egy szolgáltatás vagy webes API-t meghívó alkalmazás felhasználói hitelesítést továbbítson egy másik szolgáltatásnak vagy webes API-nak. Az OBO-folyamat a delegált felhasználói identitást és engedélyeket a kérelemláncon keresztül terjeszti. Ahhoz, hogy a középső rétegbeli szolgáltatás hitelesített kérelmeket küldjön az alsóbb rétegbeli szolgáltatásnak, a felhasználó nevében biztosítania kell egy hozzáférési jogkivonatot az Azure Active Directoryból (Azure AD).

> [!IMPORTANT]
> 2018 májusátantól `id_token` az an nem használható a nevében történő folyamathoz.  Az egyoldalas alkalmazásoknak (SpA-knak) hozzáférési jogkivonatot kell átadniuk egy középső rétegbeli bizalmas ügyfélnek az OBO-folyamatok végrehajtásához. A hívások at végző ügyfelekről a korlátozások című témakörben további [részleteket](#client-limitations)szeretne.

## <a name="on-behalf-of-flow-diagram"></a>Folyamat-diagram nevében

Az OBO-folyamat az [OAuth 2.0 engedélyezési kód engedélyezési folyamatát](v1-protocols-oauth-code.md)használó alkalmazás hitelesítése után kezdődik. Ezen a ponton az alkalmazás egy hozzáférési jogkivonatot (A jogkivonatot) küld a középső rétegbeli webes API-nak (API A), amely tartalmazza a felhasználó jogcímeket és hozzájárul az API A eléréséhez. Ezután az API A egy hitelesített kérelmet küld az alsóbb rétegbeli webes API-nak (B API).

Ezek a lépések alkotják a ![folyamat nevében: Az OAuth2.0 A folyamat nevében című lépéslépéseit jeleníti meg.](./media/v1-oauth2-on-behalf-of-flow/active-directory-protocols-oauth-on-behalf-of-flow.png)

1. Az ügyfélalkalmazás az A jogkivonattal az A API-nak kér.
1. AZ API A hitelesíti magát az Azure AD token kiállítási végpontjára, és egy jogkivonatot kér a B API eléréséhez.
1. Az Azure AD token kiállítási végpont érvényesíti az API A hitelesítő adatait az A jogkivonattal, és kiadja a b api (B token) hozzáférési jogkivonatát.
1. A B API-nak küldött kérelem b jogkivonatot tartalmaz az engedélyezési fejlécben.
1. Az API B adatokat ad vissza a biztonságos erőforrásból.

>[!NOTE]
>A közönség jogcím egy hozzáférési jogkivonat ot kérni egy alsóbb rétegbeli szolgáltatás kell az OBO-kérelmet nyújtó szolgáltatás azonosítójának kell lennie. A jogkivonatot is alá kell írni az Azure Active Directory globális aláíró kulcs (amely az alapértelmezett **alkalmazás regisztrációk** a portálon keresztül regisztrált alkalmazások).

## <a name="register-the-application-and-service-in-azure-ad"></a>Az alkalmazás és a szolgáltatás regisztrálása az Azure AD-ben

Regisztrálja a középső rétegbeli szolgáltatást és az ügyfélalkalmazást az Azure AD-ben.

### <a name="register-the-middle-tier-service"></a>A középső szintű szolgáltatás regisztrálása

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. A felső sávon válassza ki a fiókját, és a **Címtár** lista alatt válassza ki az alkalmazás Active Directory-bérlőjét.
1. Válassza a **További szolgáltatások lehetőséget** a bal oldali ablaktáblában, és válassza az Azure Active **Directory**lehetőséget.
1. Válassza **az Alkalmazásregisztrációk,** majd **az Új regisztráció lehetőséget.**
1. Adja meg az alkalmazás rövid nevét, és válassza ki az alkalmazás típusát.
1. A **Támogatott fióktípusok csoportban**válassza a Fiókok lehetőséget **bármely szervezeti címtárban és személyes Microsoft-fiókban.**
1. Állítsa be az átirányítási URI-t az alap URL-címre.
1. Válassza a **Regisztráció** elemet az alkalmazás létrehozásához.
1. Hozzon létre egy ügyféltitkos kulcsot, mielőtt kilépne az Azure Portalról.
1. Az Azure Portalon válassza ki az alkalmazást, és válassza **a tanúsítványok & titkos kulcsokat.**
1. Válassza az **Új ügyféltitkos kulcsot,** és adjon hozzá egy egy éves vagy két éves titkos kulcsot.
1. A lap mentésekor az Azure Portal megjeleníti a titkos értéket. Másolja és mentse a titkos értéket biztonságos helyre.

> [!IMPORTANT]
> Szüksége van a titok konfigurálásához az alkalmazás beállításait a megvalósításban. Ez a titkos érték nem jelenik meg újra, és nem visszakereshető semmilyen más módon. Rögzítse, amint látható az Azure Portalon.

### <a name="register-the-client-application"></a>Az ügyfélalkalmazás regisztrálása

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. A felső sávon válassza ki a fiókját, és a **Címtár** lista alatt válassza ki az alkalmazás Active Directory-bérlőjét.
1. Válassza a **További szolgáltatások lehetőséget** a bal oldali ablaktáblában, és válassza az Azure Active **Directory**lehetőséget.
1. Válassza **az Alkalmazásregisztrációk,** majd **az Új regisztráció lehetőséget.**
1. Adja meg az alkalmazás rövid nevét, és válassza ki az alkalmazás típusát.
1. A **Támogatott fióktípusok csoportban**válassza a Fiókok lehetőséget **bármely szervezeti címtárban és személyes Microsoft-fiókban.**
1. Állítsa be az átirányítási URI-t az alap URL-címre.
1. Válassza a **Regisztráció** elemet az alkalmazás létrehozásához.
1. Konfigurálja az alkalmazás engedélyeit. Az **API-engedélyek**ben válassza **az Engedély hozzáadása,** majd a Saját **API-k**lehetőséget.
1. Írja be a középső rétegbeli szolgáltatás nevét a szövegmezőbe.
1. Válassza **az Engedélyek kiválasztása,** majd **az Access \<szolgáltatás nevének>** lehetőséget.

### <a name="configure-known-client-applications"></a>Ismert ügyfélalkalmazások konfigurálása

Ebben a forgatókönyvben a középső rétegbeli szolgáltatás nak meg kell szereznie a felhasználó beleegyezését az alsóbb rétegbeli API felhasználói beavatkozás nélküli eléréséhez. Az alsóbb rétegbeli API-hoz való hozzáférés engedélyezésének lehetőségét a hitelesítés során a hozzájárulási lépés részeként előre kell bemutatni.

Kövesse az alábbi lépéseket, hogy explicit módon kösse össze az ügyfélalkalmazás regisztrációját az Azure AD-ben a középső szintű szolgáltatás regisztrációjával. Ez a művelet egyetlen párbeszédablakba egyesíti az ügyfél és a középső réteg által kért hozzájárulást.

1. Nyissa meg a középső szintű szolgáltatás regisztrációját, és válassza **a Jegyzékfájl** megnyitásához a jegyzékszerkesztő megnyitásához.
1. Keresse `knownClientApplications` meg a tömbtulajdonságot, és adja hozzá elemként az ügyfélalkalmazás ügyfélazonosítóját.
1. Mentse a jegyzékfájlt a **Mentés**lehetőség kiválasztásával.

## <a name="service-to-service-access-token-request"></a>Szolgáltatás-szolgáltatás hozzáférési jogkivonat-kérelem

Hozzáférési jogkivonat kéréséhez készítsen HTTP-postát a bérlő-specifikus Azure AD-végponthoz a következő paraméterekkel:

```
https://login.microsoftonline.com/<tenant>/oauth2/token
```

Az ügyfélalkalmazást egy megosztott titok vagy egy tanúsítvány biztosítja.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Első eset: Hozzáférési jogkivonat-kérelem megosztott titkos titokkal

Megosztott titkos kulcs használata esetén a szolgáltatás-szolgáltatás hozzáférési jogkivonat-kérelem a következő paramétereket tartalmazza:

| Paraméter |  | Leírás |
| --- | --- | --- |
| grant_type |kötelező | A jogkivonat-kérelem típusa. Az OBO-kérelmek JSON webtokent (JWT) használnak, így az értéknek **urn:ietf:params:oauth:grant-type:jwt-bearer**értékűnek kell lennie. |
| Állítás |kötelező | A kérelemben használt hozzáférési jogkivonat értéke. |
| client_id |kötelező | Az alkalmazás azonosítója a hívó szolgáltatáshoz van rendelve az Azure AD-vel való regisztráció során. Ha meg szeretné találni az alkalmazásazonosítót az Azure Portalon, válassza az **Active Directory**lehetőséget, válassza ki a címtárat, majd válassza ki az alkalmazás nevét. |
| client_secret |kötelező | A hívó szolgáltatás hoz regisztrált kulcs az Azure AD-ben. Ezt az értéket a regisztráció időpontjában fel kellett volna jegyezni. |
| Erőforrás |kötelező | A fogadó szolgáltatás (biztonságos erőforrás) alkalmazásazonosító-URI-ja. Ha meg szeretné találni az alkalmazásazonosító URI-ját az Azure Portalon, válassza az **Active Directory** lehetőséget, és válassza ki a címtárat. Jelölje ki az alkalmazás nevét, válassza a **Minden beállítás**lehetőséget, majd a **Tulajdonságok**lehetőséget. |
| requested_token_use |kötelező | Itt adható meg, hogyan kell a kérelmet feldolgozni. A Nevében folyamatban az értéket **on_behalf_of**kell. |
| scope |kötelező | A jogkivonat-kérelem hatóköreinek szóközt választott listája. Az OpenID Connect esetében meg kell adni a **hatókör openid** azonosítót.|

#### <a name="example"></a>Példa

A következő HTTP POST hozzáférési https://graph.microsoft.com jogkivonatot kér a webes API-hoz. A `client_id` hozzáférési jogkivonatot kérő szolgáltatást azonosítja.

```
// line breaks for legibility only

POST /oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_secret=0Y1W%2BY3yYb3d9N8vSjvm8WrGzVZaAaHbHHcGbcgG%2BoI%3D
&resource=https%3A%2F%2Fgraph.microsoft.com
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.ewogICJhdWQiOiAiaHR0cHM6Ly9ncmFwaC5taWNyb3NvZnQuY29tIiwKICAiaXNzIjogImh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzI2MDM5Y2NlLTQ4OWQtNDAwMi04MjkzLTViMGM1MTM0ZWFjYi8iLAogICJpYXQiOiAxNDkzNDIzMTY4LAogICJuYmYiOiAxNDkzNDIzMTY4LAogICJleHAiOiAxNDkzNDY2OTUxLAogICJhY3IiOiAiMSIsCiAgImFpbyI6ICJBU1FBMi84REFBQUE1NnZGVmp0WlNjNWdBVWwrY1Z0VFpyM0VvV2NvZEoveWV1S2ZqcTZRdC9NPSIsCiAgImFtciI6IFsKICAgICJwd2QiCiAgXSwKICAiYXBwaWQiOiAiNjI1MzkxYWYtYzY3NS00M2U1LThlNDQtZWRkM2UzMGNlYjE1IiwKICAiYXBwaWRhY3IiOiAiMSIsCiAgImVfZXhwIjogMzAyNjgzLAogICJmYW1pbHlfbmFtZSI6ICJUZXN0IiwKICAiZ2l2ZW5fbmFtZSI6ICJOYXZ5YSIsCiAgImlwYWRkciI6ICIxNjcuMjIwLjEuMTc3IiwKICAibmFtZSI6ICJOYXZ5YSBUZXN0IiwKICAib2lkIjogIjFjZDRiY2FjLWI4MDgtNDIzYS05ZTJmLTgyN2ZiYjFiYjczOSIsCiAgInBsYXRmIjogIjMiLAogICJwdWlkIjogIjEwMDMzRkZGQTEyRUQ3RkUiLAogICJzY3AiOiAiVXNlci5SZWFkIiwKICAic3ViIjogIjNKTUlaSWJlYTc1R2hfWHdDN2ZzX0JDc3kxa1l1ekZKLTUyVm1Zd0JuM3ciLAogICJ0aWQiOiAiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwKICAidW5pcXVlX25hbWUiOiAibmF2eWFAZGRvYmFsaWFub3V0bG9vay5vbm1pY3Jvc29mdC5jb20iLAogICJ1cG4iOiAibmF2eWFAZGRvYmFsaWFub3V0bG9vay5vbm1pY3Jvc29mdC5jb20iLAogICJ1dGkiOiAieEN3ZnpoYS1QMFdKUU9MeENHZ0tBQSIsCiAgInZlciI6ICIxLjAiCn0.cqmUVjfVbqWsxJLUI1Z4FRx1mNQAHP-L0F4EMN09r8FY9bIKeO-0q1eTdP11Nkj_k4BmtaZsTcK_mUygdMqEp9AfyVyA1HYvokcgGCW_Z6DMlVGqlIU4ssEkL9abgl1REHElPhpwBFFBBenOk9iHddD1GddTn6vJbKC3qAaNM5VarjSPu50bVvCrqKNvFixTb5bbdnSz-Qr6n6ACiEimiI1aNOPR2DeKUyWBPaQcU5EAK0ef5IsVJC1yaYDlAcUYIILMDLCD9ebjsy0t9pj_7lvjzUSrbMdSCCdzCqez_MSNxrk1Nu9AecugkBYp3UVUZOIyythVrj6-sVvLZKUutQ
&requested_token_use=on_behalf_of
&scope=openid
```

### <a name="second-case-access-token-request-with-a-certificate"></a>Második eset: Hozzáférési jogkivonat-kérelem tanúsítvánnyal

A szolgáltatás-szolgáltatás hozzáférési jogkivonat-kérelem tanúsítvánnyal a következő paramétereket tartalmazza:

| Paraméter |  | Leírás |
| --- | --- | --- |
| grant_type |kötelező | A jogkivonat-kérelem típusa. Az OBO-kérelmek JWT-hozzáférési jogkivonatot használnak, így az értéknek **urna:ietf:params:oauth:grant-type:jwt-bearer**értékűnek kell lennie. |
| Állítás |kötelező | A kérelemben használt jogkivonat értéke. |
| client_id |kötelező | Az alkalmazás azonosítója a hívó szolgáltatáshoz van rendelve az Azure AD-vel való regisztráció során. Ha meg szeretné találni az alkalmazásazonosítót az Azure Portalon, válassza az **Active Directory**lehetőséget, válassza ki a címtárat, majd válassza ki az alkalmazás nevét. |
| client_assertion_type |kötelező |Az értéket`urn:ietf:params:oauth:client-assertion-type:jwt-bearer` |
| client_assertion |kötelező | Egy JSON webtoken, amelyet az alkalmazás hitelesítő adataiként regisztrált tanúsítvánnyal hoz létre és ír alá. A [tanúsítványhitelesítő adatokból megtudhatja,](../develop/active-directory-certificate-credentials.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) hogyan lehet regisztrálni a tanúsítványt.|
| Erőforrás |kötelező | A fogadó szolgáltatás (biztonságos erőforrás) alkalmazásazonosító-URI-ja. Ha meg szeretné találni az alkalmazásazonosító URI-ját az Azure Portalon, válassza az **Active Directory** lehetőséget, és válassza ki a címtárat. Jelölje ki az alkalmazás nevét, válassza a **Minden beállítás**lehetőséget, majd a **Tulajdonságok**lehetőséget. |
| requested_token_use |kötelező | Itt adható meg, hogyan kell a kérelmet feldolgozni. A Nevében folyamatban az értéket **on_behalf_of**kell. |
| scope |kötelező | A jogkivonat-kérelem hatóköreinek szóközt választott listája. Az OpenID Connect esetében meg kell adni a **hatókör openid** azonosítót.|

Ezek a paraméterek majdnem ugyanazok, mint a megosztott `client_secret parameter` titok kérésével, azzal `client_assertion_type` `client_assertion`a különbséggel, hogy a paramétert két paraméter váltja fel: és a .

#### <a name="example"></a>Példa

A következő HTTP POST hozzáférési https://graph.microsoft.com jogkivonatot kér a webes API-hoz egy tanúsítvánnyal. A `client_id` hozzáférési jogkivonatot kérő szolgáltatást azonosítja.

```
// line breaks for legibility only

POST /oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&resource=https%3A%2F%2Fgraph.microsoft.com
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2Rkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tLzE5MjNmODYyLWU2ZGMtNDFhMy04MWRhLTgwMmJhZTAwYWY2ZCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzI2MDM5Y2NlLTQ4OWQtNDAwMi04MjkzLTViMGM1MTM0ZWFjYi8iLCJpYXQiOjE0OTM0MjMxNTIsIm5iZiI6MTQ5MzQyMzE1MiwiZXhwIjoxNDkzNDY2NjUyLCJhY3IiOiIxIiwiYWlvIjoiWTJaZ1lCRFF2aTlVZEc0LzM0L3dpQndqbjhYeVp4YmR1TFhmVE1QeG8yYlN2elgreHBVQSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiJiMzE1MDA3OS03YmViLTQxN2YtYTA2YS0zZmRjNzhjMzI1NDUiLCJhcHBpZGFjciI6IjAiLCJlX2V4cCI6MzAyNDAwLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidmVyIjoiMS4wIn0.R-Ke-XO7lK0r5uLwxB8g5CrcPAwRln5SccJCfEjU6IUqpqcjWcDzeDdNOySiVPDU_ZU5knJmzRCF8fcjFtPsaA4R7vdIEbDuOur15FXSvE8FvVSjP_49OH6hBYqoSUAslN3FMfbO6Z8YfCIY4tSOB2I6ahQ_x4ZWFWglC3w5mK-_4iX81bqi95eV4RUKefUuHhQDXtWhrSgIEC0YiluMvA4TnaJdLq_tWXIc4_Tq_KfpkvI004ONKgU7EAMEr1wZ4aDcJV2yf22gQ1sCSig6EGSTmmzDuEPsYiyd4NhidRZJP4HiiQh-hePBQsgcSgYGvz9wC6n57ufYKh2wm_Ti3Q
&requested_token_use=on_behalf_of
&scope=openid
```

## <a name="service-to-service-access-token-response"></a>Szolgáltatás-szolgáltatás hozzáférési jogkivonat válasza

A sikeres válasz egy JSON OAuth 2.0 válasz a következő paraméterekkel:

| Paraméter | Leírás |
| --- | --- |
| token_type |A token típusának értékét jelzi. Az Azure AD által támogatott egyetlen típus a **bemutatóra.** A tulajdonosi jogkivonatokról további információt az [OAuth 2.0 engedélyezési keretrendszerében talál: tulajdonosi token használata (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |
| scope |A jogkivonatban megadott hozzáférési tartomány. |
| expires_in |A hozzáférési jogkivonat érvényességi ideje (másodpercben). |
| expires_on |A hozzáférési jogkivonat lejártának időpontja. A dátum az 1970-01-01T0:0:0Z UTC és a lejárati idő között eltelt másodpercek számaként jelenik meg. Ez az érték határozza meg a gyorsítótárazott jogkivonatok élettartamát. |
| Erőforrás |A fogadó szolgáltatás (biztonságos erőforrás) alkalmazásazonosító-URI-ja. |
| access_token |A kért hozzáférési jogkivonat. A hívó szolgáltatás ezt a jogkivonatot használhatja a fogadó szolgáltatás hitelesítéséhez. |
| id_token |A kért azonosító jogkivonat. A hívó szolgáltatás használhatja ezt a jogkivonatot a felhasználó identitásának ellenőrzésére, és munkamenetet kezdhet a felhasználóval. |
| refresh_token |A kért hozzáférési jogkivonat frissítési jogkivonata. A hívó szolgáltatás használhatja ezt a jogkivonatot, hogy kérjen egy másik hozzáférési jogkivonatot az aktuális hozzáférési jogkivonat lejárta után. |

### <a name="success-response-example"></a>Példa sikerválaszra

A következő példa sikeres választ mutat a https://graph.microsoft.com webes API-hoz egy hozzáférési jogkivonatra vonatkozó kérelemre.

```json
{
    "token_type":"Bearer",
    "scope":"User.Read",
    "expires_in":"43482",
    "ext_expires_in":"302683",
    "expires_on":"1493466951",
    "not_before":"1493423168",
    "resource":"https://graph.microsoft.com",
    "access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRvd3MubmV0IiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiLyIsImlhdCI6MTQ5MzQyMzE2OCwibmJmIjoxNDkzNDIzMTY4LCJleHAiOjE0OTM0NjY5NTEsImFjciI6IjEiLCJhaW8iOiJBU1FBMi84REFBQUE1NnZGVmp0WlNjNWdBVWwrY1Z0VFpyM0VvV2NvZEoveWV1S2ZqcTZRdC9NPSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJhcHBpZGFjciI6IjEiLCJlX2V4cCI6MzAyNjgzLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJwdWlkIjoiMTAwMzNGRkZBMTJFRDdGRSIsInNjcCI6IlVzZXIuUmVhZCIsInN1YiI6IjNKTUlaSWJlYTc1R2hfWHdDN2ZzX0JDc3kxa1l1ekZKLTUyVm1Zd0JuM3ciLCJ0aWQiOiIyNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IiLCJ1bmlxdWVfbmFtZSI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXBuIjoibmF2eWFAZGRvYmFsaWFub3V0bG9vay5vbm1pY3Jvc29mdC5jb20iLCJ1dGkiOiJ4Q3dmemhhLVAwV0pRT0x4Q0dnS0FBIiwidmVyIjoiMS4wIn0.cqmUVjfVbqWsxJLUI1Z4FRx1mNQAHP-L0F4EMN09r8FY9bIKeO-0q1eTdP11Nkj_k4BmtaZsTcK_mUygdMqEp9AfyVyA1HYvokcgGCW_Z6DMlVGqlIU4ssEkL9abgl1REHElPhpwBFFBBenOk9iHddD1GddTn6vJbKC3qAaNM5VarjSPu50bVvCrqKNvFixTb5bbdnSz-Qr6n6ACiEimiI1aNOPR2DeKUyWBPaQcU5EAK0ef5IsVJC1yaYDlAcUYIILMDLCD9ebjsy0t9pj_7lvjzUSrbMdSCCdzCqez_MSNxrk1Nu9AecugkBYp3UVUZOIyythVrj6-sVvLZKUutQ",
    "refresh_token":"AQABAAAAAABnfiG-mA6NTae7CdWW7QfdjKGu9-t1scy_TDEmLi4eLQMjJGt_nAoVu6A4oSu1KsRiz8XyQIPKQxSGfbf2FoSK-hm2K8TYzbJuswYusQpJaHUQnSqEvdaCeFuqXHBv84wjFhuanzF9dQZB_Ng5za9xKlUENrNtlq9XuLNVKzxEyeUM7JyxzdY7JiEphWImwgOYf6II316d0Z6-H3oYsFezf4Xsjz-MOBYEov0P64UaB5nJMvDyApV-NWpgklLASfNoSPGb67Bc02aFRZrm4kLk-xTl6eKE6hSo0XU2z2t70stFJDxvNQobnvNHrAmBaHWPAcC3FGwFnBOojpZB2tzG1gLEbmdROVDp8kHEYAwnRK947Py12fJNKExUdN0njmXrKxNZ_fEM33LHW1Tf4kMX_GvNmbWHtBnIyG0w5emb-b54ef5AwV5_tGUeivTCCysgucEc-S7G8Cz0xNJ_BOiM_4bAv9iFmrm9STkltpz0-Tftg8WKmaJiC0xXj6uTf4ZkX79mJJIuuM7XP4ARIcLpkktyg2Iym9jcZqymRkGH2Rm9sxBwC4eeZXM7M5a7TJ-5CqOdfuE3sBPq40RdEWMFLcrAzFvP0VDR8NKHIrPR1AcUruat9DETmTNJukdlJN3O41nWdZOVoJM-uKN3uz2wQ2Ld1z0Mb9_6YfMox9KTJNzRzcL52r4V_y3kB6ekaOZ9wQ3HxGBQ4zFt-2U0mSszIAA",
    "id_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC8yNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IvIiwiaWF0IjoxNDkzNDIzMTY4LCJuYmYiOjE0OTM0MjMxNjgsImV4cCI6MTQ5MzQ2Njk1MSwiYW1yIjpbInB3ZCJdLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXRpIjoieEN3ZnpoYS1QMFdKUU9MeENHZ0tBQSIsInZlciI6IjEuMCJ9."
}
```

### <a name="error-response-example"></a>Példa hibaválaszra

Az Azure AD-token végpont hibaválaszt ad vissza, amikor megpróbál megszerezni egy hozzáférési jogkivonatot egy feltételes hozzáférési szabályzattal (például többtényezős hitelesítéssel) beállított alsóbb rétegbeli API-hoz. A középső rétegbeli szolgáltatásnak felszínre kell írnia ezt a hibát az ügyfélalkalmazásban, hogy az ügyfélalkalmazás biztosítani tudja a felhasználói beavatkozást a feltételes hozzáférési házirend kielégítéséhez.

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

## <a name="use-the-access-token-to-access-the-secured-resource"></a>A hozzáférési jogkivonat használata a védett erőforrás eléréséhez

A középső rétegbeli szolgáltatás a beszerzett hozzáférési jogkivonat segítségével hitelesített kérelmeket az `Authorization` alsóbb rétegbeli webes API-t a jogkivonat a fejlécben beállításával.

### <a name="example"></a>Példa

```
GET /me?api-version=2013-11-08 HTTP/1.1
Host: graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRvd3MubmV0IiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiLyIsImlhdCI6MTQ5MzQyMzE2OCwibmJmIjoxNDkzNDIzMTY4LCJleHAiOjE0OTM0NjY5NTEsImFjciI6IjEiLCJhaW8iOiJBU1FBMi84REFBQUE1NnZGVmp0WlNjNWdBVWwrY1Z0VFpyM0VvV2NvZEoveWV1S2ZqcTZRdC9NPSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJhcHBpZGFjciI6IjEiLCJlX2V4cCI6MzAyNjgzLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJwdWlkIjoiMTAwMzNGRkZBMTJFRDdGRSIsInNjcCI6IlVzZXIuUmVhZCIsInN1YiI6IjNKTUlaSWJlYTc1R2hfWHdDN2ZzX0JDc3kxa1l1ekZKLTUyVm1Zd0JuM3ciLCJ0aWQiOiIyNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IiLCJ1bmlxdWVfbmFtZSI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXBuIjoibmF2eWFAZGRvYmFsaWFub3V0bG9vay5vbm1pY3Jvc29mdC5jb20iLCJ1dGkiOiJ4Q3dmemhhLVAwV0pRT0x4Q0dnS0FBIiwidmVyIjoiMS4wIn0.cqmUVjfVbqWsxJLUI1Z4FRx1mNQAHP-L0F4EMN09r8FY9bIKeO-0q1eTdP11Nkj_k4BmtaZsTcK_mUygdMqEp9AfyVyA1HYvokcgGCW_Z6DMlVGqlIU4ssEkL9abgl1REHElPhpwBFFBBenOk9iHddD1GddTn6vJbKC3qAaNM5VarjSPu50bVvCrqKNvFixTb5bbdnSz-Qr6n6ACiEimiI1aNOPR2DeKUyWBPaQcU5EAK0ef5IsVJC1yaYDlAcUYIILMDLCD9ebjsy0t9pj_7lvjzUSrbMdSCCdzCqez_MSNxrk1Nu9AecugkBYp3UVUZOIyythVrj6-sVvLZKUutQ
```

## <a name="saml-assertions-obtained-with-an-oauth20-obo-flow"></a>Az OAuth2.0 OBO-folyamattal kapott SAML-állítások

Egyes OAuth-alapú webszolgáltatások nak más webszolgáltatás API-khoz kell hozzáférnie, amelyek nem interaktív folyamatokban saml-állításokat fogadnak el. Az Azure Active Directory saml-állítást biztosíthat egy olyan folyamat nevében, amely egy SAML-alapú webszolgáltatást használ célerőforrásként.

>[!NOTE]
>Ez egy nem szabványos bővítmény az OAuth 2.0 a nevében folyamat, amely lehetővé teszi, hogy egy OAuth2-alapú alkalmazás eléréséhez webszolgáltatás API-végpontok, amelyek saml-jogkivonatokat.

> [!TIP]
> Ha egy előtér-webalkalmazássamla által védett webszolgáltatást hív meg, egyszerűen felhívhatja az API-t, és elindíthat egy normál interaktív hitelesítési folyamatot a felhasználó meglévő munkamenetével. Csak akkor kell obo-folyamatot használnia, ha egy szolgáltatás-szolgáltatás híváshoz SAML-jogkivonat szükséges a felhasználói környezet biztosításához.

### <a name="obtain-a-saml-token-by-using-an-obo-request-with-a-shared-secret"></a>SAML-token beszerzése megosztott titkos titokkal rendelkező OBO-kérelem használatával

Az SAML-feltétel szolgáltatás-szolgáltatás kérése a következő paramétereket tartalmazza:

| Paraméter |  | Leírás |
| --- | --- | --- |
| grant_type |kötelező | A jogkivonat-kérelem típusa. JWT-t használó kérelem esetén az értéknek **urna:ietf:params:oauth:grant-type:jwt-bearer**. |
| Állítás |kötelező | A kérelemben használt hozzáférési jogkivonat értéke.|
| client_id |kötelező | Az alkalmazás azonosítója a hívó szolgáltatáshoz van rendelve az Azure AD-vel való regisztráció során. Ha meg szeretné találni az alkalmazásazonosítót az Azure Portalon, válassza az **Active Directory**lehetőséget, válassza ki a címtárat, majd válassza ki az alkalmazás nevét. |
| client_secret |kötelező | A hívó szolgáltatás hoz regisztrált kulcs az Azure AD-ben. Ezt az értéket a regisztráció időpontjában fel kellett volna jegyezni. |
| Erőforrás |kötelező | A fogadó szolgáltatás (biztonságos erőforrás) alkalmazásazonosító-URI-ja. Ez az az erőforrás, amely az SAML-token célközönsége lesz. Ha meg szeretné találni az alkalmazásazonosító URI-ját az Azure Portalon, válassza az **Active Directory** lehetőséget, és válassza ki a címtárat. Jelölje ki az alkalmazás nevét, válassza a **Minden beállítás**lehetőséget, majd a **Tulajdonságok**lehetőséget. |
| requested_token_use |kötelező | Itt adható meg, hogyan kell a kérelmet feldolgozni. A Nevében folyamatban az értéket **on_behalf_of**kell. |
| requested_token_type | kötelező | Megadja a kért jogkivonat típusát. Az érték lehet **urn:ietf:params:oauth:token-type:saml2** vagy **urn:ietf:params:oauth:token-type:saml1** az elért erőforrás követelményeitől függően. |

A válasz egy UTF8 és Base64url-be kódolt SAML-jogkivonatot tartalmaz.

- **SubjectConfirmationData egy OBO-hívásból származó SAML-helyességifeltételhez:** Ha a célalkalmazásnak címzettértéket kell igényelnie a **SubjectConfirmationData**alkalmazásban, akkor az értéknek nem helyettesítő karakter válasz URL-címnek kell lennie az erőforrás-alkalmazás konfigurációjában.
- **A SubjectConfirmationData csomópont**: A csomópont nem tartalmazhat **InResponseTo** attribútumot, mivel nem része egy SAML-válasznak. Az SAML-jogkivonatot fogadó alkalmazásnak **inResponseTo** attribútum nélkül el kell tudnia fogadni az SAML-feltételt.

- **Hozzájárulás:** Hozzájárulás szükséges ahhoz, hogy egy OAuth-folyamaton felhasználói adatokat tartalmazó SAML-jogkivonatot kapjon. Az engedélyekről és a rendszergazdai hozzájárulás beszerzéséről az [Engedélyek és hozzájárulás az Azure Active Directory 1.0-s futópontjában című témakörben talál.](https://docs.microsoft.com/azure/active-directory/azuread-dev/v1-permissions-consent)

### <a name="response-with-saml-assertion"></a>Válasz SAML állítással

| Paraméter | Leírás |
| --- | --- |
| token_type |A token típusának értékét jelzi. Az Azure AD által támogatott egyetlen típus a **bemutatóra.** A tulajdonosi jogkivonatokról további információt az [OAuth 2.0 engedélyezési keretrendszer: tulajdonosi jogkivonat-használat (RFC 6750) című](https://www.rfc-editor.org/rfc/rfc6750.txt)témakörben talál. |
| scope |A jogkivonatban megadott hozzáférési tartomány. |
| expires_in |A hozzáférési jogkivonat érvényességi ideje (másodpercben). |
| expires_on |A hozzáférési jogkivonat lejártának időpontja. A dátum az 1970-01-01T0:0:0Z UTC és a lejárati idő között eltelt másodpercek számaként jelenik meg. Ez az érték határozza meg a gyorsítótárazott jogkivonatok élettartamát. |
| Erőforrás |A fogadó szolgáltatás (biztonságos erőforrás) alkalmazásazonosító-URI-ja. |
| access_token |Az SAML-állítást visszaadó paraméter. |
| refresh_token |A frissítési jogkivonat. A hívó szolgáltatás használhatja ezt a jogkivonatot, hogy kérjen egy másik hozzáférési jogkivonatot az aktuális SAML-állítás lejárta után. |

- token_type: Hordozó
- expires_in: 3296
- ext_expires_in: 0
- expires_on: 1529627844
- Erőforrás:`https://api.contoso.com`
- access_token: \<SAML állítás\>
- issued_token_type: urn:ietf:params:oauth:token-type:saml2
- refresh_token: \<Frissítési token\>

## <a name="client-limitations"></a>Ügyfél korlátozások

A helyettesítő karakteres válasz URL-címekkel `id_token` rendelkező nyilvános ügyfelek nem használhatják az OBO-folyamatokat. A bizalmas ügyfél azonban továbbra is beválthatja az implicit támogatási folyamaton keresztül beszerzett **hozzáférési** jogkivonatokat, még akkor is, ha a nyilvános ügyfél regisztrált helyettesítő karakteres átirányítási URI-val rendelkezik.

## <a name="next-steps"></a>További lépések

További információ az OAuth 2.0 protokollról és az ügyfél hitelesítő adatait használó szolgáltatás-szolgáltatás hitelesítés végrehajtásának másik módjáról:

* [Szolgáltatás szolgáltatáshoz történő hitelesítés az OAuth 2.0 ügyfélhitelesítő adatok megadásával az Azure AD-ben](v1-oauth2-client-creds-grant-flow.md)
* [OAuth 2.0 az Azure AD-ben](v1-protocols-oauth-code.md)
