---
title: Az Azure AD-hitelesítő tanúsítványt |} Microsoft Docs
description: Ez a cikk ismerteti a regisztrációs és az alkalmazás-hitelesítési tanúsítvány hitelesítő adatok használatát
services: active-directory
documentationcenter: .net
author: navyasric
manager: mtillman
editor: ''
ms.assetid: 88f0c64a-25f7-4974-aca2-2acadc9acbd8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2018
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: f7c58b4ebd840aca555b52a03cf44ace311b64e3
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
# <a name="certificate-credentials-for-application-authentication"></a>Alkalmazás-hitelesítési tanúsítvány hitelesítő adatai

Az Azure Active Directory lehetővé teszi az saját hitelesítő adatait használja a hitelesítéshez. Például az OAuth 2.0 ügyfél hitelesítő adatok Grant flow a ([v1](active-directory-protocols-oauth-service-to-service.md), [v2](active-directory-v2-protocols-oauth-client-creds.md)) és az On-meghatalmazásos folyamat ([v1](active-directory-protocols-oauth-on-behalf-of.md), [v2](active-directory-v2-protocols-oauth-on-behalf-of.md)).
A hitelesítő adatok, használhat egy formátuma egy JSON webes Token(JWT) helyességi feltételt, amely az alkalmazás tulajdonosa a tanúsítvánnyal aláírt.

## <a name="format-of-the-assertion"></a>A helyességi feltétel formátuma
A helyességi feltétel kiszámításához, érdemes lehet a több valamelyikével [JSON Web Token](https://jwt.ms/) szalagtárak a választott nyelven történik. A token által az információkat a következő:

#### <a name="header"></a>Fejléc

| Paraméter |  Megjegyzés |
| --- | --- |
| `alg` | Meg kell **RS256** |
| `typ` | Meg kell **jwt-t** |
| `x5t` | Az X.509 tanúsítvány SHA-1 ujjlenyomat kell lennie. |

#### <a name="claims-payload"></a>Claims (Payload)

| Paraméter |  Megjegyzés |
| --- | --- |
| `aud` | A célközönség: Kell  **https://login.microsoftonline.com/ *tenant_Id*  /oauth2/token** |
| `exp` | Lejárat dátuma: a dátum, amikor a jogkivonat érvényessége lejár. Az idő másodpercben 1970. január 1. a ki (1970-01-01T0:0:0Z) UTC, amíg a token érvényességi lejárati idejének.|
| `iss` | Kibocsátó: a client_id (az ügyfélszolgáltatás Alkalmazásazonosító) kell lennie. |
| `jti` | GUID-ja: a JWT azonosító |
| `nbf` | Hatálybalépési idő: az a dátum előtt, amely a token nem használható. Az idő másodpercben 1970. január 1. a ki (1970-01-01T0:0:0Z) UTC, amíg a token lett kiállítva. |
| `sub` | Tulajdonos: megegyezik a `iss`, a client_id (az ügyfélszolgáltatás Alkalmazásazonosító) kell lennie. |

#### <a name="signature"></a>Aláírás

Az aláírás alkalmazása a tanúsítványt, a számított a [JSON Web Token RFC7519 meghatározása](https://tools.ietf.org/html/rfc7519)

### <a name="example-of-a-decoded-jwt-assertion"></a>A dekódolt JWT helyességi feltétel – példa

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

### <a name="example-of-an-encoded-jwt-assertion"></a>Egy kódolt JWT helyességi feltétel – példa

A következő karakterlánc kódolt helyességi feltétel példája. Gondosan tekintse meg a három pont (.) választja el szakasz figyelje meg.
Az első szakaszban kódolja a fejlécet, a második, a tartalom, és az utolsó a tanúsítványokat az első két szakaszok tartalmának aláírással.
```
"eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJhdWQiOiJodHRwczpcL1wvbG9naW4ubWljcm9zb2Z0b25saW5lLmNvbVwvam1wcmlldXJob3RtYWlsLm9ubWljcm9zb2Z0LmNvbVwvb2F1dGgyXC90b2tlbiIsImV4cCI6MTQ4NDU5MzM0MSwiaXNzIjoiOTdlMGE1YjctZDc0NS00MGI2LTk0ZmUtNWY3N2QzNWM2ZTA1IiwianRpIjoiMjJiM2JiMjYtZTA0Ni00MmRmLTljOTYtNjVkYmQ3MmMxYzgxIiwibmJmIjoxNDg0NTkyNzQxLCJzdWIiOiI5N2UwYTViNy1kNzQ1LTQwYjYtOTRmZS01Zjc3ZDM1YzZlMDUifQ.
Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

### <a name="register-your-certificate-with-azure-ad"></a>A tanúsítvány regisztrálása az Azure AD-val

Az ügyfélalkalmazás az Azure AD a következő módszereket az Azure portálon keresztül is társíthat a tanúsítvány hitelesítő adat:

**A tanúsítványfájl feltöltését**

Az ügyfélalkalmazást Azure-alkalmazás regisztrációja, kattintson a **beállítások**, kattintson a **kulcsok** majd **nyilvános kulcs feltöltése**. Válassza ki a tanúsítványfájl feltöltését, és kattintson a kívánt **mentése**. Menti a tanúsítvány feltöltése és az ujjlenyomat kezdetének és lejár értékek jelennek meg. 

**Az alkalmazás jegyzékében frissítése**

Tanúsítvány céllal rendelkezik, kell számítási:

- `$base64Thumbprint`, vagyis a base64 kódolás a tanúsítvány kivonata
- `$base64Value`, vagyis a base64 kódolása nyers Tanúsítványadatok

Is meg kell adnia a kulcs az alkalmazásjegyzékben szereplő azonosító egy GUID (`$keyId`).

Az ügyfélalkalmazást Azure-alkalmazás regisztrációja, nyissa meg az alkalmazás jegyzékében, és cserélje le a *keyCredentials* tulajdonságot, amelyben az új tanúsítvány adatait a következő séma:

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

Mentse a módosításokat az alkalmazás jegyzékében, és töltse fel az Azure AD. A keyCredentials tulajdonság többértékű, így előfordulhat, hogy több kulcskezelési több tanúsítványok feltöltése.
