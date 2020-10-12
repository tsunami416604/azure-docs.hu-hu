---
title: Egyszeri jelszó (OTP) hitelesítésének engedélyezése
titleSuffix: Azure AD B2C
description: Ismerje meg, hogyan állíthat be egyszeri jelszavas (OTP) forgatókönyvet Azure AD B2C egyéni szabályzatok használatával.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/02/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6b0a90eee4a1bd309a04cf355eb8d8c0564830aa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "89418908"
---
# <a name="define-a-one-time-password-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Egyszeri jelszóval kapcsolatos technikai profil definiálása egy Azure AD B2C egyéni házirendben

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A Azure Active Directory B2C (Azure AD B2C) támogatást nyújt az egyszeri jelszavak létrehozásának és ellenőrzésének kezeléséhez. Használjon egy technikai profilt a kód létrehozásához, majd később ellenőrizze a kódot.

Az egyszeri jelszóval kapcsolatos technikai profil a kód ellenőrzése során hibaüzenetet is jelez. Tervezze meg az integrációt az egyszeri jelszóval egy **érvényesítési technikai profil**használatával. Az érvényesítési technikai profil a kód ellenőrzéséhez az egyszeri jelszó-technikai profilt hívja meg. Az érvényesítési technikai profil ellenőrzi a felhasználó által megadott, a felhasználói utazás előtt megjelenő adatmennyiséget. Az érvényesítési technikai profillal egy önérvényesített oldalon egy hibaüzenet jelenik meg.

## <a name="protocol"></a>Protokoll

A **protokoll** elem **Name** attribútumát be kell állítani `Proprietary` . A **kezelő** attribútumnak tartalmaznia kell az Azure ad B2C által használt protokollkezelő-szerelvény teljesen minősített nevét:

```xml
Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

Az alábbi példa egy egyszeri jelszóval rendelkező technikai profilt mutat be:

```xml
<TechnicalProfile Id="VerifyCode">
  <DisplayName>Validate user input verification code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="generate-code"></a>Kód létrehozása

Ennek a technikai profilnak az első módja egy kód létrehozása. Alább láthatók az ehhez a módhoz konfigurálható beállítások.

### <a name="input-claims"></a>Bemeneti jogcímek

A **szabályzattípushoz** elem az egyszeri jelszavas protokoll szolgáltatójának küldendő jogcímek listáját tartalmazza. A jogcím nevét a lent megadott névre is leképezheti.

| ClaimReferenceId | Kötelező | Leírás |
| --------- | -------- | ----------- |
| azonosító | Igen | Az azonosító annak a felhasználónak a azonosításához, akinek később ellenőriznie kell a kódot. Általában annak a célhelynek az azonosítója, ahol a kód kézbesítése történik, például az e-mail cím vagy a telefonszám. |

A **InputClaimsTransformations** elem olyan **InputClaimsTransformation** -elemek gyűjteményét is tartalmazhatja, amelyek a bemeneti jogcímek módosítására vagy újak létrehozására szolgálnak, mielőtt elküldené az egyszeri jelszavas protokoll szolgáltatójának.

### <a name="output-claims"></a>Kimeneti jogcímek

A **OutputClaims** elem az egyszeri jelszavas protokoll szolgáltatója által létrehozott jogcímek listáját tartalmazza. A jogcím nevét a lent megadott névre is leképezheti.

| ClaimReferenceId | Kötelező | Leírás |
| --------- | -------- | ----------- |
| otpGenerated | Igen | Az a generált kód, amelynek a munkamenetét Azure AD B2C kezeli. |

A **OutputClaimsTransformations** elem olyan **OutputClaimsTransformation** -elemek gyűjteményét is tartalmazhatja, amelyek a kimeneti jogcímek módosítására vagy újak előállítására szolgálnak.

### <a name="metadata"></a>Metaadatok

A kód generálási módjának konfigurálásához a következő beállításokat használhatja:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| CodeExpirationInSeconds | Nem | A kód lejárati ideje másodpercben. Minimum: `60` ; Maximum: `1200` ; Alapértelmezett: `600` . |
| CodeLength | Nem | A kód hossza. Az alapértelmezett érték `6`. |
| CharacterSet | Nem | A kód karakterkészlete, amely normál kifejezésben való használatra van formázva. Például: `a-z0-9A-Z`. Az alapértelmezett érték `0-9`. A karakterkészletnek legalább 10 különböző karaktert kell tartalmaznia a megadott készletben. |
| NumRetryAttempts | Nem | Az ellenőrzési kísérletek száma, mielőtt a kód érvénytelennek minősül. Az alapértelmezett érték `5`. |
| NumCodeGenerationAttempts | Nem | A kód generálására irányuló kísérletek maximális száma azonosító alapján. Ha nincs megadva, az alapértelmezett érték 10. |
| Művelet | Igen | A végrehajtandó művelet. Lehetséges érték: `GenerateCode` . |
| ReuseSameCode | Nem | Azt határozza meg, hogy egy ismétlődő kód megadása helyett új kód generálását kell-e megadni, ha a megadott kód nem járt le, és még érvényes. Az alapértelmezett érték `false`. |

### <a name="example"></a>Példa

A következő példa a `TechnicalProfile` kód generálására szolgál:

```xml
<TechnicalProfile Id="GenerateCode">
  <DisplayName>Generate Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">GenerateCode</Item>
    <Item Key="CodeExpirationInSeconds">600</Item>
    <Item Key="CodeLength">6</Item>
    <Item Key="CharacterSet">0-9</Item>
    <Item Key="NumRetryAttempts">5</Item>
    <Item Key="NumCodeGenerationAttempts">15</Item>
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

A technikai profil második módja egy kód ellenőrzése. Alább láthatók az ehhez a módhoz konfigurálható beállítások.

### <a name="input-claims"></a>Bemeneti jogcímek

A **szabályzattípushoz** elem az egyszeri jelszavas protokoll szolgáltatójának küldendő jogcímek listáját tartalmazza. A jogcím nevét a lent megadott névre is leképezheti.

| ClaimReferenceId | Kötelező | Leírás |
| --------- | -------- | ----------- |
| azonosító | Igen | Azon felhasználó azonosítására szolgáló azonosító, aki korábban létrehozta a kódot. Általában annak a célhelynek az azonosítója, ahol a kód kézbesítése történik, például az e-mail cím vagy a telefonszám. |
| otpToVerify | Igen | A felhasználó által megadott ellenőrző kód. |

A **InputClaimsTransformations** elem olyan **InputClaimsTransformation** -elemek gyűjteményét is tartalmazhatja, amelyek a bemeneti jogcímek módosítására vagy újak létrehozására szolgálnak, mielőtt elküldené az egyszeri jelszavas protokoll szolgáltatójának.

### <a name="output-claims"></a>Kimeneti jogcímek

Nincsenek megadva kimeneti jogcímek a protokoll-szolgáltató kódjának ellenőrzése során.

A **OutputClaimsTransformations** elem olyan **OutputClaimsTransformation** -elemek gyűjteményét is tartalmazhatja, amelyek a kimeneti jogcímek módosítására vagy újak előállítására szolgálnak.

### <a name="metadata"></a>Metaadatok

A következő beállítások használhatók a kód-ellenőrzési mód használatára:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Művelet | Igen | A végrehajtandó művelet. Lehetséges érték: `VerifyCode` . |


### <a name="ui-elements"></a>Felhasználói felület elemei

A következő metaadatokkal konfigurálhatja a kód-ellenőrzési hiba esetén megjelenő hibaüzeneteket. A metaadatokat az [önérvényesített](self-asserted-technical-profile.md) technikai profilban kell konfigurálni. A hibaüzenetek [honosítható](localization-string-ids.md#one-time-password-error-messages).

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| UserMessageIfSessionDoesNotExist | Nem | A felhasználónak megjelenítendő üzenet, ha a kód-ellenőrzési munkamenet lejárt. Vagy a kód lejárt, vagy a kód soha nem lett létrehozva egy adott azonosítóhoz. |
| UserMessageIfMaxRetryAttempted | Nem | A felhasználónak megjelenítendő üzenet, ha túllépte a maximálisan engedélyezett ellenőrzési kísérleteket. |
| UserMessageIfMaxNumberOfCodeGenerated | Nem | A felhasználónak megjelenítendő üzenet, ha a kód létrehozása túllépte a kísérletek maximálisan megengedett számát. |
| UserMessageIfInvalidCode | Nem | A felhasználónak megjelenítendő üzenet, ha érvénytelen kódot adott meg. |
| UserMessageIfVerificationFailedRetryAllowed | Nem | A felhasználónak megjelenítendő üzenet, ha érvénytelen kódot adott meg, és a felhasználó megadhatja a megfelelő kódot.  |
|UserMessageIfSessionConflict|Nem| A felhasználónak megjelenítendő üzenet, ha a kód nem ellenőrizhető.|

### <a name="example"></a>Példa

A következő példa `TechnicalProfile` egy kód ellenőrzéséhez használható:

```xml
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

## <a name="next-steps"></a>Következő lépések

A következő cikkből megtudhatja, hogyan használhatja az egyéni e-mail-ellenőrzéssel rendelkező egyszeri jelszavas technikai profilt:

- Egyéni e-mail-ellenőrzés a Azure Active Directory B2Cban ([mailjet](custom-email-mailjet.md), [SendGrid](custom-email-sendgrid.md))

