---
title: Dátumjogcímek átalakítása példák egyéni házirendekhez
description: Dátumjogcímek átalakítási példák az Azure Active Directory B2C Identitáskezelési keretrendszer (IEF) sémájához.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c02ac9392d6f3f95deef38ff86250e96dfb76d96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476688"
---
# <a name="date-claims-transformations"></a>Jogcímek átalakításaidátuma

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ez a cikk példákat tartalmaz az Azure Active Directory B2C (Azure AD B2C) identitáskezelési keretrendszer séma dátumjogcím-átalakításai használatára. További információ: [ClaimsTransformations](claimstransformations.md).

## <a name="assertdatetimeisgreaterthan"></a>Assertdatetimeisgreaterthan

Ellenőrzi, hogy egy dátum- és időjogcím (karakterlánc-adattípus) későbbi-e, mint a második dátum- és időjogcím (karakterlánc-adattípus), és kivételt okoz.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (Bemeneti jogcím) | balOperand | sztring | Az első követelés típusa, amelynek későbbinek kell lennie, mint a második állítás. |
| InputClaim (Bemeneti jogcím) | jobbOperand | sztring | A második követelés típusa, amelynek korábban kell lennie, mint az első állítás. |
| InputParameter (Beviteli paraméter) | AssertIfequalto között | logikai | Itt adható meg, hogy ez az állítás megfeleljen-e, ha a bal oldali operandus megegyezik a jobb oldali operandussal. |
| InputParameter (Beviteli paraméter) | AssertIfRightOperandIsNotPresent | logikai | Itt adható meg, hogy ez az állítás megfelel-e, ha hiányzik a jobb oldali operandus. |
| InputParameter (Beviteli paraméter) | TreatasequalifWithinmillszekun belül | int | Megadja, hogy a két dátumidő között hány ezredmásodperc ben egyenlőnek lehessen tekinteni az időpontokat (például az óradöntés figyelembevételével). |

Az **AssertDateTimeIsGreaterThan** jogcímek átalakítása mindig egy [érvényesítési technikai profilból](validation-technical-profile.md) történik, amelyet [egy önérvényesítő technikai profil](self-asserted-technical-profile.md)hív meg. A **DateTimeGreaterThan** saját érvényesítésű technikai profil metaadatai szabályozzák a technikai profil által a felhasználó nak küldött hibaüzenetet. A hibaüzenetek [honosíthatók](localization-string-ids.md#claims-transformations-error-messages).

![AssertStringClaimsAreEqual végrehajtás](./media/date-transformations/assert-execution.png)

A következő példa `currentDateTime` összehasonlítja `approvedDateTime` a követelést a követeléssel. A hiba akkor `currentDateTime` jelenik `approvedDateTime`meg, ha később van, mint a. Az átalakítás az értékeket egyenlőként kezeli, ha 5 percen (30000 ezredmásodperc) belül vannak.

```XML
<ClaimsTransformation Id="AssertApprovedDateTimeLaterThanCurrentDateTime" TransformationMethod="AssertDateTimeIsGreaterThan">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="approvedDateTime" TransformationClaimType="leftOperand" />
    <InputClaim ClaimTypeReferenceId="currentDateTime" TransformationClaimType="rightOperand" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="AssertIfEqualTo" DataType="boolean" Value="false" />
    <InputParameter Id="AssertIfRightOperandIsNotPresent" DataType="boolean" Value="true" />
    <InputParameter Id="TreatAsEqualIfWithinMillseconds" DataType="int" Value="300000" />
  </InputParameters>
</ClaimsTransformation>
```

Az `login-NonInteractive` érvényesítési `AssertApprovedDateTimeLaterThanCurrentDateTime` technikai profil meghívja a jogcímek átalakítását.
```XML
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertApprovedDateTimeLaterThanCurrentDateTime" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

Az önérvényesítő technikai profil meghívja az érvényesítési **login-NonInteractive** technikai profilt.

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <Metadata>
    <Item Key="DateTimeGreaterThan">Custom error message if the provided left operand is greater than the right operand.</Item>
  </Metadata>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="login-NonInteractive" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```

### <a name="example"></a>Példa

- Bemeneti igények:
    - **leftOperand**: 2020-03-01T15:00:00.0000000Z
    - **rightOperand:** 2020-03-01T14:00:00.0000000Z
- Eredmény: Hiba történt

## <a name="convertdatetodatetimeclaim"></a>ConvertDateToDateTimeclaim

**Date** ClaimType konvertálása **DateTime** jogcímtípussá. A jogcímek átalakítása átalakítja az időformátumot, és 12:00:00-t ad a dátumhoz.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (Bemeneti jogcím) | inputClaim | dátum | A konvertálandó Jogcímtípus. |
| OutputClaim (Kimeneti jogcím) | outputClaim | Datetime | A Jogcímátalakítás meghívása után létrehozott Jogcímtípus. |

A következő példa bemutatja a `dateOfBirth` jogcím (dátum adattípus) átalakítását egy másik jogcímre `dateOfBirthWithTime` (dateTime adattípus).

```XML
  <ClaimsTransformation Id="ConvertToDateTime" TransformationMethod="ConvertDateToDateTimeClaim">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="dateOfBirth" TransformationClaimType="inputClaim" />
    </InputClaims>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="dateOfBirthWithTime" TransformationClaimType="outputClaim" />
    </OutputClaims>
  </ClaimsTransformation>
```

### <a name="example"></a>Példa

- Bemeneti igények:
    - **inputClaim**: 2020-15-03
- Kimeneti jogcímek:
    - **outputClaim**: 2020-15-03T00:00:00.0000000Z

## <a name="convertdatetimetodateclaim"></a>ConvertDateTimeTodateclaim

**DateTime** jogcímtípus dátum-jogcímtípussá alakítható át. **Date** A jogcímek átalakítása eltávolítja az időformátumot a dátumból.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (Bemeneti jogcím) | inputClaim | Datetime | A konvertálandó Jogcímtípus. |
| OutputClaim (Kimeneti jogcím) | outputClaim | dátum | A Jogcímátalakítás meghívása után létrehozott Jogcímtípus. |

A következő példa bemutatja a `systemDateTime` jogcím (dateTime adattípus) másik jogcímre `systemDate` (dátumadattípus) való átalakítását.

```XML
<ClaimsTransformation Id="ConvertToDate" TransformationMethod="ConvertDateTimeToDateClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="systemDateTime" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="systemDate" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Példa

- Bemeneti igények:
  - **inputClaim**: 2020-15-03T11:34:22.000000Z
- Kimeneti jogcímek:
  - **outputClaim**: 2020-15-03

## <a name="getcurrentdatetime"></a>GetCurrentDateTime

Az aktuális UTC dátum és idő beszerzése, és az érték hozzáadása a Jogcímtípushoz.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| OutputClaim (Kimeneti jogcím) | currentDateTime | Datetime | A Jogcímátalakítás meghívása után létrehozott Jogcímtípus. |

```XML
<ClaimsTransformation Id="GetSystemDateTime" TransformationMethod="GetCurrentDateTime">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="systemDateTime" TransformationClaimType="currentDateTime" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Példa

* Kimeneti jogcímek:
    * **currentDateTime**: 2020-15-03T11:40:35.0000000Z

## <a name="datetimecomparison"></a>DateTimeComparison összehasonlítása

Határozza meg, hogy az egyik dátumidő később, korábbi vagy egyenlő-e egy másikkal. Az eredmény egy új logikai Jogdél logikai változó, amelynek `true` értéke vagy `false`.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (Bemeneti jogcím) | firstDateTime | Datetime | Az első dateTime összehasonlítani, hogy ez korábbi vagy későbbi, mint a második dateTime. A Null érték kivételt okoz. |
| InputClaim (Bemeneti jogcím) | secondDateTime | Datetime | A második dateTime összehasonlítani, hogy ez korábbi vagy későbbi, mint az első dateTime. A null értéket a program az aktuális datetTime-ként kezeli. |
| InputParameter (Beviteli paraméter) | operátor | sztring | A következő értékek egyike: azonos, későbbi vagy korábbi, mint. |
| InputParameter (Beviteli paraméter) | timeSpanInSeconds | int | Adja hozzá az időtartományt az első datetime-hoz. |
| OutputClaim (Kimeneti jogcím) | result | logikai | A Jogcímátalakítás meghívása után létrehozott Jogcímtípus. |

Ezzel a jogcím-átalakítássegítségével határozza meg, hogy két Jogcímtípus egyenlő-e, később vagy korábban, mint egymással. Például tárolhatja azt az utolsó alkalommal, amikor egy felhasználó elfogadta a szolgáltatási feltételeket. 3 hónap elteltére megkérheti a felhasználót, hogy ismét férjen hozzá a szitarendszerhez.
A jogcím-átalakítás futtatásához először az aktuális dateTime-ot kell leállítania, és akkor is, amikor a felhasználó utoljára elfogadja a SZ.

```XML
<ClaimsTransformation Id="CompareLastTOSAcceptedWithCurrentDateTime" TransformationMethod="DateTimeComparison">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="currentDateTime" TransformationClaimType="firstDateTime" />
    <InputClaim ClaimTypeReferenceId="extension_LastTOSAccepted" TransformationClaimType="secondDateTime" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="operator" DataType="string" Value="later than" />
    <InputParameter Id="timeSpanInSeconds" DataType="int" Value="7776000" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isLastTOSAcceptedGreaterThanNow" TransformationClaimType="result" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Példa

- Bemeneti igények:
    - **firstDateTime**: 2020-01-01T00:00:00.100000Z
    - **secondDateTime**: 2020-04-01T00:00:00.100000Z
- Bemeneti paraméterek:
    - **üzemeltető:** későbbi, mint
    - **timeSpanInSeconds**: 7776000 (90 nap)
- Kimeneti jogcímek:
    - **eredmény**: igaz
