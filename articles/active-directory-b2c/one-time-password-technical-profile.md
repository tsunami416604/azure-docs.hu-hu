---
title: Egyszeri jelszó-ellenőrzés engedélyezése
titleSuffix: Azure AD B2C
description: Ismerje meg, hogyan állíthat be egy egyszeri jelszó (OTP) forgatókönyv et az Azure AD B2C egyéni szabályzatok használatával.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bd5fed45332c73c633db1137bdc23aea66fd3403
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332781"
---
# <a name="define-a-one-time-password-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Egyszeri jelszótechnikai profil definiálása az Azure AD B2C egyéni szabályzatában

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Az Azure Active Directory B2C (Azure AD B2C) támogatja az egyszeri jelszó létrehozásának és ellenőrzésének kezelését. Egy technikai profil segítségével hozzon létre egy kódot, majd ellenőrizze, hogy a kód később.

Az egyszeri jelszó technikai profil is visszaadhat egy hibaüzenetet a kód ellenőrzés során. Tervezze meg az integrációt az egyszeri jelszóval **egy érvényesítési technikai profil**használatával. Az érvényesítési technikai profil meghívja az egyszeri jelszó technikai profilt a kód ellenőrzéséhez. Az érvényesítési technikai profil ellenőrzi a felhasználó által megadott adatokat, mielőtt a felhasználói út folytatódik. Az érvényesítési technikai profil egy hibaüzenet jelenik meg egy önérvényesítő oldalon.

## <a name="protocol"></a>Protocol (Protokoll)

A **Protokoll** elem **Name** attribútumát a `Proprietary`parancsra kell állítani. A **kezelő** attribútumnak tartalmaznia kell az Azure AD B2C által használt protokollkezelő szerelvény teljesen minősített nevét:

```XML
Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

A következő példa egy egyszeri jelszótechnikai profilt mutat be:

```XML
<TechnicalProfile Id="VerifyCode">
  <DisplayName>Validate user input verification code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="generate-code"></a>Kód létrehozása

A technikai profil első módja egy kód létrehozása. Az alábbiakban az ehhez a módhoz konfigurálható beállításokat talál.

### <a name="input-claims"></a>Bemeneti jogcímek

Az **InputClaims** elem az egyszeri jelszóprotokoll-szolgáltatónak való küldéshez szükséges jogcímek listáját tartalmazza. A jogcím nevét az alábbi névre is leképezheti.

| Jogcímhivatkozásazonosítója | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Azonosító | Igen | A kód későbbi ellenőrzésére szoruló felhasználó azonosítására szolgáló azonosító. Gyakran használják annak a célnak az azonosítójaként, ahová a kódot kézbesítik, például e-mail cím vagy telefonszám. |

Az **InputClaimsTransformations** elem tartalmazhat **InputClaimsTransformation** elemek gyűjteményét, amelyek a bemeneti jogcímek módosítására vagy újak létrehozására szolgálnak, mielőtt elküldenék az egyszeri jelszóprotokoll-szolgáltatónak.

### <a name="output-claims"></a>Kimeneti jogcímek

A **OutputClaims** elem az egyszeri jelszóprotokoll-szolgáltató által létrehozott jogcímek listáját tartalmazza. A jogcím nevét az alábbi névre is leképezheti.

| Jogcímhivatkozásazonosítója | Kötelező | Leírás |
| --------- | -------- | ----------- |
| otpGenerált | Igen | A létrehozott kód, amelynek munkamenetét az Azure AD B2C kezeli. |

A **OutputClaimsTransformations** elem tartalmazhat **OutputClaimsTransformations** elemek gyűjteményét, amelyek a kimeneti jogcímek módosítására vagy újak létrehozására szolgálnak.

### <a name="metadata"></a>Metaadatok

A kódlétrehozási mód konfigurálásához a következő beállítások használhatók:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| CodeExpirationInszszek | Nem | Idő másodpercben a kód lejáratáig. Minimum: `60`; Maximális: `1200`; Alapértelmezett: `600`. |
| Kódhossza | Nem | A kód hossza. Az alapértelmezett érték `6`. |
| Karakterkészlet | Nem | A kód karakterkészlete, reguláris kifejezésben való használatra formázva. Például: `a-z0-9A-Z`. Az alapértelmezett érték `0-9`. A karakterkészletnek legalább 10 különböző karaktert kell tartalmaznia a megadott készletben. |
| NumRetryAttempts | Nem | Az ellenőrzési kísérletek száma, mielőtt a kód érvénytelennek minősülne. Az alapértelmezett érték `5`. |
| Művelet | Igen | A végrehajtandó művelet. Lehetséges érték: `GenerateCode`. |
| ÚjrafelhasználásSameCode | Nem | Azt jelzi, hogy meg kell-e adni egy duplikált kódot, és nem kell-e új kódot generálni, ha az adott kód még nem járt le, és még mindig érvényes. Az alapértelmezett érték `false`. |

### <a name="example"></a>Példa

A következő `TechnicalProfile` példa kód létrehozásához használható:

```XML
<TechnicalProfile Id="GenerateCode">
  <DisplayName>Generate Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">GenerateCode</Item>
    <Item Key="CodeExpirationInSeconds">600</Item>
    <Item Key="CodeLength">6</Item>
    <Item Key="CharacterSet">0-9</Item>
    <Item Key="NumRetryAttempts">5</Item>
    <Item Key="ReuseSameCode">false</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="identifier" PartnerClaimType="identifier" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="otpGenerated" PartnerClaimType="otpGenerated" />
  </OutputClaims>
</TechnicalProfile>
```

## <a name="verify-code"></a>Kód ellenőrzése

A technikai profil második módja egy kód ellenőrzése. Az alábbiakban az ehhez a módhoz konfigurálható beállításokat talál.

### <a name="input-claims"></a>Bemeneti jogcímek

Az **InputClaims** elem az egyszeri jelszóprotokoll-szolgáltatónak való küldéshez szükséges jogcímek listáját tartalmazza. A jogcím nevét az alábbi névre is leképezheti.

| Jogcímhivatkozásazonosítója | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Azonosító | Igen | A korábban kódot létrehozó felhasználó azonosítására szolgáló azonosító. Gyakran használják annak a célnak az azonosítójaként, ahová a kódot kézbesítik, például e-mail cím vagy telefonszám. |
| otpToVerify | Igen | A felhasználó által megadott ellenőrző kód. |

Az **InputClaimsTransformations** elem tartalmazhat **InputClaimsTransformation** elemek gyűjteményét, amelyek a bemeneti jogcímek módosítására vagy újak létrehozására szolgálnak, mielőtt elküldenék az egyszeri jelszóprotokoll-szolgáltatónak.

### <a name="output-claims"></a>Kimeneti jogcímek

A protokollszolgáltató kódellenőrzése során nem biztosítottak kimeneti jogcímek.

A **OutputClaimsTransformations** elem tartalmazhat **OutputClaimsTransformations** elemek gyűjteményét, amelyek a kimeneti jogcímek módosítására vagy újak létrehozására szolgálnak.

### <a name="metadata"></a>Metaadatok

A következő beállítások használhatók a kódellenőrzési módban:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Művelet | Igen | A végrehajtandó művelet. Lehetséges érték: `VerifyCode`. |


### <a name="ui-elements"></a>Felhasználói felület elemei

A következő metaadatok segítségével konfigurálhatja a kódellenőrzési hiba esetén megjelenő hibaüzeneteket. A metaadatokat az önérvényesítő technikai profilban kell [konfigurálni.](self-asserted-technical-profile.md) A hibaüzenetek [honosíthatók](localization-string-ids.md#one-time-password-error-messages).

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| UserMessageIfSessionDoesnotExist | Nem | A kódellenőrzési munkamenet lejárta esetén a felhasználó nak megjelenítendő üzenet. Ez vagy a kód lejárt, vagy a kód soha nem jött létre egy adott azonosítót. |
| UserMessageIfMaxRetryAttempted | Nem | Az üzenet, amelyet akkor jelenít meg a felhasználónak, ha túllépte a maximálisan engedélyezett ellenőrzési kísérleteket. |
| UserMessageIfInvalidCode | Nem | Az üzenet, amelyet a felhasználónak meg kell jelenítenie, ha érvénytelen kódot adott meg. |
|UserMessageIfSessionConflict|Nem| A felhasználó nak megjelenítendő üzenet, ha a kód nem ellenőrizhető.|

### <a name="example"></a>Példa

A következő `TechnicalProfile` példa a kód ellenőrzésére szolgál:

```XML
<TechnicalProfile Id="VerifyCode">
  <DisplayName>Verify Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">VerifyCode</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="identifier" PartnerClaimType="identifier" />
    <InputClaim ClaimTypeReferenceId="otpGenerated" PartnerClaimType="otpToVerify" />
  </InputClaims>
</TechnicalProfile>
```

## <a name="next-steps"></a>További lépések

Lásd a következő cikket, például az egyszeri jelszó technikai profil egyéni e-mail ellenőrzés:

- [Egyéni e-mailek ellenőrzése az Azure Active Directory B2C-ben](custom-email.md)

