---
title: Microsoft Identity platform azonosító jogkivonat-referenciája | Microsoft Docs
description: Ismerje meg, hogyan használható az Azure AD v 1.0 és a Microsoft Identity platform (v 2.0) végpontok által kibocsátott id_tokens.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/27/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms:custom: fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: cb279441e8ed0c0110a5b18d32609ed5b01bd7e2
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2019
ms.locfileid: "71291046"
---
# <a name="microsoft-identity-platform-id-tokens"></a>Microsoft Identity platform azonosító tokenek

`id_tokens`az alkalmazás az [OpenID Connect](v1-protocols-openid-connect-code.md) folyamat részeként elküldi az ügyfélalkalmazás számára. A felhasználók az oldalon vagy a hozzáférési jogkivonat helyett is elküldhetők, és az ügyfél a felhasználó hitelesítésére használja.

## <a name="using-the-id_token"></a>A id_token használata

Az azonosító jogkivonatokat annak ellenőrzésére kell használni, hogy a felhasználó ki és milyen további hasznos információkat igényel róluk – a [hozzáférési jogkivonat](access-tokens.md)helyett nem használható. Az általa biztosított jogcímek az alkalmazásban, az adatbázis kulcsaiként és az ügyfélalkalmazás elérésének biztosítására használhatók.  A kulcsok adatbázishoz való létrehozásakor `idp` nem ajánlott használni, mert a vendég forgatókönyveket felzavarja.  A `sub` beírást egyedül kell elvégezni (amely mindig egyedi), az `tid` útválasztáshoz használt, ha szükséges.  Ha több szolgáltatás között is meg kell osztania `oid` az adatmegosztást, a működni fog, `oid` + + `sub` `tid` mivel az összes szolgáltatás ugyanaz lesz.

## <a name="claims-in-an-id_token"></a>Jogcímek egy id_token

`id_tokens`a Microsoft-identitások [JWTs](https://tools.ietf.org/html/rfc7519), ami azt jelenti, hogy fejlécből, adattartalomból és aláírásból álló részből állnak. A fejléc és az aláírás segítségével ellenőrizheti a jogkivonat hitelességét, míg a hasznos adatok tartalmazzák az ügyfél által kért felhasználó adatait. Ha nincs megadva, az itt felsorolt jogcímek a v 1.0 és a v 2.0 jogkivonatokban is megjelennek.

### <a name="v10"></a>1\.0-s verzió

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyIsImtpZCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyJ9.eyJhdWQiOiJiMTRhNzUwNS05NmU5LTQ5MjctOTFlOC0wNjAxZDBmYzljYWEiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkvIiwiaWF0IjoxNTM2Mjc1MTI0LCJuYmYiOjE1MzYyNzUxMjQsImV4cCI6MTUzNjI3OTAyNCwiYWlvIjoiQVhRQWkvOElBQUFBcXhzdUIrUjREMnJGUXFPRVRPNFlkWGJMRDlrWjh4ZlhhZGVBTTBRMk5rTlQ1aXpmZzN1d2JXU1hodVNTajZVVDVoeTJENldxQXBCNWpLQTZaZ1o5ay9TVTI3dVY5Y2V0WGZMT3RwTnR0Z2s1RGNCdGsrTExzdHovSmcrZ1lSbXY5YlVVNFhscGhUYzZDODZKbWoxRkN3PT0iLCJhbXIiOlsicnNhIl0sImVtYWlsIjoiYWJlbGlAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiTGluY29sbiIsImdpdmVuX25hbWUiOiJBYmUiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaXBhZGRyIjoiMTMxLjEwNy4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJub25jZSI6IjEyMzUyMyIsIm9pZCI6IjA1ODMzYjZiLWFhMWQtNDJkNC05ZWMwLTFiMmJiOTE5NDQzOCIsInJoIjoiSSIsInN1YiI6IjVfSjlyU3NzOC1qdnRfSWN1NnVlUk5MOHhYYjhMRjRGc2dfS29vQzJSSlEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6IkFiZUxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJMeGVfNDZHcVRrT3BHU2ZUbG40RUFBIiwidmVyIjoiMS4wIn0=.UJQrCA6qn2bXq57qzGX_-D3HcPHqBMOKDPx4su1yKRLNErVD8xkxJLNLVRdASHqEcpyDctbdHccu6DPpkq5f0ibcaQFhejQNcABidJCTz0Bb2AbdUCTqAzdt9pdgQvMBnVH1xk3SCM6d4BbT4BkLLj10ZLasX7vRknaSjE_C5DI7Fg4WrZPwOhII1dB0HEZ_qpNaYXEiy-o94UJ94zCr07GgrqMsfYQqFR7kn-mn68AjvLcgwSfZvyR_yIK75S_K37vC3QryQ7cNoafDe9upql_6pB2ybMVlgWPs_DmbJ8g0om-sPlwyn74Cc1tW3ze-Xptw_2uVdPgWyqfuWAfq6Q
```

Tekintse meg ezt a 1.0-s [JWT.MS](https://jwt.ms/#id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyIsImtpZCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyJ9.eyJhdWQiOiJiMTRhNzUwNS05NmU5LTQ5MjctOTFlOC0wNjAxZDBmYzljYWEiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkvIiwiaWF0IjoxNTM2Mjc1MTI0LCJuYmYiOjE1MzYyNzUxMjQsImV4cCI6MTUzNjI3OTAyNCwiYWlvIjoiQVhRQWkvOElBQUFBcXhzdUIrUjREMnJGUXFPRVRPNFlkWGJMRDlrWjh4ZlhhZGVBTTBRMk5rTlQ1aXpmZzN1d2JXU1hodVNTajZVVDVoeTJENldxQXBCNWpLQTZaZ1o5ay9TVTI3dVY5Y2V0WGZMT3RwTnR0Z2s1RGNCdGsrTExzdHovSmcrZ1lSbXY5YlVVNFhscGhUYzZDODZKbWoxRkN3PT0iLCJhbXIiOlsicnNhIl0sImVtYWlsIjoiYWJlbGlAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiTGluY29sbiIsImdpdmVuX25hbWUiOiJBYmUiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaXBhZGRyIjoiMTMxLjEwNy4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJub25jZSI6IjEyMzUyMyIsIm9pZCI6IjA1ODMzYjZiLWFhMWQtNDJkNC05ZWMwLTFiMmJiOTE5NDQzOCIsInJoIjoiSSIsInN1YiI6IjVfSjlyU3NzOC1qdnRfSWN1NnVlUk5MOHhYYjhMRjRGc2dfS29vQzJSSlEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6IkFiZUxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJMeGVfNDZHcVRrT3BHU2ZUbG40RUFBIiwidmVyIjoiMS4wIn0=.UJQrCA6qn2bXq57qzGX_-D3HcPHqBMOKDPx4su1yKRLNErVD8xkxJLNLVRdASHqEcpyDctbdHccu6DPpkq5f0ibcaQFhejQNcABidJCTz0Bb2AbdUCTqAzdt9pdgQvMBnVH1xk3SCM6d4BbT4BkLLj10ZLasX7vRknaSjE_C5DI7Fg4WrZPwOhII1dB0HEZ_qpNaYXEiy-o94UJ94zCr07GgrqMsfYQqFR7kn-mn68AjvLcgwSfZvyR_yIK75S_K37vC3QryQ7cNoafDe9upql_6pB2ybMVlgWPs_DmbJ8g0om-sPlwyn74Cc1tW3ze-Xptw_2uVdPgWyqfuWAfq6Q)-mintát.

### <a name="v20"></a>2\.0-s verzió

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjFMVE16YWtpaGlSbGFfOHoyQkVKVlhlV01xbyJ9.eyJ2ZXIiOiIyLjAiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vOTE4ODA0MGQtNmM2Ny00YzViLWIxMTItMzZhMzA0YjY2ZGFkL3YyLjAiLCJzdWIiOiJBQUFBQUFBQUFBQUFBQUFBQUFBQUFJa3pxRlZyU2FTYUZIeTc4MmJidGFRIiwiYXVkIjoiNmNiMDQwMTgtYTNmNS00NmE3LWI5OTUtOTQwYzc4ZjVhZWYzIiwiZXhwIjoxNTM2MzYxNDExLCJpYXQiOjE1MzYyNzQ3MTEsIm5iZiI6MTUzNjI3NDcxMSwibmFtZSI6IkFiZSBMaW5jb2xuIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiQWJlTGlAbWljcm9zb2Z0LmNvbSIsIm9pZCI6IjAwMDAwMDAwLTAwMDAtMDAwMC02NmYzLTMzMzJlY2E3ZWE4MSIsInRpZCI6IjMzMzgwNDBkLTZjNjctNGM1Yi1iMTEyLTM2YTMwNGI2NmRhZCIsIm5vbmNlIjoiMTIzNTIzIiwiYWlvIjoiRGYyVVZYTDFpeCFsTUNXTVNPSkJjRmF0emNHZnZGR2hqS3Y4cTVnMHg3MzJkUjVNQjVCaXN2R1FPN1lXQnlqZDhpUURMcSFlR2JJRGFreXA1bW5PcmNkcUhlWVNubHRlcFFtUnA2QUlaOGpZIn0=.1AFWW-Ck5nROwSlltm7GzZvDwUkqvhSQpm55TQsmVo9Y59cLhRXpvB8n-55HCr9Z6G_31_UbeUkoz612I2j_Sm9FFShSDDjoaLQr54CreGIJvjtmS3EkK9a7SJBbcpL1MpUtlfygow39tFjY7EVNW9plWUvRrTgVk7lYLprvfzw-CIqw3gHC-T7IK_m_xkr08INERBtaecwhTeN4chPC4W3jdmw_lIxzC48YoQ0dB1L9-ImX98Egypfrlbm0IBL5spFzL6JDZIRRJOu8vecJvj1mq-IUhGt0MacxX8jdxYLP-KUu2d9MbNKpCKJuZ7p8gwTL5B7NlUdh_dmSviPWrw
```

Tekintse meg ezt a v 2.0 minta tokent a [JWT.MS](https://jwt.ms/#id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjFMVE16YWtpaGlSbGFfOHoyQkVKVlhlV01xbyJ9.eyJ2ZXIiOiIyLjAiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vOTE4ODA0MGQtNmM2Ny00YzViLWIxMTItMzZhMzA0YjY2ZGFkL3YyLjAiLCJzdWIiOiJBQUFBQUFBQUFBQUFBQUFBQUFBQUFJa3pxRlZyU2FTYUZIeTc4MmJidGFRIiwiYXVkIjoiNmNiMDQwMTgtYTNmNS00NmE3LWI5OTUtOTQwYzc4ZjVhZWYzIiwiZXhwIjoxNTM2MzYxNDExLCJpYXQiOjE1MzYyNzQ3MTEsIm5iZiI6MTUzNjI3NDcxMSwibmFtZSI6IkFiZSBMaW5jb2xuIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiQWJlTGlAbWljcm9zb2Z0LmNvbSIsIm9pZCI6IjAwMDAwMDAwLTAwMDAtMDAwMC02NmYzLTMzMzJlY2E3ZWE4MSIsInRpZCI6IjMzMzgwNDBkLTZjNjctNGM1Yi1iMTEyLTM2YTMwNGI2NmRhZCIsIm5vbmNlIjoiMTIzNTIzIiwiYWlvIjoiRGYyVVZYTDFpeCFsTUNXTVNPSkJjRmF0emNHZnZGR2hqS3Y4cTVnMHg3MzJkUjVNQjVCaXN2R1FPN1lXQnlqZDhpUURMcSFlR2JJRGFreXA1bW5PcmNkcUhlWVNubHRlcFFtUnA2QUlaOGpZIn0.1AFWW-Ck5nROwSlltm7GzZvDwUkqvhSQpm55TQsmVo9Y59cLhRXpvB8n-55HCr9Z6G_31_UbeUkoz612I2j_Sm9FFShSDDjoaLQr54CreGIJvjtmS3EkK9a7SJBbcpL1MpUtlfygow39tFjY7EVNW9plWUvRrTgVk7lYLprvfzw-CIqw3gHC-T7IK_m_xkr08INERBtaecwhTeN4chPC4W3jdmw_lIxzC48YoQ0dB1L9-ImX98Egypfrlbm0IBL5spFzL6JDZIRRJOu8vecJvj1mq-IUhGt0MacxX8jdxYLP-KUu2d9MbNKpCKJuZ7p8gwTL5B7NlUdh_dmSviPWrw)-ben.

### <a name="header-claims"></a>Fejléc jogcímei

|Igénylés | Formátum | Leírás |
|-----|--------|-------------|
|`typ` | String – mindig "JWT" | Azt jelzi, hogy a token egy JWT.|
|`alg` | Sztring | Meghatározza a jogkivonat aláírásához használt algoritmust. Példa: "RS256" |
|`kid` | Sztring | A jogkivonat aláírásához használt nyilvános kulcs ujjlenyomata. Az 1.0-s és a v 2.0 `id_tokens`-s verzióban is kibocsátva. |
|`x5t` | Sztring | Ugyanaz (használatban és értékben), `kid`mint. Ez azonban egy örökölt jogcím, amely csak a v 1.0 `id_tokens` -s verzióban, kompatibilitási célból lett kibocsátva. |

### <a name="payload-claims"></a>Hasznos adatokhoz tartozó jogcímek

Ez a lista azokat a jogcímeket jeleníti meg, amelyek a legtöbb id_tokens alapértelmezésben vannak (kivéve, ha van ilyen).  Az alkalmazás azonban [választható jogcímeket](active-directory-optional-claims.md) is használhat további jogcímek igénylésére a id_token.  Ezek a `groups` jogcím alapján a felhasználó nevével kapcsolatos információkra terjedhetnek.

|Igénylés | Formátum | Leírás |
|-----|--------|-------------|
|`aud` |  Karakterlánc, alkalmazás-azonosító URI | Azonosítja a jogkivonat kívánt címzettjét. A `id_tokens`-ben a célközönség az alkalmazáshoz hozzárendelt alkalmazás azonosítója a Azure Portal. Az alkalmazásnak ellenőriznie kell ezt az értéket, és el kell utasítania a jogkivonatot, ha az érték nem egyezik. |
|`iss` |  Karakterlánc, STS URI | Azonosítja azt a biztonságijogkivonat-szolgáltatást (STS), amely létrehozza és visszaadja a tokent, valamint azt az Azure AD-bérlőt, amelyben a felhasználó hitelesítése megtörtént. Ha a jogkivonatot a v 2.0-végpont adta ki, akkor az URI `/v2.0`-ja befejeződik.  Az a GUID, amely azt jelzi, hogy a felhasználó egy Microsoft-fiók `9188040d-6c67-4c5b-b112-36a304b66dad`vásárló felhasználója. Az alkalmazásnak a jogcím GUID részét kell használnia, hogy korlátozza azon bérlők készletét, amelyek be tudnak jelentkezni az alkalmazásba, ha vannak ilyenek. |
|`iat` |  int, egy UNIX timestamp | A "kiállító" érték azt jelzi, hogy a jogkivonat hitelesítése mikor történt.  |
|`idp`|Karakterlánc, általában egy STS URI | A jogkivonat alanyát hitelesítő identitásszolgáltatót adja meg. Ez az érték megegyezik a kiállítói jogcímek értékével, kivéve, ha a felhasználói fiók nem ugyanabban a bérlőben található, mint a kiállító – vendég. Ha a jogcím nem létezik, az azt jelenti, hogy a `iss` értéke használható.  A szervezeti környezetben használt személyes fiókok esetében (például egy Azure ad-bérlőnek meghívott személyes fiók) a `idp` jogcím "Live.com" vagy a Microsoft-fiók bérlőt `9188040d-6c67-4c5b-b112-36a304b66dad`tartalmazó STS URI lehet. |
|`nbf` |  int, egy UNIX timestamp | A "NBF" (nem korábban) jogcím azt az időpontot határozza meg, ameddig a JWT nem fogadható el a feldolgozáshoz.|
|`exp` |  int, egy UNIX timestamp | Az "exp" (lejárati idő) jogcím azt a lejárati időt azonosítja, amely után a JWT nem fogadható el feldolgozásra.  Fontos megjegyezni, hogy egy erőforrás még a token elutasítása előtt is elutasítja a jogkivonatot – Ha például a hitelesítés változására van szükség, vagy a rendszer jogkivonat-visszavonást észlelt. |
| `c_hash`| Sztring |A kód kivonata csak akkor szerepel az azonosító jogkivonatokban, ha az azonosító jogkivonatot egy OAuth 2,0-es engedélyezési kóddal adják ki. Az engedélyezési kódok hitelességének ellenőrzéséhez használható. Az érvényesítés végrehajtásáról az [OpenID Connect specifikációjában](https://openid.net/specs/openid-connect-core-1_0.html)olvashat bővebben. |
|`at_hash`| Sztring |A hozzáférési jogkivonat kivonata csak akkor szerepel az azonosító jogkivonatokban, ha az azonosító jogkivonatot egy OAuth 2,0 hozzáférési jogkivonat adja ki. A hozzáférési token hitelességének ellenőrzéséhez használható. Az érvényesítés végrehajtásáról az [OpenID Connect specifikációjában](https://openid.net/specs/openid-connect-core-1_0.html)olvashat bővebben. |
|`aio` | Átlátszatlan karakterlánc | Az Azure AD által az adatok a jogkivonat újrafelhasználásához használt belső jogcímek. Figyelmen kívül kell hagyni.|
|`preferred_username` | Sztring | A felhasználót jelölő elsődleges Felhasználónév. Ez lehet egy e-mail-cím, telefonszám vagy egy általános Felhasználónév megadott formátum nélkül. Az értéke változtatható, és idővel változhat. Mivel ez az érték változhat, nem használható az engedélyezési döntések elvégzéséhez. Ennek `profile` a jogcímnek a fogadásához a hatókörre van szükség.|
|`email` | Sztring | A `email` jogcímek alapértelmezés szerint az e-mail-címmel rendelkező vendég fiókok esetében jelennek meg.  Az alkalmazás kérheti az e-mail-jogcímet a felügyelt felhasználók számára (az erőforrással megegyező bérlőtől) a `email` [választható jogcím](active-directory-optional-claims.md)használatával.  A v 2.0-s végponton az alkalmazás az `email` OpenID Connect hatókörét is kérheti – nem kell megadnia mind a választható jogcímet, mind a hatókört a jogcím beszerzéséhez.  Az e-mail-jogcím csak a felhasználó profiljának adataiból származó címezhető leveleket támogatja. |
|`name` | Sztring | A `name` jogcím egy ember által olvasható értéket biztosít, amely azonosítja a jogkivonat tárgyát. Az érték nem garantált, hogy egyedi, megváltoztathatatlan, és csak megjelenítési célokra használható. Ennek `profile` a jogcímnek a fogadásához a hatókörre van szükség. |
|`nonce`| Sztring | Az egyszeres érték megegyezik az eredeti/Authorize-kérelemben szereplő paraméterrel a IDENTITÁSSZOLGÁLTATÓ. Ha nem egyezik, az alkalmazásnak el kell utasítania a jogkivonatot. |
|`oid` | Karakterlánc, GUID | Egy objektum megváltoztathatatlan azonosítója a Microsoft Identity Systemben, ebben az esetben egy felhasználói fiók. Ez az azonosító egyedileg azonosítja a felhasználót az alkalmazások között – két különböző alkalmazás, amely ugyanazon a felhasználón jelentkezik be, ugyanazokat `oid` az értékeket kapja meg a jogcímben. A Microsoft Graph egy adott felhasználói fiókhoz tartozó `id` tulajdonságként adja vissza ezt az azonosítót. Mivel a `oid` lehetővé teszi, hogy több alkalmazás is összekapcsolja a felhasználókat, a `profile` hatókörnek meg kell kapnia a jogcímet. Vegye figyelembe, hogy ha egyetlen felhasználó több bérlőn is létezik, akkor a felhasználó minden bérlőn egy másik objektumazonosítót fog tartalmazni – ezeket a rendszer akkor is különböző fiókoknak tekinti, ha a felhasználó ugyanazzal a hitelesítő adatokkal jelentkezik be az egyes fiókokba. A `oid` jogcím egy GUID azonosító, és nem használható fel újra. |
|`roles`| karakterláncok tömbje | A bejelentkezett felhasználóhoz hozzárendelt szerepkörök összessége. |
|`rh` | Átlátszatlan karakterlánc |Az Azure által a jogkivonatok újraellenőrzéséhez használt belső jogcím. Figyelmen kívül kell hagyni. |
|`sub` | Karakterlánc, GUID | Az a rendszerbiztonsági tag, amelyről a jogkivonat adatokat érvényesít, például egy alkalmazás felhasználóját. Ez az érték nem módosítható, és nem rendelhető hozzá újra, és nem használható újra. A tulajdonos egy páros-azonosító – egyedi egy adott alkalmazás-AZONOSÍTÓhoz. Ha egyetlen felhasználó két különböző alkalmazásba jelentkezik be két különböző ügyfél-azonosítóval, akkor ezek az alkalmazások két különböző értéket kapnak a tulajdonos jogcímek számára. Az architektúra-és adatvédelmi követelményektől függően ez lehet vagy nem kívánatos. |
|`tid` | Karakterlánc, GUID | Egy GUID, amely azt az Azure AD-bérlőt jelöli, amelyből a felhasználó származik. Munkahelyi és iskolai fiókok esetén a GUID az a szervezet, amelyhez a felhasználó tartozik. A személyes fiókok esetében az érték `9188040d-6c67-4c5b-b112-36a304b66dad`a következő:. Ennek `profile` a jogcímnek a fogadásához a hatókörre van szükség. |
|`unique_name` | Sztring | A jogkivonat alanyát azonosító, ember által olvasható értéket ad meg. Ez az érték nem garantáltan egyedi a bérlőn belül, és csak megjelenítési célokra használható. Csak a 1.0 `id_tokens`-s verzióban van kiállítva. |
|`uti` | Átlátszatlan karakterlánc | Az Azure által a jogkivonatok újraellenőrzéséhez használt belső jogcím. Figyelmen kívül kell hagyni. |
|`ver` | Karakterlánc, vagy 1,0 vagy 2,0 | Megadja a id_token verzióját. |

## <a name="validating-an-id_token"></a>Id_token ellenőrzése

A egy `id_token` [hozzáférési jogkivonat érvényesítésének](access-tokens.md#validating-tokens) első lépése hasonlít a hitelesítéshez. az ügyfélnek ellenőriznie kell, hogy a megfelelő kiállító visszaküldte-e a tokent, és hogy nem módosították-e illetéktelenül. Mivel `id_tokens` mindig JWT, sok könyvtár létezik a jogkivonatok érvényesítéséhez – javasoljuk, hogy ezt a tokent használja, és ne a saját magának.

A jogkivonat manuális érvényesítéséhez tekintse meg a [hozzáférési token érvényesítése](access-tokens.md#validating-tokens)című témakör lépéseit. A jogkivonat aláírásának ellenőrzése után a következő jogcímeket érvényesíteni kell a id_token (ezeket a jogkivonat-ellenőrzési függvénytár is végrehajthatja):

* Időbélyegek: `iat`a `nbf`, és `exp` az időbélyegek az aktuális időpont előtt vagy után kell esniük, ha szükséges. 
* Célközönség: a `aud` jogcímnek meg kell egyeznie az alkalmazáshoz tartozó alkalmazás-azonosítóval.
* Alkalom: az `nonce` adattartalomban szereplő jogcímnek meg kell egyeznie a/Authorize-végpontnak a kezdeti kérelem során átadott egyszeres paraméterrel.

## <a name="next-steps"></a>További lépések

* Tudnivalók a [hozzáférési tokenekről](access-tokens.md)
* A id_token lévő jogcímeket a [választható jogcímek](active-directory-optional-claims.md)használatával szabhatja testre.
