---
title: Tanúsítvány-hitelesítő adatokat az Azure ad-ben |} A Microsoft Docs
description: Ez a cikk ismerteti a regisztráció és a tanúsítvány hitelesítő adatokat használjanak a kérelem hitelesítés
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 88f0c64a-25f7-4974-aca2-2acadc9acbd8
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: celested
ms.reviewer: nacanuma, jmprieur
ms.custom: aaddev
ms.openlocfilehash: d9e877fd648c28564f5eccc46f9c20741fe446f8
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/26/2019
ms.locfileid: "55076315"
---
# <a name="certificate-credentials-for-application-authentication"></a>Alkalmazás-hitelesítési tanúsítvány hitelesítő adatai

Az Azure Active Directory (Azure AD) lehetővé teszi, hogy az alkalmazás saját hitelesítő adatait használja a hitelesítéshez, például az OAuth 2.0-s ügyfélhitelesítő adatok flow-ban ([1.0-s verziójú](v1-oauth2-client-creds-grant-flow.md), [v2.0](v2-oauth2-client-creds-grant-flow.md)) és az On-meghatalmazásos a folyamat ([1.0-s verziójú](v1-oauth2-on-behalf-of-flow.md), [v2.0](v2-oauth2-on-behalf-of-flow.md)).

A hitelesítő adatok, amelyek egy alkalmazás használhatja a hitelesítéshez egy űrlap egy JSON webes Token(JWT) helyességi feltétel, amely az alkalmazás tulajdonosa tanúsítvánnyal aláírt.

## <a name="assertion-format"></a>Helyességi feltétel formátum
A helyességi feltétel számítási, használhatja egy a többhöz [JSON Web Token](https://jwt.ms/) kódtárak a választott nyelven. Az adatokat a token által a következők:

### <a name="header"></a>Fejléc

| Paraméter |  Megjegyzés |
| --- | --- |
| `alg` | Meg kell **RS256** |
| `typ` | Meg kell **JWT** |
| `x5t` | X.509 tanúsítvány SHA-1 ujjlenyomatnak kell lennie |

### <a name="claims-payload"></a>Jogcímek (tartalom)

| Paraméter |  Megjegyzések |
| --- | --- |
| `aud` | Célközönség: Meg kell  **https://login.microsoftonline.com/ *tenant_Id*  /oauth2/jogkivonat** |
| `exp` | Lejárati dátum: a dátum, amikor a jogkivonat érvényessége lejár. Az idő 1970. január 1. a jelenik meg a másodpercek számát (1970-01-01T0:0:0Z) (UTC), amíg a token érvényessége lejár.|
| `iss` | Kiállító: a client_id (az ügyfél szolgáltatás alkalmazás azonosítója) kell lennie. |
| `jti` | GUID-ja: a JWT azonosító |
| `nbf` | Nem előtt: az a dátum előtt, amely a token nem használható. Az idő 1970. január 1. a jelenik meg a másodpercek számát (1970-01-01T0:0:0Z) (UTC), amíg a token lett kiállítva. |
| `sub` | Tárgy: Megegyezik a `iss`, kell lennie a client_id (az ügyfél szolgáltatás alkalmazás azonosítója) |

### <a name="signature"></a>Aláírás

Az aláírás számított a tanúsítvány alkalmazása leírtak szerint a [JSON Web Token RFC7519 specifikáció](https://tools.ietf.org/html/rfc7519)

## <a name="example-of-a-decoded-jwt-assertion"></a>A dekódolt JWT-előfeltétel példa

```
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

## <a name="example-of-an-encoded-jwt-assertion"></a>Egy kódolt JWT-előfeltétel példa

A következő karakterláncot a kódolt helyességi feltétel példája. Ha körültekintően, megfigyelheti, hogy ponttal (.) elválasztott három szakaszra osztható:
* Az első szakaszban kódolja a fejléc
* A második szakasz kódolja a hasznos adat
* Az utolsó szakaszban a számított a tanúsítványokat az első két szakasz tartalmát az aláírás

```
"eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJhdWQiOiJodHRwczpcL1wvbG9naW4ubWljcm9zb2Z0b25saW5lLmNvbVwvam1wcmlldXJob3RtYWlsLm9ubWljcm9zb2Z0LmNvbVwvb2F1dGgyXC90b2tlbiIsImV4cCI6MTQ4NDU5MzM0MSwiaXNzIjoiOTdlMGE1YjctZDc0NS00MGI2LTk0ZmUtNWY3N2QzNWM2ZTA1IiwianRpIjoiMjJiM2JiMjYtZTA0Ni00MmRmLTljOTYtNjVkYmQ3MmMxYzgxIiwibmJmIjoxNDg0NTkyNzQxLCJzdWIiOiI5N2UwYTViNy1kNzQ1LTQwYjYtOTRmZS01Zjc3ZDM1YzZlMDUifQ.
Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

## <a name="register-your-certificate-with-azure-ad"></a>A tanúsítvány regisztrálása az Azure ad-vel

A tanúsítványalapú hitelesítő adatot is társíthat az ügyfélalkalmazás az Azure ad-ben az Azure Portalon az alábbi módszerek bármelyikével:

### <a name="uploading-the-certificate-file"></a>A tanúsítvány-fájl feltöltése

Az Azure-alkalmazás regisztrációja az ügyfélalkalmazás:
1. Válassza ki **beállítások > kulcsok** majd **nyilvános kulcs feltöltése**. 
2. Válassza ki a feltöltendő tanúsítványfájlt.
3. Kattintson a **Mentés** gombra. 
   
   Miután menti, a tanúsítvány feltöltése, és az ujjlenyomat, a kezdő dátum és a lejárati értékek jelennek meg. 

### <a name="updating-the-application-manifest"></a>Az alkalmazásjegyzék frissítése

Tanúsítvány céllal rendelkező, kell számítási:

- `$base64Thumbprint`, azaz a base64 kódolást, a tanúsítvány kivonata
- `$base64Value`, azaz a base64 kódolás a tanúsítvány nyers adatok

Emellett meg kell adnia az alkalmazásjegyzékben-kulcs azonosításához, egy GUID Azonosítót (`$keyId`).

Az Azure-alkalmazás regisztrációja az ügyfélalkalmazás:
1. Nyissa meg az alkalmazásjegyzékben.
2. Cserélje le a *keyCredentials* tulajdonság, amely az új tanúsítvány adatait a következő séma használatával.

   ```
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
3. Mentse a módosításokat az alkalmazásjegyzékben, és a jegyzék majd feltöltése az Azure ad-hez. 

   A `keyCredentials` tulajdonság többértékű adatelemeket, így kifinomultabb kezelésének több tanúsítványt tölthet fel.
   
## <a name="code-sample"></a>Kódminta

A mintakód a [tanúsítványok démon alkalmazások az Azure ad hitelesítése](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential) bemutatja, hogyan alkalmazás használja a hitelesítéshez saját hitelesítő adatait. Azt is bemutatja, hogyan zajlik [hozzon létre egy önaláírt tanúsítványt](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential#create-a-self-signed-certificate) használatával a `New-SelfSignedCertificate` Powershell-parancsot. Is előnyeit, és használja a [alkalmazás létrehozási parancsfájlok](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential/blob/master/AppCreationScripts/AppCreationScripts.md) tanúsítványok létrehozása, az ujjlenyomat számítási és így tovább.
