---
title: Az Azure MFA technikai profiljai az egyéni házirendekben
titleSuffix: Azure AD B2C
description: Egyéni házirend-referenciák az Azure Multi-Factor Authentication (MFA) technikai profiljaihoz Azure AD B2Cban.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: a8aaea6b2afb4d89e6e667edba0eeba2f4ddcca8
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75480215"
---
# <a name="define-an-azure-mfa-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Azure MFA technikai profil definiálása egy Azure AD B2C egyéni szabályzatban

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A Azure Active Directory B2C (Azure AD B2C) támogatást nyújt a telefonszámok Azure Multi-Factor Authentication (MFA) használatával történő ellenőrzéséhez. Ezzel a technikai profillal kód készíthető és küldhető egy telefonszámra, majd ellenőrizheti a kódot.

Az Azure MFA technikai profilja szintén hibaüzenetet adhat vissza. Az Azure MFA-nal való integrációt **érvényesítési technikai profil**segítségével alakíthatja ki. Az érvényesítési műszaki profil az Azure MFA szolgáltatást hívja meg. Az érvényesítési technikai profil ellenőrzi a felhasználó által megadott, a felhasználói utazás előtt megjelenő adatmennyiséget. Az érvényesítési technikai profillal egy önérvényesített oldalon egy hibaüzenet jelenik meg.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="protocol"></a>Protocol (Protokoll)

A **protokoll** elem **Name** attribútumát `Proprietary`értékre kell állítani. A **kezelő** attribútumnak tartalmaznia kell az Azure ad B2C által használt protokollkezelő-szerelvény teljesen minősített nevét:

```
Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

Az alábbi példa egy Azure MFA technikai profilt mutat be:

```XML
<TechnicalProfile Id="AzureMfa-SendSms">
    <DisplayName>Send Sms</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    ...
```

## <a name="send-sms"></a>SMS küldése

Ennek a technikai profilnak az első módja egy kód létrehozása és elküldése. Ehhez a módhoz a következő beállítások konfigurálhatók.

### <a name="input-claims"></a>Bemeneti jogcímek

A **szabályzattípushoz** elem tartalmazza az Azure MFA számára küldendő jogcímek listáját. A jogcím nevét a MFA technikai profiljában definiált névre is leképezheti.

| ClaimReferenceId | Szükséges | Leírás |
| --------- | -------- | ----------- |
| userPrincipalName | Igen | A telefonszámot birtokló felhasználó azonosítója. |
| Telefonszám | Igen | Az SMS-kód küldésére szolgáló telefonszám. |
| CompanyName | Nem |A vállalat neve az SMS-ben. Ha nincs megadva, a rendszer az alkalmazás nevét használja. |
| locale | Nem | Az SMS területi beállítása. Ha nincs megadva, a rendszer a felhasználó böngésző területi beállítását használja. |

A **InputClaimsTransformations** elem olyan **InputClaimsTransformation** -elemek gyűjteményét is tartalmazhatja, amelyek a bemeneti jogcímek módosítására vagy újak előállítására szolgálnak az Azure MFA szolgáltatásba való küldés előtt.

### <a name="output-claims"></a>Kimeneti jogcímek

Az Azure MFA protokoll szolgáltatója nem ad vissza **OutputClaims**, így nem kell megadnia a kimeneti jogcímeket. Megadhat azonban olyan jogcímeket, amelyeket nem ad vissza az Azure MFA-identitás szolgáltatója, ha beállítja a `DefaultValue` attribútumot.

A **OutputClaimsTransformations** elem olyan **OutputClaimsTransformation** -elemek gyűjteményét is tartalmazhatja, amelyek a kimeneti jogcímek módosítására vagy újak előállítására szolgálnak.

### <a name="metadata"></a>Metaadatok

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| Művelet | Igen | **OneWaySMS**kell lennie.  |
| UserMessageIfInvalidFormat | Nem | Egyéni hibaüzenet, ha a megadott telefonszám nem érvényes telefonszám |
| UserMessageIfCouldntSendSms | Nem | Egyéni hibaüzenet, ha a megadott telefonszám nem fogad SMS-t |
| UserMessageIfServerError | Nem | Egyéni hibaüzenet, ha a kiszolgáló belső hibát észlelt |

### <a name="return-an-error-message"></a>Hibaüzenet küldése

A [metaadatok](#metadata)című cikkben leírtak szerint testreszabhatja a felhasználó számára megjelenített hibaüzenetet különböző hibák esetén. Az üzeneteket továbbra is honosíthatja a területi beállítás előállításával. Példa:

```XML
<Item Key="en.UserMessageIfInvalidFormat">Invalid phone number.</Item>
```

### <a name="example-send-an-sms"></a>Példa: SMS küldése

Az alábbi példa egy Azure MFA technikai profilt mutat be, amely egy kód SMS-ben történő küldésére szolgál.

```XML
<TechnicalProfile Id="AzureMfa-SendSms">
    <DisplayName>Send Sms</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <Metadata>
        <Item Key="Operation">OneWaySMS</Item>
    </Metadata>
    <InputClaimsTransformations>
        <InputClaimsTransformation ReferenceId="CombinePhoneAndCountryCode" />
        <InputClaimsTransformation ReferenceId="ConvertStringToPhoneNumber" />
    </InputClaimsTransformations>
    <InputClaims>
        <InputClaim ClaimTypeReferenceId="userPrincipalName" />
        <InputClaim ClaimTypeReferenceId="fullPhoneNumber" PartnerClaimType="phoneNumber" />
    </InputClaims>
</TechnicalProfile>
```

## <a name="verify-code"></a>Kód ellenőrzése

A technikai profil második módja egy kód ellenőrzése. Ehhez a módhoz a következő beállítások konfigurálhatók.

### <a name="input-claims"></a>Bemeneti jogcímek

A **szabályzattípushoz** elem tartalmazza az Azure MFA számára küldendő jogcímek listáját. A jogcím nevét a MFA technikai profiljában definiált névre is leképezheti.

| ClaimReferenceId | Szükséges | Leírás |
| --------- | -------- | ----------- | ----------- |
| Telefonszám| Igen | Ugyanazt a telefonszámot használja, mint korábban a kód elküldéséhez. A rendszer a telefonos ellenőrzési munkamenetek megkeresésére is használatos. |
| verificationCode  | Igen | A felhasználó által ellenőrizendő ellenőrző kód |

A **InputClaimsTransformations** elem olyan **InputClaimsTransformation** -elemek gyűjteményét is tartalmazhatja, amelyek a bemeneti jogcímek módosítására vagy újak létrehozására szolgálnak az Azure MFA szolgáltatás meghívása előtt.

### <a name="output-claims"></a>Kimeneti jogcímek

Az Azure MFA protokoll szolgáltatója nem ad vissza **OutputClaims**, így nem kell megadnia a kimeneti jogcímeket. Megadhat azonban olyan jogcímeket, amelyeket nem ad vissza az Azure MFA-identitás szolgáltatója, ha beállítja a `DefaultValue` attribútumot.

A **OutputClaimsTransformations** elem olyan **OutputClaimsTransformation** -elemek gyűjteményét is tartalmazhatja, amelyek a kimeneti jogcímek módosítására vagy újak előállítására szolgálnak.

## <a name="metadata"></a>Metaadatok

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| Művelet | Igen | **Ellenőrizni** kell |
| UserMessageIfInvalidFormat | Nem | Egyéni hibaüzenet, ha a megadott telefonszám nem érvényes telefonszám |
| UserMessageIfWrongCodeEntered | Nem | Egyéni hibaüzenet, ha az ellenőrzéshez megadott kód helytelen |
| UserMessageIfMaxAllowedCodeRetryReached | Nem | Egyéni hibaüzenet, ha a felhasználó túl sokszor próbált meg ellenőrző kódot |
| UserMessageIfThrottled | Nem | Egyéni hibaüzenet, ha a felhasználó szabályozva van |
| UserMessageIfServerError | Nem | Egyéni hibaüzenet, ha a kiszolgáló belső hibát észlelt |

### <a name="return-an-error-message"></a>Hibaüzenet küldése

A [metaadatok](#metadata)című cikkben leírtak szerint testreszabhatja a felhasználó számára megjelenített hibaüzenetet különböző hibák esetén. Az üzeneteket továbbra is honosíthatja a területi beállítás előállításával. Példa:

```XML
<Item Key="en.UserMessageIfWrongCodeEntered">Wrong code has been entered.</Item>
```

### <a name="example-verify-a-code"></a>Példa: kód ellenőrzése

A következő példa egy Azure MFA technikai profilt mutat be, amely a kód ellenőrzéséhez használatos.

```XML
<TechnicalProfile Id="AzureMfa-VerifySms">
    <DisplayName>Verify Sms</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <Metadata>
        <Item Key="Operation">Verify</Item>
    </Metadata>
    <InputClaims>
        <InputClaim ClaimTypeReferenceId="phoneNumber" PartnerClaimType="phoneNumber" />
        <InputClaim ClaimTypeReferenceId="verificationCode" />
    </InputClaims>
</TechnicalProfile>
```
