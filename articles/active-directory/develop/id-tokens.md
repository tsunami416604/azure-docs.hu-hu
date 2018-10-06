---
title: Az Azure Active Directory-azonosító jogkivonat-referencia |} A Microsoft Docs
description: Id_tokens használatával projektsablon által kibocsátott a v1 és v2 végpontok az Azure ad-ben.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/05/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 85c8100dd567308500fac1c1d8577c84da98013f
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48815358"
---
# <a name="id-tokens"></a>Azonosító jogkivonatok

`id_tokens` az ügyfélalkalmazás részeként érkeznek egy [OpenID Connect](v1-protocols-openid-connect-code.md) folyamat. Elküldött egymás mellett vagy helyett egy hozzáférési jogkivonatot, és a felhasználó hitelesítéséhez az ügyfél által használt. 

## <a name="using-the-idtoken"></a>A id_token használatával

Ellenőrizze, hogy a felhasználó ki magukat kell, és a velük kapcsolatos további hasznos információk – nem használandó engedélyezési helyén használt azonosító-jogkivonatokat egy [hozzáférési jogkivonat](access-tokens.md). A jogcímeket biztosít UX az alkalmazást, kulcskezelő egy adatbázist, és az ügyfélalkalmazás való hozzáférés biztosítása belül használható. 

## <a name="claims-in-an-idtoken"></a>Id_tokent a jogcímeket

`id_tokens` a Microsoft identity vannak [JWTs](https://tools.ietf.org/html/rfc7519), ami azt jelenti, fejléc, hasznos és aláírás feladatának állnak. Használhatja a fejlécének és adattartalmának bontása a token hitelességének ellenőrzése közben a hasznos adatok tartalmazzák a felhasználó, az ügyfél által kért információk. Nincs másként jelölve az itt felsorolt összes jogcímek 1.0-s verziója és a 2.0-s verziójú jogkivonatok jelennek meg.

### <a name="v10"></a>1.0-s verzió

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyIsImtpZCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyJ9.eyJhdWQiOiJiMTRhNzUwNS05NmU5LTQ5MjctOTFlOC0wNjAxZDBmYzljYWEiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkvIiwiaWF0IjoxNTM2Mjc1MTI0LCJuYmYiOjE1MzYyNzUxMjQsImV4cCI6MTUzNjI3OTAyNCwiYWlvIjoiQVhRQWkvOElBQUFBcXhzdUIrUjREMnJGUXFPRVRPNFlkWGJMRDlrWjh4ZlhhZGVBTTBRMk5rTlQ1aXpmZzN1d2JXU1hodVNTajZVVDVoeTJENldxQXBCNWpLQTZaZ1o5ay9TVTI3dVY5Y2V0WGZMT3RwTnR0Z2s1RGNCdGsrTExzdHovSmcrZ1lSbXY5YlVVNFhscGhUYzZDODZKbWoxRkN3PT0iLCJhbXIiOlsicnNhIl0sImVtYWlsIjoiYWJlbGlAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiTGluY29sbiIsImdpdmVuX25hbWUiOiJBYmUiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaXBhZGRyIjoiMTMxLjEwNy4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJub25jZSI6IjEyMzUyMyIsIm9pZCI6IjA1ODMzYjZiLWFhMWQtNDJkNC05ZWMwLTFiMmJiOTE5NDQzOCIsInJoIjoiSSIsInN1YiI6IjVfSjlyU3NzOC1qdnRfSWN1NnVlUk5MOHhYYjhMRjRGc2dfS29vQzJSSlEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6IkFiZUxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJMeGVfNDZHcVRrT3BHU2ZUbG40RUFBIiwidmVyIjoiMS4wIn0=.UJQrCA6qn2bXq57qzGX_-D3HcPHqBMOKDPx4su1yKRLNErVD8xkxJLNLVRdASHqEcpyDctbdHccu6DPpkq5f0ibcaQFhejQNcABidJCTz0Bb2AbdUCTqAzdt9pdgQvMBnVH1xk3SCM6d4BbT4BkLLj10ZLasX7vRknaSjE_C5DI7Fg4WrZPwOhII1dB0HEZ_qpNaYXEiy-o94UJ94zCr07GgrqMsfYQqFR7kn-mn68AjvLcgwSfZvyR_yIK75S_K37vC3QryQ7cNoafDe9upql_6pB2ybMVlgWPs_DmbJ8g0om-sPlwyn74Cc1tW3ze-Xptw_2uVdPgWyqfuWAfq6Q
```

Az 1.0-s verziójú minta lexikális elem szerepel az megtekintése [jwt.ms](https://jwt.ms/#id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyIsImtpZCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyJ9.eyJhdWQiOiJiMTRhNzUwNS05NmU5LTQ5MjctOTFlOC0wNjAxZDBmYzljYWEiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkvIiwiaWF0IjoxNTM2Mjc1MTI0LCJuYmYiOjE1MzYyNzUxMjQsImV4cCI6MTUzNjI3OTAyNCwiYWlvIjoiQVhRQWkvOElBQUFBcXhzdUIrUjREMnJGUXFPRVRPNFlkWGJMRDlrWjh4ZlhhZGVBTTBRMk5rTlQ1aXpmZzN1d2JXU1hodVNTajZVVDVoeTJENldxQXBCNWpLQTZaZ1o5ay9TVTI3dVY5Y2V0WGZMT3RwTnR0Z2s1RGNCdGsrTExzdHovSmcrZ1lSbXY5YlVVNFhscGhUYzZDODZKbWoxRkN3PT0iLCJhbXIiOlsicnNhIl0sImVtYWlsIjoiYWJlbGlAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiTGluY29sbiIsImdpdmVuX25hbWUiOiJBYmUiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaXBhZGRyIjoiMTMxLjEwNy4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJub25jZSI6IjEyMzUyMyIsIm9pZCI6IjA1ODMzYjZiLWFhMWQtNDJkNC05ZWMwLTFiMmJiOTE5NDQzOCIsInJoIjoiSSIsInN1YiI6IjVfSjlyU3NzOC1qdnRfSWN1NnVlUk5MOHhYYjhMRjRGc2dfS29vQzJSSlEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6IkFiZUxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJMeGVfNDZHcVRrT3BHU2ZUbG40RUFBIiwidmVyIjoiMS4wIn0=.UJQrCA6qn2bXq57qzGX_-D3HcPHqBMOKDPx4su1yKRLNErVD8xkxJLNLVRdASHqEcpyDctbdHccu6DPpkq5f0ibcaQFhejQNcABidJCTz0Bb2AbdUCTqAzdt9pdgQvMBnVH1xk3SCM6d4BbT4BkLLj10ZLasX7vRknaSjE_C5DI7Fg4WrZPwOhII1dB0HEZ_qpNaYXEiy-o94UJ94zCr07GgrqMsfYQqFR7kn-mn68AjvLcgwSfZvyR_yIK75S_K37vC3QryQ7cNoafDe9upql_6pB2ybMVlgWPs_DmbJ8g0om-sPlwyn74Cc1tW3ze-Xptw_2uVdPgWyqfuWAfq6Q).

### <a name="v20"></a>2.0-s verzió

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjFMVE16YWtpaGlSbGFfOHoyQkVKVlhlV01xbyJ9.eyJ2ZXIiOiIyLjAiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vOTE4ODA0MGQtNmM2Ny00YzViLWIxMTItMzZhMzA0YjY2ZGFkL3YyLjAiLCJzdWIiOiJBQUFBQUFBQUFBQUFBQUFBQUFBQUFJa3pxRlZyU2FTYUZIeTc4MmJidGFRIiwiYXVkIjoiNmNiMDQwMTgtYTNmNS00NmE3LWI5OTUtOTQwYzc4ZjVhZWYzIiwiZXhwIjoxNTM2MzYxNDExLCJpYXQiOjE1MzYyNzQ3MTEsIm5iZiI6MTUzNjI3NDcxMSwibmFtZSI6IkFiZSBMaW5jb2xuIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiQWJlTGlAbWljcm9zb2Z0LmNvbSIsIm9pZCI6IjAwMDAwMDAwLTAwMDAtMDAwMC02NmYzLTMzMzJlY2E3ZWE4MSIsInRpZCI6IjMzMzgwNDBkLTZjNjctNGM1Yi1iMTEyLTM2YTMwNGI2NmRhZCIsIm5vbmNlIjoiMTIzNTIzIiwiYWlvIjoiRGYyVVZYTDFpeCFsTUNXTVNPSkJjRmF0emNHZnZGR2hqS3Y4cTVnMHg3MzJkUjVNQjVCaXN2R1FPN1lXQnlqZDhpUURMcSFlR2JJRGFreXA1bW5PcmNkcUhlWVNubHRlcFFtUnA2QUlaOGpZIn0=.1AFWW-Ck5nROwSlltm7GzZvDwUkqvhSQpm55TQsmVo9Y59cLhRXpvB8n-55HCr9Z6G_31_UbeUkoz612I2j_Sm9FFShSDDjoaLQr54CreGIJvjtmS3EkK9a7SJBbcpL1MpUtlfygow39tFjY7EVNW9plWUvRrTgVk7lYLprvfzw-CIqw3gHC-T7IK_m_xkr08INERBtaecwhTeN4chPC4W3jdmw_lIxzC48YoQ0dB1L9-ImX98Egypfrlbm0IBL5spFzL6JDZIRRJOu8vecJvj1mq-IUhGt0MacxX8jdxYLP-KUu2d9MbNKpCKJuZ7p8gwTL5B7NlUdh_dmSviPWrw
```

Megtekintheti a v2.0-minta lexikális elem szerepel az [jwt.ms](https://jwt.ms/#id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjFMVE16YWtpaGlSbGFfOHoyQkVKVlhlV01xbyJ9.eyJ2ZXIiOiIyLjAiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vOTE4ODA0MGQtNmM2Ny00YzViLWIxMTItMzZhMzA0YjY2ZGFkL3YyLjAiLCJzdWIiOiJBQUFBQUFBQUFBQUFBQUFBQUFBQUFJa3pxRlZyU2FTYUZIeTc4MmJidGFRIiwiYXVkIjoiNmNiMDQwMTgtYTNmNS00NmE3LWI5OTUtOTQwYzc4ZjVhZWYzIiwiZXhwIjoxNTM2MzYxNDExLCJpYXQiOjE1MzYyNzQ3MTEsIm5iZiI6MTUzNjI3NDcxMSwibmFtZSI6IkFiZSBMaW5jb2xuIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiQWJlTGlAbWljcm9zb2Z0LmNvbSIsIm9pZCI6IjAwMDAwMDAwLTAwMDAtMDAwMC02NmYzLTMzMzJlY2E3ZWE4MSIsInRpZCI6IjMzMzgwNDBkLTZjNjctNGM1Yi1iMTEyLTM2YTMwNGI2NmRhZCIsIm5vbmNlIjoiMTIzNTIzIiwiYWlvIjoiRGYyVVZYTDFpeCFsTUNXTVNPSkJjRmF0emNHZnZGR2hqS3Y4cTVnMHg3MzJkUjVNQjVCaXN2R1FPN1lXQnlqZDhpUURMcSFlR2JJRGFreXA1bW5PcmNkcUhlWVNubHRlcFFtUnA2QUlaOGpZIn0=.1AFWW-Ck5nROwSlltm7GzZvDwUkqvhSQpm55TQsmVo9Y59cLhRXpvB8n-55HCr9Z6G_31_UbeUkoz612I2j_Sm9FFShSDDjoaLQr54CreGIJvjtmS3EkK9a7SJBbcpL1MpUtlfygow39tFjY7EVNW9plWUvRrTgVk7lYLprvfzw-CIqw3gHC-T7IK_m_xkr08INERBtaecwhTeN4chPC4W3jdmw_lIxzC48YoQ0dB1L9-ImX98Egypfrlbm0IBL5spFzL6JDZIRRJOu8vecJvj1mq-IUhGt0MacxX8jdxYLP-KUu2d9MbNKpCKJuZ7p8gwTL5B7NlUdh_dmSviPWrw).

### <a name="header-claims"></a>Fejléc jogcímek

|Jogcím | Formátum | Leírás |
|-----|--------|-------------|
|`typ` | Karakterlánc - mindig "JWT" | Azt jelzi, hogy a jogkivonat jwt-t.|
|`alg` | Sztring | Azt jelzi, hogy a jogkivonat aláírásához használt algoritmust. Példa: "RS256" |
|`kid` | Sztring | A nyilvános kulcsot, ez a token aláírásához használt ujjlenyomatát. Az 1.0-s verziója és a v2.0 kibocsátott `id_tokens`. |
|`x5t` | Sztring | Azonos (a használati és érték) `kid`. Ez azonban csak az 1.0-s verziójú kibocsátott örökölt jogcím `id_tokens` kompatibilitási célból. |

### <a name="payload-claims"></a>Hasznos adat jogcímek

|Jogcím | Formátum | Leírás |
|-----|--------|-------------|
|`aud` | Az Alkalmazásazonosító URI-karakterláncot | A jogkivonat az illetékes címzett azonosítja. A `id_tokens`, a célközönségét az alkalmazás Alkalmazásazonosító, az Azure Portalon az alkalmazáshoz rendelt. Az alkalmazás kell érvényesíteni ezt az értéket, és elutasítja a tokent, ha az érték nem egyezik. |
|`iss` | Az STS-URI-karakterláncot | Azonosítja a biztonsági jogkivonat-szolgáltatás (STS) hoz létre, és a jogkivonatot, és az Azure AD-bérlővel, amelyben a felhasználó hitelesítési adja vissza. Ha a jogkivonat bocsátotta a v2.0-végpont, belül véget ér. az URI-t `/v2.0`. A GUID, amely azt jelzi, hogy a felhasználó Microsoft-fiók fogyasztói felhasználója `9188040d-6c67-4c5b-b112-36a304b66dad`. Az alkalmazás, amely bejelentkezhet az alkalmazásba, ha van ilyen bérlők korlátozni a jogcím GUID részének kell használni. |
|`iat` | int, a UNIX-időbélyege | "Feltüntetett tulajdonos:" azt jelenti, amikor a hitelesítés a jogkivonat történt. |
|`nbf` | int, a UNIX-időbélyege | Az "nbf" (előtte) jogcím azonosítja az idő előtt, amely a JWT kell nem lesznek elfogadva feldolgozásra.|
|`exp` | int, a UNIX-időbélyege | Az "exp" (lejárati ideje) jogcím azonosítja a lejárati időt, vagy azt követően, amely a JWT kell nem lesznek elfogadva feldolgozásra. Fontos megjegyezni, hogy egy erőforrás elutasíthatja a jogkivonatot, valamint - megadott idő előtti Ha például szükség egy az megváltoztatására hitelesítést, vagy a tokenek visszavonása észlelt. |
| `c_hash`| Sztring |A kód kivonat csak akkor, ha az azonosító jogkivonat, az OAuth 2.0 engedélyezési kóddal kiadott azonosító-jogkivonatokat szerepel. Az engedélyezési kódot hitelességének ellenőrzéséhez használható. További ellenőrzés elvégzésével kapcsolatos információkért lásd: a [OpenID Connect specifikáció](http://openid.net/specs/openid-connect-core-1_0.html). |
|`at_hash`| Sztring |Jogkivonat kivonata azonosító szerepel a hozzáférési jogkivonatok, csak ha az azonosító jogkivonat kiadott egy OAuth 2.0 hozzáférési jogkivonatban. Hozzáférési jogkivonat hitelességének ellenőrzéséhez használható. További ellenőrzés elvégzésével kapcsolatos információkért lásd: a [OpenID Connect specifikáció](http://openid.net/specs/openid-connect-core-1_0.html). |
|`aio` | Átlátszatlan karakterlánc | Egy jogkivonat újrafelhasználásra erőforrásrekord-adatokat az Azure AD által használt belső jogcímet. Figyelmen kívül lehet hagyni.|
|`preferred_username` | Sztring | Az elsődleges felhasználónév, amely a felhasználó jelöli. Ez lehet egy e-mail-címet, telefonszámot vagy egy általános felhasználónév nélkül egy megadott formátumban. Az érték mutable és idővel változhatnak. Mivel ezekre kapott válaszokon, ez az érték nem használható az engedélyezési döntésekhez. A `profile` hatókör szükséges, ezt a kérelmet kap.|
|`name` | Sztring | A `name` jogcím biztosítja az emberek számára olvasható érték, amely azonosítja a jogkivonat tárgyában. Az érték nem garantált, hogy egyedi legyen, ezekre kapott válaszokon, és úgy tervezték, hogy csak megjelenítési célokra használható. A `profile` hatókör szükséges, ezt a kérelmet kap. |
|`nonce`| Sztring | Az egyszeri megegyezik a paraméter tartalmazza az eredeti vagy az Identitásszolgáltató irányuló kérelem engedélyezéséhez. Ha nem egyezik, az alkalmazás a jogkivonatot kell utasítania. |
|`oid` | Karakterlánc, egy GUID Azonosítót | A Microsoft identity rendszeren, ebben az esetben egy felhasználói fiókot egy objektum nem módosítható azonosítója. Alkalmazások közötti egyedileg azonosítja a felhasználó ezt az Azonosítót – ugyanaz a felhasználó bejelentkezik, két különböző alkalmazás ugyanazt az értéket a fog kapni a `oid` jogcím. A Microsoft Graph ezt az Azonosítót ad vissza a `id` tulajdonság egy adott felhasználói fiókhoz. Mivel a `oid` korrelációját, ha a felhasználók, több alkalmazás lehetővé teszi a `profile` hatókör szükséges, ezt a kérelmet kap. Vegye figyelembe, hogy ha egy felhasználó több bérlő már létezik, a felhasználó fogja tartalmazni az egyes bérlők egy másik objektum azonosítója – a különböző fiókok, annak ellenére, hogy a felhasználó bejelentkezik az egyes ugyanazokkal a hitelesítő adatokkal rendelkező fiók minősülnek. |
|`rh` | Átlátszatlan karakterlánc |Egy belső jogcím kísérelje meg újra érvényesítését jogkivonatok Azure segítségével. Figyelmen kívül lehet hagyni. |
|`sub` | Karakterlánc, egy GUID Azonosítót | Az egyszerű arról, hogy mely a token használjon esetleg imperatív állításokat információkat, például a felhasználó az alkalmazás. Ez az érték nem módosítható és nem hozzárendelni és újra felhasználható. A tulajdonos az páros azonosító – egyedi legyen egy adott alkalmazás azonosítója. Ezért ha egy felhasználó bejelentkezik a két különböző ügyfél-azonosító használatával két különböző alkalmazásokat, ezeket az alkalmazásokat két különböző értékeket, a tulajdonos jogcím fog kapni. Ez előfordulhat, hogy, vagy előfordulhat, hogy nem megfelelő az architektúra és adatvédelmi követelményeitől függően. |
|`tid` | Karakterlánc, egy GUID Azonosítót | Egy GUID jelöli, hogy a felhasználó Azure AD-bérlővel. Munkahelyi és iskolai fiókok esetében a GUID azonosító a szervezet, amely a felhasználó tartozik Bérlőazonosítója nem módosítható. Személyes fiókok esetében az érték `9188040d-6c67-4c5b-b112-36a304b66dad`. A `profile` hatókör szükséges, ezt a kérelmet kap. |
|`unique_name` | Sztring | A jogkivonat alanyát azonosító, ember által olvasható értéket ad meg. Ez az érték nem garantált egy bérlőn belül egyedinek kell lennie, és csak megjelenítési célokra használható. Csak az 1.0-s verziójú kiadott `id_tokens`. |
|`uti` | Átlátszatlan karakterlánc | Egy belső jogcím kísérelje meg újra érvényesítését jogkivonatok Azure segítségével. Figyelmen kívül lehet hagyni. |
|`ver` | Karakterlánc, 1.0 vagy 2.0-s | Azt jelzi, hogy a id_token verzióját. |

## <a name="validating-an-idtoken"></a>Id_token ellenőrzése

Ellenőrzése egy `id_token` nagyon hasonlít az első lépés a [hozzáférési jogkivonat érvényesítése](access-tokens.md#validating-tokens) – az ügyfél ellenőrizze, hogy a megfelelő kiállítótól vissza a tokent küldött, és, hogy ez még nem módosították illetéktelenül. Mivel `id_tokens` jwt-t minden esetben vannak, ezek a jogkivonatok érvényesítésére létezik számos kódtár – javasoljuk, hogy ezek helyett saját valamelyikét használja. 

Manuális ellenőrzése a jogkivonat, a részleteket a lépéseket a [hozzáférési jogkivonat érvényesítése](access-tokens.md#validating-tokens). Miután ellenőrizte a jogkivonat aláírása, a következő jogcímeket kell ellenőriznie a id_token (ezek is történhet a jogkivonat érvényesítésére kódtár által):

* Időbélyegeket: a `iat`, `nbf`, és `exp` időbélyeggel kell az összes esik a rendelkezésre állás előtt vagy után az aktuális idő, szükség szerint. 
* Célközönség: a `aud` jogcím meg kell egyeznie az Alkalmazásazonosító az alkalmazás.
* Egyszeri: a `nonce` az adattartalomban szereplő jogcímek meg kell egyeznie az átadott nonce paraméter a / authorize végponton a kezdeti kérés.

## <a name="next-steps"></a>További lépések

* Ismerje meg [az Azure AD hozzáférési jogkivonatok](access-tokens.md)
