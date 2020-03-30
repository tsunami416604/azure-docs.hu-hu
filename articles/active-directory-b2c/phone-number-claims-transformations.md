---
title: Telefonszám jogcímek átalakítása az egyéni házirendekben
titleSuffix: Azure AD B2C
description: Egyéni szabályzathivatkozás az Azure AD B2C-ben a telefonszám-jogcímek átalakításaesetén.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bd26b2b475e293a1fda1b007289ba7c3eef35136
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183931"
---
# <a name="define-phone-number-claims-transformations-in-azure-ad-b2c"></a>Telefonszámjogcím-átalakítások definiálása az Azure AD B2C-ben

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ez a cikk referencia- és példákat tartalmaz az Azure Active Directory B2C (Azure AD B2C) identitáskezelési keretrendszerséma jogcím-átalakításai telefonszám-átalakításaihoz. A jogcímátalakításokról általában a [ClaimsTransformations](claimstransformations.md)című témakörben talál további információt.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="convertphonenumberclaimtostring"></a>ConvertPhoneNumberClaimtostring

Adattípust `phoneNumber` adattípussá `string` alakít át.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (Bemeneti jogcím) | phoneNumber (telefonszám) | phoneNumber (telefonszám) |  A Karakterláncra konvertálandó Jogcímtípus. |
| OutputClaim (Kimeneti jogcím) | phoneNumberString | sztring | A jogcímátalakítás meghívása után létrehozott Jogcímtípus. |

Ebben a példában a cellPhoneNumber követelés `phoneNumber` egy értéktípusú konvertálja a cellPhone jogcím egy érték típusa `string`.

```XML
<ClaimsTransformation Id="PhoneNumberToString" TransformationMethod="ConvertPhoneNumberClaimToString">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="cellPhoneNumber" TransformationClaimType="phoneNumber" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="cellPhone" TransformationClaimType="phoneNumberString" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Példa

- Bemeneti igények:
  - **telefonszáma**: +11234567890 (phoneNumber)
- Kimeneti jogcímek:
  - **phoneNumberString**: +11234567890 (karakterlánc)


## <a name="convertstringtophonenumberclaim"></a>ConvertStringToPhoneNumberclaim

Ez a jogcímátalakítás ellenőrzi a telefonszám formátumát. Ha érvényes formátumú, módosítsa az Azure AD B2C által használt szabványos formátumra. Ha a megadott telefonszám nem érvényes formátumú, a rendszer hibaüzenetet ad vissza.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (Bemeneti jogcím) | phoneNumberString | sztring |  A telefonszám karakterlánc-jogcímigénye. A telefonszámnak nemzetközi formátumban kell lennie, egy vezető "+" és országkóddal kiegészítve. Ha a `country` bemeneti jogcím meg van adva, a telefonszám helyi formátumú (országkód nélkül). |
| InputClaim (Bemeneti jogcím) | ország | sztring | [Nem kötelező] Az ISO3166 formátumban (a kétbetűs ISO-3166 országkód) lévő telefonszám országkódjának karakterláncigénye. |
| OutputClaim (Kimeneti jogcím) | outputClaim | phoneNumber (telefonszám) | A jogcímek átalakításának eredménye. |

A **ConvertStringToPhoneNumberClaim** jogcímek átalakítása mindig egy [érvényesítési technikai profilból](validation-technical-profile.md) történik, amelyet [egy önérvényesítő technikai profil](self-asserted-technical-profile.md) vagy kijelzővezérlő hív [meg.](display-controls.md) A **UserMessageIfClaimsTransformationInvalidPhoneNumber** saját érvényesítésű technikai profil metaadatai szabályozzák a felhasználó számára megjelenő hibaüzenetet.

![A hibaüzenet végrehajtási útvonalának diagramja](./media/phone-authentication/assert-execution.png)

Ezzel a jogcímátalakítással biztosíthatja, hogy a megadott karakterlánc-jogcím érvényes telefonszám legyen. Ha nem, hibaüzenet jelenik meg. A következő példa ellenőrzi, hogy a **phoneString** ClaimType valóban érvényes telefonszám, majd visszaadja a telefonszámot a szabványos Azure AD B2C formátumban. Ellenkező esetben hibaüzenet jelenik meg.

```XML
<ClaimsTransformation Id="ConvertStringToPhoneNumber" TransformationMethod="ConvertStringToPhoneNumberClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phoneString" TransformationClaimType="phoneNumberString" />
    <InputClaim ClaimTypeReferenceId="countryCode" TransformationClaimType="country" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

Az önérvényesítő technikai profil, amely meghívja az érvényesítési technikai profilt, amely tartalmazza ezt a jogcímátalakítást, meghatározhatja a hibaüzenetet.

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignup-Phone">
  <Metadata>
    <Item Key="UserMessageIfClaimsTransformationInvalidPhoneNumber">Custom error message if the phone number is not valid.</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

### <a name="example-1"></a>1. példa

- Bemeneti igények:
  - **phoneNumberString**: 033 456-7890
  - **ország**: DK
- Kimeneti jogcímek:
  - **outputClaim**: +450334567890

### <a name="example-2"></a>2. példa

- Bemeneti igények:
  - **phoneNumberString**: +1 (123) 456-7890
- Kimeneti jogcímek:
  - **outputClaim**: +11234567890


## <a name="getnationalnumberandcountrycodefromphonenumberstring"></a>GetNationalNumberandcountrycodeFromPhonenumberstring

Ez kinyeri az országkódot és a nemzeti számot a bemeneti jogcímből, és opcionálisan kivételt tesz, ha a megadott telefonszám érvénytelen.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (Bemeneti jogcím) | phoneNumber (telefonszám) | sztring | A telefonszám karakterlánc-jogcímigénye. A telefonszámnak nemzetközi formátumban kell lennie, egy vezető "+" és országkóddal kiegészítve. |
| InputParameter (Beviteli paraméter) | throwExceptionOnFailure | logikai | [Nem kötelező] Egy paraméter, amely jelzi, hogy a rendszer kivételt okoz-e, ha a telefonszám érvénytelen. Az alapértelmezett érték hamis. |
| InputParameter (Beviteli paraméter) | országkódtípus | sztring | [Nem kötelező] Az országkód típusát jelző paraméter a kimeneti jogcímben. A rendelkezésre álló értékek a **CallingCode** (egy ország nemzetközi hívókódja, például +1) vagy **az ISO3166** (a kétbetűs ISO-3166 országkód). |
| OutputClaim (Kimeneti jogcím) | nationalNumber (szám | sztring | A telefonszám nemzeti számára vonatkozó karakterlánc-jogcím. |
| OutputClaim (Kimeneti jogcím) | országKód | sztring | A telefonszám országkódjának karakterlánc-jogcímigénye. |


Ha a **GetNationalNumberAndCountryCodeFromPhoneNumber** jogcímek átalakítása egy [érvényesítési technikai profilból](validation-technical-profile.md) történik, amelyet [egy önérvényesítő technikai profil](self-asserted-technical-profile.md) vagy egy [megjelenítés-vezérlő művelet](display-controls.md#display-control-actions)hív meg, akkor a **UserMessageIfPhoneNumberParseFailure** saját érvényesítésű technikai profil metaadatai szabályozzák a felhasználó számára megjelenített hibaüzenetet.

![A hibaüzenet végrehajtási útvonalának diagramja](./media/phone-authentication/assert-execution.png)

Ezzel a jogcímátalakítással feloszthatja a teljes telefonszámot az országkódra és a nemzeti számra. Ha a megadott telefonszám érvénytelen, hibaüzenetet is eldönthet.

A következő példa megpróbálja felosztani a telefonszámot nemzeti számra és országkódra. Ha a telefonszám érvényes, a telefonszámot a nemzeti szám felülírja. Ha a telefonszám érvénytelen, a program nem jelenik meg kivételben, és a telefonszám nak továbbra is megvan az eredeti értéke.

```XML
<ClaimsTransformation Id="GetNationalNumberAndCountryCodeFromPhoneNumberString" TransformationMethod="GetNationalNumberAndCountryCodeFromPhoneNumberString">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="phoneNumber" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="throwExceptionOnFailure" DataType="boolean" Value="false" />
    <InputParameter Id="countryCodeType" DataType="string" Value="ISO3166" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="nationalNumber" TransformationClaimType="nationalNumber" />
    <OutputClaim ClaimTypeReferenceId="countryCode" TransformationClaimType="countryCode" />
  </OutputClaims>
</ClaimsTransformation>
```

Az önérvényesítő technikai profil, amely meghívja az érvényesítési technikai profilt, amely tartalmazza ezt a jogcímátalakítást, meghatározhatja a hibaüzenetet.

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignup-Phone">
  <Metadata>
    <Item Key="UserMessageIfPhoneNumberParseFailure">Custom error message if the phone number is not valid.</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

### <a name="example-1"></a>1. példa

- Bemeneti igények:
  - **telefonszáma:**+49 (123) 456-7890
- Bemeneti paraméterek:
  - **throwExceptionOnFailure**: hamis
  - **országCodeType**: ISO3166
- Kimeneti jogcímek:
  - **nemzetiszám**: 1234567890
  - **országKód**: DE

### <a name="example-2"></a>2. példa

- Bemeneti igények:
  - **telefonszáma:**+49 (123) 456-7890
- Bemeneti paraméterek
  - **throwExceptionOnFailure**: hamis
  - **countryCodeType**: CallingCode
- Kimeneti jogcímek:
  - **nemzetiszám**: 1234567890
  - **országKód**: +49
