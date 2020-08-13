---
title: A Microsoft Identity platform tanúsítványának hitelesítő adatai
titleSuffix: Microsoft identity platform
description: Ez a cikk a tanúsítvány hitelesítő adatainak regisztrálását és használatát ismerteti az alkalmazás hitelesítéséhez.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/12/2020
ms.author: hirsin
ms.reviewer: nacanuma, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 06f15257148342879a164005a8f4fb302c539e67
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2020
ms.locfileid: "88163662"
---
# <a name="microsoft-identity-platform-application-authentication-certificate-credentials"></a>Microsoft Identity platform-alkalmazás hitelesítési tanúsítványának hitelesítő adatai

A Microsoft Identity platform lehetővé teszi, hogy az alkalmazások a saját hitelesítő adataikat használják a hitelesítéshez, például a OAuth 2,0 [ügyfél hitelesítő adatait adja](v2-oauth2-client-creds-grant-flow.md) meg, és a [befelé irányuló](v2-oauth2-on-behalf-of-flow.md) (OBO) folyamatot.

Az egyik hitelesítő adat, amelyet az alkalmazás használhat a hitelesítéshez, egy [JSON web token](./security-tokens.md#json-web-tokens-jwts-and-claims) (JWT)-állítás, amely az alkalmazás tulajdonában lévő tanúsítvánnyal van aláírva.

## <a name="assertion-format"></a>Érvényesítési formátum

Az állítás kiszámításához használhatja a számos JWT-függvénytár egyikét a választott nyelven. Az adatokat a token a [fejlécben](#header), a [jogcímek](#claims-payload)és az [aláírás](#signature)alapján végzi el.

### <a name="header"></a>Fejléc

| Paraméter |  Megjegyzés |
| --- | --- |
| `alg` | **RS256** kell lennie |
| `typ` | **JWT** kell lennie |
| `x5t` | Az X. 509 tanúsítvány kivonata (más néven a tanúsítvány SHA-1 *ujjlenyomata*) Base64 karakterlánc-értékként van kódolva. Például egy X. 509 tanúsítvány kivonata miatt `84E05C1D98BCE3A5421D225B140B36E86A3D5534` a jogcím a következő: `x5t` `hOBcHZi846VCHSJbFAs26Go9VTQ` . |

### <a name="claims-payload"></a>Jogcímek (hasznos adatok)

| Paraméter |  Megjegyzések |
| --- | --- |
| `aud` | Célközönség: legyen`https://login.microsoftonline.com/<your-tenant-id>/oauth2/token` |
| `exp` | Lejárati dátum: a jogkivonat lejárati dátuma. Az idő a (z) január 1-től 1970 (1970-01-01T0:0: 0Z) UTC szerint, a jogkivonat érvényességének lejárta előtt. Javasoljuk, hogy rövid lejárati időt használjon – 10 percet egy órára.|
| `iss` | Kiállító: az ügyfélszolgáltatás client_id (*alkalmazás-ügyfél) azonosítója* . |
| `jti` | GUID: a JWT azonosítója |
| `nbf` | Nem előtte: az a dátum, amely előtt a jogkivonat nem használható. Az idő a (z) január 1-től 1970 (1970-01-01T0:0: 0Z) UTC-számú másodpercnek felel meg az érvényesítési időpontig. |
| `sub` | A tulajdonos: a `iss` (z) esetében az ügyfélszolgáltatás client_id (*Application (ügyfél) azonosítója* ) |

### <a name="signature"></a>Aláírás

Az aláírás kiszámítása a tanúsítvány [JSON web token RFC7519-specifikációban](https://tools.ietf.org/html/rfc7519)leírt módon történő alkalmazásával történik.

## <a name="example-of-a-decoded-jwt-assertion"></a>Dekódolású JWT-érvényesítési példa

```JSON
{
  "alg": "RS256",
  "typ": "JWT",
  "x5t": "gx8tGysyjcRqKjFPnd7RFwvwZI0"
}
.
{
  "aud": "https: //login.microsoftonline.com/contoso.onmicrosoft.com/oauth2/token",
  "exp": 1484593341,
  "iss": "97e0a5b7-d745-40b6-94fe-5f77d35c6e05",
  "jti": "22b3bb26-e046-42df-9c96-65dbd72c1c81",
  "nbf": 1484592741,
  "sub": "97e0a5b7-d745-40b6-94fe-5f77d35c6e05"
}
.
"Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

## <a name="example-of-an-encoded-jwt-assertion"></a>Példa kódolt JWT-kijelentésre

A következő karakterlánc egy példa a kódolt állításra. Ha alaposan meggondolja, három szakaszt pont () választja el egymástól `.` :

* Az első szakasz kódolja a *fejlécet*
* A második szakasz kódolja a *jogcímeket* (hasznos adatok)
* Az utolsó szakasz az első két szakasz tartalmából származó tanúsítványokkal kiszámított *aláírás* .

```
"eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJhdWQiOiJodHRwczpcL1wvbG9naW4ubWljcm9zb2Z0b25saW5lLmNvbVwvam1wcmlldXJob3RtYWlsLm9ubWljcm9zb2Z0LmNvbVwvb2F1dGgyXC90b2tlbiIsImV4cCI6MTQ4NDU5MzM0MSwiaXNzIjoiOTdlMGE1YjctZDc0NS00MGI2LTk0ZmUtNWY3N2QzNWM2ZTA1IiwianRpIjoiMjJiM2JiMjYtZTA0Ni00MmRmLTljOTYtNjVkYmQ3MmMxYzgxIiwibmJmIjoxNDg0NTkyNzQxLCJzdWIiOiI5N2UwYTViNy1kNzQ1LTQwYjYtOTRmZS01Zjc3ZDM1YzZlMDUifQ.
Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

## <a name="register-your-certificate-with-microsoft-identity-platform"></a>A tanúsítvány regisztrálása a Microsoft Identity platformmal

A tanúsítvány hitelesítő adatait a Microsoft Identity platformon található ügyfélalkalmazás alapján a következő módszerek bármelyikével társíthatja a Azure Portal használatával:

### <a name="uploading-the-certificate-file"></a>A tanúsítványfájl feltöltése

Az ügyfélalkalmazás Azure-alkalmazásának regisztrációja:
1. Válassza ki a **tanúsítványok & Secrets**elemet.
2. Kattintson a **tanúsítvány feltöltése** elemre, és válassza ki a feltölteni kívánt tanúsítványt.
3. Kattintson a **Hozzáadás** parancsra.
  A tanúsítvány feltöltése után a rendszer megjeleníti az ujjlenyomatot, a kezdési dátumot és a lejárati értékeket.

### <a name="updating-the-application-manifest"></a>Az alkalmazás jegyzékfájljának frissítése

A tanúsítvány birtokában a következőket kell kiszámítani:

- `$base64Thumbprint`-A tanúsítvány kivonatának Base64 kódolású értéke
- `$base64Value`-A tanúsítvány nyers adatmennyiségének Base64 kódolású értéke

Meg kell adnia egy GUID azonosítót is a kulcs azonosításához az alkalmazás jegyzékfájljában ( `$keyId` ).

Az ügyfélalkalmazás Azure-alkalmazásának regisztrációja:
1. Válassza ki a **jegyzékfájlt** az alkalmazás jegyzékfájljának megnyitásához.
2. Cserélje le a " *hitelesítő adatok* " tulajdonságot az új tanúsítvány adataira a következő séma használatával.

   ```JSON
   "keyCredentials": [
       {
           "customKeyIdentifier": "$base64Thumbprint",
           "keyId": "$keyid",
           "type": "AsymmetricX509Cert",
           "usage": "Verify",
           "value":  "$base64Value"
       }
   ]
   ```
3. Mentse a módosításokat az alkalmazás-jegyzékfájlba, majd töltse fel a jegyzékfájlt a Microsoft Identity platformba.

   A `keyCredentials` tulajdonság többértékű, így több tanúsítvány is feltölthető a gazdagabb kulcsok kezeléséhez.

## <a name="next-steps"></a>További lépések

A Microsoft Identity platform code minta használatával a GitHubon a [.net Core Daemon Console alkalmazás](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) azt mutatja be, hogyan használja az alkalmazás a saját hitelesítő adatait a hitelesítéshez. Azt is bemutatja, hogyan [hozhat létre önaláírt tanúsítványt](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/tree/master/1-Call-MSGraph#optional-use-the-automation-script) a `New-SelfSignedCertificate` PowerShell-parancsmag használatával. Az [alkalmazás-létrehozási szkripteket](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/master/1-Call-MSGraph/AppCreationScripts-withCert/AppCreationScripts.md) is használhatja a minta tárházban tanúsítványok létrehozásához, az ujjlenyomat kiszámításához stb.
