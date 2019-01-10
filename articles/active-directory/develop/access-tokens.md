---
title: Az Azure Active Directory hozzáférési jogkivonatok hivatkozás |} A Microsoft Docs
description: Ismerje meg az 1.0-s és 2.0-s verziójú végpontok az Azure AD által kibocsátott hozzáférési jogkivonatok.
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
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 164fc42d905c9354a58ea6f66a739ea05f12e601
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2019
ms.locfileid: "54157768"
---
# <a name="azure-active-directory-access-tokens"></a>Az Azure Active Directory hozzáférési jogkivonatok

Hozzáférési jogkivonatok engedélyezése az ügyfelek számára védi az Azure API-k biztonságos hívása. Az Azure Active Directory (Azure AD) hozzáférési jogkivonatok vannak [JWTs](https://tools.ietf.org/html/rfc7519), Base64 kódolású JSON-objektumok az Azure által aláírt. Az ügyfelek csak az erőforrás hozzáférési jogkivonat tartalma, nem átlátszó beállításhalmazokkal tokenek elsődlegesen kell kezelnie. Érvényesítési és hibakeresési célra, a fejlesztők dekódolása is JWTs egy helyet, például [jwt.ms](https://jwt.ms). Az ügyfél is szükséges hozzáférési jogkivonat beszerzése vagy végpontról (1.0 vagy 2.0-s verzió) számos protokollt használ.

Egy hozzáférési jogkivonatot kér, amikor az Azure AD, az is kínál az alkalmazás a hozzáférési jogkivonat bizonyos metaadatait adja vissza. Ezen információk közé tartozik a hozzáférési jogkivonatot, és a hatóköröket, amelyek esetében érvényes lejárati idejét. Ezek az adatok lehetővé teszi az alkalmazás intelligens gyorsítótárazás hozzáférési jogkivonatokat elemezni a hozzáférési jogkivonat maga nélkül.

Ha az alkalmazás egy erőforrást (webes API-t), amely az ügyfelek lekérdezhetik-e a hozzáférést, a hozzáférési jogkivonatok használható hitelesítésre és engedélyezésre, például a felhasználó, ügyfél, kibocsátó, engedélyek és további hasznos információkat adja meg. 

Az alábbi részekből megtudhatja, hogyan erőforrás ellenőrzése és belül egy hozzáférési jogkivonatot a jogcímek használata.

> [!NOTE]
> Az ügyfélalkalmazás (például hotmail.com vagy outlook.com) személyes fiókkal történő tesztelés során azt tapasztalhatja, hogy a hozzáférési jogkivonatot az ügyfél által fogadott egy olyan átlátszatlan karakterlánc. Ennek az oka az elért erőforrás kért örökölt MSA (Microsoft-fiók) jegyek vannak titkosítva, és az ügyfél nem tudja értelmezni.

## <a name="sample-tokens"></a>Minta jogkivonatok

1.0-s és 2.0-s verziójú jogkivonatok hasonlóan néz ki, és számos, ugyanazon jogcímeket tartalmaznak. Az egyes például itt.

### <a name="v10"></a>1.0-s verzió

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiJlZjFkYTlkNC1mZjc3LTRjM2UtYTAwNS04NDBjM2Y4MzA3NDUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTUyMjIyOS8iLCJpYXQiOjE1MzcyMzMxMDYsIm5iZiI6MTUzNzIzMzEwNiwiZXhwIjoxNTM3MjM3MDA2LCJhY3IiOiIxIiwiYWlvIjoiQVhRQWkvOElBQUFBRm0rRS9RVEcrZ0ZuVnhMaldkdzhLKzYxQUdyU091TU1GNmViYU1qN1hPM0libUQzZkdtck95RCtOdlp5R24yVmFUL2tES1h3NE1JaHJnR1ZxNkJuOHdMWG9UMUxrSVorRnpRVmtKUFBMUU9WNEtjWHFTbENWUERTL0RpQ0RnRTIyMlRJbU12V05hRU1hVU9Uc0lHdlRRPT0iLCJhbXIiOlsid2lhIl0sImFwcGlkIjoiNzVkYmU3N2YtMTBhMy00ZTU5LTg1ZmQtOGMxMjc1NDRmMTdjIiwiYXBwaWRhY3IiOiIwIiwiZW1haWwiOiJBYmVMaUBtaWNyb3NvZnQuY29tIiwiZmFtaWx5X25hbWUiOiJMaW5jb2xuIiwiZ2l2ZW5fbmFtZSI6IkFiZSAoTVNGVCkiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMjIyNDcvIiwiaXBhZGRyIjoiMjIyLjIyMi4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJvaWQiOiIwMjIyM2I2Yi1hYTFkLTQyZDQtOWVjMC0xYjJiYjkxOTQ0MzgiLCJyaCI6IkkiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJsM19yb0lTUVUyMjJiVUxTOXlpMmswWHBxcE9pTXo1SDNaQUNvMUdlWEEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6ImFiZWxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJGVnNHeFlYSTMwLVR1aWt1dVVvRkFBIiwidmVyIjoiMS4wIn0=.D3H6pMUtQnoJAGq6AHd
```

Megtekintheti az 1.0-s verziójú jogkivonatot [JWT.ms](https://jwt.ms/#access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiJlZjFkYTlkNC1mZjc3LTRjM2UtYTAwNS04NDBjM2Y4MzA3NDUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTUyMjIyOS8iLCJpYXQiOjE1MzcyMzMxMDYsIm5iZiI6MTUzNzIzMzEwNiwiZXhwIjoxNTM3MjM3MDA2LCJhY3IiOiIxIiwiYWlvIjoiQVhRQWkvOElBQUFBRm0rRS9RVEcrZ0ZuVnhMaldkdzhLKzYxQUdyU091TU1GNmViYU1qN1hPM0libUQzZkdtck95RCtOdlp5R24yVmFUL2tES1h3NE1JaHJnR1ZxNkJuOHdMWG9UMUxrSVorRnpRVmtKUFBMUU9WNEtjWHFTbENWUERTL0RpQ0RnRTIyMlRJbU12V05hRU1hVU9Uc0lHdlRRPT0iLCJhbXIiOlsid2lhIl0sImFwcGlkIjoiNzVkYmU3N2YtMTBhMy00ZTU5LTg1ZmQtOGMxMjc1NDRmMTdjIiwiYXBwaWRhY3IiOiIwIiwiZW1haWwiOiJBYmVMaUBtaWNyb3NvZnQuY29tIiwiZmFtaWx5X25hbWUiOiJMaW5jb2xuIiwiZ2l2ZW5fbmFtZSI6IkFiZSAoTVNGVCkiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMjIyNDcvIiwiaXBhZGRyIjoiMjIyLjIyMi4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJvaWQiOiIwMjIyM2I2Yi1hYTFkLTQyZDQtOWVjMC0xYjJiYjkxOTQ0MzgiLCJyaCI6IkkiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJsM19yb0lTUVUyMjJiVUxTOXlpMmswWHBxcE9pTXo1SDNaQUNvMUdlWEEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6ImFiZWxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJGVnNHeFlYSTMwLVR1aWt1dVVvRkFBIiwidmVyIjoiMS4wIn0=.D3H6pMUtQnoJAGq6AHd).

### <a name="v20"></a>2.0-s verzió

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiI2ZTc0MTcyYi1iZTU2LTQ4NDMtOWZmNC1lNjZhMzliYjEyZTMiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE1MzcyMzEwNDgsIm5iZiI6MTUzNzIzMTA0OCwiZXhwIjoxNTM3MjM0OTQ4LCJhaW8iOiJBWFFBaS84SUFBQUF0QWFaTG8zQ2hNaWY2S09udHRSQjdlQnE0L0RjY1F6amNKR3hQWXkvQzNqRGFOR3hYZDZ3TklJVkdSZ2hOUm53SjFsT2NBbk5aY2p2a295ckZ4Q3R0djMzMTQwUmlvT0ZKNGJDQ0dWdW9DYWcxdU9UVDIyMjIyZ0h3TFBZUS91Zjc5UVgrMEtJaWpkcm1wNjlSY3R6bVE9PSIsImF6cCI6IjZlNzQxNzJiLWJlNTYtNDg0My05ZmY0LWU2NmEzOWJiMTJlMyIsImF6cGFjciI6IjAiLCJuYW1lIjoiQWJlIExpbmNvbG4iLCJvaWQiOiI2OTAyMjJiZS1mZjFhLTRkNTYtYWJkMS03ZTRmN2QzOGU0NzQiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJhYmVsaUBtaWNyb3NvZnQuY29tIiwicmgiOiJJIiwic2NwIjoiYWNjZXNzX2FzX3VzZXIiLCJzdWIiOiJIS1pwZmFIeVdhZGVPb3VZbGl0anJJLUtmZlRtMjIyWDVyclYzeERxZktRIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoiZnFpQnFYTFBqMGVRYTgyUy1JWUZBQSIsInZlciI6IjIuMCJ9.pj4N-w_3Us9DrBLfpCt
```

Megtekintheti a v2.0 jogkivonat a [JWT.ms](https://jwt.ms/#access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiI2ZTc0MTcyYi1iZTU2LTQ4NDMtOWZmNC1lNjZhMzliYjEyZTMiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE1MzcyMzEwNDgsIm5iZiI6MTUzNzIzMTA0OCwiZXhwIjoxNTM3MjM0OTQ4LCJhaW8iOiJBWFFBaS84SUFBQUF0QWFaTG8zQ2hNaWY2S09udHRSQjdlQnE0L0RjY1F6amNKR3hQWXkvQzNqRGFOR3hYZDZ3TklJVkdSZ2hOUm53SjFsT2NBbk5aY2p2a295ckZ4Q3R0djMzMTQwUmlvT0ZKNGJDQ0dWdW9DYWcxdU9UVDIyMjIyZ0h3TFBZUS91Zjc5UVgrMEtJaWpkcm1wNjlSY3R6bVE9PSIsImF6cCI6IjZlNzQxNzJiLWJlNTYtNDg0My05ZmY0LWU2NmEzOWJiMTJlMyIsImF6cGFjciI6IjAiLCJuYW1lIjoiQWJlIExpbmNvbG4iLCJvaWQiOiI2OTAyMjJiZS1mZjFhLTRkNTYtYWJkMS03ZTRmN2QzOGU0NzQiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJhYmVsaUBtaWNyb3NvZnQuY29tIiwicmgiOiJJIiwic2NwIjoiYWNjZXNzX2FzX3VzZXIiLCJzdWIiOiJIS1pwZmFIeVdhZGVPb3VZbGl0anJJLUtmZlRtMjIyWDVyclYzeERxZktRIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoiZnFpQnFYTFBqMGVRYTgyUy1JWUZBQSIsInZlciI6IjIuMCJ9.pj4N-w_3Us9DrBLfpCt).

## <a name="claims-in-access-tokens"></a>A hozzáférési jogkivonatokban jogcímek

JWTs művelethez három adatra van felosztva: 

* **Fejléc** -ismerteti, hogyan [a jogkivonat érvényesítéséhez](#validating-tokens) többek között a jogkivonatot, és hogyan aláírás típusát információkat.
* **Hasznos adat** – tartalmazza az összes fontos adatait, a felhasználó vagy az alkalmazást, amely megpróbálja meghívni a szolgáltatást.
* **Aláírás** – a jogkivonat érvényesítéséhez használt nyersanyag.

Minden időszak választja el egymástól (`.`), és külön-külön Base64-kódolású.

Jogcím jelen, csak akkor, ha egy érték létezik, és töltse fel. Így az alkalmazás kell lépnek a jogcím megtalálható-e egy függőséget. Ilyenek például `pwd_exp` (nem minden bérlő szükséges jelszó lejár) vagy `family_name` ([ügyfél-hitelesítő adatok](v1-oauth2-client-creds-grant-flow.md) folyamatok vannak nevében alkalmazások, amelyek nevek nem rendelkezik). Hozzáférési jogkivonat érvényesítésére használt jogcímek mindig lesz található.

> [!NOTE]
> Egyes jogcímek biztonságos jogkivonatok újbóli esetén az Azure AD segítségével. Ezek, mivel nem nyilvános felhasználásra a leírásban másként "Átlátszatlan" lesznek megjelölve. Előfordulhat, hogy ezeket a jogcímeket, vagy a jogkivonat nem szerepelhet, és újak vehetők fel külön értesítés nélkül.

### <a name="header-claims"></a>Fejléc jogcímek

|Jogcím | Formátum | Leírás |
|--------|--------|-------------|
| `typ` | Karakterlánc - mindig "JWT" | Azt jelzi, hogy a jogkivonat jwt-t.|
| `nonce` | Karakterlánc | Ismétlésének támadások elleni védelmére szolgáló egyedi azonosítója. Az erőforrást rögzítheti replays elleni védelem érdekében ezt az értéket. |
| `alg` | Karakterlánc | Azt jelzi, hogy a jogkivonat, például a "RS256" aláírásához használt algoritmust |
| `kid` | Karakterlánc | Adja meg a nyilvános kulcsot, amely a token aláírásához használt ujjlenyomatát. Hozzáférési jogkivonatok az 1.0-s verziója és a v2.0 rendelkezésre. |
| `x5t` | Karakterlánc | Működik (a használati és érték) ugyanaz, mint `kid`. `x5t` örökölt jogcím bocsásson ki csak az 1.0-s verziójú hozzáférési jogkivonatok kompatibilitási célból. |

### <a name="payload-claims"></a>Hasznos adat jogcímek

| Jogcím | Formátum | Leírás |
|-----|--------|-------------|
| `aud` | Az Alkalmazásazonosító URI-karakterláncot | A jogkivonat az illetékes címzett azonosítja. A hozzáférési jogkivonatokban a célközönségét az alkalmazás Alkalmazásazonosító, az Azure Portalon az alkalmazáshoz rendelt. Az alkalmazás kell érvényesíteni ezt az értéket, és elutasítja a tokent, ha az érték nem egyezik. |
| `iss` | Az STS-URI-karakterláncot | Azonosítja a biztonsági jogkivonat-szolgáltatás (STS) hoz létre, és a jogkivonatot, és az Azure AD-bérlővel, amelyben a felhasználó hitelesítési adja vissza. Ha a kiállított biztonsági jogkivonat v2.0 jogkivonat (lásd a `ver` jogcím), belül véget ér. az URI-t `/v2.0`. A GUID, amely azt jelzi, hogy a felhasználó Microsoft-fiók fogyasztói felhasználója `9188040d-6c67-4c5b-b112-36a304b66dad`. Az alkalmazás, amely bejelentkezhet az alkalmazásba, ha van ilyen bérlők korlátozni a jogcím GUID részének kell használni. |
|`idp`|Karakterlánc, általában egy STS URI | A jogkivonat alanyát hitelesítő identitásszolgáltatót adja meg. Ez az érték megegyezik a kiállító jogcím értékét, kivéve, ha a felhasználói fiók nem ugyanahhoz a bérlőhöz, a kibocsátó - Vendégek, például. Ha a jogcím nem található, azt jelenti, hogy értékét `iss` is lehet használni.  Személyes fiókok orgnizational környezetben (például egy személyes fiók meghívjuk az Azure AD-bérlő), használja a `idp` jogcím lehet "live.com" és a egy STS URI, amely tartalmazza a Microsoft-fiók bérlő `9188040d-6c67-4c5b-b112-36a304b66dad`. |  
| `iat` | int, a UNIX-időbélyege | "Feltüntetett tulajdonos:" azt jelenti, amikor a hitelesítés a jogkivonat történt. |
| `nbf` | int, a UNIX-időbélyege | Az "nbf" (előtte) jogcím azonosítja az idő előtt, amely a JWT kell nem lesznek elfogadva feldolgozásra. |
| `exp` | int, a UNIX-időbélyege | Az "exp" (lejárati ideje) jogcím azonosítja a lejárati időt, vagy azt követően, amely a JWT kell nem lesznek elfogadva feldolgozásra. Fontos megjegyezni, hogy egy erőforrás utasíthatja a jogkivonatot, megadott idő előtti amikor hitelesítés módosítása nem szükséges, vagy értéket észlelt a tokenek visszavonása. |
| `acr` | Karakterlánc, a "0" vagy "1" | A "Hitelesítés adatbáziskörnyezet osztályának" jogcímet. "0" érték azt jelzi, hogy a végfelhasználói hitelesítés nem felelt meg az ISO/IEC 29115 követelményeinek. |
| `aio` | Átlátszatlan karakterlánc | Egy jogkivonat újrafelhasználásra erőforrásrekord-adatokat az Azure AD által használt belső jogcímet. Erőforrások ne használja ezt a kérelmet. |
| `amr` | JSON-karakterláncok tömbje | Jelenleg csak az 1.0-s verziójú jogkivonatok. Azonosítja a jogkivonat tárgyában hitelesítésének módját. Lásd: [a amr jogcím szakasz](#the-amr-claim) további részletekért. |
| `appid` | Karakterlánc, egy GUID Azonosítót | Jelenleg csak az 1.0-s verziójú jogkivonatok. Az Alkalmazásazonosítót az ügyfél-token használatával. Az alkalmazás saját maga, vagy egy felhasználó nevében működhet. Az alkalmazás azonosítója általában egy alkalmazás az objektumot határozza meg, de a szolgáltatásnév-objektumot az Azure ad-ben is jelenthet. |
| `appidacr` | "0", "1" vagy "2" | Jelenleg csak az 1.0-s verziójú jogkivonatok. Azt jelzi, hogy az ügyfél hitelesítése. Nyilvános ügyfél az érték "0". Ügyfél-Azonosítóját és ügyfélkulcsát használnak, ha értéke "1". Ha ügyféltanúsítvány a hitelesítéshez használt, az érték "2". |
| `azp` | Karakterlánc, egy GUID Azonosítót | 2.0-s verziójú jogkivonatok csak szerepelnek. Az Alkalmazásazonosítót az ügyfél-token használatával. Az alkalmazás saját maga, vagy egy felhasználó nevében működhet. Az alkalmazás azonosítója általában egy alkalmazás az objektumot határozza meg, de a szolgáltatásnév-objektumot az Azure ad-ben is jelenthet. |
| `azpacr` | "0", "1" vagy "2" | 2.0-s verziójú jogkivonatok csak szerepelnek. Azt jelzi, hogy az ügyfél hitelesítése. Nyilvános ügyfél az érték "0". Ügyfél-Azonosítóját és ügyfélkulcsát használnak, ha értéke "1". Ha ügyféltanúsítvány a hitelesítéshez használt, az érték "2". |
| `groups` | JSON-tömböt a GUID azonosítók | A tulajdonos csoporttagságok képviselő objektumazonosítók biztosít. Ezek az értékek egyedi (lásd az Objektumazonosító) és biztonságosan használható például az erőforrás hozzáférési kényszerítése a hozzáférés kezelésében. A csoportok jogcím szerepel a csoportok konfigurálhatóak alkalmazásonkénti particionálja a `groupMembershipClaims` tulajdonságát a [alkalmazásjegyzék](reference-app-manifest.md). NULL értékű kizárja az összes csoport, csak az Active Directory biztonsági csoport tagságát, és a egy értéket "All" lesz tartalmazzák a biztonsági csoportok és az Office 365 terjesztési listák "SecurityGroup" értéket tartalmazza. <br><br>Tekintse meg a `hasgroups` használata részletesen alábbi jogcím a `groups` az implicit engedélyezés jogcím. <br>Más folyamatok Ha a felhasználó szerepel csoportok számának megfelelően egy határértéket (150 SAML, 200 a JWT), majd kerettúllépési jogcím hozzáadódik a jogcím-adatforrások mutat, amely tartalmazza azokat a csoportokat a felhasználó Graph-végpont. |
| `hasgroups` | Logikai | Ha van ilyen, mindig `true`, jelzi, hogy a felhasználó szerepel-e legalább egy csoporthoz. Helyén használt a `groups` JWTs jogcím típusú implicit engedélyezés folyamatokban, ha a teljes csoportok jogcím lenne kiterjesztése a URI töredék kívül eső URL-cím hossza (jelenleg a 6 vagy a további csoportokat). Azt jelzi, hogy az ügyfélnek használnia kell a grafikon, a felhasználói csoportok meghatározására (`https://graph.windows.net/{tenantID}/users/{userID}/getMemberObjects`). |
| `groups:src1` | JSON-objektum | A jogkivonat-kérelmeket, amelyek nem korlátozott hossza (lásd: `hasgroups` fent), de továbbra is túl nagy a jogkivonatot, a felhasználó a teljes csoportok listáját mutató hivatkozást tartalmaz. Az elosztott jogcímként, a SAML helyén új jogcímként JWTs a `groups` jogcím. <br><br>**Példaérték JWT**: <br> `"groups":"src1"` <br> `"_claim_sources`: `"src1" : { "endpoint" : "https://graph.windows.net/{tenantID}/users/{userID}/getMemberObjects" }`|
| `preferred_name` | Karakterlánc | 2.0-s verziójú jogkivonatok csak szerepelnek. Az elsődleges felhasználónév, amely a felhasználó jelöli. Ez lehet egy e-mail-címet, telefonszámot vagy egy általános felhasználónév nélkül egy megadott formátumban. Az érték mutable és idővel változhatnak. Mivel ezekre kapott válaszokon, ez az érték nem használható az engedélyezési döntésekhez. A `profile` hatókör szükséges, ezt a kérelmet kap. |
| `name` | Karakterlánc | Itt az emberek számára olvasható érték, amely azonosítja a jogkivonat tárgyában. Az érték nem garantált, hogy egyedi legyen, ezekre kapott válaszokon, és úgy tervezték, hogy csak megjelenítési célokra használható. A `profile` hatókör szükséges, ezt a kérelmet kap. |
| `oid` | Karakterlánc, egy GUID Azonosítót | A Microsoft identity platform, ebben az esetben egy felhasználói fiókot az objektum nem módosítható azonosítója. Is használható hitelesítési ellenőrzések elvégzéséhez biztonságosan és a egy kulcsként adatbázistáblák. Alkalmazások közötti egyedileg azonosítja a felhasználó ezt az Azonosítót – ugyanaz a felhasználó bejelentkezik, két különböző alkalmazás ugyanazt az értéket a fog kapni a `oid` jogcím. Ebből kifolyólag `oid` tétele a Microsoft online szolgáltatásaihoz, például a Microsoft Graph-lekérdezések használható. A Microsoft Graph ezt az Azonosítót ad vissza a `id` tulajdonság egy adott felhasználói fiókhoz. Mivel a `oid` korrelációját, ha a felhasználók, több alkalmazás lehetővé teszi a `profile` hatókör szükséges, ezt a kérelmet kap. Vegye figyelembe, hogy ha egy felhasználó több bérlő már létezik, a felhasználó fogja tartalmazni az egyes bérlők egy másik objektum azonosítója – a különböző fiókok, annak ellenére, hogy a felhasználó bejelentkezik az egyes ugyanazokkal a hitelesítő adatokkal rendelkező fiók minősülnek. |
| `rh` | Átlátszatlan karakterlánc | Egy belső jogcím kísérelje meg újra érvényesítését jogkivonatok Azure segítségével. Erőforrások ne használja ezt a kérelmet. |
| `scp` | Karakterlánc, szóközzel elválasztott hatókörök listája | Az alkalmazás, amelynek az ügyfélalkalmazás rendelkezik a kért (és a kapott) által közzétett hatókörök készletét hozzájárulás megadása. Az alkalmazás ellenőrizze, hogy azok érvényes, az alkalmazás által elérhetővé tett ezeken a hatókörökön, és ezeken a hatókörökön értéke alapján engedélyezési döntésekhez. Csak tartalmaz [felhasználói jogkivonatok](#user-and-application-tokens). |
| `roles`| Karakterlánc, szóközzel elválasztva engedélyek listája | Az alkalmazás, amely a kérelmező alkalmazással meghívásához engedélyt kaptak által elérhetővé tett engedélyek készletét. Ennek során használatos a [ügyfél-hitelesítő-adatok](v1-oauth2-client-creds-grant-flow.md) flow felhasználói hatókörök helyett, és csak a jelen [alkalmazások jogkivonatok](#user-and-application-tokens). |
| `sub` | Karakterlánc, egy GUID Azonosítót | Az egyszerű arról, hogy mely a token használjon esetleg imperatív állításokat információkat, például a felhasználó az alkalmazás. Ez az érték nem módosítható és nem hozzárendelni és újra felhasználható. Ez az engedélyezési ellenőrzéséhez biztonságosan, például amikor a jogkivonat-erőforrás eléréséhez használható, és adatbázistáblák kulcsként használhatók. Mivel a tulajdonos mindig szerepel a jogkivonatokat, hogy az Azure AD-problémák, azt javasoljuk, ez az érték egy általános célú engedélyezési rendszerben. Az a tulajdonos, azonban a páros azonosító – egyedi legyen egy adott alkalmazás azonosítója. Ezért ha egy felhasználó bejelentkezik a két különböző ügyfél-azonosító használatával két különböző alkalmazásokat, ezeket az alkalmazásokat két különböző értékeket, a tulajdonos jogcím fog kapni. Ez előfordulhat, hogy, vagy előfordulhat, hogy nem megfelelő az architektúra és adatvédelmi követelményeitől függően. |
| `tid` | Karakterlánc, egy GUID Azonosítót | Az Azure AD-bérlő arról, hogy a felhasználó jelöli. Munkahelyi és iskolai fiókok esetében a GUID azonosító a szervezet, amely a felhasználó tartozik Bérlőazonosítója nem módosítható. Személyes fiókok esetében az érték `9188040d-6c67-4c5b-b112-36a304b66dad`. A `profile` hatókör szükséges, ezt a kérelmet kap. |
| `unique_name` | Karakterlánc | Jelenleg csak az 1.0-s verziójú jogkivonatok. A jogkivonat alanyát azonosító, ember által olvasható értéket ad meg. Ez az érték nem garantált egy bérlőn belül egyedinek kell lennie, és csak megjelenítési célokra használható. |
| `upn` | Karakterlánc | A felhasználó felhasználóneve. Egy telefonszám, e-mail címét vagy formázatlan karakterlánc lehet. Csak használandó megjelenítési célokra és újbóli hitelesítés forgatókönyveket biztosító felhasználónév mutatók. |
| `uti` | Átlátszatlan karakterlánc | Egy belső jogcím kísérelje meg újra érvényesítését jogkivonatok Azure segítségével. Erőforrások ne használja ezt a kérelmet. |
| `ver` | Karakterlánc, 1.0 vagy 2.0-s | Azt jelzi, hogy a verzió a hozzáférési jogkivonat. |

#### <a name="v10-basic-claims"></a>Alapszintű jogcímek 1.0-s verzió

A következő jogcímek 1.0-s verziójú jogkivonatok, ha van ilyen fog szerepelni, de alapértelmezés szerint a 2.0-s verziójú jogkivonatok nem szerepelnek. 2.0-s verziójú, és meg kell ezeket a jogcímeket, használatakor tanúsítványkérelmeket használatával [választható jogcímek](active-directory-optional-claims.md).

| Jogcím | Formátum | Leírás |
|-----|--------|-------------|
| `ipaddr`| Karakterlánc | Az IP-cím a felhasználó hitelesítést hajtottak végre. |
| `onprem_sid`| A karakterlánc [biztonsági azonosító formátuma](https://docs.microsoft.com/windows/desktop/SecAuthZ/sid-components) | Azokban az esetekben, ahol a felhasználó rendelkezik-e egy a helyszíni hitelesítéshez ezt az igényt a SID biztosít. Használhat `onprem_sid` engedélyezési az örökölt alkalmazások számára. |
| `pwd_exp`| int, a UNIX-időbélyege | Azt jelzi, ha a felhasználó jelszava lejár. |
| `pwd_url`| Karakterlánc | Egy URL-címet, ahol a felhasználók visszaállíthatják a jelszavukat küldhető. |
| `in_corp`|logikai | Ha az ügyfél bejelentkezik a vállalati hálózatról jelek. Ha nem, a a jogcím lehetőség nem része. |
| `nickname`| Karakterlánc | A felhasználó, az első vagy utolsó külön további neve.|
| `family_name` | Karakterlánc | Az utolsó neve, Vezetéknév vagy felhasználó családnév biztosít a user objektum meghatározottak szerint. |
| `given_name` | Karakterlánc | Az első biztosít vagy a user objektum készletként, a felhasználó utóneve. |

#### <a name="the-amr-claim"></a>A `amr` jogcím

A Microsoft identitások hitelesíthetők többféle módon, ami fontos lehet az alkalmazás. A `amr` jogcím egy tömb, amely több elemet is tartalmazhat, mint például `["mfa", "rsa", "pwd"]`, egy jelszó és a hitelesítő alkalmazás használt hitelesítéshez. 

| Érték | Leírás |
|-----|-------------|
| `pwd` | Jelszavas hitelesítést, vagy a felhasználó Microsoft-jelszavát, vagy egy alkalmazás titkos. |
| `rsa` | Hitelesítés alapján történt a koncepció igazolása RSA-kulcs, például az a [Microsoft Authenticator alkalmazás](https://aka.ms/AA2kvvu). Ez magában foglalja, ha a hitelesítést egy birtokolt X509 szolgáltatást egy önaláírt JWT hajtotta végre tanúsítványt. |
| `otp` | Használatával az e-mailben vagy szöveges üzenetben egyszer használatos jelszót. |
| `fed` | Egy összevont hitelesítés helyességi feltétel (például a jwt-t vagy SAML) lett megadva. |
| `wia` | Integrált Windows-hitelesítés |
| `mfa` | A multi-factor authentication lett megadva. Ha ez megtalálható a további hitelesítési módszerek is bekerülnek. |
| `ngcmfa` | Egyenértékű `mfa`bizonyos speciális hitelesítő adat típusú kialakítási szolgál. |
| `wiaormfa`| A felhasználó Windows- vagy egy MFA hitelesítőadat hitelesítésére használja. |
| `none` | Nincs hitelesítés hajtottak végre. |

## <a name="validating-tokens"></a>Token ellenőrzése

Id_token és a egy access_token ellenőrzéséhez az alkalmazás ellenőrizni kell a jogkivonat-aláírási és a jogcímeket. Annak érdekében, hogy hozzáférési jogkivonatainak érvényesítéséhez, az alkalmazás is ellenőrizni kell a kibocsátó, a célközönség és az aláíró jogkivonatokat. Ezek kell érvényesíteni az OpenID felderítési dokumentum értékekkel. Például a bérlő független a dokumentum verziója található: [ https://login.microsoftonline.com/common/.well-known/openid-configuration ](https://login.microsoftonline.com/common/.well-known/openid-configuration). 

Az Azure ad-ben közbenső szoftvert a hozzáférési jogkivonatok érvényességének beépített funkciókkal rendelkezik, és akkor is böngészhet a [minták](https://docs.microsoft.com/azure/active-directory/active-directory-code-samples) található egy Ön által választott nyelven. Az explicit módon ellenőrzése a JWT jogkivonat további információkért lásd: a [JWT-ellenőrzés manuális minta](https://github.com/Azure-Samples/active-directory-dotnet-webapi-manual-jwt-validation). 

Kínálunk kódtárakat és kódmintákkal, amelyek bemutatják, hogyan könnyedén megfelelhet a jogkivonat érvényesítésére. Az alábbi információkat biztosítunk azoknak, akik szeretné tudni, hogy az alapul szolgáló folyamat. Érhetők el is számos külső nyílt forráskódú függvénytárak JWT-ellenőrzés – legalább egy lehetőség a szinte minden platformon és nyelven is elérhető. Az Azure AD hitelesítési kódtárai és Kódminták kapcsolatos további információkért lásd: [1.0-s verziójú hitelesítési tárak](active-directory-authentication-libraries.md) és [2.0-s verziójú hitelesítési tárak](reference-v2-libraries.md).

### <a name="validating-the-signature"></a>Az aláírás ellenőrzése

Jwt-t tartalmaz három szegmensek, amelyeket a `.` karakter. Az első szegmens más néven a **fejléc**, mint a második a **törzs**, és a harmadik, mint a **aláírás**. Az aláírás szegmens a token hitelességének ellenőrzéséhez, hogy az alkalmazás által is megbízhatónak használható.

Az Azure AD által kiállított jogkivonatokban van bejelentkezve az iparági szabványos aszimmetrikus titkosítási algoritmusok, például RSA-256 használatával. A JWT fejlécében a token aláírásához használt kulcs és a titkosítási módra vonatkozó információkat tartalmazza:

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "x5t": "iBjL1Rcqzhiy4fpxIxdZqohM2Yk",
  "kid": "iBjL1Rcqzhiy4fpxIxdZqohM2Yk"
}
```

A `alg` jogcím azt jelzi, hogy a jogkivonat, ideje aláírásához használt algoritmust az `kid` jogcím azt jelzi, hogy a jogkivonat aláírásához használt adott nyilvános kulcs.

Lekérdezhet az idő az Azure AD egy meghatározott készlete nyilvános-titkos kulcspárok használatával id_token előfordulhat, hogy alá. Az Azure AD elforgatása a kulcsok rendszeres időközönként, lehetséges készletét, így az alkalmazás automatikusan kezeli a fő módosításokat kell írni. Az Azure AD által használt nyilvános kulcsok frissítései ésszerű gyakoriságot van 24 óránként.

Az aláíró kulcs adatokat kell érvényesíteni az aláírást, az OpenID Connect metaadat-dokumentumban található használatával szerezheti be:

```
https://login.microsoftonline.com/common/.well-known/openid-configuration
```

> [!TIP]
> Próbálja ki a [URL-cím](https://login.microsoftonline.com/common/.well-known/openid-configuration) böngészőben!

A metaadat-dokumentum:

* Van egy JSON-objektumot tartalmazó számos hasznos információt tartalmaz, például az OpenID Connect hitelesítést végrehajtásához szükséges különböző végpontok helyét. 
* Tartalmaz egy `jwks_uri`, helyét, a jogkivonatok aláírásához használt nyilvános kulcsok készlete révén. A JSON-dokumentum helyén található a `jwks_uri` tartalmazza az összes nyilvános kulcsadatokat idő adott pillanatban használja. Az alkalmazás használhatja a `kid` jogcím a JWT fejlécben, válassza ki, melyik nyilvános kulcs ebben a dokumentumban használt egy adott jogkivonat aláírása. Majd műveleteket hajthat végre a megfelelő nyilvános kulcsot és a jelzett algoritmus használatával aláírás-ellenőrzése.

> [!NOTE]
> Az 1.0-s verziójú végpont adja vissza, mind a `x5t` és `kid` állítja, miközben a v2.0-végpont csak válaszol az a `kid` jogcím. Továbbítja, javasoljuk, hogy használja a `kid` jogcím a jogkivonat érvényesítéséhez.

Aláírás-ellenőrzés végrehajtása terjed ki a dokumentum - érhetők el számos nyílt forráskódú kódtár segít, ha szükséges.

### <a name="claims-based-authorization"></a>Jogcímalapú engedélyezési

Az alkalmazás üzleti logikája szabja meg ezt a lépést, néhány gyakori hitelesítési módszerek alatt vannak leírva.

* Ellenőrizze a `scp` vagy `roles` ellenőrizze, hogy minden jelenlegi hatókör azokat az API által elérhetővé tett egyeznek jogcímet, és lehetővé teszi az ügyfél a kért művelet végrehajtásához.
* Győződjön meg arról, hívja az API használatával a hívó ügyfél számára engedélyezett a `appid` jogcím.
* A meghívó ügyfél használatával hitelesítés állapotának ellenőrzésére `appidacr` – nem lehet 0 Ha nyilvános ügyfelek nem jogosultak az API-t.
* Ellenőrizze az listájában múltbeli `nonce` ellenőrzése a jogkivonat nem folyamatban visszajátszani jogcímeket.
* Ellenőrizze, hogy a `tid` megegyezik a bérlő egy van engedélyezve, az API meghívásához.
* Használja a `acr` ellenőrizze, hogy a felhasználó végrehajtotta az MFA jogcímet. Ez lehet kényszerítése az [feltételes hozzáférési](https://docs.microsoft.com/azure/active-directory/conditional-access/overview).
* Ha a kért a `roles` vagy `groups` a hozzáférési jogkivonat jogcímeiben győződjön meg arról, hogy a felhasználó szerepel-e a csoport hajthatja végre ezt a műveletet.
  * A jogkivonatokat az implicit folyamatot használja, valószínűleg kell lekérdezni a [Microsoft Graph](https://developer.microsoft.com/graph/) ezen adatok esetében, mert gyakran túl nagy ahhoz, hogy illeszkedjen a jogkivonatban. 

## <a name="user-and-application-tokens"></a>Felhasználói és tokenek

Az alkalmazás jelenhet meg (a szokásos folyamat) felhasználó nevében, vagy közvetlenül az alkalmazásból való jogkivonatok (keresztül a [ügyfél hitelesítő adatai a flow](v1-oauth2-client-creds-grant-flow.md)). Ezek csak alkalmazás token jelzi, hogy a hívás érkezik egy alkalmazást, és nem rendelkezik egy felhasználót, hogy biztonsági másolatot készít. Ezek a jogkivonatok ugyanúgy kezeli a rendszer nagymértékben, néhány eltéréssel:

* Csak az alkalmazásra vonatkozó jogkivonatok nem kap egy `scp` jogcím, és ehelyett lesz egy `roles` jogcím. Ez az alkalmazás engedélye (ellentétben a delegált engedélyek) hol lesznek rögzítve. Delegált és az engedélyekkel kapcsolatos további információkért tekintse meg az engedélyt, és beleegyezik [1.0-s verziójú](v1-permissions-and-consent.md) és [v2.0](v2-permissions-and-consent.md).
* Sok ember-specifikus jogcímek nem fog megjelenni, mint például `name`.

## <a name="token-revocation"></a>A tokenek visszavonása

Frissítési jogkivonatok érvénytelenítve, vagy bármikor, számos okból visszavonva. Ezek két fő kategóriába sorolhatók: időtúllépések és által okozott hibával találkozzanak.

### <a name="token-timeouts"></a>Token időtúllépések

* MaxInactiveTime: Ha a frissítési jogkivonat nem használták a MaxInactiveTime által időn belül, a frissítési jogkivonat már nem érvényes lesz. 
* MaxSessionAge: Ha valami nem az alapértelmezett (csak visszavont) MaxAgeSessionMultiFactor vagy MaxAgeSessionSingleFactor állított be, majd újbóli hitelesítés lesz szükség a beállítása a MaxAgeSession * az idő elteltével. 
* Példák:
  * A bérlő rendelkezik egy 5 napos MaxInactiveTime szabadságon heti hiba történt a felhasználó, és így AAD rendelkezik nem látható a felhasználó új jogkivonat kérést 7 nap. A következő alkalommal, amikor a felhasználó egy új jogkivonatot kér fognak találni a frissítési Token vissza lett vonva, és újra azokat kell adnia a hitelesítő adatait.
  * A bizalmas alkalmazásokhoz rendelkezik egy MaxAgeSessionSingleFactor 1 nap. Ha egy felhasználó bejelentkezik, hétfőn, és a "frissítő kedd" (miután 25 óra telt el), akkor kell hitelesítse magát újra.

### <a name="revocation"></a>Visszavont tanúsítványok

|   | Jelszó-alapú cookie-k | Jelszavas tokent | Nem-jelszó-alapú cookie-k | Nem – jelszavas tokent | Bizalmas ügyfél jogkivonat| 
|---|-----------------------|----------------------|---------------------------|--------------------------|--------------------------|
| Jelszó lejárata | Aktív marad| Aktív marad | Aktív marad | Aktív marad | Aktív marad |
| Felhasználó jelszavát | Visszavonva | Visszavonva | Aktív marad | Aktív marad | Aktív marad |
| Felhasználó végrehajtja az SSPR | Visszavonva | Visszavonva | Aktív marad | Aktív marad | Aktív marad |
| Rendszergazdai jelszó alaphelyzetbe állítása | Visszavonva | Visszavonva | Aktív marad | Aktív marad | Aktív marad |
| Felhasználó visszavonja a frissítési biztonsági jogkivonat [PowerShell-lel](https://docs.microsoft.com/powershell/module/azuread/revoke-azureadsignedinuserallrefreshtoken) | Visszavonva | Visszavonva |Visszavonva | Visszavonva |Visszavonva | Visszavonva |
| Rendszergazdai a bérlőhöz tartozó összes frissítési biztonsági jogkivonat visszahívja azokat [PowerShell-lel](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken) | Visszavonva | Visszavonva |Visszavonva | Visszavonva |Visszavonva | Visszavonva |
| [Egyszeri kijelentkezéshez](v1-protocols-openid-connect-code.md#single-sign-out) a weben | Visszavonva | Aktív marad | Visszavonva | Aktív marad | Aktív marad |

> [!NOTE]
> Egy "jelszó nem-alapú" Bejelentkezés az egyik, a felhasználó nem írja be a jelszót, hogy álljon a. A face használata esetén például a Windows Hello, egy FIDO-kulcs vagy egy PIN-kódot. 
>
> A Windows elsődleges frissítési jogkivonat létezik egy ismert probléma. Ha lekérte a PRT keresztül egy jelszót, és ezután a felhasználó bejelentkezik Hello használatával, azzal nem módosítja az eredeti, a PRT, és azt a rendszer visszavonja, ha a felhasználó megváltoztatja a jelszavát.

## <a name="next-steps"></a>További lépések

* Ismerje meg [ `id_tokens` az Azure ad-ben](id-tokens.md).
* Megismerheti a engedély és beleegyezik [1.0-s verziójú](v1-permissions-and-consent.md) és [v2.0](v2-permissions-and-consent.md).
