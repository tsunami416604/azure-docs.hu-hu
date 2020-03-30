---
title: OAuth 2.0 eszközkód-áramlás | Azure
titleSuffix: Microsoft identity platform
description: Jelentkezzen be böngésző nélküli felhasználók. Beágyazott és böngésző nélküli hitelesítési folyamatokat hozhat létre az eszközengedélyezési támogatás használatával.
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
ms.date: 11/19/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: b45ba0c0b417be9cf308fedbb7fad2f6ad5fceaf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77159731"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-device-authorization-grant-flow"></a>A Microsoft identity platform és az OAuth 2.0 eszközengedélyezési engedélyezési folyamat

A Microsoft identity platform támogatja az [eszköz engedélyezési támogatást,](https://tools.ietf.org/html/rfc8628)amely lehetővé teszi a felhasználók számára, hogy jelentkezzen be a bemeneti korlátozott eszközök, például egy smart TV, IoT-eszköz vagy nyomtató.  A folyamat engedélyezéséhez az eszköz a felhasználó nak egy másik eszköz böngészőjében lévő weblapra látogat.  Miután a felhasználó bejelentkezik, az eszköz képes hozzáférési jogkivonatok és a jogkivonatok szükség szerint frissítési.  

Ez a cikk azt ismerteti, hogy miként programozhat közvetlenül az alkalmazásban lévő protokoll ellen.  Ha lehetséges, azt javasoljuk, hogy a támogatott Microsoft Authentication Libraries (MSAL) helyett [a jogkivonatok beszerzéséhez és a biztonságos webes API-k hívásához](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)használja.  Is vessen egy pillantást a [minta alkalmazások at MSAL](sample-v2-code.md).

> [!NOTE]
> A Microsoft identity platform végpontja nem támogatja az összes Azure Active Directory-forgatókönyvet és -szolgáltatást. Annak megállapításához, hogy a Microsoft identity platform végpontját kell-e használnia, olvassa el a [Microsoft identity platform korlátait.](active-directory-v2-limitations.md)

## <a name="protocol-diagram"></a>Protokolldiagram

A teljes eszközkód-folyamat hasonlít a következő diagramhoz. A cikk későbbi lépéseit ismertetjük.

![Eszközkód-áramlás](./media/v2-oauth2-device-code/v2-oauth-device-flow.svg)

## <a name="device-authorization-request"></a>Eszközengedélyezési kérelem

Az ügyfélnek először ellenőriznie kell a hitelesítési kiszolgálón a hitelesítés kezdeményezéséhez használt eszköz és felhasználói kód. Az ügyfél a végpontról `/devicecode` gyűjti ezt a kérést. Ebben a kérésben az ügyfélnek tartalmaznia kell a felhasználótól beszerezni szükséges engedélyeket is. Attól a pillanattól kezdve, hogy a kérelem elküldésre kerül, a felhasználónak csak 15 perce van a bejelentkezésre (a szokásos `expires_in`érték), így csak akkor küldje el ezt a kérést, ha a felhasználó jelezte, hogy készen áll a bejelentkezésre.

> [!TIP]
> Próbálja meg végrehajtani ezt a kérést a Postman!
> [![Próbálja meg futtatni ezt a kérést a Postman ben](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```
// Line breaks are for legibility only.

POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/devicecode
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
scope=user.read%20openid%20profile

```

| Paraméter | Állapot | Leírás |
| --- | --- | --- |
| `tenant` | Kötelező | Lehet /common, /consumers vagy /organizations.  Ez lehet az a címtár-bérlő is, amelytől guid vagy rövid névformátumban szeretne engedélyt kérni.  |
| `client_id` | Kötelező | Az **Alkalmazás (ügyfél) azonosító,** amely az [Azure Portal – Alkalmazás regisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) az alkalmazáshoz rendelt élmény. |
| `scope` | Ajánlott | A [hatókörek](v2-permissions-and-consent.md) területválasztó listája, amelyhez a felhasználónak hozzá kell járulnia.  |

### <a name="device-authorization-response"></a>Eszközengedélyezési válasz

A sikeres válasz egy JSON-objektum lesz, amely tartalmazza a szükséges információkat, hogy a felhasználó bejelentkezhet.  

| Paraméter | Formátum | Leírás |
| ---              | --- | --- |
|`device_code`     | Sztring | Hosszú karakterlánc, amely az ügyfél és az engedélyezési kiszolgáló közötti munkamenet ellenőrzésére szolgál. Az ügyfél ezt a paramétert használja a hozzáférési jogkivonat lekérésére az engedélyezési kiszolgálótól. |
|`user_code`       | Sztring | Egy rövid karakterlánc jelenik meg a felhasználó, amely a munkamenet azonosítására használt egy másodlagos eszközön.|
|`verification_uri`| URI | Az URI a felhasználónak `user_code` meg kell mennie a bejelentkezéshez. |
|`expires_in`      | int | A lejárat előtti `device_code` `user_code` másodpercek száma. |
|`interval`        | int | Az ügyfél várakozási ideje a lekérdezési kérelmek között. |
| `message`        | Sztring | Ember által olvasható karakterlánc, amely utasításokat tartalmaz a felhasználó számára. Ez honosítható egy **lekérdezési paraméternek** az `?mkt=xx-XX`űrlap kérésébe való betöltésével, a megfelelő nyelvi kulturális kód kitöltésével. |

> [!NOTE]
> A `verification_uri_complete` válaszmező jelenleg nem szerepel vagy nem támogatott.  Azért említjük ezt, mert ha `verification_uri_complete` elolvassa a [szabványlátok,](https://tools.ietf.org/html/rfc8628) hogy szerepel egy opcionális része az eszköz kódáramlás szabvány.

## <a name="authenticating-the-user"></a>A felhasználó hitelesítése

Miután `user_code` megkapta `verification_uri`a és a , az ügyfél megjeleníti ezeket a felhasználónak, utasítva őket, hogy jelentkezzenek be a mobiltelefonvagy pc böngésző.

Ha a felhasználó személyes fiókkal hitelesíti magát (a /common vagy /consumers oldalon), a rendszer megkéri, hogy jelentkezzen be újra, hogy a hitelesítési állapotot átvigye az eszközre.  Arra is felkérik őket, hogy adják meg hozzájárulásuk, hogy megbizonyosodjanak arról, hogy tisztában vannak a kapott engedélyekkel.  Ez nem vonatkozik a hitelesítéshez használt munkahelyi vagy iskolai fiókokra. 

Amíg a felhasználó a, `verification_uri`az ügyfél a `/token` rendszer segítségével a rendszer `device_code`nek kell lekérnie a kért jogkivonat végpontját.

``` 
POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
Content-Type: application/x-www-form-urlencoded

grant_type: urn:ietf:params:oauth:grant-type:device_code
client_id: 6731de76-14a6-49ae-97bc-6eba6914391e
device_code: GMMhmHCXhWEzkobqIHGG_EnNYYsAkukHspeYUk9E8...
```

| Paraméter | Kötelező | Leírás|
| -------- | -------- | ---------- |
| `tenant`  | Kötelező | Ugyanaz a bérlő vagy bérlőalias, amelyet a kezdeti kérelemben használt. | 
| `grant_type` | Kötelező | Kell`urn:ietf:params:oauth:grant-type:device_code`|
| `client_id`  | Kötelező | Meg kell `client_id` egyeznie a használt az eredeti kérelemben. |
| `device_code`| Kötelező | Az `device_code` eszköz engedélyezési kérelmében visszaadott.  |

### <a name="expected-errors"></a>Várt hibák

Az eszközkód-folyamat egy lekérdezési protokoll, így az ügyfélnek a hitelesítés befejezése előtt hibákfogadására kell számítania.  

| Hiba | Leírás | Ügyfélművelet |
| ------ | ----------- | -------------|
| `authorization_pending` | A felhasználó még nem fejezte be a hitelesítést, de nem szakította meg a folyamatot. | Ismételje meg a `interval` kérést legalább másodpercek elteltével. |
| `authorization_declined` | A végfelhasználó megtagadta az engedélyezési kérelmet.| Állítsa le a lekérdezést, és térjen vissza nem hitelesített állapotba.  |
| `bad_verification_code`| A `device_code` rendszer `/token` nem ismerte fel a végpontra küldött üzenetet. | Ellenőrizze, hogy az ügyfél `device_code` a megfelelő tetszést küldi-e a kérelemben. |
| `expired_token` | Legalább `expires_in` másodpercek teltek el, és ezzel `device_code`a hitelesítés már nem lehetséges. | Állítsa le a lekérdezést, és térjen vissza nem hitelesített állapotba. |   

### <a name="successful-authentication-response"></a>Sikeres hitelesítési válasz

A sikeres jogkivonat-válasz így fog kinézni:

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
| `token_type` | Sztring| Mindig "Hordozó. |
| `scope` | Szóköz elválasztott karakterláncok | Ha egy hozzáférési jogkivonatot adott vissza, ez felsorolja azokat a hatóköröket, amelyekre a hozzáférési jogkivonat érvényes. |
| `expires_in`| int | A mellékelt hozzáférési jogkivonat érvényessége előtt másodpercek száma. |
| `access_token`| Átlátszatlan karakterlánc | A kért [hatókörökhöz](v2-permissions-and-consent.md) kiállítva.  |
| `id_token`   | Jwt | Akkor van `scope` kiadva, `openid` ha az eredeti paraméter tartalmazza a hatókört.  |
| `refresh_token` | Átlátszatlan karakterlánc | Kiadva, `scope` ha `offline_access`az eredeti paraméter is benne van.  |

A frissítési jogkivonat segítségével új hozzáférési jogkivonatokat és frissítési jogkivonatokat szerezhet az [OAuth-kód folyamatdokumentációjában](v2-oauth2-auth-code-flow.md#refresh-the-access-token)dokumentált ugyanazon folyamat használatával.  
