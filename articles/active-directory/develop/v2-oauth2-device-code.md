---
title: Az Azure AD v2.0 használata a felhasználók a böngésző nélküli eszközökön |} A Microsoft Docs
description: Az eszköz kódmegadásának használata embedded és a böngésző nélküli hitelesítési folyamatokat hozhat létre.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 780eec4d-7ee1-48b7-b29f-cd0b8cb41ed3
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 83c1deb7c767c29046e6c1af4452270e90b391df
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2018
ms.locfileid: "49987732"
---
# <a name="azure-active-directory-v20-and-the-oauth-20-device-code-flow"></a>Az Azure Active Directory 2.0-s verzió és az OAuth 2.0 eszköz kódfolyamat

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Az Azure AD támogatja a [eszköz kódmegadás](https://tools.ietf.org/html/draft-ietf-oauth-device-flow-12), amely lehetővé teszi, hogy a felhasználók számára, hogy jelentkezzen be a bemeneti korlátozott eszközök, mint egy Okostelevízió, IoT-eszközből vagy nyomtató.  Ahhoz, hogy ez a folyamat, az eszköz a böngészőben egy másik eszközön, hogy jelentkezzen be a felhasználói látogatás egy weblap rendelkezik.  Miután a felhasználó bejelentkezik, az eszköz nem képes hozzáférési és frissítési jogkivonatok, igény szerint.  

> [!Important] 
> Jelenleg a v2.0-végpont csak támogatja az eszköz a folyamat az Azure AD-bérlőt, de nem személyes fiókokat.  Ez azt jelenti, hogy kell használnia egy ábráját végponton, vagy a szervezetek végpont.  
>
> Személyes fiókokat is meghív egy Azure AD-bérlővel, használhatja az eszköz folyamat engedély megadása, de csak a bérlői kontextusában lesz.

> [!NOTE]
> A v2.0-végpont nem támogatja az összes Azure Active Directory-forgatókönyvek és funkciók. Annak megállapításához, hogy használjon a v2.0-végpont, olvassa el [v2.0 korlátozások](active-directory-v2-limitations.md).
>

## <a name="protocol-diagram"></a>Protokoll diagramja

A teljes eszköz hitelesítésikód-folyamata a következő diagram hasonlóan néz ki. A cikk későbbi részében lépések mindegyikét ismertetünk.

![Eszköz kódfolyamat](media/v2-oauth2-device-flow/v2-oauth-device-flow.png)

## <a name="device-authorization-request"></a>Eszköz engedélyezési kérése

Az ügyfél először ellenőriznie kell az eszköz- és kódot, amelyről a hitelesítési authentication-kiszolgálóval.  Az ügyfél ezt a kérelmet a gyűjti a `/devicecode` végpont. A kéréshez az ügyfél is tartalmaznia kell az engedélyeket kell beszerezni a felhasználó elől.  Attól a pillanattól kezdve a kérelem érkezik, a felhasználó rendelkezik-e bejelentkezni csak 15 perc (a szokásos értéke `expires_in`), így csak ilyen kérést, ha a felhasználó azt jelezte, azok készen jelentkezzen be.

```
// Line breaks are for legibility only.

POST https://login.microsoftonline.com/{tenant}/devicecode
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
scope=user.read%20openid%20profile

```

| Paraméter | Állapot | Leírás |
| --- | --- | --- |
| bérlő |Szükséges |A directory-bérlőhöz, amelyet szeretne az engedélyt. Ez lehet GUID vagy rövid név formátumban.  |
| client_id |Szükséges |Az alkalmazás AZONOSÍTÓJÁT, amely a [alkalmazásregisztrációs portálon](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) az alkalmazáshoz rendelt. |
| scope | Ajánlott | Szóközzel elválasztott listáját [hatókörök](v2-permissions-and-consent.md) , hogy szeretné-e a felhasználót, hogy engedélyt adjanak az.  |

### <a name="device-authorization-response"></a>Eszköz hitelesítési válasza

A sikeres válasz, hogy a felhasználó jelentkezzen be a szükséges adatokat tartalmazó JSON-objektum lesz.  

| Paraméter | Formátum | Leírás |
| ---              | --- | --- |
|`device_code`     |Sztring| Hosszú karakterlánc, amellyel az ügyfél és az engedélyezési kiszolgáló között a munkamenet ellenőrzése.  Ez szolgál az ügyfél által a hozzáférési jogkivonatot kér az engedélyezési kiszolgálón. |
|`user_code`       |Sztring| A felhasználó a munkamenet egy másodlagos eszközön azonosításához használt rövid karakterlánc.|
|`verification_uri`|URI| A felhasználónak el kell küldeni az URI a `user_code` annak érdekében, hogy jelentkezzen be. |
|`verification_uri_complete`|URI| Egy URI egyesítése a `user_code` és a `verification_uri`(például Bluetooth egy eszközre vagy QR-kód) használatával a felhasználó nem szöveges továbbítására szolgál.  |
|`expires_in`      |int| Mielőtt másodpercben a `device_code` és `user_code` lejár. |
|`interval`        |int| Az ügyfélnek várnia kell lekérdezési kérelmek közötti másodpercek számát. |
| `message`        |Sztring| A felhasználói utasításokat emberek számára olvasható karakterlánc.  Ez azzal kell honosított egy **lekérdezési paraméter** az űrlap a kérésben `?mkt=xx-XX`, a megfelelő nyelvi kulturális környezet kód töltését. |

## <a name="authenticating-the-user"></a>A felhasználó hitelesítése

Fogadás után a `user_code` és `verification_uri`, az ügyfél megjeleníti ezeket a felhasználó ezt őket a mobiltelefonon vagy PC-böngésző adatokkal való bejelentkezést.  Emellett az ügyfél egy QR-kódot vagy egy hasonló mechanizmus megjelenítéséhez használható a `verfication_uri_complete`, amely vesz igénybe, írja be a lépés a `user_code` a felhasználó számára.

A felhasználó hitelesíti magát amíg a `verification_uri`, az ügyfél lekérdezési kell lennie a `/token` végpont a kért jogkivonat használatával a `device_code`.

``` 
POST https://login.microsoftonline.com/tenant/oauth2/v2.0/token
Content-Type: application/x-www-form-urlencoded

grant_type: urn:ietf:params:oauth:grant-type:device_code
client_id: 6731de76-14a6-49ae-97bc-6eba6914391e
device_code: GMMhmHCXhWEzkobqIHGG_EnNYYsAkukHspeYUk9E8
```

|Paraméter | Szükséges | Leírás|
| -------- | -------- | ---------- |
|`grant_type` | Szükséges| Kell lennie `urn:ietf:params:oauth:grant-type:device_code`|
|`client_id`  | Szükséges| Meg kell egyeznie a `client_id` a kiindulási kérelemhez használt. |
|`device_code`| Szükséges| A `device_code` adja vissza az eszköz engedélyezési kérésben.  |

### <a name="expected-errors"></a>Várt hibák

Mivel az eszköz kódfolyamat lekérdezési protokoll, az ügyfél kell várhatóan fogadási hibák, mielőtt a felhasználó hitelesítése befejeződött.  

| Hiba | Leírás | Ügyfélművelet |
|------ | ----------- | -------------|
| `authorization_pending` |  A felhasználó rendelkezik még nem fejeződött be hitelesítéséhez, de nem megszakította a folyamatot. | Ismételje meg a kérelem után legalább `interval` másodperc. |
| `authorization_declined`|  A felhasználó a hitelesítési kérelem megtagadva.| Lekérdezés leállítása, és a egy nem hitelesített állapotba visszaállítása.  |
| `bad_verification_code`|A `device_code` küldött a `/token` végpont nem ismerhető fel. | Győződjön meg arról, hogy az ügyfél a megfelelő elküldése `device_code` a kérésben. |
| `expired_token`|  Legalább `expires_in` másodperc megfeleltek, és a hitelesítés már nem lehetséges a `device_code`. | Lekérdezés leállítása, és a egy nem hitelesített állapotba visszaállítása. |


### <a name="succesful-authentication-response"></a>Sikeres hitelesítési válaszra.

Token sikeres válasz fog kinézni:

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
|`token_type` | Sztring| Mindig "tulajdonosi. |
|`scope` | Hely adható karakterláncok | Egy hozzáférési jogkivonatot adott vissza, ha ez a hozzáférési jogkivonat érvénytelen, a hatókörök sorolja fel. |
|`expires_in`| int | A csomagban foglalt hozzáférési jogkivonat előtt eltelt másodpercek száma esetén érvényes. |
|`access_token`| Átlátszatlan karakterlánc | Kiadva a [hatókörök](v2-permissions-and-consent.md) , amely lett kérve.  |
|`id_token`   | JWT | Kiadott, ha az eredeti `scope` paraméter tartalmazza a `openid` hatókör.  |
|`refresh_token` | Átlátszatlan karakterlánc | Ha kiállított az eredeti `scope` paraméter `offline_access`.  |

A frissítési jogkivonat segítségével szerezzen új hozzáférési jogkivonatok, és frissítési jogkivonatok segítségével részletes leírást talál az ugyanezt a folyamatot a [OAuth kód flow dokumentáció](v2-oauth2-auth-code-flow.md#refresh-the-access-token).  