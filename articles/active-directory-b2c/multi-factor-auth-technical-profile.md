---
title: Azure MFA technikai profiljai egyéni szabályzatokban
titleSuffix: Azure AD B2C
description: Egyéni szabályzat-hivatkozás az Azure Többtényezős hitelesítés (MFA) technikai profilok az Azure AD B2C.Custom policy reference for Azure Multi-Factor Authentication (MFA) technical profiles in Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c9ed0e329b498112feafaf21c34e85ea436cbb77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332807"
---
# <a name="define-an-azure-mfa-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Azure MFA-technikai profil definiálása egy Azure AD B2C egyéni szabályzatban

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Az Azure Active Directory B2C (Azure AD B2C) támogatja a telefonszám ellenőrzését az Azure többtényezős hitelesítés (MFA) használatával. Ezzel a technikai profillal kódot hozhat létre és küldhet egy telefonszámra, majd ellenőrizheti a kódot. Az Azure MFA technikai profil is visszaadhat egy hibaüzenetet.  Az érvényesítési technikai profil ellenőrzi a felhasználó által megadott adatokat, mielőtt a felhasználói út folytatódik. Az érvényesítési technikai profil, egy hibaüzenet jelenik meg egy önérvényesítő oldalon.

Ez a technikai profil:

- Nem biztosít felületet a felhasználóval való interakcióhoz. Ehelyett a felhasználói felületet [egy saját érvényesítésű](self-asserted-technical-profile.md) technikai profilból, vagy egy [megjelenítési vezérlőből](display-controls.md) [érvényesítési technikai profilként](validation-technical-profile.md)hívják meg.
- Az Azure MFA-szolgáltatás használatával generál, és küldjön egy kódot egy telefonszámot, majd ellenőrzi a kódot.  
- A telefonszámot sms-ben ellenőrzi.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="protocol"></a>Protocol (Protokoll)

A **Protokoll** elem **Name** attribútumát a `Proprietary`parancsra kell állítani. A **kezelő** attribútumnak tartalmaznia kell az Azure AD B2C által használt protokollkezelő szerelvény teljesen minősített nevét:

```
Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

A következő példa egy Azure MFA technikai profilt mutat be:

```XML
<TechnicalProfile Id="AzureMfa-SendSms">
    <DisplayName>Send Sms</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    ...
```

## <a name="send-sms"></a>SMS küldése

A technikai profil első módja egy kód létrehozása és elküldése. Ehhez a módhoz a következő beállítások konfigurálhatók.

### <a name="input-claims"></a>Bemeneti jogcímek

Az **InputClaims** elem az Azure MFA-nak küldő jogcímek listáját tartalmazza. A jogcím nevét az MFA technikai profiljában meghatározott névhez is hozzárendelje.

| Jogcímhivatkozásazonosítója | Kötelező | Leírás |
| --------- | -------- | ----------- |
| userPrincipalName | Igen | A telefonszámot birtokló felhasználó azonosítója. |
| phoneNumber (telefonszám) | Igen | Az a telefonszám, amelyhez SMS-kódot kell küldeni. |
| vállalatneve | Nem |A cég neve az SMS-ben. Ha nincs megadva, a készülék az alkalmazás nevét használja. |
| locale | Nem | Az SMS területi beállítása. Ha nincs megadva, a felhasználó böngészőterületi beállítását használja a rendszer. |

Az **InputClaimsTransformations** elem tartalmazhat **InputClaimsTransformation** elemek gyűjteményét, amelyek a bemeneti jogcímek módosítására vagy újak létrehozására szolgálnak az Azure MFA-szolgáltatásnak való küldés előtt.

### <a name="output-claims"></a>Kimeneti jogcímek

Az Azure MFA protokoll szolgáltató nem ad vissza **kimeneti jogcímeket,** így nincs szükség kimeneti jogcímek megadása. Azonban az Azure MFA-identitásszolgáltató által nem visszaadott jogcímeket is megadhat, feltéve, hogy beállítja az `DefaultValue` attribútumot.

A **OutputClaimsTransformations** elem tartalmazhat **OutputClaimsTransformations** elemek gyűjteményét, amelyek a kimeneti jogcímek módosítására vagy újak létrehozására szolgálnak.

### <a name="metadata"></a>Metaadatok

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Művelet | Igen | Kell **OneWaySMS**.  |

#### <a name="ui-elements"></a>Felhasználói felület elemei

Az sms-hiba küldésekor megjelenő hibaüzenetek konfigurálására a következő metaadatok használhatók. A metaadatokat az önérvényesítő technikai profilban kell [konfigurálni.](self-asserted-technical-profile.md) A hibaüzenetek [honosíthatók](localization-string-ids.md#azure-mfa-error-messages).

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| UserMessageIfCouldntSendSms | Nem | Felhasználói hibaüzenet, ha a megadott telefonszám nem fogadja el az SMS-t. |
| UserMessageIfInvalidFormat | Nem | Felhasználói hibaüzenet, ha a megadott telefonszám nem érvényes telefonszám. |
| UserMessageIfServerError | Nem | Felhasználói hibaüzenet, ha a kiszolgáló belső hibát észlelt. |
| UserMessageIfThrottled| Nem | Felhasználói hibaüzenet, ha egy kérést szabályoztak.|

### <a name="example-send-an-sms"></a>Példa: SMS küldése

A következő példa egy Azure MFA technikai profilt mutat be, amely egy kód SMS-ben történő küldésére szolgál.

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

Az **InputClaims** elem az Azure MFA-nak küldő jogcímek listáját tartalmazza. A jogcím nevét az MFA technikai profiljában meghatározott névhez is hozzárendelje.

| Jogcímhivatkozásazonosítója | Kötelező | Leírás |
| --------- | -------- | ----------- | ----------- |
| phoneNumber (telefonszám)| Igen | Ugyanaz a telefonszám, mint amit korábban a kód küldéséhez használtak. Azt is használják, hogy keresse meg a telefon ellenőrzési ülésén. |
| verificationCode  | Igen | Az ellenőrizendő felhasználó által megadott ellenőrző kód |

Az **InputClaimsTransformations** elem tartalmazhat **InputClaimsTransformation** elemek gyűjteményét, amelyek a bemeneti jogcímek módosítására vagy újak létrehozására szolgálnak az Azure MFA-szolgáltatás hívása előtt.

### <a name="output-claims"></a>Kimeneti jogcímek

Az Azure MFA protokoll szolgáltató nem ad vissza **kimeneti jogcímeket,** így nincs szükség kimeneti jogcímek megadása. Azonban az Azure MFA-identitásszolgáltató által nem visszaadott jogcímeket is megadhat, feltéve, hogy beállítja az `DefaultValue` attribútumot.

A **OutputClaimsTransformations** elem tartalmazhat **OutputClaimsTransformations** elemek gyűjteményét, amelyek a kimeneti jogcímek módosítására vagy újak létrehozására szolgálnak.

### <a name="metadata"></a>Metaadatok

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Művelet | Igen | **Ellenőrizni** kell |

#### <a name="ui-elements"></a>Felhasználói felület elemei

A következő metaadatok segítségével konfigurálhatja a kódellenőrzési hiba esetén megjelenő hibaüzeneteket. A metaadatokat az önérvényesítő technikai profilban kell [konfigurálni.](self-asserted-technical-profile.md) A hibaüzenetek [honosíthatók](localization-string-ids.md#azure-mfa-error-messages).

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| UserMessageIfMaxAllowedCodeRetryReached| Nem | Felhasználói hibaüzenet, ha a felhasználó túl sokszor kísérelt meg ellenőrző kódot. |
| UserMessageIfServerError | Nem | Felhasználói hibaüzenet, ha a kiszolgáló belső hibát észlelt. |
| UserMessageIfThrottled| Nem | Felhasználói hibaüzenet, ha a kérelem szabályozott.|
| UserMessageIfWrongCodeEntered| Nem| Felhasználói hibaüzenet, ha az ellenőrzésre megadott kód hibás.|

### <a name="example-verify-a-code"></a>Példa: kód ellenőrzése

A következő példa egy Azure MFA technikai profilt mutat be a kód ellenőrzéséhez.

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
