---
title: Logikai érték-jogcímek átalakítása példák az identitás élmény keretrendszer sémát az Azure Active Directory B2C |} A Microsoft Docs
description: Logikai érték átalakítása példák az identitás élmény keretrendszer sémát az Azure Active Directory B2C jogcímmel.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: fea0f8c2c2bcab94202916594e66514f2ec87396
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55180126"
---
# <a name="boolean-claims-transformations"></a>Logikai jogcímek átalakítása

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ez a cikk példákat ad a logikai jogcímek átalakítása az identitás-kezelőfelületi keretrendszer séma használatával az Azure Active Directory (Azure AD) B2C-ben. További információkért lásd: [ClaimsTransformations](claimstransformations.md).

## <a name="andclaims"></a>AndClaims

Egy két logikai inputClaims és műveletet hajtja végre, és beállítja a a kimeneti jogcím és a művelet eredményét.

| Elem  | TransformationClaimType  | Adattípus  | Megjegyzések |
|-------| ------------------------ | ---------- | ----- |
| Bemeneti jogcím | inputClaim1 | logikai | Az első takar értékelheti ki. |
| Bemeneti jogcím | inputClaim2  | logikai | A második ClaimType értékelheti ki. |
|OutputClaim | outputClaim | logikai | Az a jogcím-átalakítás után előállított ClaimTypes meghívása (IGAZ vagy hamis). |

A következő jogcímek átalakítását bemutatja, hogyan lehet, és két logikai ClaimTypes: `isEmailNotExist`, és `isSocialAccount`. A kimeneti jogcímek `presentEmailSelfAsserted` értékre van állítva `true` mind a bemeneti jogcímek e `true`. Egy vezénylési lépés a segítségével egy előfeltétel előbeállítást önellenőrzött oldal, csak akkor, ha egy közösségi fiók e-mail-címe üres.

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

- A bemeneti jogcímek:
    - **inputClaim1**: igaz
    - **inputClaim2**: False (hamis)
- Kimeneti jogcímek:
    - **kimeneti jogcím**: False (hamis)


## <a name="assertbooleanclaimisequaltovalue"></a>AssertBooleanClaimIsEqualToValue

Ellenőrzi, hogy két jogcímek logikai értékek egyenlő-e, és kivételt jelez, ha azok nem.

| Elem | TransformationClaimType  | Adattípus  | Megjegyzések |
| ---- | ------------------------ | ---------- | ----- |
| Bemeneti jogcím | Bemeneti jogcím | logikai | Az egyenértékű takar. |
| InputParameter |valueToCompareTo | logikai | Az érték összehasonlításához (true vagy false). |

A **AssertBooleanClaimIsEqualToValue** jogcímek átalakítását a rendszer mindig futtatja a egy [ellenőrzési technikai profil](validation-technical-profile.md) , hívja meg a [önálló kiszolgáló által megerősített technikai profil](self-asserted-technical-profile.md). A **UserMessageIfClaimsTransformationBooleanValueIsNotEqual** önellenőrzött technikai profil metaadataiban szabályozza a hibaüzenet, amely a technikai profil jeleníti meg a felhasználó számára.

![AssertStringClaimsAreEqual execution](./media/boolean-transformations/assert-execution.png)

A következő jogcímek átalakítását bemutatja, hogyan ellenőrizheti egy logikai takar az értékét egy `true` értéket. Ha az érték a `accountEnabled` takar értéke HAMIS, egy hibaüzenet fordul elő.

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


A `login-NonInteractive` érvényesítési technikai profil hívások a `AssertAccountEnabledIsTrue` jogcím-átalakítás.
```XML
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertAccountEnabledIsTrue" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

Az önellenőrzött technikai profilban meghívja az érvényesítés **bejelentkezési nem interaktív** technikai profil.

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

- A bemeneti jogcímek:
    - **bemeneti jogcím**: False (hamis)
    - **valueToCompareTo**: true
- Eredmény: Hiba lépett fel

## <a name="notclaims"></a>NotClaims

A logikai bemeneti jogcím még nem műveletet hajt végre, és beállítja a kimeneti jogcím és a művelet eredményét.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| Bemeneti jogcím | Bemeneti jogcím | logikai | Az igényt az működtetni. |
| OutputClaim | outputClaim | logikai | A ClaimTypes, amelyek előállítják a ClaimsTransformation (IGAZ vagy hamis) meghívása után. |

Logikai negálást végez egy jogcímet a jogcím-átalakítást használja.

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

- A bemeneti jogcímek:
    - **bemeneti jogcím**: False (hamis)
- Kimeneti jogcímek:
    - **kimeneti jogcím**: igaz

## <a name="orclaims"></a>OrClaims 

Egy vagy két logikai inputClaims, kiszámítja, és beállítja a kimeneti jogcím és a művelet eredményét.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| Bemeneti jogcím | inputClaim1 | logikai | Az első takar értékelheti ki. |
| Bemeneti jogcím | inputClaim2 | logikai | A második ClaimType értékelheti ki. |
| OutputClaim | outputClaim | logikai | Az előállított után ez ClaimsTransformation ClaimTypes meghívása (IGAZ vagy hamis). |

A következő jogcímek átalakítását bemutatja, hogyan `Or` két logikai ClaimTypes. Az orchestration lépésben segítségével másnak az előfeltétele előbeállítást önellenőrzött oldal, ha az érték egy, a jogcímek `true`.

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

- A bemeneti jogcímek:
    - **inputClaim1**: igaz
    - **inputClaim2**: False (hamis)
- Kimeneti jogcímek:
    - **kimeneti jogcím**: igaz

