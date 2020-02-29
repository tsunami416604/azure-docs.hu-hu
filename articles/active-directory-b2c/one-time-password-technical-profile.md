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
ms.date: 02/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 701fb64dd85526bc79cab48bf36d4583da71ca76
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78184026"
---
# <a name="define-a-one-time-password-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Egyszeri jelszóval kapcsolatos technikai profil definiálása egy Azure AD B2C egyéni házirendben

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A Azure Active Directory B2C (Azure AD B2C) támogatást nyújt az egyszeri jelszavak létrehozásának és ellenőrzésének kezeléséhez. Használjon egy technikai profilt a kód létrehozásához, majd később ellenőrizze a kódot.

Az egyszeri jelszóval kapcsolatos technikai profil a kód ellenőrzése során hibaüzenetet is jelez. Tervezze meg az integrációt az egyszeri jelszóval egy **érvényesítési technikai profil**használatával. Az érvényesítési technikai profil a kód ellenőrzéséhez az egyszeri jelszó-technikai profilt hívja meg. Az érvényesítési technikai profil ellenőrzi a felhasználó által megadott, a felhasználói utazás előtt megjelenő adatmennyiséget. Az érvényesítési technikai profillal egy önérvényesített oldalon egy hibaüzenet jelenik meg.

## <a name="protocol"></a>Protokoll

A **protokoll** elem **Name** attribútumát `Proprietary`értékre kell állítani. A **kezelő** attribútumnak tartalmaznia kell az Azure ad B2C által használt protokollkezelő-szerelvény teljesen minősített nevét:

```XML
Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

Az alábbi példa egy egyszeri jelszóval rendelkező technikai profilt mutat be:

```XML
<TechnicalProfile Id="VerifyCode">
  <DisplayName>Validate user input verification code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="generate-code"></a>Kód létrehozása

Ennek a technikai profilnak az első módja egy kód létrehozása. Alább láthatók az ehhez a módhoz konfigurálható beállítások.

### <a name="input-claims"></a>Bemeneti jogcímek

A **szabályzattípushoz** elem az egyszeri jelszavas protokoll szolgáltatójának küldendő jogcímek listáját tartalmazza. A jogcím nevét a lent megadott névre is leképezheti.

| ClaimReferenceId | Szükséges | Leírás |
| --------- | -------- | ----------- |
| azonosító | Igen | Az azonosító annak a felhasználónak a azonosításához, akinek később ellenőriznie kell a kódot. Általában annak a célhelynek az azonosítója, ahol a kód kézbesítése történik, például az e-mail cím vagy a telefonszám. |

A **InputClaimsTransformations** elem olyan **InputClaimsTransformation** -elemek gyűjteményét is tartalmazhatja, amelyek a bemeneti jogcímek módosítására vagy újak létrehozására szolgálnak, mielőtt elküldené az egyszeri jelszavas protokoll szolgáltatójának.

### <a name="output-claims"></a>Kimeneti jogcímek

A **OutputClaims** elem az egyszeri jelszavas protokoll szolgáltatója által létrehozott jogcímek listáját tartalmazza. A jogcím nevét a lent megadott névre is leképezheti.

| ClaimReferenceId | Szükséges | Leírás |
| --------- | -------- | ----------- |
| otpGenerated | Igen | Az a generált kód, amelynek a munkamenetét Azure AD B2C kezeli. |

A **OutputClaimsTransformations** elem olyan **OutputClaimsTransformation** -elemek gyűjteményét is tartalmazhatja, amelyek a kimeneti jogcímek módosítására vagy újak előállítására szolgálnak.

### <a name="metadata"></a>Metaadatok

A kód generálásának és karbantartásának konfigurálásához a következő beállítások használhatók:

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| CodeExpirationInSeconds | Nem | A kód lejárati ideje másodpercben. Minimum: `60`; Maximális érték: `1200`; Alapértelmezett: `600`. |
| CodeLength | Nem | A kód hossza. Az alapértelmezett érték `6`. |
| CharacterSet | Nem | A kód karakterkészlete, amely normál kifejezésben való használatra van formázva. Például: `a-z0-9A-Z`. Az alapértelmezett érték `0-9`. A karakterkészletnek legalább 10 különböző karaktert kell tartalmaznia a megadott készletben. |
| NumRetryAttempts | Nem | Az ellenőrzési kísérletek száma, mielőtt a kód érvénytelennek minősül. Az alapértelmezett érték `5`. |
| Művelet | Igen | A végrehajtandó művelet. Lehetséges értékek: `GenerateCode`vagy `VerifyCode`. |
| ReuseSameCode | Nem | Azt határozza meg, hogy egy ismétlődő kód megadása helyett új kód generálását kell-e megadni, ha a megadott kód nem járt le, és még érvényes. Az alapértelmezett érték `false`. |

### <a name="returning-error-message"></a>Hibaüzenet küldése

A kód generálási módjához nem érkezett hibaüzenet.

### <a name="example"></a>Példa

A következő példa a kód generálására szolgál `TechnicalProfile`:

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

A technikai profil második módja egy kód ellenőrzése. Alább láthatók az ehhez a módhoz konfigurálható beállítások.

### <a name="input-claims"></a>Bemeneti jogcímek

A **szabályzattípushoz** elem az egyszeri jelszavas protokoll szolgáltatójának küldendő jogcímek listáját tartalmazza. A jogcím nevét a lent megadott névre is leképezheti.

| ClaimReferenceId | Szükséges | Leírás |
| --------- | -------- | ----------- |
| azonosító | Igen | Azon felhasználó azonosítására szolgáló azonosító, aki korábban létrehozta a kódot. Általában annak a célhelynek az azonosítója, ahol a kód kézbesítése történik, például az e-mail cím vagy a telefonszám. |
| otpToVerify | Igen | A felhasználó által megadott ellenőrző kód. |

A **InputClaimsTransformations** elem olyan **InputClaimsTransformation** -elemek gyűjteményét is tartalmazhatja, amelyek a bemeneti jogcímek módosítására vagy újak létrehozására szolgálnak, mielőtt elküldené az egyszeri jelszavas protokoll szolgáltatójának.

### <a name="output-claims"></a>Kimeneti jogcímek

Nincsenek megadva kimeneti jogcímek a protokoll-szolgáltató kódjának ellenőrzése során.

A **OutputClaimsTransformations** elem olyan **OutputClaimsTransformation** -elemek gyűjteményét is tartalmazhatja, amelyek a kimeneti jogcímek módosítására vagy újak előállítására szolgálnak.

### <a name="metadata"></a>Metaadatok

A következő beállításokkal konfigurálhatja a kód-ellenőrzési hiba esetén megjelenő hibaüzenetet:

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| UserMessageIfSessionDoesNotExist | Nem | A felhasználónak megjelenítendő üzenet, ha a kód-ellenőrzési munkamenet lejárt. Vagy a kód lejárt, vagy a kód soha nem lett létrehozva egy adott azonosítóhoz. |
| UserMessageIfMaxRetryAttempted | Nem | A felhasználónak megjelenítendő üzenet, ha túllépte a maximálisan engedélyezett ellenőrzési kísérleteket. |
| UserMessageIfInvalidCode | Nem | A felhasználónak megjelenítendő üzenet, ha érvénytelen kódot adott meg. |

### <a name="returning-error-message"></a>Hibaüzenet küldése

A [metaadatokban](#metadata)leírtaknak megfelelően testreszabhatja a felhasználó számára megjelenített hibaüzenetet különböző hibák esetén. Az üzenetek további lokalizálása a területi beállítás előjavításával végezhető el, például:

```XML
<Item Key="en.UserMessageIfInvalidCode">Wrong code has been entered.</Item>
```

### <a name="example"></a>Példa

A következő példa a kód ellenőrzéséhez használható `TechnicalProfile`:

```XML
<TechnicalProfile Id="VerifyCode">
    <DisplayName>Verify Code</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <Metadata>
        <Item Key="Operation">VerifyCode</Item>
        <Item Key="UserMessageIfInvalidCode">Wrong code has been entered.</Item>
        <Item Key="UserMessageIfSessionDoesNotExist">Code has expired.</Item>
        <Item Key="UserMessageIfMaxRetryAttempted">You've tried too many times.</Item>
    </Metadata>
    <InputClaims>
        <InputClaim ClaimTypeReferenceId="identifier" PartnerClaimType="identifier" />
        <InputClaim ClaimTypeReferenceId="otpGenerated" PartnerClaimType="otpToVerify" />
    </InputClaims>
</TechnicalProfile>
```

## <a name="next-steps"></a>További lépések

A következő cikkből megtudhatja, hogyan használhatja az egyéni e-mail-ellenőrzéssel rendelkező egyszer használatos jelszó-ellenőrző profilt:

- [Egyéni e-mail-ellenőrzés Azure Active Directory B2C](custom-email.md)

