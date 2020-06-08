---
title: Logikai jogcím-átalakítási példák egyéni házirendekhez
titleSuffix: Azure AD B2C
description: Logikai jogcím-átalakítási példák a Azure Active Directory B2C Identity Experience Framework (IEF) sémájához.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/06/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 02d488108474084346d9e37d5cc6ecbe3a8a05c6
ms.sourcegitcommit: f57fa5f3ce40647eda93f8be4b0ab0726d479bca
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2020
ms.locfileid: "84484273"
---
# <a name="boolean-claims-transformations"></a>Logikai jogcím-átalakítások

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ez a cikk példákat tartalmaz a Azure Active Directory B2C (Azure AD B2C) Identity Experience Framework sémájának logikai jogcímek átalakítására. További információ: [ClaimsTransformations](claimstransformations.md).

## <a name="andclaims"></a>Szerződésszegéssel

Két logikai Szabályzattípushoz és műveletet hajt végre, és beállítja a outputClaim a művelet eredményével.

| Item  | TransformationClaimType  | Adattípus  | Megjegyzések |
|-------| ------------------------ | ---------- | ----- |
| InputClaim | inputClaim1 | logikai | A ClaimType első kiértékelése. |
| InputClaim | inputClaim2  | logikai | A második ClaimType kiértékelése. |
|OutputClaim | outputClaim | logikai | A jogcím-átalakítás meghívása után előállított ClaimTypes (TRUE vagy FALSE). |

A következő jogcím-átalakítás azt mutatja be, hogyan és hogyan lehet két logikai ClaimTypes: `isEmailNotExist` és `isSocialAccount` . A kimeneti jogcím `presentEmailSelfAsserted` akkor van beállítva, `true` Ha a bemeneti jogcímek értéke is `true` . Egy előkészítési lépésben feltételt használhat egy önjelölt lap beállítására, csak akkor, ha a közösségi fiók e-mail-címe üres.

```XML
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="AndClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="isEmailNotExist" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="isSocialAccount" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="presentEmailSelfAsserted" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example-of-andclaims"></a>Példa szerződésszegéssel

- Bemeneti jogcímek:
    - **inputClaim1**: true
    - **inputClaim2**: hamis
- Kimeneti jogcímek:
    - **outputClaim**: hamis


## <a name="assertbooleanclaimisequaltovalue"></a>AssertBooleanClaimIsEqualToValue

Ellenőrzi, hogy a két jogcím logikai értékei egyenlőek-e, és kivételt jelez, ha nem.

| Item | TransformationClaimType  | Adattípus  | Megjegyzések |
| ---- | ------------------------ | ---------- | ----- |
| inputClaim | inputClaim | logikai | Az érvényesíteni kívánt ClaimType. |
| InputParameter |valueToCompareTo | logikai | Az összehasonlítandó érték (igaz vagy hamis). |

Az **AssertBooleanClaimIsEqualToValue** jogcímek átalakítását mindig egy [önérvényesített technikai profil](self-asserted-technical-profile.md)által hívott [érvényesítési műszaki profilból](validation-technical-profile.md) hajtja végre a rendszer. A **UserMessageIfClaimsTransformationBooleanValueIsNotEqual** önérvényesített technikai profil metaadatai a technikai profil által a felhasználónak megjelenített hibaüzenetet vezérlik. A hibaüzenetek [honosítható](localization-string-ids.md#claims-transformations-error-messages).

![AssertStringClaimsAreEqual-végrehajtás](./media/boolean-transformations/assert-execution.png)

A következő jogcím-átalakítás azt mutatja be, hogyan ellenőrizhető egy értékkel rendelkező logikai ClaimType értéke `true` . Ha a `accountEnabled` claimType értéke hamis, a rendszer hibaüzenetet küld.

```XML
<ClaimsTransformation Id="AssertAccountEnabledIsTrue" TransformationMethod="AssertBooleanClaimIsEqualToValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="accountEnabled" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="valueToCompareTo" DataType="boolean" Value="true" />
  </InputParameters>
</ClaimsTransformation>
```


Az `login-NonInteractive` érvényesítési technikai profil meghívja a `AssertAccountEnabledIsTrue` jogcím-átalakítást.

```XML
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertAccountEnabledIsTrue" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

Az önérvényesített technikai profil meghívja az érvényesítési **bejelentkezést nem interaktív** technikai profilt.

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <Metadata>
    <Item Key="UserMessageIfClaimsTransformationBooleanValueIsNotEqual">Custom error message if account is disabled.</Item>
  </Metadata>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="login-NonInteractive" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```

### <a name="example-of-assertbooleanclaimisequaltovalue"></a>Példa AssertBooleanClaimIsEqualToValue

- Bemeneti jogcímek:
    - **inputClaim**: hamis
    - **valueToCompareTo**: true
- Eredmény: feldobott hiba

## <a name="comparebooleanclaimtovalue"></a>CompareBooleanClaimToValue

Ellenőrzi, hogy a jogcímek logikai értéke egyenlő-e a vagy a értékkel `true` `false` , és visszaküldi a tömörítés eredményét.

| Item | TransformationClaimType  | Adattípus  | Megjegyzések |
| ---- | ------------------------ | ---------- | ----- |
| InputClaim | inputClaim | logikai | Az érvényesíteni kívánt ClaimType. |
| InputParameter |valueToCompareTo | logikai | Az összehasonlítandó érték (igaz vagy hamis). |
| OutputClaim | compareResult | logikai | A ClaimsTransformation után létrehozott ClaimType meghívása megtörtént. |

A következő jogcím-átalakítás azt mutatja be, hogyan ellenőrizhető egy értékkel rendelkező logikai ClaimType értéke `true` . Ha a `IsAgeOver21Years` claimType értéke egyenlő `true` , akkor a jogcím-átalakítás visszatér `true` , ellenkező esetben `false` .

```XML
<ClaimsTransformation Id="AssertAccountEnabled" TransformationMethod="CompareBooleanClaimToValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="IsAgeOver21Years" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="valueToCompareTo" DataType="boolean" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim  ClaimTypeReferenceId="accountEnabled" TransformationClaimType="compareResult"/>
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example-of-comparebooleanclaimtovalue"></a>Példa CompareBooleanClaimToValue

- Bemeneti jogcímek:
    - **inputClaim**: hamis
- Bemeneti paraméterek:
    - **valueToCompareTo**: true
- Kimeneti jogcímek:
    - **compareResult**: hamis

## <a name="notclaims"></a>NotClaims

A nem végez műveletet a logikai inputClaim, és a művelet eredményével beállítja a outputClaim.

| Item | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | logikai | Az üzemeltetni kívánt jogcím. |
| OutputClaim | outputClaim | logikai | A ClaimsTransformation után létrehozott ClaimTypes (TRUE vagy FALSE). |

A jogcím-átalakítás használatával logikai tagadást hajthat végre a jogcímen.

```XML
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="NotClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userExists" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="userExists" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example-of-notclaims"></a>Példa NotClaims

- Bemeneti jogcímek:
    - **inputClaim**: hamis
- Kimeneti jogcímek:
    - **outputClaim**: true

## <a name="orclaims"></a>OrClaims

Kiszámítja a vagy a két logikai Szabályzattípushoz, és beállítja a outputClaim a művelet eredményével.

| Item | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | logikai | A ClaimType első kiértékelése. |
| InputClaim | inputClaim2 | logikai | A második ClaimType kiértékelése. |
| OutputClaim | outputClaim | logikai | A ClaimsTransformation meghívása után előállított ClaimTypes (TRUE vagy FALSE). |

A következő jogcím-átalakítás azt mutatja be, hogyan lehet `Or` két logikai ClaimTypes. A előkészítési lépésben feltételt használhat egy önérvényesített lap beállításához, ha az egyik jogcím értéke `true` .

```XML
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="OrClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="isLastTOSAcceptedNotExists" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="isLastTOSAcceptedGreaterThanNow" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="presentTOSSelfAsserted" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example-of-orclaims"></a>Példa OrClaims

- Bemeneti jogcímek:
    - **inputClaim1**: true
    - **inputClaim2**: hamis
- Kimeneti jogcímek:
    - **outputClaim**: true
