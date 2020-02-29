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
ms.date: 02/03/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 65f5b9bc09bb74ccbc63a457ba8e5681de8eefab
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78189941"
---
# <a name="boolean-claims-transformations"></a>Logikai jogcím-átalakítások

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ez a cikk példákat tartalmaz a Azure Active Directory B2C (Azure AD B2C) Identity Experience Framework sémájának logikai jogcímek átalakítására. További információ: [ClaimsTransformations](claimstransformations.md).

## <a name="andclaims"></a>AndClaims

Két logikai Szabályzattípushoz és műveletet hajt végre, és beállítja a outputClaim a művelet eredményével.

| Elem  | TransformationClaimType  | Adattípus  | Megjegyzések |
|-------| ------------------------ | ---------- | ----- |
| inputClaim | inputClaim1 | Logikai érték | A ClaimType első kiértékelése. |
| inputClaim | inputClaim2  | Logikai érték | A második ClaimType kiértékelése. |
|outputClaim | outputClaim | Logikai érték | A jogcím-átalakítás meghívása után előállított ClaimTypes (TRUE vagy FALSE). |

A következő jogcím-átalakítás azt mutatja be, hogyan lehet és két logikai ClaimTypes: `isEmailNotExist`és `isSocialAccount`. A kimeneti jogcím `presentEmailSelfAsserted` `true`, ha a bemeneti jogcímek értéke `true`. Egy előkészítési lépésben feltételt használhat egy önjelölt lap beállítására, csak akkor, ha a közösségi fiók e-mail-címe üres.

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

### <a name="example"></a>Példa

- Bemeneti jogcímek:
    - **inputClaim1**: true
    - **inputClaim2**: hamis
- Kimeneti jogcímek:
    - **outputClaim**: hamis


## <a name="assertbooleanclaimisequaltovalue"></a>AssertBooleanClaimIsEqualToValue

Ellenőrzi, hogy a két jogcím logikai értékei egyenlőek-e, és kivételt jelez, ha nem.

| Elem | TransformationClaimType  | Adattípus  | Megjegyzések |
| ---- | ------------------------ | ---------- | ----- |
| inputClaim | inputClaim | Logikai érték | Az érvényesíteni kívánt ClaimType. |
| InputParameter |valueToCompareTo | Logikai érték | Az összehasonlítandó érték (igaz vagy hamis). |

Az **AssertBooleanClaimIsEqualToValue** jogcímek átalakítását mindig egy [önérvényesített technikai profil](self-asserted-technical-profile.md)által hívott [érvényesítési műszaki profilból](validation-technical-profile.md) hajtja végre a rendszer. A **UserMessageIfClaimsTransformationBooleanValueIsNotEqual** önérvényesített technikai profil metaadatai a technikai profil által a felhasználónak megjelenített hibaüzenetet vezérlik.

![AssertStringClaimsAreEqual-végrehajtás](./media/boolean-transformations/assert-execution.png)

A következő jogcím-átalakítás azt mutatja be, hogyan ellenőrizhető egy `true` értékkel rendelkező logikai ClaimType értéke. Ha a `accountEnabled` ClaimType értéke hamis, a rendszer hibaüzenetet küld.

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


A `login-NonInteractive` érvényesítő technikai profil meghívja a `AssertAccountEnabledIsTrue` jogcím-átalakítást.
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

### <a name="example"></a>Példa

- Bemeneti jogcímek:
    - **inputClaim**: hamis
    - **valueToCompareTo**: true
- Eredmény: feldobott hiba

## <a name="comparebooleanclaimtovalue"></a>CompareBooleanClaimToValue

Ellenőrzi, hogy a jogcímek logikai értéke `true` vagy `false`-e, és visszaadja-e a tömörítés eredményét.

| Elem | TransformationClaimType  | Adattípus  | Megjegyzések |
| ---- | ------------------------ | ---------- | ----- |
| inputClaim | inputClaim | Logikai érték | Az érvényesíteni kívánt ClaimType. |
| InputParameter |valueToCompareTo | Logikai érték | Az összehasonlítandó érték (igaz vagy hamis). |
| outputClaim | compareResult | Logikai érték | A ClaimsTransformation után létrehozott ClaimType meghívása megtörtént. |


A következő jogcím-átalakítás azt mutatja be, hogyan ellenőrizhető egy `true` értékkel rendelkező logikai ClaimType értéke. Ha a `IsAgeOver21Years` ClaimType értéke `true`, akkor a jogcím-átalakítás visszaadja `true`, ellenkező esetben `false`.

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

### <a name="example"></a>Példa

- Bemeneti jogcímek:
    - **inputClaim**: hamis
- Bemeneti paraméterek:
    - **valueToCompareTo**: true
- Kimeneti jogcímek:
    - **compareResult**: hamis



## <a name="notclaims"></a>NotClaims

A nem végez műveletet a logikai inputClaim, és a művelet eredményével beállítja a outputClaim.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | inputClaim | Logikai érték | Az üzemeltetni kívánt jogcím. |
| outputClaim | outputClaim | Logikai érték | A ClaimsTransformation után létrehozott ClaimTypes (TRUE vagy FALSE). |

A jogcím-átalakítás használatával logikai tagadást hajthat végre a jogcímen.

```XML
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="NotClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userExists" TransformationClaimType="inputClaim" />
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="userExists" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Példa

- Bemeneti jogcímek:
    - **inputClaim**: hamis
- Kimeneti jogcímek:
    - **outputClaim**: true

## <a name="orclaims"></a>OrClaims

Kiszámítja a vagy a két logikai Szabályzattípushoz, és beállítja a outputClaim a művelet eredményével.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | inputClaim1 | Logikai érték | A ClaimType első kiértékelése. |
| inputClaim | inputClaim2 | Logikai érték | A második ClaimType kiértékelése. |
| outputClaim | outputClaim | Logikai érték | A ClaimsTransformation meghívása után előállított ClaimTypes (TRUE vagy FALSE). |

A következő jogcím-átalakítás azt mutatja be, hogyan `Or` két logikai ClaimTypes. A előkészítési lépésben előfeltételt használhat egy önérvényesített lap beállításához, ha az egyik jogcím értéke `true`.

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
</ClaimsTransformation>
```

### <a name="example"></a>Példa

- Bemeneti jogcímek:
    - **inputClaim1**: true
    - **inputClaim2**: hamis
- Kimeneti jogcímek:
    - **outputClaim**: true
