---
title: Microsoft identity platformazonosító tokenjére vonatkozó hivatkozás
description: Ismerje meg, hogyan használhatja az Azure AD 1.0-s és a Microsoft Identity platform (2.0-s verzió) végpontok által kibocsátott id_tokens.
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
ms.openlocfilehash: 3ede70719c124caebc541df8578ab2720fc68cd5
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885514"
---
# <a name="microsoft-identity-platform-id-tokens"></a>Microsoft-identitásplatform-azonosító tokenjei

`id_tokens`[openId connect](v2-protocols-oidc.md) folyamat részeként küldik el az ügyfélalkalmazásnak. Ezek egymás mellett vagy egy hozzáférési jogkivonat helyett elküldhetők, és az ügyfél a felhasználó hitelesítésére használja őket.

## <a name="using-the-id_token"></a>A id_token használata

Az azonosító jogkivonatokat annak ellenőrzésére kell használni, hogy a felhasználó az, akinek állítja, és további hasznos információkat kap róluk – nem használható hozzáférési [jogkivonat](access-tokens.md)helyett engedélyezésre. Az általa biztosított jogcímek az alkalmazáson belüli ux-hez, az adatbázis kulcsaiként használhatók, és hozzáférést biztosítanak az ügyfélalkalmazáshoz.  Az adatbázis kulcsainak `idp` létrehozásakor nem szabad használni, mert elrontja a vendégforgatókönyveket.  A kulcsolást `sub` egyedül kell elvégezni (ami `tid` mindig egyedi), szükség esetén útválasztáshoz használva.  Ha meg kell osztania `oid` + `sub` + `tid` az adatokat a szolgáltatások `oid`között, működni fog, mivel több szolgáltatás is ugyanazt kapja.

## <a name="claims-in-an-id_token"></a>Követelések egy id_token

`id_tokens`microsoftos identitásesetén [jwt-k,](https://tools.ietf.org/html/rfc7519)ami azt jelenti, hogy fejlécből, hasznos adatból és aláírási részből állnak. A fejléc és az aláírás segítségével ellenőrizheti a jogkivonat hitelességét, míg a hasznos adat az ügyfél által kért felhasználó adatait tartalmazza. Kivéve, ha megjegyezte, az itt felsorolt összes jogcím mind az 1.0-s, mind a 2.0-s jogkivonatokban megjelenik.

### <a name="v10"></a>1.0-s verzió

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyIsImtpZCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyJ9.eyJhdWQiOiJiMTRhNzUwNS05NmU5LTQ5MjctOTFlOC0wNjAxZDBmYzljYWEiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkvIiwiaWF0IjoxNTM2Mjc1MTI0LCJuYmYiOjE1MzYyNzUxMjQsImV4cCI6MTUzNjI3OTAyNCwiYWlvIjoiQVhRQWkvOElBQUFBcXhzdUIrUjREMnJGUXFPRVRPNFlkWGJMRDlrWjh4ZlhhZGVBTTBRMk5rTlQ1aXpmZzN1d2JXU1hodVNTajZVVDVoeTJENldxQXBCNWpLQTZaZ1o5ay9TVTI3dVY5Y2V0WGZMT3RwTnR0Z2s1RGNCdGsrTExzdHovSmcrZ1lSbXY5YlVVNFhscGhUYzZDODZKbWoxRkN3PT0iLCJhbXIiOlsicnNhIl0sImVtYWlsIjoiYWJlbGlAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiTGluY29sbiIsImdpdmVuX25hbWUiOiJBYmUiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaXBhZGRyIjoiMTMxLjEwNy4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJub25jZSI6IjEyMzUyMyIsIm9pZCI6IjA1ODMzYjZiLWFhMWQtNDJkNC05ZWMwLTFiMmJiOTE5NDQzOCIsInJoIjoiSSIsInN1YiI6IjVfSjlyU3NzOC1qdnRfSWN1NnVlUk5MOHhYYjhMRjRGc2dfS29vQzJSSlEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6IkFiZUxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJMeGVfNDZHcVRrT3BHU2ZUbG40RUFBIiwidmVyIjoiMS4wIn0=.UJQrCA6qn2bXq57qzGX_-D3HcPHqBMOKDPx4su1yKRLNErVD8xkxJLNLVRdASHqEcpyDctbdHccu6DPpkq5f0ibcaQFhejQNcABidJCTz0Bb2AbdUCTqAzdt9pdgQvMBnVH1xk3SCM6d4BbT4BkLLj10ZLasX7vRknaSjE_C5DI7Fg4WrZPwOhII1dB0HEZ_qpNaYXEiy-o94UJ94zCr07GgrqMsfYQqFR7kn-mn68AjvLcgwSfZvyR_yIK75S_K37vC3QryQ7cNoafDe9upql_6pB2ybMVlgWPs_DmbJ8g0om-sPlwyn74Cc1tW3ze-Xptw_2uVdPgWyqfuWAfq6Q
```

Tekintse meg ezt a v1.0-s mintatotot [a jwt.ms.](https://jwt.ms/#id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyIsImtpZCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyJ9.eyJhdWQiOiJiMTRhNzUwNS05NmU5LTQ5MjctOTFlOC0wNjAxZDBmYzljYWEiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkvIiwiaWF0IjoxNTM2Mjc1MTI0LCJuYmYiOjE1MzYyNzUxMjQsImV4cCI6MTUzNjI3OTAyNCwiYWlvIjoiQVhRQWkvOElBQUFBcXhzdUIrUjREMnJGUXFPRVRPNFlkWGJMRDlrWjh4ZlhhZGVBTTBRMk5rTlQ1aXpmZzN1d2JXU1hodVNTajZVVDVoeTJENldxQXBCNWpLQTZaZ1o5ay9TVTI3dVY5Y2V0WGZMT3RwTnR0Z2s1RGNCdGsrTExzdHovSmcrZ1lSbXY5YlVVNFhscGhUYzZDODZKbWoxRkN3PT0iLCJhbXIiOlsicnNhIl0sImVtYWlsIjoiYWJlbGlAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiTGluY29sbiIsImdpdmVuX25hbWUiOiJBYmUiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaXBhZGRyIjoiMTMxLjEwNy4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJub25jZSI6IjEyMzUyMyIsIm9pZCI6IjA1ODMzYjZiLWFhMWQtNDJkNC05ZWMwLTFiMmJiOTE5NDQzOCIsInJoIjoiSSIsInN1YiI6IjVfSjlyU3NzOC1qdnRfSWN1NnVlUk5MOHhYYjhMRjRGc2dfS29vQzJSSlEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6IkFiZUxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJMeGVfNDZHcVRrT3BHU2ZUbG40RUFBIiwidmVyIjoiMS4wIn0=.UJQrCA6qn2bXq57qzGX_-D3HcPHqBMOKDPx4su1yKRLNErVD8xkxJLNLVRdASHqEcpyDctbdHccu6DPpkq5f0ibcaQFhejQNcABidJCTz0Bb2AbdUCTqAzdt9pdgQvMBnVH1xk3SCM6d4BbT4BkLLj10ZLasX7vRknaSjE_C5DI7Fg4WrZPwOhII1dB0HEZ_qpNaYXEiy-o94UJ94zCr07GgrqMsfYQqFR7kn-mn68AjvLcgwSfZvyR_yIK75S_K37vC3QryQ7cNoafDe9upql_6pB2ybMVlgWPs_DmbJ8g0om-sPlwyn74Cc1tW3ze-Xptw_2uVdPgWyqfuWAfq6Q)

### <a name="v20"></a>2.0-s verzió

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjFMVE16YWtpaGlSbGFfOHoyQkVKVlhlV01xbyJ9.eyJ2ZXIiOiIyLjAiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vOTEyMjA0MGQtNmM2Ny00YzViLWIxMTItMzZhMzA0YjY2ZGFkL3YyLjAiLCJzdWIiOiJBQUFBQUFBQUFBQUFBQUFBQUFBQUFJa3pxRlZyU2FTYUZIeTc4MmJidGFRIiwiYXVkIjoiNmNiMDQwMTgtYTNmNS00NmE3LWI5OTUtOTQwYzc4ZjVhZWYzIiwiZXhwIjoxNTM2MzYxNDExLCJpYXQiOjE1MzYyNzQ3MTEsIm5iZiI6MTUzNjI3NDcxMSwibmFtZSI6IkFiZSBMaW5jb2xuIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiQWJlTGlAbWljcm9zb2Z0LmNvbSIsIm9pZCI6IjAwMDAwMDAwLTAwMDAtMDAwMC02NmYzLTMzMzJlY2E3ZWE4MSIsInRpZCI6IjkxMjIwNDBkLTZjNjctNGM1Yi1iMTEyLTM2YTMwNGI2NmRhZCIsIm5vbmNlIjoiMTIzNTIzIiwiYWlvIjoiRGYyVVZYTDFpeCFsTUNXTVNPSkJjRmF0emNHZnZGR2hqS3Y4cTVnMHg3MzJkUjVNQjVCaXN2R1FPN1lXQnlqZDhpUURMcSFlR2JJRGFreXA1bW5PcmNkcUhlWVNubHRlcFFtUnA2QUlaOGpZIn0.1AFWW-Ck5nROwSlltm7GzZvDwUkqvhSQpm55TQsmVo9Y59cLhRXpvB8n-55HCr9Z6G_31_UbeUkoz612I2j_Sm9FFShSDDjoaLQr54CreGIJvjtmS3EkK9a7SJBbcpL1MpUtlfygow39tFjY7EVNW9plWUvRrTgVk7lYLprvfzw-CIqw3gHC-T7IK_m_xkr08INERBtaecwhTeN4chPC4W3jdmw_lIxzC48YoQ0dB1L9-ImX98Egypfrlbm0IBL5spFzL6JDZIRRJOu8vecJvj1mq-IUhGt0MacxX8jdxYLP-KUu2d9MbNKpCKJuZ7p8gwTL5B7NlUdh_dmSviPWrw
```

Tekintse meg ezt a v2.0-s mintatotot [a jwt.ms.](https://jwt.ms/#id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjFMVE16YWtpaGlSbGFfOHoyQkVKVlhlV01xbyJ9.eyJ2ZXIiOiIyLjAiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vOTEyMjA0MGQtNmM2Ny00YzViLWIxMTItMzZhMzA0YjY2ZGFkL3YyLjAiLCJzdWIiOiJBQUFBQUFBQUFBQUFBQUFBQUFBQUFJa3pxRlZyU2FTYUZIeTc4MmJidGFRIiwiYXVkIjoiNmNiMDQwMTgtYTNmNS00NmE3LWI5OTUtOTQwYzc4ZjVhZWYzIiwiZXhwIjoxNTM2MzYxNDExLCJpYXQiOjE1MzYyNzQ3MTEsIm5iZiI6MTUzNjI3NDcxMSwibmFtZSI6IkFiZSBMaW5jb2xuIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiQWJlTGlAbWljcm9zb2Z0LmNvbSIsIm9pZCI6IjAwMDAwMDAwLTAwMDAtMDAwMC02NmYzLTMzMzJlY2E3ZWE4MSIsInRpZCI6IjkxMjIwNDBkLTZjNjctNGM1Yi1iMTEyLTM2YTMwNGI2NmRhZCIsIm5vbmNlIjoiMTIzNTIzIiwiYWlvIjoiRGYyVVZYTDFpeCFsTUNXTVNPSkJjRmF0emNHZnZGR2hqS3Y4cTVnMHg3MzJkUjVNQjVCaXN2R1FPN1lXQnlqZDhpUURMcSFlR2JJRGFreXA1bW5PcmNkcUhlWVNubHRlcFFtUnA2QUlaOGpZIn0.1AFWW-Ck5nROwSlltm7GzZvDwUkqvhSQpm55TQsmVo9Y59cLhRXpvB8n-55HCr9Z6G_31_UbeUkoz612I2j_Sm9FFShSDDjoaLQr54CreGIJvjtmS3EkK9a7SJBbcpL1MpUtlfygow39tFjY7EVNW9plWUvRrTgVk7lYLprvfzw-CIqw3gHC-T7IK_m_xkr08INERBtaecwhTeN4chPC4W3jdmw_lIxzC48YoQ0dB1L9-ImX98Egypfrlbm0IBL5spFzL6JDZIRRJOu8vecJvj1mq-IUhGt0MacxX8jdxYLP-KUu2d9MbNKpCKJuZ7p8gwTL5B7NlUdh_dmSviPWrw)

### <a name="header-claims"></a>Fejlécjogcímek

|Jogcím | Formátum | Leírás |
|-----|--------|-------------|
|`typ` | String - mindig "JWT" | Azt jelzi, hogy a token jwt.|
|`alg` | Sztring | A jogkivonat aláírásához használt algoritmust jelzi. Példa: "RS256" |
|`kid` | Sztring | A jogkivonat aláírásához használt nyilvános kulcs ujjlenyomata. Mind az 1.0-s, mind `id_tokens`a 2.0-s értékben kibocsátott érték. |
|`x5t` | Sztring | Ugyanaz (használatban és értékben), mint `kid`. Ez azonban egy örökölt jogcím, amelyet `id_tokens` csak az 1.0-s verzióban ad nak ki kompatibilitási célokra. |

### <a name="payload-claims"></a>Hasznos teherre vonatkozó igények

Ez a lista azokat a jogcímeket jeleníti meg, amelyek alapértelmezés szerint a legtöbb id_tokens (kivéve, ha nincs feltüntetve).  Az alkalmazás azonban [opcionális jogcímeket](active-directory-optional-claims.md) is használhat további jogcímek igénylésére a id_token.  Ezek a jogcímtől a `groups` felhasználó nevével kapcsolatos információkig terjedhetnek.

|Jogcím | Formátum | Leírás |
|-----|--------|-------------|
|`aud` |  String, egy alkalmazásazonosító URI-ja | A jogkivonat címzettjét azonosítja. A `id_tokens`alkalmazásban a célközönség az alkalmazás alkalmazásazonosítója, amely az Azure Portalon van hozzárendelve az alkalmazáshoz. Az alkalmazásnak érvényesítenie kell ezt az értéket, és el kell utasítania a jogkivonatot, ha az érték nem egyezik. |
|`iss` |  Karakterlánc, STS URI | Azonosítja a biztonsági jogkivonat-szolgáltatás (STS), amely a jogkivonatot létrehozó és visszaadja, és az Azure AD-bérlő, amelyben a felhasználó hitelesítése. Ha a jogkivonatot a v2.0 végpont adta `/v2.0`ki, az URI a .  A GUID, amely azt jelzi, hogy a felhasználó `9188040d-6c67-4c5b-b112-36a304b66dad`egy microsoftos fiók fogyasztói felhasználója, a . Az alkalmazásnak a jogcím GUID-részét kell használnia az alkalmazásba bejelentkező bérlők készletének korlátozására, ha van ilyen. |
|`iat` |  int, UNIX időbélyeg | A "Issued At" azt jelzi, hogy a token hitelesítése mikor történt.  |
|`idp`|Karakterlánc, általában STS URI | A jogkivonat alanyát hitelesítő identitásszolgáltatót adja meg. Ez az érték megegyezik a Kiállító jogcím értékével, kivéve, ha a felhasználói fiók nem ugyanabban a bérlőben van, mint a kibocsátó - például a vendégek. Ha a jogcím nincs jelen, az `iss` azt jelenti, hogy helyette az érték használható.  A szervezeti környezetben használt személyes fiókok (például egy Azure AD-bérlőnek `idp` meghívott személyes fiók) esetében a jogcím lehet "live.com" vagy a Microsoft-fiók bérlőjét `9188040d-6c67-4c5b-b112-36a304b66dad`tartalmazó STS URI. |
|`nbf` |  int, UNIX időbélyeg | Az "nbf" (nem korábban) állítás azonosítja azt az időt, amely előtt a JWT nem fogadható el feldolgozásra.|
|`exp` |  int, UNIX időbélyeg | Az "exp" (lejárati idő) jogcím azonosítja a lejárati időt, vagy amely után a JWT nem fogadható el a feldolgozásra.  Fontos megjegyezni, hogy egy erőforrás elutasíthatja a jogkivonatot, mielőtt ez az idő is – ha például a hitelesítés módosítása szükséges, vagy egy jogkivonat visszavonása észlelése. |
| `c_hash`| Sztring |A kódkivonat csak akkor szerepel az azonosítójogtolókban, ha az azonosító jogkivonat ot OAuth 2.0 engedélyezési kóddal adják ki. Az engedélyezési kód hitelességének ellenőrzésére használható. Az ellenőrzés végrehajtásáról az [OpenID Connect specifikációban talál részleteket.](https://openid.net/specs/openid-connect-core-1_0.html) |
|`at_hash`| Sztring |A hozzáférési jogkivonat-kivonat csak akkor szerepel az azonosítójogkivonatokban, ha az azonosító jogkivonatot egy OAuth 2.0-s hozzáférési jogkivonattal adják ki. A hozzáférési jogkivonat hitelességének ellenőrzésére használható. Az ellenőrzés végrehajtásáról az [OpenID Connect specifikációban talál részleteket.](https://openid.net/specs/openid-connect-core-1_0.html) |
|`aio` | Átlátszatlan karakterlánc | Az Azure AD által használt belső jogcím a jogkivonat újrafelhasználása adatok rögzítésére. Figyelmen kívül kell hagyni.|
|`preferred_username` | Sztring | A felhasználót jelképező elsődleges felhasználónév. Ez lehet egy e-mail cím, telefonszám, vagy egy általános felhasználónév nélkül megadott formátumban. Értéke változékony, és idővel változhat. Mivel ez az érték módosítható, ezt az értéket nem szabad engedélyezési döntések meghozatalához használni. A `profile` jogcím fogadásához a hatókör szükséges.|
|`email` | Sztring | A `email` jogcím alapértelmezés szerint jelen van az e-mail címmel rendelkező vendégfiókok esetében.  Az alkalmazás kérheti az e-mail jogcímet a felügyelt felhasználók (az azonos bérlő, mint az erőforrás) a `email` választható [jogcím](active-directory-optional-claims.md)használatával.  A v2.0-s végponton az alkalmazás `email` is kérheti az OpenID Connect hatókört – nem kell mind a választható jogcímet, sem a hatókört a jogcím lekéréséhez.  Az e-mail jogcím csak a felhasználó profiladataiból származó címezhető e-maileket támogatja. |
|`name` | Sztring | A `name` jogcím egy ember által olvasható értéket biztosít, amely azonosítja a jogkivonat tárgyát. Az érték nem garantáltan egyedi, változékony, és úgy tervezték, hogy csak megjelenítési célokra használható. A `profile` jogcím fogadásához a hatókör szükséges. |
|`nonce`| Sztring | A nonce megegyezik az idp-nek küldött eredeti /authorize kérésben szereplő paraméterrel. Ha nem egyezik, az alkalmazás el kell utasítania a jogkivonatot. |
|`oid` | Karakterlánc, GUID | A Microsoft-identitásrendszerben lévő objektum, ebben az esetben egy felhasználói fiók nem módosítható azonosítója. Ez az azonosító egyedileg azonosítja a felhasználót az alkalmazások között – két `oid` különböző alkalmazás, amelyek ugyanabban a felhasználóban jelentkeznek be, ugyanazt az értéket kapják a jogcímben. A Microsoft Graph ezt az `id` azonosítót adja vissza egy adott felhasználói fiók tulajdonságaként. Mivel `oid` a lehetővé teszi, hogy `profile` több alkalmazás korrelálja a felhasználókat, a hatókör szükséges a jogcím fogadásához. Vegye figyelembe, hogy ha egy felhasználó több bérlőben is létezik, a felhasználó minden bérlőben más-más objektumazonosítót fog tartalmazni – azok különböző fiókoknak számítanak, még akkor is, ha a felhasználó minden fiókba ugyanazzal a hitelesítő adatokkal jelentkezik be. A `oid` jogcím GUID azonosító, és nem használható fel újra. |
|`roles`| Karakterláncok tömbje | A bejelentkező felhasználóhoz rendelt szerepkörök készlete. |
|`rh` | Átlátszatlan karakterlánc |Az Azure által a jogkivonatok újraérvényesítéséhez használt belső jogcím. Figyelmen kívül kell hagyni. |
|`sub` | Karakterlánc, GUID | Az a főtag, amelyről a jogkivonat adatokat, például egy alkalmazás felhasználóját érvényesíti. Ez az érték nem módosítható, és nem rendelhető hozzá újra és nem használható fel újra. A tárgy egy páronkénti azonosító – egy adott alkalmazásazonosítóegyedi. Ha egy felhasználó két különböző ügyfélazonosítóval jelentkezik be két különböző alkalmazásba, ezek az alkalmazások két különböző értéket kapnak a tulajdonosjogcímhez. Ez lehet, hogy nem kívánatos, attól függően, hogy az architektúra és az adatvédelmi követelmények. |
|`tid` | Karakterlánc, GUID | Egy GUID, amely az Azure AD-bérlő, amely a felhasználó a guid. Munkahelyi és iskolai fiókok esetén a GUID annak a szervezetnek a nem módosítható bérlői azonosítója, amelyhez a felhasználó tartozik. Személyes fiókok esetén az `9188040d-6c67-4c5b-b112-36a304b66dad`érték . A `profile` jogcím fogadásához a hatókör szükséges. |
|`unique_name` | Sztring | A jogkivonat alanyát azonosító, ember által olvasható értéket ad meg. Ez az érték egy adott időpontban egyedi, de mivel az e-mailek és más azonosítók újra felhasználhatók, ez az érték újra megjelenhet más fiókokban, és ezért csak megjelenítési célokra használható. Csak az 1.0-s `id_tokens`részben adják ki. |
|`uti` | Átlátszatlan karakterlánc | Az Azure által a jogkivonatok újraérvényesítéséhez használt belső jogcím. Figyelmen kívül kell hagyni. |
|`ver` | 1.0 vagy 2.0 karakterlánc | A id_token verzióját jelzi. |


> [!NOTE]
> A v1 és v2 id_token különbségei vannak az információ mennyisége fogják folytatni, amint az a fenti példákban. A verzió lényegében meghatározza az Azure AD platform végpontját, ahonnan kiadták. [Az Azure AD Oauth implementációja](https://docs.microsoft.com/azure/active-directory/develop/about-microsoft-identity-platform) az évek során fejlődött. Jelenleg két különböző oAuth-végpontok AzureAD-alkalmazások. Használhatja az új végpontok, amelyek v2-ként vannak kategorizálva, vagy a régi, amely azt mondta, hogy v1. Az Oauth végpontok mindkettő esetében eltérőek. A V2-végpont az újabb, ahol megpróbáljuk áttelepíteni a v1 végpont összes szolgáltatását, és javasoljuk az új fejlesztőknek a v2-végpont használatát. 
> - V1: Azure Active Directory-végpontok:`https://login.microsoftonline.com/common/oauth2/authorize`
> - V2: Microsoft Identity Platform végpontok:`https://login.microsoftonline.com/common/oauth2/v2.0/authorize`

## <a name="validating-an-id_token"></a>Id_token érvényesítése

A hozzáférési `id_token` jogkivonat érvényesítésének első lépéséhez hasonló a hozzáférési jogkivonat érvényesítése – az ügyfélnek ellenőriznie kell, hogy a megfelelő kibocsátó visszaküldte-e a jogkivonatot, és hogy nem módosították.Validating an is similar to the first step [of validating an accessating an accessating of a accesstok](access-tokens.md#validating-tokens) – your client should validate that the correct issuer sent sent back the token and that it has been nem tampered with. Mivel `id_tokens` mindig jwt, sok könyvtár létezik, hogy érvényesítse ezeket a tokeneket - azt javasoljuk, hogy használja az egyik ilyen helyett csinálja magad.

A jogkivonat manuális érvényesítéséhez tekintse meg a [hozzáférési jogkivonat érvényesítésének lépéseit.](access-tokens.md#validating-tokens) A jogkivonat on-ra történő aláírás érvényesítése után a következő jogcímeket kell érvényesíteni a id_token (ezeket a jogkivonat-érvényesítési könyvtár is elvégezheti):

* Időbélyegek: `iat`a `nbf`, `exp` és az időbélyegek kell minden esik előtt vagy után az aktuális idő, a megfelelő módon. 
* Célközönség: `aud` a jogcímnek meg kell egyeznie az alkalmazás alkalmazásazonosítójának.
* Nonce: `nonce` a kifizetési jogcímnek meg kell egyeznie a /authorize végpontnak az első kérelem során átadott nonce paraméterrel.

## <a name="next-steps"></a>További lépések

* További információ a [hozzáférési jogkivonatokról](access-tokens.md)
* A id_token a jogcímek testreszabása [választható jogcímek](active-directory-optional-claims.md)használatával.
