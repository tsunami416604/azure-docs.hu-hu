---
title: Microsoft-identitásplatform-tanúsítvány hitelesítő adatai
titleSuffix: Microsoft identity platform
description: Ez a cikk az alkalmazáshitelesítéshez szükséges tanúsítványhitelesítő adatok regisztrációját és használatát ismerteti.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 88f0c64a-25f7-4974-aca2-2acadc9acbd8
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: ryanwi
ms.reviewer: nacanuma, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 26030c12d98d796ceb1f66f198aede6e40eebd94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399018"
---
# <a name="microsoft-identity-platform-application-authentication-certificate-credentials"></a>Microsoft identity platformalkalmazás hitelesítési tanúsítványának hitelesítő adatai

A Microsoft identity platform lehetővé teszi, hogy egy alkalmazás saját hitelesítő adatait használja a hitelesítéshez, például az [OAuth 2.0 ügyfélhitelesítő adatok megadása flowv2.0](v2-oauth2-client-creds-grant-flow.md) és a [Kapcsolati folyamat ) .](v2-oauth2-on-behalf-of-flow.md)

Az alkalmazás által hitelesítéshez használható hitelesítő adatok egyik formája egy JSON webtoken(JWT) tanúsítványlal aláírt, az alkalmazás tulajdonában lévő tanúsítványsal aláírt feltétel.

## <a name="assertion-format"></a>Helyességi formátum
Microsoft-identitásplatform A helyesség kiszámításához használhatja a sok [JSON](https://jwt.ms/) webtoken-kódtárat az Ön által választott nyelven. A token által szállított információk a következők:

### <a name="header"></a>Fejléc

| Paraméter |  Megjegyzés |
| --- | --- |
| `alg` | Kell **RS256** |
| `typ` | Kell **JWT** |
| `x5t` | Az X.509 Tanúsítvány SHA-1 ujjlenyomatának kell lennie |

### <a name="claims-payload"></a>Követelések (hasznos teher)

| Paraméter |  Megjegyzések |
| --- | --- |
| `aud` | Közönség: Meg kell ** https://login.microsoftonline.com/ *tenant_Id*/oauth2/token** |
| `exp` | Lejárati dátum: a dátum, amikor a token lejár. Az idő az 1970.|
| `iss` | Kiállító: az client_id (az ügyfélszolgáltatás alkalmazásazonosítója) |
| `jti` | GUID: a JWT-azonosító |
| `nbf` | Nincs előtte: az a dátum, amely előtt a token nem használható. Az idő az 1970. |
| `sub` | Tárgy: A `iss`, kell a client_id (alkalmazásazonosítóaz ügyfélszolgáltatás) |

### <a name="signature"></a>Aláírás

Az aláírás kiszámítása a [JSON Web Token RFC7519 specifikációban](https://tools.ietf.org/html/rfc7519) leírtak szerint történik

## <a name="example-of-a-decoded-jwt-assertion"></a>Példa dekódolt JWT-állításra

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

## <a name="example-of-an-encoded-jwt-assertion"></a>Példa kódolt JWT-állításra

A következő karakterlánc egy példa a kódolt helyességi feltételre. Ha figyelmesen megnézed, három, pontokkal elválasztott szakaszt észlelsz(.)
* Az első szakasz kódolja a fejlécet
* A második szakasz kódolja a hasznos
* Az utolsó szakasz az első két szakasz tartalmából származó tanúsítványokkal kiszámított aláírás.

```
"eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJhdWQiOiJodHRwczpcL1wvbG9naW4ubWljcm9zb2Z0b25saW5lLmNvbVwvam1wcmlldXJob3RtYWlsLm9ubWljcm9zb2Z0LmNvbVwvb2F1dGgyXC90b2tlbiIsImV4cCI6MTQ4NDU5MzM0MSwiaXNzIjoiOTdlMGE1YjctZDc0NS00MGI2LTk0ZmUtNWY3N2QzNWM2ZTA1IiwianRpIjoiMjJiM2JiMjYtZTA0Ni00MmRmLTljOTYtNjVkYmQ3MmMxYzgxIiwibmJmIjoxNDg0NTkyNzQxLCJzdWIiOiI5N2UwYTViNy1kNzQ1LTQwYjYtOTRmZS01Zjc3ZDM1YzZlMDUifQ.
Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

## <a name="register-your-certificate-with-microsoft-identity-platform"></a>Tanúsítvány regisztrálása a Microsoft identity platformmal

A tanúsítványhitelesítő adatokat az ügyfélalkalmazáshoz társíthatja a Microsoft identity platformon az Azure Portalon keresztül az alábbi módszerek bármelyikével:

### <a name="uploading-the-certificate-file"></a>A tanúsítványfájl feltöltése

Az Ügyfélalkalmazás Azure-alkalmazásregisztrációjában:
1. Válassza **a Tanúsítványok & titkos kulcsok lehetőséget.**
2. Kattintson a **Tanúsítvány feltöltése** elemre, és válassza ki a feltöltendő tanúsítványfájlt.
3. Kattintson a **Hozzáadás** gombra.
  A tanúsítvány feltöltése után megjelennek az ujjlenyomat, a kezdési dátum és a lejárati értékek.

### <a name="updating-the-application-manifest"></a>Az alkalmazásjegyzék frissítése

Miután rendelkezik egy tanúsítvánnyal, ki kell számítania a következőket:

- `$base64Thumbprint`, amely a tanúsítvány kivonatának base64 kódolása
- `$base64Value`, amely a tanúsítvány nyers adatainak base64 kódolása

Az alkalmazásjegyzékben (`$keyId`) lévő kulcs azonosításához guid azonosítót is meg kell adnia.

Az Ügyfélalkalmazás Azure-alkalmazásregisztrációjában:
1. Az alkalmazásjegyzék megnyitásához válassza a **Jegyzékfájl** lehetőséget.
2. Cserélje le a *keyCredentials tulajdonságot* az új tanúsítványadatokra a következő séma használatával.

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
3. Mentse a módosításokat az alkalmazásjegyzékbe, majd töltse fel a jegyzékfájlt a Microsoft identity platformra.

   A `keyCredentials` tulajdonság többértékű, így több tanúsítványt is feltölthet a gazdagabb kulcskezeléshez.

## <a name="code-sample"></a>Kódminta

> [!NOTE]
> Az X5T-fejlécet úgy kell kiszámítania, hogy a tanúsítvány kivonatával 64-es alapkarakterláncra konvertálja. A C# mezőben ezt `System.Convert.ToBase64String(cert.GetCertHash());`a kódot a .

A [Microsoft identity platformot használó .NET Core démonkonzolalkalmazás](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) kódmintája bemutatja, hogyan használja az alkalmazás a saját hitelesítő adatait a hitelesítéshez. Azt is bemutatja, hogyan hozhat létre `New-SelfSignedCertificate` [önaláírt tanúsítványt](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/tree/master/1-Call-MSGraph#optional-use-the-automation-script) a Powershell paranccsal. Azt is kihasználhatja, és használja az [alkalmazás létrehozása parancsfájlok](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/master/1-Call-MSGraph/AppCreationScripts-withCert/AppCreationScripts.md) a tanúsítványok létrehozásához, kiszámítja az ujjlenyomatot, és így tovább.
