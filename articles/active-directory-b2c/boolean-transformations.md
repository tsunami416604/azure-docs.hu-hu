---
title: Logikai jogcímek átalakítási példái egyéni házirendekhez
titleSuffix: Azure AD B2C
description: Logikai jogcímek átalakítási példák az Azure Active Directory B2C Identitáskezelési keretrendszer (IEF) sémájához.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ea3b273070702144d5296d07cb8712da044819a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471965"
---
# <a name="boolean-claims-transformations"></a>Logikai jogcímek átalakítása

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ez a cikk példákat tartalmaz az Azure Active Directory B2C (Azure AD B2C) identitáskezelési keretrendszer séma logikai jogcímátalakításai használatára. További információ: [ClaimsTransformations](claimstransformations.md).

## <a name="andclaims"></a>És követelések

Két logikai inputClaims és egy és műveletet hajt végre, és a művelet eredményével beállítja a outputClaim-et.

| Elem  | TransformationClaimType  | Adattípus  | Megjegyzések |
|-------| ------------------------ | ---------- | ----- |
| InputClaim (Bemeneti jogcím) | inputClaim1 | logikai | Az első kiértékelt Jogcímtípus. |
| InputClaim (Bemeneti jogcím) | inputClaim2  | logikai | A második kiértékelt Jogcímtípus. |
|OutputClaim (Kimeneti jogcím) | outputClaim | logikai | A jogcímátalakítás meghívása után létrekövetkező jogcímtípusok (igaz vagy hamis). |

A következő jogcímek átalakítása bemutatja, hogyan `isEmailNotExist`kell `isSocialAccount`és két logikai jogcímtípus: és . A kimeneti jogcím `presentEmailSelfAsserted` akkor van beállítva, `true` ha mindkét input jogcím értéke . `true` Vezénylési lépésben egy előfeltétel segítségével előre beállított egy önérvényesítő oldalt, csak akkor, ha egy közösségi fiók e-mail üres.

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

- Bemeneti igények:
    - **inputClaim1**: igaz
    - **inputClaim2**: hamis
- Kimeneti jogcímek:
    - **outputClaim**: false


## <a name="assertbooleanclaimisequaltovalue"></a>AssertBooleanClaimIsEqualToValue

Ellenőrzi, hogy két jogcím logikai értéke egyenlő-e, és kivételt okoz, ha nem.

| Elem | TransformationClaimType  | Adattípus  | Megjegyzések |
| ---- | ------------------------ | ---------- | ----- |
| inputClaim | inputClaim | logikai | A jogcímtípus. |
| InputParameter (Beviteli paraméter) |értékÖsszehasonlításto | logikai | Az összehasonlítandó érték (igaz vagy hamis). |

Az **AssertBooleanClaimIsEqualToValue** jogcímek átalakítása mindig egy [érvényesítési technikai profilból](validation-technical-profile.md) történik, amelyet [egy önérvényesítő technikai profil](self-asserted-technical-profile.md)hív meg. A **UserMessageIfClaimsTransformationBooleanValueIsNotEqual** saját érvényesítésű technikai profil metaadatai szabályozzák a technikai profil által a felhasználó számára megjelenő hibaüzenetet. A hibaüzenetek [honosíthatók](localization-string-ids.md#claims-transformations-error-messages).

![AssertStringClaimsAreEqual végrehajtás](./media/boolean-transformations/assert-execution.png)

A következő jogcímek átalakítása bemutatja, hogyan ellenőrizheti egy `true` értékkel rendelkező logikai Jogcímtípus értékét. Ha a `accountEnabled` ClaimType értéke hamis, hibaüzenet jelenik meg.

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


Az `login-NonInteractive` érvényesítési `AssertAccountEnabledIsTrue` technikai profil meghívja a jogcímek átalakítását.
```XML
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertAccountEnabledIsTrue" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

Az önérvényesítő technikai profil meghívja az érvényesítési **login-NonInteractive** technikai profilt.

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

- Bemeneti igények:
    - **inputClaim**: hamis
    - **valueToCompareTo**: igaz
- Eredmény: Hiba történt

## <a name="comparebooleanclaimtovalue"></a>BooleanClaimToValue összehasonlítása

Ellenőrzi, hogy a jogcím logikai értéke megegyezik-e a `true` `false`vagy ával, és visszaadja a tömörítés eredményét.

| Elem | TransformationClaimType  | Adattípus  | Megjegyzések |
| ---- | ------------------------ | ---------- | ----- |
| InputClaim (Bemeneti jogcím) | inputClaim | logikai | A jogcímtípus. |
| InputParameter (Beviteli paraméter) |értékÖsszehasonlításto | logikai | Az összehasonlítandó érték (igaz vagy hamis). |
| OutputClaim (Kimeneti jogcím) | compareResult (eredmény) | logikai | A Jogcímátalakítás meghívása után létrehozott Jogcímtípus. |


A következő jogcímek átalakítása bemutatja, hogyan ellenőrizheti egy `true` értékkel rendelkező logikai Jogcímtípus értékét. Ha `IsAgeOver21Years` a ClaimType értéke megegyezik `true`a , `true`a `false`jogcím-transzformáció egyébként visszaadja a .

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

- Bemeneti igények:
    - **inputClaim**: hamis
- Bemeneti paraméterek:
    - **valueToCompareTo**: igaz
- Kimeneti jogcímek:
    - **compareResult**: false



## <a name="notclaims"></a>Nem követelések

A logikai inputClaim nem műveletet hajt végre, és a művelet eredményével beállítja a outputClaim-et.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (Bemeneti jogcím) | inputClaim | logikai | A működtetendő követelés. |
| OutputClaim (Kimeneti jogcím) | outputClaim | logikai | A Jogcímátalakítás meghívása után létrehozott jogcímtípusok (igaz vagy hamis). |

Ezzel a jogcímátalakítással logikai negation-t hajthat végre egy jogcímen.

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

- Bemeneti igények:
    - **inputClaim**: hamis
- Kimeneti jogcímek:
    - **outputClaim**: igaz

## <a name="orclaims"></a>Vagykövetelések

Két logikai inputClaims értéket számít ki, és a művelet eredményével beállítja a outputClaim-et.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (Bemeneti jogcím) | inputClaim1 | logikai | Az első kiértékelt Jogcímtípus. |
| InputClaim (Bemeneti jogcím) | inputClaim2 | logikai | A második kiértékelt Jogcímtípus. |
| OutputClaim (Kimeneti jogcím) | outputClaim | logikai | A Jogcímátalakítás meghívása után létrekövetkező jogcímtípusok (igaz vagy hamis). |

A következő jogcímek átalakítása `Or` bemutatja, hogyan két logikai jogcímtípusok. A vezénylési lépésben egy előfeltétel segítségével előre beállíthat egy önérvényesített `true`oldalt, ha az egyik jogcím értéke .

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

- Bemeneti igények:
    - **inputClaim1**: igaz
    - **inputClaim2**: hamis
- Kimeneti jogcímek:
    - **outputClaim**: igaz
