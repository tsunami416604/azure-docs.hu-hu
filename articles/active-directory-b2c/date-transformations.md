---
title: A jogcímek átalakítására vonatkozó példák egyéni házirendekhez
description: A jogcímek átalakítására vonatkozó példák a Azure Active Directory B2C Identity Experience Framework (IEF) sémájához.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/03/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: f3e5a7b90892f0ed0243d448ea1ac63fb56f277f
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78188834"
---
# <a name="date-claims-transformations"></a>A jogcímek átalakításának dátuma

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ez a cikk példákat tartalmaz a Azure Active Directory B2C (Azure AD B2C) Identity Experience Framework sémájának a Date jogcím-átalakításának használatára. További információ: [ClaimsTransformations](claimstransformations.md).

## <a name="assertdatetimeisgreaterthan"></a>AssertDateTimeIsGreaterThan

Ellenőrzi, hogy egy dátum-és időjogcím (karakterlánc adattípusa) későbbi-e, mint egy második dátum és idő jogcím (string adattípus), és kivételt dob.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | leftOperand | Karakterlánc | Az első jogcím típusa, amelynek a második jogcímnél későbbinek kell lennie. |
| inputClaim | rightOperand | Karakterlánc | Második jogcím típusa, amelynek az első jogcímnél korábbinak kell lennie. |
| InputParameter | AssertIfEqualTo | Logikai érték | Meghatározza, hogy ez az állítás akkor legyen-e, ha a bal oldali operandus egyenlő a jobb oldali operandussal. |
| InputParameter | AssertIfRightOperandIsNotPresent | Logikai érték | Azt adja meg, hogy ez az állítás a megfelelő operandus hiánya esetén legyen-e átadva. |
| InputParameter | TreatAsEqualIfWithinMillseconds | int | Megadja, hogy hány ezredmásodpercet kell engedélyezni a két dátum között, hogy az időpontok egyenlőek legyenek (például az óra eldöntése miatt). |

Az **AssertDateTimeIsGreaterThan** jogcímek átalakítását mindig egy [önérvényesített technikai profil](self-asserted-technical-profile.md)által hívott [érvényesítési műszaki profilból](validation-technical-profile.md) hajtja végre a rendszer. A **DateTimeGreaterThan** önérvényesített technikai profil metaadatai a technikai profil által a felhasználónak megjelenített hibaüzenetet vezérlik.

![AssertStringClaimsAreEqual-végrehajtás](./media/date-transformations/assert-execution.png)

Az alábbi példa összehasonlítja az `currentDateTime` jogcímet a `approvedDateTime` jogcímsel. A rendszer hibát jelez, ha a `currentDateTime` későbbi, mint `approvedDateTime`. Az átalakítás egyenlőként kezeli az értékeket, ha 5 percen belül (30000 ezredmásodperc) eltérnek.

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

A `login-NonInteractive` érvényesítő technikai profil meghívja a `AssertApprovedDateTimeLaterThanCurrentDateTime` jogcím-átalakítást.
```XML
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertApprovedDateTimeLaterThanCurrentDateTime" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

Az önérvényesített technikai profil meghívja az érvényesítési **bejelentkezést nem interaktív** technikai profilt.

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

- Bemeneti jogcímek:
    - **leftOperand**: 2018 – 10 – 01T15:00:00.0000000 z
    - **rightOperand**: 2018 – 10 – 01T14:00:00.0000000 z
- Eredmény: feldobott hiba

## <a name="convertdatetodatetimeclaim"></a>ConvertDateToDateTimeClaim

A **dátum** claimType egy **datetime** claimType alakítja át. A jogcím-átalakítás átalakítja az időformátumot, és hozzáadja a 12:00:00-as dátumot a dátumhoz.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | inputClaim | dátum | Az átalakítandó ClaimType. |
| outputClaim | outputClaim | Dátum és idő | A ClaimsTransformation után létrehozott ClaimType meghívása megtörtént. |

Az alábbi példa azt mutatja be, hogy a jogcím `dateOfBirth` (dátum adattípus) átalakítása egy másik jogcímre `dateOfBirthWithTime` (dateTime adattípus).

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

- Bemeneti jogcímek:
    - **inputClaim**: 2019-06-01
- Kimeneti jogcímek:
    - **outputClaim**: 1559347200 (2019 12:00:00. június 1.)

## <a name="convertdatetimetodateclaim"></a>ConvertDateTimeToDateClaim

Egy **datetime** claimType alakít át egy **dátum** -claimType. A jogcím-átalakítás eltávolítja az időformátumot a dátumból.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | inputClaim | Dátum és idő | Az átalakítandó ClaimType. |
| outputClaim | outputClaim | dátum | A ClaimsTransformation után létrehozott ClaimType meghívása megtörtént. |

Az alábbi példa azt mutatja be, hogy a jogcím `systemDateTime` (dateTime adattípus) átalakítása egy másik jogcímre `systemDate` (dátum adattípus).

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

- Bemeneti jogcímek:
  - **inputClaim**: 1559347200 (2019 12:00:00. június 1.)
- Kimeneti jogcímek:
  - **outputClaim**: 2019-06-01

## <a name="getcurrentdatetime"></a>GetCurrentDateTime

Az aktuális UTC dátum és idő lekérése és az érték hozzáadása egy ClaimType.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| outputClaim | currentDateTime | Dátum és idő | A ClaimsTransformation után létrehozott ClaimType meghívása megtörtént. |

```XML
<ClaimsTransformation Id="GetSystemDateTime" TransformationMethod="GetCurrentDateTime">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="systemDateTime" TransformationClaimType="currentDateTime" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Példa

* Kimeneti jogcímek:
    * **currentDateTime**: 1534418820 (augusztus 16, 2018 11:27:00 am)

## <a name="datetimecomparison"></a>DateTimeComparison

Annak megállapítása, hogy egy dateTime később, korábban vagy egyenlő-e egy másikkal. Az eredmény egy új logikai ClaimType logikai érték, amelynek értéke `true` vagy `false`.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | firstDateTime | Dátum és idő | Az első dateTime érték, amely azt hasonlítja össze, hogy korábbi vagy későbbi, mint a második dateTime. A Null érték kivételt jelez. |
| inputClaim | secondDateTime | Dátum és idő | A második dátum és idő, amely azt hasonlítja össze, hogy az előbbi vagy későbbi, mint az első dateTime. A null értéket a rendszer az aktuális datetTime kezeli. |
| InputParameter | operator | Karakterlánc | A következő értékek egyike: azonos, későbbi, vagy korábbi, mint. |
| InputParameter | timeSpanInSeconds | int | Adja hozzá a TimeSpan az első datetime értékhez. |
| outputClaim | Eredmény | Logikai érték | A ClaimsTransformation után létrehozott ClaimType meghívása megtörtént. |

Ezzel a jogcím-átalakítással megállapíthatja, hogy két ClaimTypes egyenlő, később vagy korábban, mint egymást. Előfordulhat például, hogy a felhasználó az utolsó alkalommal fogadta el a szolgáltatási feltételeket (TOS). 3 hónap elteltével megkérheti a felhasználót, hogy ismét hozzáférjen a TSz-hez.
A jogcím-átalakítás futtatásához először le kell kérnie az aktuális dateTime értéket, és az utolsó alkalommal a felhasználó elfogadja a TSz-ket.

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

- Bemeneti jogcímek:
    - **firstDateTime**: 2018-01-01T00:00:00.100000 z
    - **secondDateTime**: 2018-04-01T00:00:00.100000 z
- Bemeneti paraméterek:
    - **operátor**: később, mint
    - **timeSpanInSeconds**: 7776000 (90 nap)
- Kimeneti jogcímek:
    - **eredmény**: igaz
