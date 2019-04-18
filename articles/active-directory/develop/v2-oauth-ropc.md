---
title: Használja a Microsoft identitásplatformja ROPC használó felhasználók bejelentkeztetéséhez |} Az Azure
description: Böngésző nélküli hitelesítés támogatása folyamatok használata az erőforrás tulajdonosának jelszava hitelesítő adatok megadásával.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8c1372263bfa3f684d30ad583bfb6a9d434c3cc2
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59499937"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-resource-owner-password-credential"></a>A Microsoft identity platform és az OAuth 2.0-s erőforrás tulajdonos jelszavára vonatkozó hitelesítőadat

A Microsoft identity platform támogatja a [erőforrás tulajdonos jelszavára vonatkozó hitelesítőadat (ROPC) biztosítása](https://tools.ietf.org/html/rfc6749#section-4.3), amely lehetővé teszi, hogy az alkalmazás közvetlenül kezeli a jelszavukat a felhasználói bejelentkezés. A ROPC folyamat szükséges nagyfokú megbízhatóság és a felhasználó kapta, és a fejlesztők csak használja ezt a folyamatot nem lehet használni a más, az biztonságosabb, folyamatok.

> [!IMPORTANT]
> * A Microsoft identity platform végpont ROPC csak az Azure AD-bérlőt, nem személyes fiókokat támogatja. Ez azt jelenti, hogy egy bérlő-specifikus végpontot kell használnia (`https://login.microsoftonline.com/{TenantId_or_Name}`) vagy a `organizations` végpont.
> * Személyes fiókokat, amelyek ügyfeleinket meghívjuk az Azure AD-bérlő ROPC nem használható.
> * Fiókok, amelyek nem rendelkeznek a jelszavak ROPC keresztül nem tud bejelentkezni. Ebben az esetben javasoljuk, hogy inkább egy másik folyamat az alkalmazáshoz.
> * Ha a felhasználók jelentkezzen be az alkalmazás többtényezős hitelesítés (MFA) használatára van szükség, akkor le lesz tiltva helyette.

## <a name="protocol-diagram"></a>Protokoll diagramja

Az alábbi ábrán látható a ROPC folyamatot.

![ROPC folyamat](media/v2-oauth2-ropc/v2-oauth-ropc.png)

## <a name="authorization-request"></a>Engedélyezési kérés

A ROPC flow-t egyetlen kérelem&mdash;azonosítása és a felhasználói hitelesítő adatokat küld az ügyfél az Identitásszolgáltató, és ezután cserébe az jogkivonatokat kap. Az ügyfél ezt megelőzően kell igényelnie a felhasználó e-mail-cím (UPN) és a jelszót. A kérelem sikeres, után azonnal az ügyfélnek kell kulcsvédelmet a felhasználó hitelesítő adatait a memóriából. Ez soha nem menteni kell azokat.

> [!TIP]
> Próbálja ki a Postmanben a kérelem végrehajtása!
> [![A Postmanben futtatása](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)


```
// Line breaks and spaces are for legibility only.

POST {tenant}/oauth2/v2.0/token
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=user.read%20openid%20profile%20offline_access
&username=MyUsername@myTenant.com
&password=SuperS3cret
&grant_type=password
```

| Paraméter | Állapot | Leírás |
| --- | --- | --- |
| `tenant` | Szükséges | A directory-bérlővel, amelyet a felhasználó be szeretne. Ez lehet GUID vagy rövid név formátumban. Ez a paraméter nem állítható be `common` vagy `consumers`, de beállítható `organizations`. |
| `grant_type` | Szükséges | Meg kell `password`. |
| `username` | Szükséges | A felhasználó e-mail címét. |
| `password` | Szükséges | A felhasználó jelszavát. |
| `scope` | Ajánlott | Szóközzel elválasztott listáját [hatókörök](v2-permissions-and-consent.md), vagy az alkalmazáshoz szükséges jogosultságokkal. Ezeken a hatókörökön kell kell egyezett bele kelljen egy rendszergazda vagy a felhasználó egy interaktív folyamat. |

### <a name="successful-authentication-response"></a>A sikeres hitelesítési válaszra.

A következő token sikeres válasz egy példát mutat be:

```json
{
    "token_type": "Bearer",
    "scope": "User.Read profile openid email",
    "expires_in": 3599,
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD..."
}
```

| Paraméter | Formátum | Leírás |
| --------- | ------ | ----------- |
| `token_type` | String | Mindig `Bearer`. |
| `scope` | Szóközzel elválasztva karakterláncok | Egy hozzáférési jogkivonatot adott vissza, ha ezt a paramétert a a hozzáférési jogkivonat érvénytelen, a hatókörök sorolja fel. |
| `expires_in`| int | A csomagban foglalt hozzáférési jogkivonat érvénytelen, a másodpercek számát. |
| `access_token`| Átlátszatlan karakterlánc | Kiadva a [hatókörök](v2-permissions-and-consent.md) , amely lett kérve. |
| `id_token` | JWT | Kiadott, ha az eredeti `scope` paraméter tartalmazza a `openid` hatókör. |
| `refresh_token` | Átlátszatlan karakterlánc | Ha kiállított az eredeti `scope` paraméter `offline_access`. |

A frissítési jogkivonat segítségével szerezzen új hozzáférési jogkivonatok, és frissítési jogkivonatok segítségével ismertetett folyamatot a [OAuth kód flow dokumentáció](v2-oauth2-auth-code-flow.md#refresh-the-access-token).

### <a name="error-response"></a>Hiba történt a válasz

Ha a felhasználó még nem biztosított, a helyes felhasználónevet vagy jelszót, vagy az ügyfél a kért jóváhagyás nem kapott, a hitelesítés sikertelen lesz.

| Hiba | Leírás | Ügyfélművelet |
|------ | ----------- | -------------|
| `invalid_grant` | A hitelesítés nem sikerült | A hitelesítő adatok hibásak, vagy az ügyfél nem rendelkezik a kért hatókörhöz jóváhagyás. A hatókörök nem kapnak, ha egy `consent_required` hiba adja vissza. Ha ez történik, az ügyfél egy interaktív kérdés egy WebView-t vagy a böngésző használatával kell küldenie a felhasználót. |
| `invalid_request` | A kérés nem megfelelően állították össze. | Engedélyezési típusa nem támogatott a a `/common` vagy `/consumers` hitelesítési környezeteket.  Használat `/organizations` helyette. |
| `invalid_client` | Az alkalmazás nem megfelelően van beállítva | Ez akkor fordulhat elő, ha a `allowPublicClient` tulajdonság true a [alkalmazásjegyzék](reference-app-manifest.md). A `allowPublicClient` tulajdonság van szükség, mert a ROPC engedély nem kell átirányítási URI-t. Az Azure AD nem tudja, hogy az alkalmazás egy nyilvános ügyfélalkalmazás vagy bizalmas az ügyfélalkalmazások, kivéve, ha a tulajdonság értéke. Vegye figyelembe, hogy ROPC csak a támogatott nyilvános ügyfélalkalmazások. |

## <a name="learn-more"></a>Részletek

* Próbálja ki ROPC maga használatával a [konzol mintaalkalmazás](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2).
* Annak megállapításához, hogy használjon a v2.0-végpont, olvassa el [a Microsoft identity platform korlátozásai](active-directory-v2-limitations.md).
