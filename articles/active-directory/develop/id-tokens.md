---
title: Microsoft Identity platform azonosító jogkivonat-referenciája | Microsoft Docs
description: Ismerje meg, hogyan használhatók az Azure AD v 1.0 és a Microsoft Identity platform (v 2.0) végpontok által kibocsátott id_tokens.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms:custom: fasttrack-edit
ms.openlocfilehash: 1efd027edb85cabcfdc2a170771ef19182b5c9f8
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77160950"
---
# <a name="microsoft-identity-platform-id-tokens"></a>Microsoft Identity platform azonosító tokenek

az `id_tokens` az [OpenID Connect](v2-protocols-oidc.md) folyamat részeként küldik el az ügyfélalkalmazás számára. A felhasználók az oldalon vagy a hozzáférési jogkivonat helyett is elküldhetők, és az ügyfél a felhasználó hitelesítésére használja.

## <a name="using-the-id_token"></a>A id_token használata

Az azonosító jogkivonatokat annak ellenőrzésére kell használni, hogy a felhasználó ki és milyen további hasznos információkat igényel róluk – a [hozzáférési jogkivonat](access-tokens.md)helyett nem használható. Az általa biztosított jogcímek az alkalmazásban, az adatbázis kulcsaiként és az ügyfélalkalmazás elérésének biztosítására használhatók.  Ha kulcsokat hoz létre egy adatbázishoz, `idp`t nem szabad használni, mert a vendég forgatókönyveket felzavarja.  A beírást csak `sub` kell elvégezni (amely mindig egyedi), az útválasztáshoz használt `tid`, ha szükséges.  Ha a szolgáltatások között meg kell osztania az adatmegosztást, `oid`+`sub`+`tid` fog működni, mivel az összes szolgáltatás ugyanazt a `oid`t kapja meg.

## <a name="claims-in-an-id_token"></a>Jogcímek egy id_token

a Microsoft-identitások `id_tokens` [JWTs](https://tools.ietf.org/html/rfc7519), ami azt jelenti, hogy egy fejlécből, adattartalomból és aláírásból álló részből állnak. A fejléc és az aláírás segítségével ellenőrizheti a jogkivonat hitelességét, míg a hasznos adatok tartalmazzák az ügyfél által kért felhasználó adatait. Ha nincs megadva, az itt felsorolt jogcímek a v 1.0 és a v 2.0 jogkivonatokban is megjelennek.

### <a name="v10"></a>1\.0-s verzió

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyIsImtpZCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyJ9.eyJhdWQiOiJiMTRhNzUwNS05NmU5LTQ5MjctOTFlOC0wNjAxZDBmYzljYWEiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkvIiwiaWF0IjoxNTM2Mjc1MTI0LCJuYmYiOjE1MzYyNzUxMjQsImV4cCI6MTUzNjI3OTAyNCwiYWlvIjoiQVhRQWkvOElBQUFBcXhzdUIrUjREMnJGUXFPRVRPNFlkWGJMRDlrWjh4ZlhhZGVBTTBRMk5rTlQ1aXpmZzN1d2JXU1hodVNTajZVVDVoeTJENldxQXBCNWpLQTZaZ1o5ay9TVTI3dVY5Y2V0WGZMT3RwTnR0Z2s1RGNCdGsrTExzdHovSmcrZ1lSbXY5YlVVNFhscGhUYzZDODZKbWoxRkN3PT0iLCJhbXIiOlsicnNhIl0sImVtYWlsIjoiYWJlbGlAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiTGluY29sbiIsImdpdmVuX25hbWUiOiJBYmUiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaXBhZGRyIjoiMTMxLjEwNy4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJub25jZSI6IjEyMzUyMyIsIm9pZCI6IjA1ODMzYjZiLWFhMWQtNDJkNC05ZWMwLTFiMmJiOTE5NDQzOCIsInJoIjoiSSIsInN1YiI6IjVfSjlyU3NzOC1qdnRfSWN1NnVlUk5MOHhYYjhMRjRGc2dfS29vQzJSSlEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6IkFiZUxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJMeGVfNDZHcVRrT3BHU2ZUbG40RUFBIiwidmVyIjoiMS4wIn0=.UJQrCA6qn2bXq57qzGX_-D3HcPHqBMOKDPx4su1yKRLNErVD8xkxJLNLVRdASHqEcpyDctbdHccu6DPpkq5f0ibcaQFhejQNcABidJCTz0Bb2AbdUCTqAzdt9pdgQvMBnVH1xk3SCM6d4BbT4BkLLj10ZLasX7vRknaSjE_C5DI7Fg4WrZPwOhII1dB0HEZ_qpNaYXEiy-o94UJ94zCr07GgrqMsfYQqFR7kn-mn68AjvLcgwSfZvyR_yIK75S_K37vC3QryQ7cNoafDe9upql_6pB2ybMVlgWPs_DmbJ8g0om-sPlwyn74Cc1tW3ze-Xptw_2uVdPgWyqfuWAfq6Q
```

Tekintse meg ezt a 1.0-s [JWT.MS](https://jwt.ms/#id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyIsImtpZCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyJ9.eyJhdWQiOiJiMTRhNzUwNS05NmU5LTQ5MjctOTFlOC0wNjAxZDBmYzljYWEiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkvIiwiaWF0IjoxNTM2Mjc1MTI0LCJuYmYiOjE1MzYyNzUxMjQsImV4cCI6MTUzNjI3OTAyNCwiYWlvIjoiQVhRQWkvOElBQUFBcXhzdUIrUjREMnJGUXFPRVRPNFlkWGJMRDlrWjh4ZlhhZGVBTTBRMk5rTlQ1aXpmZzN1d2JXU1hodVNTajZVVDVoeTJENldxQXBCNWpLQTZaZ1o5ay9TVTI3dVY5Y2V0WGZMT3RwTnR0Z2s1RGNCdGsrTExzdHovSmcrZ1lSbXY5YlVVNFhscGhUYzZDODZKbWoxRkN3PT0iLCJhbXIiOlsicnNhIl0sImVtYWlsIjoiYWJlbGlAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiTGluY29sbiIsImdpdmVuX25hbWUiOiJBYmUiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaXBhZGRyIjoiMTMxLjEwNy4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJub25jZSI6IjEyMzUyMyIsIm9pZCI6IjA1ODMzYjZiLWFhMWQtNDJkNC05ZWMwLTFiMmJiOTE5NDQzOCIsInJoIjoiSSIsInN1YiI6IjVfSjlyU3NzOC1qdnRfSWN1NnVlUk5MOHhYYjhMRjRGc2dfS29vQzJSSlEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6IkFiZUxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJMeGVfNDZHcVRrT3BHU2ZUbG40RUFBIiwidmVyIjoiMS4wIn0=.UJQrCA6qn2bXq57qzGX_-D3HcPHqBMOKDPx4su1yKRLNErVD8xkxJLNLVRdASHqEcpyDctbdHccu6DPpkq5f0ibcaQFhejQNcABidJCTz0Bb2AbdUCTqAzdt9pdgQvMBnVH1xk3SCM6d4BbT4BkLLj10ZLasX7vRknaSjE_C5DI7Fg4WrZPwOhII1dB0HEZ_qpNaYXEiy-o94UJ94zCr07GgrqMsfYQqFR7kn-mn68AjvLcgwSfZvyR_yIK75S_K37vC3QryQ7cNoafDe9upql_6pB2ybMVlgWPs_DmbJ8g0om-sPlwyn74Cc1tW3ze-Xptw_2uVdPgWyqfuWAfq6Q)-mintát.

### <a name="v20"></a>2\.0-s verzió

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjFMVE16YWtpaGlSbGFfOHoyQkVKVlhlV01xbyJ9.eyJ2ZXIiOiIyLjAiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vOTEyMjA0MGQtNmM2Ny00YzViLWIxMTItMzZhMzA0YjY2ZGFkL3YyLjAiLCJzdWIiOiJBQUFBQUFBQUFBQUFBQUFBQUFBQUFJa3pxRlZyU2FTYUZIeTc4MmJidGFRIiwiYXVkIjoiNmNiMDQwMTgtYTNmNS00NmE3LWI5OTUtOTQwYzc4ZjVhZWYzIiwiZXhwIjoxNTM2MzYxNDExLCJpYXQiOjE1MzYyNzQ3MTEsIm5iZiI6MTUzNjI3NDcxMSwibmFtZSI6IkFiZSBMaW5jb2xuIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiQWJlTGlAbWljcm9zb2Z0LmNvbSIsIm9pZCI6IjAwMDAwMDAwLTAwMDAtMDAwMC02NmYzLTMzMzJlY2E3ZWE4MSIsInRpZCI6IjkxMjIwNDBkLTZjNjctNGM1Yi1iMTEyLTM2YTMwNGI2NmRhZCIsIm5vbmNlIjoiMTIzNTIzIiwiYWlvIjoiRGYyVVZYTDFpeCFsTUNXTVNPSkJjRmF0emNHZnZGR2hqS3Y4cTVnMHg3MzJkUjVNQjVCaXN2R1FPN1lXQnlqZDhpUURMcSFlR2JJRGFreXA1bW5PcmNkcUhlWVNubHRlcFFtUnA2QUlaOGpZIn0.1AFWW-Ck5nROwSlltm7GzZvDwUkqvhSQpm55TQsmVo9Y59cLhRXpvB8n-55HCr9Z6G_31_UbeUkoz612I2j_Sm9FFShSDDjoaLQr54CreGIJvjtmS3EkK9a7SJBbcpL1MpUtlfygow39tFjY7EVNW9plWUvRrTgVk7lYLprvfzw-CIqw3gHC-T7IK_m_xkr08INERBtaecwhTeN4chPC4W3jdmw_lIxzC48YoQ0dB1L9-ImX98Egypfrlbm0IBL5spFzL6JDZIRRJOu8vecJvj1mq-IUhGt0MacxX8jdxYLP-KUu2d9MbNKpCKJuZ7p8gwTL5B7NlUdh_dmSviPWrw
```

Tekintse meg ezt a v 2.0 minta tokent a [JWT.MS](https://jwt.ms/#id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjFMVE16YWtpaGlSbGFfOHoyQkVKVlhlV01xbyJ9.eyJ2ZXIiOiIyLjAiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vOTEyMjA0MGQtNmM2Ny00YzViLWIxMTItMzZhMzA0YjY2ZGFkL3YyLjAiLCJzdWIiOiJBQUFBQUFBQUFBQUFBQUFBQUFBQUFJa3pxRlZyU2FTYUZIeTc4MmJidGFRIiwiYXVkIjoiNmNiMDQwMTgtYTNmNS00NmE3LWI5OTUtOTQwYzc4ZjVhZWYzIiwiZXhwIjoxNTM2MzYxNDExLCJpYXQiOjE1MzYyNzQ3MTEsIm5iZiI6MTUzNjI3NDcxMSwibmFtZSI6IkFiZSBMaW5jb2xuIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiQWJlTGlAbWljcm9zb2Z0LmNvbSIsIm9pZCI6IjAwMDAwMDAwLTAwMDAtMDAwMC02NmYzLTMzMzJlY2E3ZWE4MSIsInRpZCI6IjkxMjIwNDBkLTZjNjctNGM1Yi1iMTEyLTM2YTMwNGI2NmRhZCIsIm5vbmNlIjoiMTIzNTIzIiwiYWlvIjoiRGYyVVZYTDFpeCFsTUNXTVNPSkJjRmF0emNHZnZGR2hqS3Y4cTVnMHg3MzJkUjVNQjVCaXN2R1FPN1lXQnlqZDhpUURMcSFlR2JJRGFreXA1bW5PcmNkcUhlWVNubHRlcFFtUnA2QUlaOGpZIn0.1AFWW-Ck5nROwSlltm7GzZvDwUkqvhSQpm55TQsmVo9Y59cLhRXpvB8n-55HCr9Z6G_31_UbeUkoz612I2j_Sm9FFShSDDjoaLQr54CreGIJvjtmS3EkK9a7SJBbcpL1MpUtlfygow39tFjY7EVNW9plWUvRrTgVk7lYLprvfzw-CIqw3gHC-T7IK_m_xkr08INERBtaecwhTeN4chPC4W3jdmw_lIxzC48YoQ0dB1L9-ImX98Egypfrlbm0IBL5spFzL6JDZIRRJOu8vecJvj1mq-IUhGt0MacxX8jdxYLP-KUu2d9MbNKpCKJuZ7p8gwTL5B7NlUdh_dmSviPWrw)-ben.

### <a name="header-claims"></a>Fejléc jogcímei

|Jogcím | Formátum | Leírás |
|-----|--------|-------------|
|`typ` | String – mindig "JWT" | Azt jelzi, hogy a token egy JWT.|
|`alg` | Sztring | Meghatározza a jogkivonat aláírásához használt algoritmust. Példa: "RS256" |
|`kid` | Sztring | A jogkivonat aláírásához használt nyilvános kulcs ujjlenyomata. Az 1.0-s és a v 2.0 `id_tokens`is kibocsátva. |
|`x5t` | Sztring | Ugyanaz (használatban és értékben) `kid`ként. Azonban ez egy örökölt jogcím, amely csak a 1.0-s verzióban, `id_tokens` kompatibilitási célból lett kibocsátva. |

### <a name="payload-claims"></a>Hasznos adatokhoz tartozó jogcímek

Ez a lista azokat a jogcímeket jeleníti meg, amelyek a legtöbb id_tokens alapértelmezés szerint (kivéve, ha van ilyen).  Az alkalmazás azonban [választható jogcímeket](active-directory-optional-claims.md) is használhat a id_token további jogcímek igényléséhez.  Ezek az `groups` jogcímtől a felhasználó nevével kapcsolatos információkra terjedhetnek.

|Jogcím | Formátum | Leírás |
|-----|--------|-------------|
|`aud` |  Karakterlánc, alkalmazás-azonosító URI | Azonosítja a jogkivonat kívánt címzettjét. `id_tokens`a célközönség az alkalmazáshoz hozzárendelt alkalmazás-azonosító, amelyet a Azure Portal kell rendelnie az alkalmazáshoz. Az alkalmazásnak ellenőriznie kell ezt az értéket, és el kell utasítania a jogkivonatot, ha az érték nem egyezik. |
|`iss` |  Karakterlánc, STS URI | Azonosítja azt a biztonságijogkivonat-szolgáltatást (STS), amely létrehozza és visszaadja a tokent, valamint azt az Azure AD-bérlőt, amelyben a felhasználó hitelesítése megtörtént. Ha a jogkivonatot a v 2.0-végpont adta ki, akkor az URI `/v2.0`végződik.  Az a GUID, amely azt jelzi, hogy a felhasználó egy Microsoft-fiók fogyasztó felhasználója `9188040d-6c67-4c5b-b112-36a304b66dad`. Az alkalmazásnak a jogcím GUID részét kell használnia, hogy korlátozza azon bérlők készletét, amelyek be tudnak jelentkezni az alkalmazásba, ha vannak ilyenek. |
|`iat` |  int, egy UNIX timestamp | A "kiállító" érték azt jelzi, hogy a jogkivonat hitelesítése mikor történt.  |
|`idp`|Karakterlánc, általában egy STS URI | A jogkivonat alanyát hitelesítő identitásszolgáltatót adja meg. Ez az érték megegyezik a kiállítói jogcímek értékével, kivéve, ha a felhasználói fiók nem ugyanabban a bérlőben található, mint a kiállító – vendég. Ha a jogcím nem létezik, az azt jelenti, hogy a `iss` értéke használható helyette.  A szervezeti környezetben használt személyes fiókok esetében (például egy Azure AD-bérlőhöz meghívott személyes fiók) a `idp` jogcím a következő lehet: "live.com" vagy egy STS URI, amely tartalmazza a Microsoft-fiók bérlő `9188040d-6c67-4c5b-b112-36a304b66dad`. |
|`nbf` |  int, egy UNIX timestamp | A "NBF" (nem korábban) jogcím azt az időpontot határozza meg, ameddig a JWT nem fogadható el a feldolgozáshoz.|
|`exp` |  int, egy UNIX timestamp | Az "exp" (lejárati idő) jogcím azt a lejárati időt azonosítja, amely után a JWT nem fogadható el feldolgozásra.  Fontos megjegyezni, hogy egy erőforrás még a token elutasítása előtt is elutasítja a jogkivonatot – Ha például a hitelesítés változására van szükség, vagy a rendszer jogkivonat-visszavonást észlelt. |
| `c_hash`| Sztring |A kód kivonata csak akkor szerepel az azonosító jogkivonatokban, ha az azonosító jogkivonatot egy OAuth 2,0-es engedélyezési kóddal adják ki. Az engedélyezési kódok hitelességének ellenőrzéséhez használható. Az érvényesítés végrehajtásáról az [OpenID Connect specifikációjában](https://openid.net/specs/openid-connect-core-1_0.html)olvashat bővebben. |
|`at_hash`| Sztring |A hozzáférési jogkivonat kivonata csak akkor szerepel az azonosító jogkivonatokban, ha az azonosító jogkivonatot egy OAuth 2,0 hozzáférési jogkivonat adja ki. A hozzáférési token hitelességének ellenőrzéséhez használható. Az érvényesítés végrehajtásáról az [OpenID Connect specifikációjában](https://openid.net/specs/openid-connect-core-1_0.html)olvashat bővebben. |
|`aio` | Átlátszatlan karakterlánc | Az Azure AD által az adatok a jogkivonat újrafelhasználásához használt belső jogcímek. Figyelmen kívül kell hagyni.|
|`preferred_username` | Sztring | A felhasználót jelölő elsődleges Felhasználónév. Ez lehet egy e-mail-cím, telefonszám vagy egy általános Felhasználónév megadott formátum nélkül. Az értéke változtatható, és idővel változhat. Mivel ez az érték változhat, nem használható az engedélyezési döntések elvégzéséhez. A jogcím fogadásához a `profile` hatókör szükséges.|
|`email` | Sztring | A `email` jogcímek alapértelmezés szerint az e-mail-címmel rendelkező vendég fiókoknál jelennek meg.  Az alkalmazás a `email` [választható jogcím](active-directory-optional-claims.md)használatával kérheti az e-mail-jogcímet a felügyelt felhasználók (az erőforrással azonos bérlőtől).  A v 2.0-s végponton az alkalmazás a `email` OpenID Connect hatókört is kérheti – nem kell megadnia a választható jogcímet és a hatókört a jogcím beszerzéséhez.  Az e-mail-jogcím csak a felhasználó profiljának adataiból származó címezhető leveleket támogatja. |
|`name` | Sztring | Az `name` jogcím egy ember által olvasható értéket biztosít, amely a jogkivonat tárgyát azonosítja. Az érték nem garantált, hogy egyedi, megváltoztathatatlan, és csak megjelenítési célokra használható. A jogcím fogadásához a `profile` hatókör szükséges. |
|`nonce`| Sztring | Az egyszeres érték megegyezik az eredeti/Authorize-kérelemben szereplő paraméterrel a IDENTITÁSSZOLGÁLTATÓ. Ha nem egyezik, az alkalmazásnak el kell utasítania a jogkivonatot. |
|`oid` | Karakterlánc, GUID | Egy objektum megváltoztathatatlan azonosítója a Microsoft Identity Systemben, ebben az esetben egy felhasználói fiók. Ez az azonosító egyedileg azonosítja a felhasználót az alkalmazások között – két különböző alkalmazás, amely ugyanazon a felhasználón jelentkezik be, ugyanazokat az értékeket kapja meg az `oid` jogcímben. A Microsoft Graph egy adott felhasználói fiók `id` tulajdonságának adja vissza ezt az azonosítót. Mivel a `oid` lehetővé teszi több alkalmazás számára a felhasználók korrelációját, a jogcímek fogadásához a `profile` hatókörre van szükség. Vegye figyelembe, hogy ha egyetlen felhasználó több bérlőn is létezik, akkor a felhasználó minden bérlőn egy másik objektumazonosítót fog tartalmazni – ezeket a rendszer akkor is különböző fiókoknak tekinti, ha a felhasználó ugyanazzal a hitelesítő adatokkal jelentkezik be az egyes fiókokba. Az `oid` jogcím egy GUID azonosító, ezért nem használható fel újra. |
|`roles`| karakterláncok tömbje | A bejelentkezett felhasználóhoz hozzárendelt szerepkörök összessége. |
|`rh` | Átlátszatlan karakterlánc |Az Azure által a jogkivonatok újraellenőrzéséhez használt belső jogcím. Figyelmen kívül kell hagyni. |
|`sub` | Karakterlánc, GUID | Az a rendszerbiztonsági tag, amelyről a jogkivonat adatokat érvényesít, például egy alkalmazás felhasználóját. Ez az érték nem módosítható, és nem rendelhető hozzá újra, és nem használható újra. A tulajdonos egy páros-azonosító – egyedi egy adott alkalmazás-AZONOSÍTÓhoz. Ha egyetlen felhasználó két különböző alkalmazásba jelentkezik be két különböző ügyfél-azonosítóval, akkor ezek az alkalmazások két különböző értéket kapnak a tulajdonos jogcímek számára. Az architektúra-és adatvédelmi követelményektől függően ez lehet vagy nem kívánatos. |
|`tid` | Karakterlánc, GUID | Egy GUID, amely azt az Azure AD-bérlőt jelöli, amelyből a felhasználó származik. Munkahelyi és iskolai fiókok esetén a GUID az a szervezet, amelyhez a felhasználó tartozik. A személyes fiókok esetében az érték `9188040d-6c67-4c5b-b112-36a304b66dad`. A jogcím fogadásához a `profile` hatókör szükséges. |
|`unique_name` | Sztring | A jogkivonat alanyát azonosító, ember által olvasható értéket ad meg. Ez az érték egy adott időpontban egyedi, de az e-mailek és más azonosítók újból felhasználhatók, ez az érték más fiókokban is újra szerepelhet, ezért csak megjelenítési célokra használható. Csak a v 1.0 `id_tokens`kiadásban. |
|`uti` | Átlátszatlan karakterlánc | Az Azure által a jogkivonatok újraellenőrzéséhez használt belső jogcím. Figyelmen kívül kell hagyni. |
|`ver` | Karakterlánc, vagy 1,0 vagy 2,0 | Megadja a id_token verzióját. |


> [!NOTE]
> A v1-es és v2-es id_token különbségek vannak a fenti példákban látható információk mennyiségével. A verzió lényegében azt az Azure AD platform-végpontot határozza meg, ahonnan a kiadást adták. Az [Azure ad OAuth megvalósítása](https://docs.microsoft.com/azure/active-directory/develop/about-microsoft-identity-platform) az évek során fejlődött. Jelenleg két különböző oAuth-végponttal rendelkezünk a AzureAD-alkalmazásokhoz. Használhatja a v2-ként kategorizált új végpontokat vagy a régit, amely a v1. A OAuth végpontok mindkettő eltérő. A v2-végpont annál újabb, ahol a v1-végpont összes funkcióját megpróbáljuk áttelepíteni, és az új fejlesztőknek javasoljuk, hogy a v2-végpontot használják. 
> - V1: Azure Active Directory végpontok: `https://login.microsoftonline.com/common/oauth2/authorize`
> - V2: Microsoft Identity platform-végpontok: `https://login.microsoftonline.com/common/oauth2/v2.0/authorize`

## <a name="validating-an-id_token"></a>Id_token ellenőrzése

Egy `id_token` érvényesítése hasonló a [hozzáférési token érvényesítésének](access-tokens.md#validating-tokens) első lépéséhez – az ügyfélnek ellenőriznie kell, hogy a megfelelő kiállító visszaküldte-e a jogkivonatot, és hogy nem módosították-e illetéktelenül. Mivel a `id_tokens` mindig egy JWT, sok könyvtár létezik a tokenek érvényesítéséhez – azt javasoljuk, hogy használja ezeket a jogkivonatokat, ne pedig saját magának.

A jogkivonat manuális érvényesítéséhez tekintse meg a [hozzáférési token érvényesítése](access-tokens.md#validating-tokens)című témakör lépéseit. A jogkivonat aláírásának ellenőrzése után a következő jogcímeket érvényesíteni kell a id_tokenban (ezeket a jogkivonat-ellenőrzési függvénytár is végrehajthatja):

* Időbélyegek: a `iat`, `nbf`és `exp` időbélyegek az aktuális időpont előtt vagy után kell esniük. 
* Célközönség: a `aud` jogcímnek meg kell egyeznie az alkalmazáshoz tartozó alkalmazás-AZONOSÍTÓval.
* Alkalom: az adattartalomban lévő `nonce` jogcímnek meg kell egyeznie a/Authorize-végpontnak a kezdeti kérelem során átadott egyszeres paraméterrel.

## <a name="next-steps"></a>Következő lépések

* Tudnivalók a [hozzáférési tokenekről](access-tokens.md)
* A id_token jogcímeit a [választható jogcímek](active-directory-optional-claims.md)használatával szabhatja testre.
