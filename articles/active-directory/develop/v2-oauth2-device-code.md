---
title: Felhasználói bejelentkezés a Microsoft Identity platform használatával böngésző nélküli eszközökön | Azure
description: Beágyazott és böngésző nélküli hitelesítési folyamatokat hozhat létre az eszköz kódjának megadása alapján.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 274c4e89ff3f996cc71cdacdfb7b5b72e813ae4b
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2019
ms.locfileid: "68297663"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-device-code-flow"></a>Microsoft Identity platform és a OAuth 2,0-es eszköz kódjának folyamata

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

A Microsoft Identity platform támogatja az [eszköz kódjainak](https://tools.ietf.org/html/draft-ietf-oauth-device-flow-12)használatát, amely lehetővé teszi a felhasználók számára, hogy bejelentkezzenek a bemeneti korlátozás alá tartozó eszközökre, például egy intelligens TV-re, IoT eszközre vagy nyomtatóra.  A folyamat engedélyezéséhez az eszközön a felhasználó meglátogat egy weblapot a böngészőben egy másik eszközre a bejelentkezéshez.  Ha a felhasználó bejelentkezik, az eszköz igény szerint elérheti a hozzáférési jogkivonatokat, és szükség szerint frissítheti a jogkivonatokat.  

> [!IMPORTANT]
> Jelenleg a Microsoft Identity platform végpontja csak az Azure AD-bérlők esetében támogatja az eszközök áramlását, a személyes fiókokat azonban nem.  Ez azt jelenti, hogy egy, bérlőként beállított végpontot vagy a `organizations` végpontot kell használnia.  Ez a támogatás hamarosan engedélyezve lesz. 
>
> Az Azure AD-bérlőnek meghívott személyes fiókok képesek lesznek használni az eszköz flow-támogatását, de csak a bérlő kontextusában.
>
> További Megjegyzés: a `verification_uri_complete` válasz mező jelenleg nem szerepel vagy nem támogatott.  

> [!NOTE]
> A Microsoft Identity platform végpontja nem támogatja az összes Azure Active Directory forgatókönyvet és funkciót. Annak megállapításához, hogy a Microsoft Identity platform-végpontot kell-e használni, olvassa el a [Microsoft Identity platform korlátozásait](active-directory-v2-limitations.md)ismertetőt.

## <a name="protocol-diagram"></a>Protokoll diagramja

A teljes eszköz kódjának folyamata a következő diagramhoz hasonlóan néz ki. A cikk későbbi részében leírt lépéseket ismertetjük.

![Eszköz kódjának folyamata](./media/v2-oauth2-device-code/v2-oauth-device-flow.svg)

## <a name="device-authorization-request"></a>Eszköz-engedélyezési kérelem

Az ügyfélnek először ellenőriznie kell a hitelesítési kiszolgálót a hitelesítés kezdeményezéséhez használt eszköz és felhasználói kód esetében. Az ügyfél ezt a kérést a `/devicecode` végponttól gyűjti. Ebben a kérelemben az ügyfélnek tartalmaznia kell a felhasználótól beszerzett engedélyeket is. A kérelem elküldésekor a felhasználónak csak 15 perce van a bejelentkezéshez (a szokásos érték `expires_in`), ezért csak akkor hajtsa végre ezt a kérést, ha a felhasználó jelezte, hogy készen áll a bejelentkezésre.

> [!TIP]
> Próbálja meg végrehajtani a kérelmet postán!
> [![Próbálja meg futtatni ezt a kérelmet postán](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```
// Line breaks are for legibility only.

POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/devicecode
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
scope=user.read%20openid%20profile

```

| Paraméter | Állapot | Leírás |
| --- | --- | --- |
| `tenant` | Szükséges |Az a címtár-bérlő, amelyre engedélyt szeretne kérni. Ez lehet a GUID vagy a felhasználóbarát név formátuma.  |
| `client_id` | Szükséges | Az alkalmazáshoz hozzárendelt [Azure Portal – Alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) felhasználói felület **(ügyfél) azonosítója** . |
| `scope` | Ajánlott | Egy szóközzel tagolt lista, [](v2-permissions-and-consent.md) melyben a felhasználónak jóvá kell hagynia a hatókört.  |

### <a name="device-authorization-response"></a>Eszköz-engedélyezési válasz

A sikeres válasz egy JSON-objektum, amely tartalmazza a szükséges információkat, amelyek lehetővé teszik a felhasználó számára a bejelentkezést.  

| Paraméter | Formátum | Leírás |
| ---              | --- | --- |
|`device_code`     | Sztring | Egy hosszú karakterlánc, amellyel ellenőrizhető a munkamenet az ügyfél és az engedélyezési kiszolgáló között. Az ügyfél ezzel a paraméterrel kéri le a hozzáférési jogkivonatot az engedélyezési kiszolgálóról. |
|`user_code`       | Sztring | Egy rövid karakterlánc, amelyet a rendszer a másodlagos eszköz munkamenetének azonosítására használt felhasználó számára mutat.|
|`verification_uri`| URI | A bejelentkezéshez a felhasználónak az URI azonosítóval `user_code` kell rendelkeznie. |
|`expires_in`      | int | A `device_code` és`user_code` a lejárat előtti másodpercek száma. |
|`interval`        | int | Azon másodpercek száma, ameddig az ügyfélnek várnia kell a lekérdezési kérelmek között. |
| `message`        | Sztring | Egy ember által olvasható karakterlánc, amely a felhasználóra vonatkozó utasításokat tartalmaz. Ez honosítható úgy, hogy egy **lekérdezési paramétert** is megadhat az űrlap `?mkt=xx-XX`kérelmében, a megfelelő nyelvi kulturális kód kitöltésével. |

## <a name="authenticating-the-user"></a>A felhasználó hitelesítése

A `user_code` és`verification_uri`a megérkezése után az ügyfél megjeleníti ezeket a felhasználókat, és arra utasítja őket, hogy jelentkezzenek be a mobiltelefon vagy a számítógép böngészője segítségével.  Emellett az ügyfél QR-kódot vagy hasonló mechanizmust is használhat a `verfication_uri_complete`megjelenítéséhez, amely elvégzi a felhasználóhoz `user_code` való belépés lépését.

Amíg a felhasználó hitelesítést `verification_uri`végez, az ügyfélnek a használatával `device_code`kell lekérdezni `/token` a végpontot a kért jogkivonat számára.

``` 
POST https://login.microsoftonline.com/tenant/oauth2/v2.0/token
Content-Type: application/x-www-form-urlencoded

grant_type: urn:ietf:params:oauth:grant-type:device_code
client_id: 6731de76-14a6-49ae-97bc-6eba6914391e
device_code: GMMhmHCXhWEzkobqIHGG_EnNYYsAkukHspeYUk9E8
```

| Paraméter | Szükséges | Leírás|
| -------- | -------- | ---------- |
| `grant_type` | Szükséges | Kötelező`urn:ietf:params:oauth:grant-type:device_code`|
| `client_id`  | Szükséges | Meg kell egyeznie `client_id` a kezdeti kérelemben használt értékkel. |
| `device_code`| Szükséges | A `device_code` visszaadott eszköz engedélyezési kérelme.  |

### <a name="expected-errors"></a>Várt hibák

Az eszköz kódjának folyamata egy lekérdezési protokoll, így az ügyfélnek meg kell várnia, hogy hibákat kapjon, mielőtt a felhasználó befejezte a hitelesítést.  

| Hiba | Leírás | Ügyfél művelete |
| ------ | ----------- | -------------|
| `authorization_pending` | A felhasználó nem fejezte be a hitelesítést, de nem szakította meg a folyamatot. | Ismételje meg a kérést `interval` legalább másodperc múlva. |
| `authorization_declined` | A végfelhasználó megtagadta az engedélyezési kérelmet.| A lekérdezés leállítása és visszaállítása nem hitelesített állapotra.  |
| `bad_verification_code`| Nem ismerhető fel `/token` a végpontnak elküldve.`device_code` | Győződjön meg arról, hogy az ügyfél elküldi `device_code` a megfelelőt a kérelemben. |
| `expired_token` | Legalább `expires_in` másodperc telt `device_code`el, és a hitelesítés már nem lehetséges. | A lekérdezés leállítása és visszaállítása nem hitelesített állapotra. |

### <a name="successful-authentication-response"></a>Sikeres hitelesítési válasz

A jogkivonat sikeres válasza A következőképpen fog kinézni:

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
| `token_type` | Sztring| Mindig "tulajdonos". |
| `scope` | Szóközzel tagolt karakterláncok | Ha egy hozzáférési jogkivonatot adott vissza, akkor felsorolja azokat a hatóköröket, amelyekre érvényes a hozzáférési jogkivonat. |
| `expires_in`| int | A befoglalt hozzáférési jogkivonat érvényessége előtti másodpercek száma. |
| `access_token`| Átlátszatlan karakterlánc | A kért [hatókörökhöz](v2-permissions-and-consent.md) lett kiállítva.  |
| `id_token`   | JWT | Kiadva, ha `scope` az eredeti paraméter `openid` tartalmazza a hatókört.  |
| `refresh_token` | Átlátszatlan karakterlánc | Kiadva, ha `scope` az eredeti `offline_access`paraméter szerepel.  |

A frissítési jogkivonattal új hozzáférési jogkivonatok szerezhetők be, és a tokenek frissítése a [OAuth-programkód dokumentációjában](v2-oauth2-auth-code-flow.md#refresh-the-access-token)ismertetett folyamattal megegyező folyamattal végezhető el.  
