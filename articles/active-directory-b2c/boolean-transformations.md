---
title: Logikai jogcím-átalakítási példák egyéni házirendekhez
titleSuffix: Azure AD B2C
description: Logikai jogcím-átalakítási példák a Azure Active Directory B2C Identity Experience Framework (IEF) sémájához.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: dcebcc3e2021938f3fd3bde236ef08e4f26b8a97
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/09/2019
ms.locfileid: "74949891"
---
# <a name="boolean-claims-transformations"></a>Logikai jogcím-átalakítások

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ez a cikk példákat tartalmaz a Azure Active Directory B2C (Azure AD B2C) Identity Experience Framework sémájának logikai jogcímek átalakítására. További információ: [ClaimsTransformations](claimstransformations.md).

## <a name="andclaims"></a>Szerződésszegéssel

Két logikai Szabályzattípushoz és műveletet hajt végre, és beállítja a outputClaim a művelet eredményével.

| Tétel  | TransformationClaimType  | Adattípus  | Megjegyzések |
|-------| ------------------------ | ---------- | ----- |
| InputClaim | inputClaim1 | logikai | A ClaimType első kiértékelése. |
| InputClaim | inputClaim2  | logikai | A második ClaimType kiértékelése. |
|outputClaim | outputClaim | logikai | A jogcím-átalakítás meghívása után előállított ClaimTypes (TRUE vagy FALSE). |

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

| Tétel | TransformationClaimType  | Adattípus  | Megjegyzések |
| ---- | ------------------------ | ---------- | ----- |
| InputClaim | InputClaim | logikai | Az érvényesíteni kívánt ClaimType. |
| InputParameter |valueToCompareTo | logikai | Az összehasonlítandó érték (igaz vagy hamis). |

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

## <a name="notclaims"></a>NotClaims

A nem végez műveletet a logikai inputClaim, és a művelet eredményével beállítja a outputClaim.

| Tétel | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim | logikai | Az üzemeltetni kívánt jogcím. |
| outputClaim | outputClaim | logikai | A ClaimsTransformation után létrehozott ClaimTypes (TRUE vagy FALSE). |

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

| Tétel | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | logikai | A ClaimType első kiértékelése. |
| InputClaim | inputClaim2 | logikai | A második ClaimType kiértékelése. |
| outputClaim | outputClaim | logikai | A ClaimsTransformation meghívása után előállított ClaimTypes (TRUE vagy FALSE). |

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

