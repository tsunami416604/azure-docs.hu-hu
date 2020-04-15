---
title: Microsoft identity platform hozzáférési jogkivonatok hivatkozása | Azure
description: Az Azure AD 1.0-s és a Microsoft Identity platform (2.0-s verzió) végpontjai által kibocsátott hozzáférési jogkivonatok.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 3/27/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40, fasttrack-edit
ms.openlocfilehash: ed583abc8f60f3d367bf75254807e3f28cd0f1c9
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309715"
---
# <a name="microsoft-identity-platform-access-tokens"></a>Microsoft-identitásplatform-hozzáférési jogkivonatok

A hozzáférési jogkivonatok lehetővé teszik az ügyfelek számára, hogy biztonságosan hívhassák meg az Azure által védett API-kat. A Microsoft identity platform hozzáférési jogkivonatok [JWTs](https://tools.ietf.org/html/rfc7519), Base64 kódolt JSON-objektumok az Azure által aláírt. Az ügyfelek nek a hozzáférési jogkivonatokat átlátszatlan karakterláncként kell kezelniük, mivel a jogkivonat tartalma csak az erőforrásra szolgál. Az érvényesítés és a hibakeresés érdekében a fejlesztők dekódolhatják a JWT-ket egy olyan webhely használatával, mint [a jwt.ms.](https://jwt.ms) Az ügyfél kaphat egy hozzáférési jogkivonatot a v1.0 végpont vagy a v2.0-végpont segítségével a különböző protokollok.

Amikor az ügyfél hozzáférési jogkivonatot kér, az Azure AD is visszaad néhány metaadatot a hozzáférési jogkivonatról az alkalmazás felhasználása esetén. Ez az információ tartalmazza a hozzáférési jogkivonat lejárati idejét és azokat a hatóköröket, amelyekre érvényes. Ezek az adatok lehetővé teszik, hogy az alkalmazás a hozzáférési jogkivonatok intelligens gyorsítótárazását anélkül végezze el, hogy magát a hozzáférési jogkivonatot kellene elemeznie.

Ha az alkalmazás olyan erőforrás (webes API), amelyhez az ügyfelek hozzáférést kérhetnek, a hozzáférési jogkivonatok hasznos információkat nyújtanak a hitelesítéshez és az engedélyezéshez, például a felhasználó, az ügyfél, a kibocsátó, az engedélyek és egyebek.

Tekintse meg a következő szakaszokat, amelyek ből megtudhatja, hogy egy erőforrás hogyan érvényesítheti és használhatja a hozzáférési jogkivonaton belüli jogcímeket.

> [!IMPORTANT]
> Access-jogkivonatok a jogkivonat *közönsége* alapján jönnek létre, ami azt jelenti, hogy a jogkivonat hatóköreit birtokló alkalmazás.  Így egy erőforrás-beállítás `accessTokenAcceptedVersion` az [alkalmazásjegyzékben,](reference-app-manifest.md#manifest-reference) amely lehetővé teszi, hogy `2` a v1.0-végpontot hívó ügyfél egy v2.0-s hozzáférési jogkivonatot kapjon.  Hasonlóképpen ez az oka annak, hogy az ügyfél [nem kötelező jogcímének](active-directory-optional-claims.md) módosítása `user.read`nem módosítja a jogkivonat kérésének időpontjában kapott hozzáférési jogkivonatot, amely az erőforrás tulajdonában van.
> Ugyanebből az okból, miközben az ügyfélalkalmazás tesztelése egy személyes fiók (például hotmail.com vagy outlook.com), előfordulhat, hogy az ügyfél által fogadott hozzáférési jogkivonat egy átlátszatlan karakterlánc. Ennek az az oka, hogy az elérni kívánt erőforrás olyan örökölt MSA (Microsoft-fiók) jegyeket kért, amelyek titkosítottak, és amelyeket az ügyfél nem ért.

## <a name="sample-tokens"></a>Mintatokenek

az 1.0-s és a 2.0-s számú tokenek hasonlóak, és ugyanazokat a jogcímeket tartalmazzák. Egy példa az egyes itt található.

### <a name="v10"></a>1.0-s verzió

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiJlZjFkYTlkNC1mZjc3LTRjM2UtYTAwNS04NDBjM2Y4MzA3NDUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTUyMjIyOS8iLCJpYXQiOjE1MzcyMzMxMDYsIm5iZiI6MTUzNzIzMzEwNiwiZXhwIjoxNTM3MjM3MDA2LCJhY3IiOiIxIiwiYWlvIjoiQVhRQWkvOElBQUFBRm0rRS9RVEcrZ0ZuVnhMaldkdzhLKzYxQUdyU091TU1GNmViYU1qN1hPM0libUQzZkdtck95RCtOdlp5R24yVmFUL2tES1h3NE1JaHJnR1ZxNkJuOHdMWG9UMUxrSVorRnpRVmtKUFBMUU9WNEtjWHFTbENWUERTL0RpQ0RnRTIyMlRJbU12V05hRU1hVU9Uc0lHdlRRPT0iLCJhbXIiOlsid2lhIl0sImFwcGlkIjoiNzVkYmU3N2YtMTBhMy00ZTU5LTg1ZmQtOGMxMjc1NDRmMTdjIiwiYXBwaWRhY3IiOiIwIiwiZW1haWwiOiJBYmVMaUBtaWNyb3NvZnQuY29tIiwiZmFtaWx5X25hbWUiOiJMaW5jb2xuIiwiZ2l2ZW5fbmFtZSI6IkFiZSAoTVNGVCkiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMjIyNDcvIiwiaXBhZGRyIjoiMjIyLjIyMi4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJvaWQiOiIwMjIyM2I2Yi1hYTFkLTQyZDQtOWVjMC0xYjJiYjkxOTQ0MzgiLCJyaCI6IkkiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJsM19yb0lTUVUyMjJiVUxTOXlpMmswWHBxcE9pTXo1SDNaQUNvMUdlWEEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6ImFiZWxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJGVnNHeFlYSTMwLVR1aWt1dVVvRkFBIiwidmVyIjoiMS4wIn0.D3H6pMUtQnoJAGq6AHd
```

Tekintse meg ezt a v1.0 token [JWT.ms](https://jwt.ms/#access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiJlZjFkYTlkNC1mZjc3LTRjM2UtYTAwNS04NDBjM2Y4MzA3NDUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTUyMjIyOS8iLCJpYXQiOjE1MzcyMzMxMDYsIm5iZiI6MTUzNzIzMzEwNiwiZXhwIjoxNTM3MjM3MDA2LCJhY3IiOiIxIiwiYWlvIjoiQVhRQWkvOElBQUFBRm0rRS9RVEcrZ0ZuVnhMaldkdzhLKzYxQUdyU091TU1GNmViYU1qN1hPM0libUQzZkdtck95RCtOdlp5R24yVmFUL2tES1h3NE1JaHJnR1ZxNkJuOHdMWG9UMUxrSVorRnpRVmtKUFBMUU9WNEtjWHFTbENWUERTL0RpQ0RnRTIyMlRJbU12V05hRU1hVU9Uc0lHdlRRPT0iLCJhbXIiOlsid2lhIl0sImFwcGlkIjoiNzVkYmU3N2YtMTBhMy00ZTU5LTg1ZmQtOGMxMjc1NDRmMTdjIiwiYXBwaWRhY3IiOiIwIiwiZW1haWwiOiJBYmVMaUBtaWNyb3NvZnQuY29tIiwiZmFtaWx5X25hbWUiOiJMaW5jb2xuIiwiZ2l2ZW5fbmFtZSI6IkFiZSAoTVNGVCkiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMjIyNDcvIiwiaXBhZGRyIjoiMjIyLjIyMi4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJvaWQiOiIwMjIyM2I2Yi1hYTFkLTQyZDQtOWVjMC0xYjJiYjkxOTQ0MzgiLCJyaCI6IkkiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJsM19yb0lTUVUyMjJiVUxTOXlpMmswWHBxcE9pTXo1SDNaQUNvMUdlWEEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6ImFiZWxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJGVnNHeFlYSTMwLVR1aWt1dVVvRkFBIiwidmVyIjoiMS4wIn0.D3H6pMUtQnoJAGq6AHd).

### <a name="v20"></a>2.0-s verzió

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiI2ZTc0MTcyYi1iZTU2LTQ4NDMtOWZmNC1lNjZhMzliYjEyZTMiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE1MzcyMzEwNDgsIm5iZiI6MTUzNzIzMTA0OCwiZXhwIjoxNTM3MjM0OTQ4LCJhaW8iOiJBWFFBaS84SUFBQUF0QWFaTG8zQ2hNaWY2S09udHRSQjdlQnE0L0RjY1F6amNKR3hQWXkvQzNqRGFOR3hYZDZ3TklJVkdSZ2hOUm53SjFsT2NBbk5aY2p2a295ckZ4Q3R0djMzMTQwUmlvT0ZKNGJDQ0dWdW9DYWcxdU9UVDIyMjIyZ0h3TFBZUS91Zjc5UVgrMEtJaWpkcm1wNjlSY3R6bVE9PSIsImF6cCI6IjZlNzQxNzJiLWJlNTYtNDg0My05ZmY0LWU2NmEzOWJiMTJlMyIsImF6cGFjciI6IjAiLCJuYW1lIjoiQWJlIExpbmNvbG4iLCJvaWQiOiI2OTAyMjJiZS1mZjFhLTRkNTYtYWJkMS03ZTRmN2QzOGU0NzQiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJhYmVsaUBtaWNyb3NvZnQuY29tIiwicmgiOiJJIiwic2NwIjoiYWNjZXNzX2FzX3VzZXIiLCJzdWIiOiJIS1pwZmFIeVdhZGVPb3VZbGl0anJJLUtmZlRtMjIyWDVyclYzeERxZktRIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoiZnFpQnFYTFBqMGVRYTgyUy1JWUZBQSIsInZlciI6IjIuMCJ9.pj4N-w_3Us9DrBLfpCt
```

Tekintse meg ezt a v2.0 token [JWT.ms.](https://jwt.ms/#access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiI2ZTc0MTcyYi1iZTU2LTQ4NDMtOWZmNC1lNjZhMzliYjEyZTMiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE1MzcyMzEwNDgsIm5iZiI6MTUzNzIzMTA0OCwiZXhwIjoxNTM3MjM0OTQ4LCJhaW8iOiJBWFFBaS84SUFBQUF0QWFaTG8zQ2hNaWY2S09udHRSQjdlQnE0L0RjY1F6amNKR3hQWXkvQzNqRGFOR3hYZDZ3TklJVkdSZ2hOUm53SjFsT2NBbk5aY2p2a295ckZ4Q3R0djMzMTQwUmlvT0ZKNGJDQ0dWdW9DYWcxdU9UVDIyMjIyZ0h3TFBZUS91Zjc5UVgrMEtJaWpkcm1wNjlSY3R6bVE9PSIsImF6cCI6IjZlNzQxNzJiLWJlNTYtNDg0My05ZmY0LWU2NmEzOWJiMTJlMyIsImF6cGFjciI6IjAiLCJuYW1lIjoiQWJlIExpbmNvbG4iLCJvaWQiOiI2OTAyMjJiZS1mZjFhLTRkNTYtYWJkMS03ZTRmN2QzOGU0NzQiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJhYmVsaUBtaWNyb3NvZnQuY29tIiwicmgiOiJJIiwic2NwIjoiYWNjZXNzX2FzX3VzZXIiLCJzdWIiOiJIS1pwZmFIeVdhZGVPb3VZbGl0anJJLUtmZlRtMjIyWDVyclYzeERxZktRIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoiZnFpQnFYTFBqMGVRYTgyUy1JWUZBQSIsInZlciI6IjIuMCJ9.pj4N-w_3Us9DrBLfpCt)

## <a name="claims-in-access-tokens"></a>Jogcímek a hozzáférési jogkivonatokban

A JWT-k három részre vannak osztva:

* **Fejléc** – A [jogkivonat érvényesítésével](#validating-tokens) kapcsolatos információkat tartalmaz, beleértve a jogkivonat típusát és az aláírás módját.
* **Hasznos tartalom** – Tartalmazza a szolgáltatás hívását megkísérlő felhasználó vagy alkalmazás összes fontos adatát.
* **Aláírás** – A token érvényesítéséhez használt nyersanyag.

Minden darabot egy pont`.`( ) választ el egymástól, és külön-külön Base64 kódolással.

A jogcímek csak akkor vannak jelen, ha létezik érték a kitöltéshez. Így az alkalmazás nem vehet függőséget a jogcím jelen. Példák `pwd_exp` közé tartozik (nem minden bérlő `family_name` jelszó lejár) vagy (ügyfél hitelesítő adatok ([v1.0](../azuread-dev/v1-oauth2-client-creds-grant-flow.md), [v2.0](v2-oauth2-client-creds-grant-flow.md)) folyamatok az alkalmazások nevében, amelyek nem rendelkeznek névvel). A hozzáférési jogkivonat érvényesítéséhez használt jogcímek mindig jelen lesznek.

> [!NOTE]
> Egyes jogcímek segítségével azure AD biztonságos jogkivonatok újbóli felhasználás esetén. Ezeket a leírásban "Átlátszatlan" néven jelölik meg, hogy azok nem nyilvános fogyasztásra szolgálnak. Ezek a jogcímek megjelenhetnek vagy nem jelennek meg egy jogkivonatban, és újak is hozzáadhatók értesítés nélkül.

### <a name="header-claims"></a>Fejlécjogcímek

|Jogcím | Formátum | Leírás |
|--------|--------|-------------|
| `typ` | String - mindig "JWT" | Azt jelzi, hogy a token jwt.|
| `nonce` | Sztring | A token-visszajátszásos támadások elleni védelemre használt egyedi azonosító. Az erőforrás rögzítheti ezt az értéket a visszajátszások elleni védelem érdekében. |
| `alg` | Sztring | A token aláírásához használt algoritmust jelzi, például "RS256" |
| `kid` | Sztring | A jogkivonat aláírásához használt nyilvános kulcs ujjlenyomatát adja meg. A 1.0-s és a 2.0-s és a 2.0-s jelű hozzáférési jogkivonatokban is kibocsátott. |
| `x5t` | Sztring | Ugyanúgy működik (használatban és `kid`értékben), mint a . `x5t`egy örökölt jogcím, amelyet csak az 1.0-s verzióbeli hozzáférési jogkivonatokban bocsátanak ki kompatibilitási célokra. |

### <a name="payload-claims"></a>Hasznos teherre vonatkozó igények

| Jogcím | Formátum | Leírás |
|-----|--------|-------------|
| `aud` | String, egy alkalmazásazonosító URI-ja | A jogkivonat címzettjét azonosítja. Az azonosítójogtokban a közönség az alkalmazás alkalmazásazonosítója, amely az Azure Portalon van hozzárendelve az alkalmazáshoz. Az alkalmazásnak érvényesítenie kell ezt az értéket, és el kell utasítania a jogkivonatot, ha az érték nem egyezik. |
| `iss` | Karakterlánc, STS URI | Azonosítja a biztonsági jogkivonat-szolgáltatás (STS), amely a jogkivonatot létrehozó és visszaadja, és az Azure AD-bérlő, amelyben a felhasználó hitelesítése. Ha a kibocsátott jogkivonat egy v2.0-s jogkivonat (lásd a `ver` jogcímet), az URI a `/v2.0`. A GUID, amely azt jelzi, hogy a felhasználó `9188040d-6c67-4c5b-b112-36a304b66dad`egy microsoftos fiók fogyasztói felhasználója, a . Az alkalmazásnak a jogcím GUID-részét kell használnia az alkalmazásba bejelentkező bérlők készletének korlátozására, ha van ilyen. |
|`idp`| Karakterlánc, általában STS URI | A jogkivonat alanyát hitelesítő identitásszolgáltatót adja meg. Ez az érték megegyezik a Kiállító jogcím értékével, kivéve, ha a felhasználói fiók nem ugyanabban a bérlőben van, mint a kibocsátó - például a vendégek. Ha a jogcím nincs jelen, az `iss` azt jelenti, hogy helyette az érték használható.  A szervezeti környezetben használt személyes fiókok (például egy Azure AD-bérlőnek `idp` meghívott személyes fiók) esetében a jogcím lehet "live.com" vagy a Microsoft-fiók bérlőjét `9188040d-6c67-4c5b-b112-36a304b66dad`tartalmazó STS URI. |
| `iat` | int, UNIX időbélyeg | A "Issued At" azt jelzi, hogy a token hitelesítése mikor történt. |
| `nbf` | int, UNIX időbélyeg | Az "nbf" (nem korábban) állítás azonosítja azt az időt, amely előtt a JWT nem fogadható el feldolgozásra. |
| `exp` | int, UNIX időbélyeg | Az "exp" (lejárati idő) jogcím azonosítja a lejárati időt, vagy amely után a JWT nem fogadható el feldolgozásra. Fontos megjegyezni, hogy egy erőforrás elutasíthatja a jogkivonatot, mielőtt ez az idő is, például ha a hitelesítés módosítása szükséges, vagy egy jogkivonat visszavonása észlelése. |
| `aio` | Átlátszatlan karakterlánc | Az Azure AD által használt belső jogcím a jogkivonat újrafelhasználása adatok rögzítésére. Az erőforrások nem használhatják ezt a jogcímet. |
| `acr` | Karakterlánc, "0" vagy "1" | Csak az 1.0-s kulcsban jelen van. A "Hitelesítési környezet osztály" jogcím. A "0" érték azt jelzi, hogy a végfelhasználói hitelesítés nem felelt meg az ISO/IEC 29115 követelményeinek. |
| `amr` | JSON karakterláncok tömbje | Csak az 1.0-s kulcsban jelen van. Azonosítja, hogy a jogkivonat tárgya hogyan lett hitelesítve. További részleteket [az amr-igénylési részben](#the-amr-claim) talál. |
| `appid` | Karakterlánc, GUID | Csak az 1.0-s kulcsban jelen van. Az ügyfél alkalmazásazonosítója a jogkivonathasználatával. Az alkalmazás önmagát vagy a felhasználó nevében járhat el. Az alkalmazásazonosító általában egy alkalmazásobjektumot jelöl, de az Azure AD-ben is képviselhet egy egyszerű szolgáltatásobjektumot. |
| `appidacr` | "0", "1" vagy "2" | Csak az 1.0-s kulcsban jelen van. Azt jelzi, hogy az ügyfél hogyan lett hitelesítve. Nyilvános ügyfél esetén az érték "0". Ha ügyfélazonosítót és ügyféltitkos kulcsot használ, az érték "1". Ha a hitelesítéshez ügyféltanúsítványt használtak, az érték "2". |
| `azp` | Karakterlánc, GUID | Csak a 2.0-s v2.0-s tokenekben van jelen, ami helyettesíti a programot. `appid` Az ügyfél alkalmazásazonosítója a jogkivonathasználatával. Az alkalmazás önmagát vagy a felhasználó nevében járhat el. Az alkalmazásazonosító általában egy alkalmazásobjektumot jelöl, de az Azure AD-ben is képviselhet egy egyszerű szolgáltatásobjektumot. |
| `azpacr` | "0", "1" vagy "2" | Csak a 2.0-s v2.0-s tokenekben van jelen, ami helyettesíti a programot. `appidacr` Azt jelzi, hogy az ügyfél hogyan lett hitelesítve. Nyilvános ügyfél esetén az érték "0". Ha ügyfélazonosítót és ügyféltitkos kulcsot használ, az érték "1". Ha a hitelesítéshez ügyféltanúsítványt használtak, az érték "2". |
| `preferred_username` | Sztring | A felhasználót jelképező elsődleges felhasználónév. Ez lehet egy e-mail cím, telefonszám, vagy egy általános felhasználónév nélkül megadott formátumban. Értéke változékony, és idővel változhat. Mivel ez az érték módosítható, ezt az értéket nem szabad engedélyezési döntések meghozatalához használni.  Ezt fel lehet használni a felhasználónév tippeket mégis. A `profile` kérelem megszerzéséhez a hatály szükséges. |
| `name` | Sztring | Egy ember által olvasható értéket ad, amely azonosítja a jogkivonat tárgyát. Az érték nem garantált, hogy egyedi, változékony, és úgy tervezték, hogy csak megjelenítési célokra használható. A `profile` kérelem megszerzéséhez a hatály szükséges. |
| `scp` | Karakterlánc, a hatókörök szóközt választott listája | Az alkalmazás által elérhetővé tett hatókörök készlete, amelyhez az ügyfélalkalmazás beleegyezést kért (és kapott). Az alkalmazásnak ellenőriznie kell, hogy ezek a hatókörök érvényesek-e az alkalmazás által elérhetővé téve, és a hatókörök értéke alapján kell engedélyezési döntéseket hoznia. Csak a [felhasználói jogkivonatokhoz](#user-and-application-tokens)tartozik. |
| `roles` | Karakterláncok tömbje, engedélyek listája | Az alkalmazás által elérhetővé tett engedélyek készlete, amelynek hívására a kérelmező alkalmazás vagy felhasználó engedélyt kapott. Az [alkalmazásjogkivonatok](#user-and-application-tokens)esetében ezt használja az ügyfél hitelesítő adatáramlása ([1.0- as](../azuread-dev/v1-oauth2-client-creds-grant-flow.md) [, 2.0-s](v2-oauth2-client-creds-grant-flow.md)érték ) a felhasználói hatókörök helyett.  Felhasználói [jogkivonatok](#user-and-application-tokens) esetén ez a rendszer azokat a szerepköröket tölti fel, amelyekhez a felhasználó a célalkalmazásban hozzá volt rendelve. |
| `wids` | [RoleTemplateID](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#role-template-ids) GUID-azonosítók tömbje | A felhasználóhoz rendelt bérlői szerepköröket [jelöli a rendszergazdai szerepkörök lapján](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#role-template-ids)található szerepkörök szakaszából.  Ez a jogcím alkalmazásonként, az `groupMembershipClaims` [alkalmazásjegyzék](reference-app-manifest.md)tulajdonságán keresztül van konfigurálva.  Az "All" vagy a "DirectoryRole" beállítás szükséges.  Előfordulhat, hogy a tokenhossz-problémák miatt az implicit folyamaton keresztül kapott jogkivonatokban nincs jelen. |
| `groups` | GUID-tömb JSON-tömbje | Olyan objektumazonosítókat tartalmaz, amelyek a tulajdonos csoporttagságait jelölik. Ezek az értékek egyediek (lásd: Objektumazonosító), és biztonságosan használhatók a hozzáférés kezeléséhez, például az erőforrás-hozzáférés engedélyezésének érvényesítéséhez. A csoportok jogcímében szereplő csoportok alkalmazásonként, az `groupMembershipClaims` [alkalmazásjegyzék](reference-app-manifest.md)tulajdonságán keresztül vannak konfigurálva. A null érték kizárja az összes csoportot, a "SecurityGroup" érték csak az Active Directory biztonsági csoport tagságait tartalmazza, a "Minden" érték pedig a biztonsági csoportokat és az Office 365 terjesztési listákat is tartalmazza. <br><br>A `hasgroups` `groups` jogcím implicit támogatással való használatával kapcsolatos részleteket lásd az alábbi jogcímben. <br>Más folyamatok esetén, ha a felhasználó által megadott csoportok száma túllép egy korlátot (150 az SAML, 200 a JWT esetében), akkor a rendszer egy túllépési jogcímet ad hozzá a Microsoft Graph végpontjára mutató jogcímforrásokhoz, amelyek a felhasználó csoportjainak listáját tartalmazzák. |
| `hasgroups` | Logikai | Ha jelen `true`van, mindig, a felhasználó legalább egy csoportban van. A jwt-k `groups` igénye helyett implicit támogatási folyamatokban használatos, ha a teljes csoportjogcím kiterjesztené az URI-töredéket az URL-hossz korlátokon (jelenleg 6 vagy több csoporton) túlis. Azt jelzi, hogy az ügyfélnek a Microsoft Graph API-t kell használnia a felhasználó csoportjainak ( meghatározásához ).`https://graph.microsoft.com/v1.0/users/{userID}/getMemberObjects` |
| `groups:src1` | JSON-objektum | A token kérelmek, amelyek nem `hasgroups` hosszú korlátozott (lásd fent), de még mindig túl nagy a jogkivonat, a felhasználó teljes csoportlistájára mutató hivatkozást. A JWT-k esetében, mint elosztott követelés, az SAML esetében mint új követelés a `groups` követelés helyett. <br><br>**Példa JWT Érték**: <br> `"groups":"src1"` <br> `"_claim_sources`: `"src1" : { "endpoint" : "https://graph.microsoft.com/v1.0/users/{userID}/getMemberObjects" }` |
| `sub` | Karakterlánc, GUID | Az a főtag, amelyről a jogkivonat adatokat, például egy alkalmazás felhasználóját érvényesíti. Ez az érték nem módosítható, és nem rendelhető hozzá újra és nem használható fel újra. Az engedélyezési ellenőrzések biztonságos végrehajtására használható, például amikor a jogkivonatot egy erőforrás eléréséhez használják, és kulcsként használható az adatbázistáblákban. Mivel a tulajdonos mindig jelen van a jogkivonatok, amelyek az Azure AD-problémák, azt javasoljuk, hogy ezt az értéket egy általános célú engedélyezési rendszer. A téma azonban egy páronkénti azonosító – egy adott alkalmazásazonosító egyedi. Ezért ha egy felhasználó két különböző ügyfélazonosítóhasználatával jelentkezik be két különböző alkalmazásba, ezek az alkalmazások két különböző értéket kapnak a tulajdonosjogcímhez. Ez lehet, hogy nem kívánatos, attól függően, hogy az architektúra és az adatvédelmi követelmények. Lásd még `oid` a jogcím (amely nem ugyanaz marad a bérlőn belüli alkalmazások között). |
| `oid` | Karakterlánc, GUID | A Microsoft identitásplatformon lévő objektum, ebben az esetben egy felhasználói fiók nem módosítható azonosítója. Az engedélyezési ellenőrzések biztonságos és az adatbázistáblák kulcsaként is használható. Ez az azonosító egyedileg azonosítja a felhasználót az alkalmazások között – két `oid` különböző alkalmazás, amelyek ugyanabban a felhasználóban jelentkeznek be, ugyanazt az értéket kapják a jogcímben. Így `oid` lehet használni, amikor lekérdezéseket a Microsoft online szolgáltatások, mint például a Microsoft Graph. A Microsoft Graph ezt az `id` azonosítót adja vissza egy adott felhasználói fiók [tulajdonságaként.](/graph/api/resources/user) Mivel `oid` a lehetővé teszi, hogy `profile` több alkalmazás korrelálja a felhasználókat, a hatókör szükséges a jogcím fogadásához. Vegye figyelembe, hogy ha egy felhasználó több bérlőben is létezik, a felhasználó minden bérlőben más-más objektumazonosítót fog tartalmazni – azok különböző fiókoknak minősülnek, még akkor is, ha a felhasználó minden fiókba ugyanazzal a hitelesítő adatokkal jelentkezik be. |
| `tid` | Karakterlánc, GUID | Az Azure AD-bérlő, amely a felhasználó a. Munkahelyi és iskolai fiókok esetén a GUID annak a szervezetnek a nem módosítható bérlői azonosítója, amelyhez a felhasználó tartozik. Személyes fiókok esetén az `9188040d-6c67-4c5b-b112-36a304b66dad`érték . A `profile` kérelem megszerzéséhez a hatály szükséges. |
| `unique_name` | Sztring | Csak az 1.0-s kulcsban jelen van. A jogkivonat alanyát azonosító, ember által olvasható értéket ad meg. Ez az érték nem garantált, hogy egyedi a bérlőn belül, és csak megjelenítési célokra használható. |
| `uti` | Átlátszatlan karakterlánc | Az Azure által a jogkivonatok újraérvényesítéséhez használt belső jogcím. Az erőforrások nem használhatják ezt a jogcímet. |
| `rh` | Átlátszatlan karakterlánc | Az Azure által a jogkivonatok újraérvényesítéséhez használt belső jogcím. Az erőforrások nem használhatják ezt a jogcímet. |
| `ver` | String, `1.0` vagy vagy`2.0` | A hozzáférési jogkivonat verzióját jelzi. |

**Csoportok túlórajogigénye**

Annak érdekében, hogy a jogkivonat mérete ne haladja meg a HTTP-fejléc méretkorlátait, az Azure AD korlátozza a csoportok jogcímében található objektumazonosítók számát. Ha egy felhasználó több csoport tagja, mint a túlterhelési korlát (150 SAML-jogkivonatok, 200 JWT-jogkivonatok), majd az Azure AD nem bocsátja ki a csoportok jogcíma a jogkivonatban. Ehelyett tartalmaz egy túllépési jogcímet a jogkivonatban, amely jelzi az alkalmazásnak a Microsoft Graph API lekérdezését a felhasználó csoporttagságának lekéréséhez.

```JSON
{
  ...
  "_claim_names": {
   "groups": "src1"
    },
    {
  "_claim_sources": {
    "src1": {
        "endpoint":"[Url to get this user's group membership from]"
        }
       }
     }
  ...
 }
 ```

Használhatja a `BulkCreateGroups.ps1` megadott [az Alkalmazáslétrehozása parancsfájlok](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ/5-2-Groups/AppCreationScripts) mappában, hogy segítsen teszt túlterhelés forgatókönyvek.

#### <a name="v10-basic-claims"></a>1.0-s alapvető követelések

A következő jogcímek szerepelni fognak az 1.0-s vtokenekben, ha vannak ilyenek, de alapértelmezés szerint nem szerepelnek a 2.0-s jogkivonatokban. Ha a 2.0-s vagyos ta-s vagyonkezelési csomaghasználata esetén a jogcímek valamelyikére van szüksége, kérje őket [a választható jogcímek](active-directory-optional-claims.md)használatával.

| Jogcím | Formátum | Leírás |
|-----|--------|-------------|
| `ipaddr`| Sztring | Az az IP-cím, amelyről a felhasználó hitelesített. |
| `onprem_sid`| Karakterlánc [SID formátumban](https://docs.microsoft.com/windows/desktop/SecAuthZ/sid-components) | Azokban az esetekben, amikor a felhasználó rendelkezik egy helyszíni hitelesítés, ez a jogcím biztosítja a biztonsági azonosítót. Az örökölt `onprem_sid` alkalmazásokban engedélyezésre használható.|
| `pwd_exp`| int, UNIX időbélyeg | Azt jelzi, hogy a felhasználó jelszava mikor jár le. |
| `pwd_url`| Sztring | Olyan URL-cím, amelynek a felhasználók a jelszó alaphelyzetbe állításához küldhetők. |
| `in_corp`| logikai | Jelzi, ha az ügyfél a vállalati hálózatról jelentkezik be. Ha nem, a követelés nem szerepel. |
| `nickname`| Sztring | A felhasználó további neve, a vezeték- vagy utónévtől elkülönítve.|
| `family_name` | Sztring | Megadja a felhasználó vezetéknevét, vezetéknevét vagy családnevét a felhasználói objektumban meghatározottak szerint. |
| `given_name` | Sztring | Megadja a felhasználó első vagy megadott nevét a felhasználói objektumon beállítottan. |
| `upn` | Sztring | A felhasználó felhasználóneve. Lehet telefonszám, e-mail cím vagy formázatlan karakterlánc. Csak megjelenítési célokra és felhasználónév-emlékeztetők megadásával használható újrahitelesítési forgatókönyvekben. |

#### <a name="the-amr-claim"></a>Az `amr` állítás

A Microsoft-identitások különböző módokon hitelesíthetik magukat, ami az alkalmazás szempontjából releváns lehet. A `amr` jogcím egy olyan tömb, amely `["mfa", "rsa", "pwd"]`több elemet is tartalmazhat, például egy jelszót és a Hitelesítő alkalmazást egyaránt használó hitelesítéshez.

| Érték | Leírás |
|-----|-------------|
| `pwd` | Jelszó-hitelesítés, a felhasználó Microsoft-jelszava vagy egy alkalmazás ügyféltkatitkára. |
| `rsa` | A hitelesítés egy RSA-kulcs igazolásán alapult, például a [Microsoft Authenticator alkalmazással.](https://aka.ms/AA2kvvu) Ez magában foglalja azt is, ha a hitelesítést egy önaláírt JWT végezte egy szolgáltatás tulajdonában lévő X509 tanúsítvánnyal. |
| `otp` | Egyszeri jelkód e-mailben vagy szöveges üzenetben. |
| `fed` | Összevont hitelesítési feltételt (például JWT vagy SAML) használt. |
| `wia` | Windows integrált hitelesítés |
| `mfa` | Többtényezős hitelesítést használt. Ha ez jelen van, a többi hitelesítési módszer is szerepelni fog. |
| `ngcmfa` | Egyenértékű `mfa`a bizonyos speciális hitelesítő adatok kiépítéséhez használt. |
| `wiaormfa`| A felhasználó a Windows vagy az MFA hitelesítő adatait használta a hitelesítéshez. |
| `none` | Nem történt hitelesítés. |

## <a name="validating-tokens"></a>Jogkivonatok érvényesítése

Egy id_token vagy egy access_token érvényesítéséhez az alkalmazásnak ellenőriznie kell a jogkivonat aláírását és a jogcímeket. A hozzáférési jogkivonatok érvényesítéséhez az alkalmazásnak a kibocsátót, a közönséget és az aláíró jogkivonatokat is ellenőriznie kell. Ezeket az OpenID felderítési dokumentum értékei alapján kell érvényesíteni. A dokumentum bérlőtől független verziója például [https://login.microsoftonline.com/common/.well-known/openid-configuration](https://login.microsoftonline.com/common/.well-known/openid-configuration)a helyen található.

Az Azure AD köztes szoftver beépített képességekkel rendelkezik a hozzáférési jogkivonatok érvényesítéséhez, és a [minták](https://docs.microsoft.com/azure/active-directory/active-directory-code-samples) között böngészve megtalálhatja az Ön által választott nyelvet.

Könyvtárakat és kódmintákat biztosítunk, amelyek bemutatják a jogkivonat-érvényesítés kezelését. Az alábbi információk azok számára, akik szeretnék megérteni az alapul szolgáló folyamatot. Vannak is több harmadik- csapat nyit- forrás könyvtárak elérhető részére JWT érvényesítés - van legalább egy választás részére majdnem mind emelvény és nyelv odakinn. Az Azure AD hitelesítést árak és kódminták további információt a [1.0-s hitelesítési kódtárak](../azuread-dev/active-directory-authentication-libraries.md) és [a 2.0-s hitelesítési kódtárak című témakörben talál.](reference-v2-libraries.md)

### <a name="validating-the-signature"></a>Az aláírás érvényesítése

A JWT három szegmenst tartalmaz, amelyeket a `.` karakter választ el egymástól. Az első szegmenst **fejlécnek**, a másodikat **törzsnek**, a harmadikat **aláírásnak nevezik**. Az aláírási szegmens segítségével ellenőrizheti a jogkivonat hitelességét, hogy az alkalmazás megbízható legyen.

Az Azure AD által kiadott jogkivonatok alá vannak írva az iparági szabványnak megfelelő aszimmetrikus titkosítási algoritmusok, például az RS256 használatával. A JWT fejléce a jogkivonat aláírásához használt kulcsra és titkosítási módszerre vonatkozó információkat tartalmazza:

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "x5t": "iBjL1Rcqzhiy4fpxIxdZqohM2Yk",
  "kid": "iBjL1Rcqzhiy4fpxIxdZqohM2Yk"
}
```

A `alg` jogcím azt az algoritmust jelzi, amely `kid` et a jogkivonat aláírásához használták, míg a jogcím a jogkivonat érvényesítéséhez használt nyilvános kulcsot jelzi.

Az Azure AD egy adott időpontban aláírhat egy id_token a nyilvános és titkos kulcspárok egy bizonyos készletének használatával. Az Azure AD rendszeres időközönként elforgatja a kulcsok lehetséges készletét, ezért az alkalmazást úgy kell megírni, hogy automatikusan kezelje ezeket a kulcsmódosításokat. Az Azure AD által használt nyilvános kulcsok frissítéseinek ésszerű gyakorisága 24 óránként.

Az aláírás érvényesítéséhez szükséges aláíró kulcs adatait az [OpenID Connect metaadat-dokumentum](v2-protocols-oidc.md#fetch-the-openid-connect-metadata-document) segítségével szerezheti be, amely a következő helyen található:

```
https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration
```

> [!TIP]
> Próbálja ki ezt az [URL-t](https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration) a böngészőben!

Ez a metaadat-dokumentum:

* Egy JSON-objektum, amely számos hasznos információt tartalmaz, például az OpenID Connect hitelesítéshez szükséges különböző végpontok helyét.
* Tartalmaz `jwks_uri`egy , amely megadja a tokenek aláírásához használt nyilvános kulcsok készletének helyét. A json webkulcs (JWK) `jwks_uri` található tartalmazza az összes nyilvános kulcs információt használatban az adott pillanatban.  A JWK formátumleírása az [RFC 7517 ..](https://tools.ietf.org/html/rfc7517)  Az alkalmazás használhatja a `kid` jogcímet a JWT fejlécében annak kiválasztásához, hogy ebben a dokumentumban melyik nyilvános kulcs lett használva egy adott jogkivonat aláírásához. Ezután a megfelelő nyilvános kulcs és a jelzett algoritmus használatával elvégezheti az aláírás-ellenőrzést.

> [!NOTE]
> A v1.0-végpont a `x5t` `kid` jogcímeket és a jogcímeket is visszaadja, míg a v2.0 végpont csak a `kid` jogcímekkel válaszol. A jövőben azt javasoljuk, hogy a `kid` jogcím használatával érvényesítse a jogkivonatot.

Az aláírás-ellenőrzés nem tartozik a dokumentum hatálya alá – számos nyílt forráskódú tár áll rendelkezésre, amelyek szükség esetén segítenek önnek.  A Microsoft Identity platform azonban rendelkezik egy jogkivonat-aláíró kiterjesztéssel a szabványokhoz – egyéni aláíró kulcsok.

Ha az alkalmazás a [jogcím-leképezési](active-directory-claims-mapping.md) funkció használata miatt rendelkezik egyéni aláírási kulcsokkal, hozzá kell fűznie egy `appid` lekérdezési paramétert, amely tartalmazza az alkalmazás azonosítóazonosítóját, hogy `jwks_uri` mutasson az alkalmazás aláírási kulcsának adataira, amelyeket az érvényesítéshez kell használni. Például: `https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e` a `jwks_uri` `https://login.microsoftonline.com/{tenant}/discovery/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e`részét tartalmazza.

### <a name="claims-based-authorization"></a>Jogcímalapú engedélyezés

Az alkalmazás üzleti logikája határozza meg ezt a lépést, néhány közös engedélyezési módszerek az alábbiakban lefektetett.

* Ellenőrizze `scp` a `roles` vagy a jogcím, hogy ellenőrizze, hogy az összes jelen hatókörök egyeznek-e az API által elérhetővé tett, és lehetővé teszi az ügyfél számára, hogy a kért műveletet.
* Győződjön meg arról, hogy a `appid` hívó ügyfél a jogcím használatával hívhatja meg az API-t.
* Ellenőrizze a hívó ügyfél hitelesítési állapotát a használatával `appidacr` – nem lehet 0, ha a nyilvános ügyfelek nem hívhatják meg az API-t.
* Ellenőrizze a korábbi `nonce` jogcímek listájával, hogy ellenőrizze, hogy a token nem kerül-e visszajátszásra.
* Ellenőrizze, `tid` hogy a bérlő, amely az API-t hívhatja.
* A `acr` jogcím segítségével ellenőrizze, hogy a felhasználó végrehajtotta-e az MFA-t. Ezt a feltételes hozzáférés sel kell [érvényesíteni.](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
* Ha a hozzáférési `roles` jogkivonatban kért vagy `groups` jogcímeket kért, ellenőrizze, hogy a felhasználó a művelethez engedélyezett csoportban van-e.
  * Az implicit folyamat használatával lekért jogkivonatok esetében valószínűleg le kell kérdeznie a [Microsoft Graph-ot](https://developer.microsoft.com/graph/) ezekhez az adatokhoz, mivel gyakran túl nagy ahhoz, hogy elférjen a jogkivonatban.

## <a name="user-and-application-tokens"></a>Felhasználói és alkalmazásjogkivonatok

Az alkalmazás jogkivonatokat kaphat egy felhasználó (a szokásos folyamat) nevében vagy közvetlenül egy alkalmazástól (az ügyfél hitelesítő adatfolyamatán keresztül ([1.0,](../azuread-dev/v1-oauth2-client-creds-grant-flow.md) [2.0- es érték).](v2-oauth2-client-creds-grant-flow.md) Ezek az csak alkalmazásjogkivonatok azt jelzik, hogy ez a hívás egy alkalmazástól érkezik, és nem rendelkezik a felhasználó által támogatott. Ezek a tokenek kezelése nagyjából azonos, néhány különbséggel:

* A csak alkalmazásjogkivonatokkal `scp` nem lesz jogcím, `roles` és ehelyett jogcímük is lehet. Ez az a pont, ahol az alkalmazásengedélyek rögzítésre kerülnek (a delegált engedélyekkel szemben). A delegált és alkalmazásengedélyekről további információt az Engedély és hozzájárulás című témakörben talál ([1.0,](../azuread-dev/v1-permissions-consent.md) [2.0- es érték](v2-permissions-and-consent.md)).
* Sok ember-specifikus állítások hiányoznak, `name` `upn`mint például a vagy .
* A `sub` `oid` és a követelések ugyanaz lesz.

## <a name="token-revocation"></a>Token visszavonása

A frissítési jogkivonatok bármikor érvényteleníthetők vagy visszavonhatók, különböző okok miatt. Ezek két fő kategóriába sorolhatók: időkérés és visszavonás.

### <a name="token-timeouts"></a>Token időmegtatulásai

A [token élettartamának konfigurálása](active-directory-configurable-token-lifetimes.md)használatával a frissítési jogkivonatok élettartama módosítható.  Ez normális, és várható, hogy egyes tokenek használat nélkül menjenek (pl. a felhasználó 3 hónapig nem nyitja meg az alkalmazást), ezért lejár.  Az alkalmazások olyan esetekben találkoznak, amikor a bejelentkezési kiszolgáló a kora miatt elutasítja a frissítési jogkivonatot. 

* MaxInactiveTime: Ha a frissítési jogkivonatot nem használták a MaxInactiveTime által diktált időn belül, a frissítési jogkivonat már nem lesz érvényes.
* MaxSessionAge: Ha a MaxAgeSessionMultiFactor vagy a MaxAgeSessionSingleFactor az alapértelmezetttől eltérő (A visszavonásig) beállításra van beállítva, akkor a MaxAgeSession* munkamenetben beállított idő elteltével újra hitelesítésre lesz szükség.
* Példák:
  * A bérlő egy MaxInactiveTime öt nap, és a felhasználó ment nyaralni egy hétig, és így az Azure AD nem látott új jogkivonat-kérelmet a felhasználó tól 7 nap. A következő alkalommal, amikor a felhasználó új jogkivonatot kér, meg fogja találni a frissítési jogkivonatot visszavonták, és újra meg kell adnia a hitelesítő adatait.
  * Egy bizalmas alkalmazás egy nap MaxAgeSessionSingleFactor rendelkezik. Ha a felhasználó hétfőn és kedden (25 óra eltelte után) bejelentkezik, újra kell hitelesítenie magát.

### <a name="revocation"></a>Visszavonási

A frissítési jogkivonatokat a kiszolgáló visszavonhatja a hitelesítő adatok változása, illetve a használat vagy a rendszergazdai művelet miatt.  A frissítési jogkivonatok két osztályba tartoznak : a bizalmas ügyfeleknek (a jobb szélső oszlopnak) és a nyilvános ügyfeleknek (az összes többi oszlopnak) kiadott tokenek.   

|   | Jelszóalapú cookie | Jelszóalapú jogkivonat | Nem jelszóalapú cookie | Nem jelszóalapú jogkivonat | Bizalmas ügyféltoken |
|---|-----------------------|----------------------|---------------------------|--------------------------|---------------------------|
| A jelszó lejár | Életben marad | Életben marad | Életben marad | Életben marad | Életben marad |
| A felhasználó által módosított jelszó | Revoked | Revoked | Életben marad | Életben marad | Életben marad |
| A felhasználó nem SSPR | Revoked | Revoked | Életben marad | Életben marad | Életben marad |
| Rendszergazda alaphelyzetbe állítja a jelszót | Revoked | Revoked | Életben marad | Életben marad | Életben marad |
| A felhasználó visszavonja a frissítési jogkivonatokat a [PowerShellen keresztül](https://docs.microsoft.com/powershell/module/azuread/revoke-azureadsignedinuserallrefreshtoken) | Revoked | Revoked | Revoked | Revoked | Revoked |
| A rendszergazda visszavonja a felhasználó összes frissítési jogkivonatát a [PowerShellen keresztül](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken) | Revoked | Revoked |Revoked | Revoked | Revoked |
| Egyszeri kijelentkezés ([1.0- as](../azuread-dev/v1-protocols-openid-connect-code.md#single-sign-out), [2.0-s )](v2-protocols-oidc.md#single-sign-out) a weben | Revoked | Életben marad | Revoked | Életben marad | Életben marad |

> [!NOTE]
> A "Nem jelszó alapú" bejelentkezés olyan bejelentkezés, ahol a felhasználó nem írt be jelszót a beírásához. Például az arc használata a Windows Hello, a FIDO2 kulcs vagy a PIN-kódot.
>
> A Windows 10 elsődleges frissítési tokenjei (PRT) a hitelesítő adatok alapján vannak elkülönítve. Például a Windows Hello és a jelszó saját PRT-k, egymástól elkülönítve. Amikor egy felhasználó hello hitelesítő adattal (PIN-kóddal vagy biometrikus adatokkal) jelentkezik be, majd módosítja a jelszót, a korábban beszerzett jelszóalapú PRT-t visszavonják. Ha jelszóval jelentkezik be, azzal érvényteleníti a régi PRT-t, és újat kér.
>
> A frissítési jogkivonatok nem érvénytelenítve vagy visszavonva, ha egy új hozzáférési jogkivonat lekéréséhez és a frissítési jogkivonat frissítéséhez használják.  Azonban az alkalmazás nak el kell vetnie a régit, amint azt használják, és cserélje le az újat, mivel az új jogkivonat új lejárati idővel rendelkezik. 

## <a name="next-steps"></a>További lépések

* További információ [ `id_tokens` az Azure AD-ben.](id-tokens.md)
* További információ az engedélyről és a hozzájárulásról ( [1.0 ,](../azuread-dev/v1-permissions-consent.md) [2.0).](v2-permissions-and-consent.md)
