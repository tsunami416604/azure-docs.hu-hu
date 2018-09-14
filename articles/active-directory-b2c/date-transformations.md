---
title: Dátum-jogcímek átalakítása példák az identitás élmény keretrendszer sémát az Azure Active Directory B2C |} A Microsoft Docs
description: Dátum jogcímek átalakítása példák az identitás élmény keretrendszer sémát az Azure Active Directory B2C a.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: ff96b9a63e7340788ef2474ce9934145c184e1e1
ms.sourcegitcommit: f983187566d165bc8540fdec5650edcc51a6350a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/13/2018
ms.locfileid: "45542769"
---
# <a name="date-claims-transformations"></a>Dátum jogcím-átalakítás

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ez a cikk példákat ad az identitás-kezelőfelületi keretrendszer séma Dátumátalakítások jogcímek használata az Azure Active Directory (Azure AD) B2C-ben. További információkért lásd: [ClaimsTransformations](claimstransformations.md).

## <a name="assertdatetimeisgreaterthan"></a>AssertDateTimeIsGreaterThan 

Ellenőrzi, hogy egy dátum és idő jogcím (karakterlánc) adattípusa nagyobb, mint a második dátum és idő jogcím (karakterlánc adattípusú), és kivételt jelez.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| Bemeneti jogcím | leftOperand | sztring | Első jogcím típusa, amely a második jogcím nagyobbnak kell lennie. |
| Bemeneti jogcím | rightOperand | sztring | Második jogcím típusa, ami lehet kisebb, mint az első jogcímet. |
| InputParameter | AssertIfEqualTo | logikai | Itt adhatja meg, hogy ezt az előfeltételt kell átadnia, ha a bal oldali operandusához megegyezik a jobb oldali operandusnak. |
| InputParameter | AssertIfRightOperandIsNotPresent | logikai | Itt adhatja meg, hogy ezt az előfeltételt kell átadnia, ha a jobb oldali operandusa nem található. |
| InputParameter | TreatAsEqualIfWithinMillseconds | int | Itt adhatja meg, hogy a kettő között ezredmásodpercben kell figyelembe venni az időpontokat időpontok egyenlő (például fiók torzulása óráját). |

A **AssertDateTimeIsGreaterThan** jogcímek átalakítását a rendszer mindig futtatja a egy [ellenőrzési technikai profil](validation-technical-profile.md) , hívja meg a [önálló kiszolgáló által megerősített technikai profil](self-asserted-technical-profile.md). A **DateTimeGreaterThan** önellenőrzött technikai profil metaadataiban szabályozza a hibaüzenet, amely a technikai profil jeleníti meg a felhasználó számára.

![AssertStringClaimsAreEqual végrehajtása](./media/date-transformations/assert-execution.png)

A következő példában a `currentDateTime` jogcím a `approvedDateTime` jogcím. Hiba lépett fel, ha `currentDateTime` nagyobb, mint `approvedDateTime`. Az átalakítás értékek, egyenlő, ha azok belül 5 perc (30000 ezredmásodperc) különbség kezeli.

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

A `login-NonInteractive` érvényesítési technikai profil hívások a `AssertApprovedDateTimeLaterThanCurrentDateTime` jogcím-átalakítás.
```XML
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertApprovedDateTimeLaterThanCurrentDateTime" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

Az önellenőrzött technikai profilban meghívja az érvényesítés **bejelentkezési nem interaktív** technikai profil.

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

- A bemeneti jogcímek:
    - **leftOperand**: 2018-10-01T15:00:00.0000000Z
    - **rightOperand**: 2018-10-01T14:00:00.0000000Z
- Eredmény: Hiba lépett fel


## <a name="convertdatetodatetimeclaim"></a>ConvertDateToDateTimeClaim

Konvertálja a **dátum** takar, egy **DateTime** takar. A jogcímek átalakítását a időformátumra alakítja át, és 12:00:00-kor hozzáadja a dátumhoz.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| Bemeneti jogcím | Bemeneti jogcím | dátum | A ClaimType konvertálni. |
| kimeneti jogcím | kimeneti jogcím | Dátum és idő | A takar, amelyek a ClaimsTransformation meghívása után jön létre. |

A következő példa bemutatja a jogcím átalakítása `dateOfBirth` (dátum adattípusú) egy másik jogcímek `dateOfBirthWithTime` (dátum/idő adattípussal).

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

- A bemeneti jogcímek:
    - **bemeneti jogcím**: a 2019-06-01
- Kimeneti jogcímek:
    - **kimeneti jogcím**: 1559347200 (június 1 2019 12:00:00-kor)

## <a name="getcurrentdatetime"></a>GetCurrentDateTime

Az aktuális UTC-dátum és idő lekérése, és adja hozzá az érték egy takar.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| kimeneti jogcím | currentDateTime | Dátum és idő | A takar, amelyek a ClaimsTransformation meghívása után jön létre. |

```XML
<ClaimsTransformation Id="GetSystemDateTime" TransformationMethod="GetCurrentDateTime">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="systemDateTime" TransformationClaimType="currentDateTime" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Példa

* Kimeneti jogcímek:
    * **currentDateTime**: 1534418820 (16, 2018 augusztus 11:27:00-kor)

## <a name="datetimecomparison"></a>DateTimeComparison

Határozza meg, hogy egy dátum és idő nagyobb, kisebb vagy egyenlő másik-e. Ez egy igaz vagy hamis értéket az új logikai takar logikai.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| Bemeneti jogcím | firstDateTime | Dátum és idő | Az első dateTime összehasonlítására. NULL érték kivételt jelez. |
| Bemeneti jogcím | secondDateTime | Dátum és idő | A második dátum és idő végrehajtásához. NULL érték aktuális datetTime kezeli. |
| InputParameter | Operátor | sztring | Következő értékek egyikét: ugyanazt, későbbi, mint vagy régebbi. |
| InputParameter | timeSpanInSeconds | int | Adja hozzá a timespan első dátuma és időpontja. |
| kimeneti jogcím | Eredmény | logikai | A takar, amelyek a ClaimsTransformation meghívása után jön létre. |

Használja a jogcím-átalakítás annak meghatározásához, hogy két ClaimTypes, nagyobb, kisebb vagy egyenlő egymástól. Például előfordulhat, hogy az utolsó időpont, a felhasználó elfogadta a szolgáltatások (TOS) tárolja. 3 hónap letelte után kérje meg a felhasználó ismét hozzáférhet a távközlési.
Futtassa a jogcím-átalakítást, szüksége lesz az aktuális dátum és idő lekérni, és is a legutóbbi alkalommal felhasználó fogadja el a távközlési.

```XML
<ClaimsTransformation Id="CompareLastTOSAcceptedWithCurrentDateTime" TransformationMethod="DateTimeComparison">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="currentDateTime" TransformationClaimType="firstDateTime" />
    <InputClaim ClaimTypeReferenceId="extension_LastTOSAccepted" TransformationClaimType="secondDateTime" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="operator" DataType="string" Value="greater than" />
    <InputParameter Id="timeSpanInSeconds" DataType="int" Value="7776000" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isLastTOSAcceptedGreaterThanNow" TransformationClaimType="result" />
  </OutputClaims>      
</ClaimsTransformation>
```

### <a name="example"></a>Példa

- A bemeneti jogcímek:
    - **firstDateTime**: 2018-01-01T00:00:00.100000Z
    - **secondDateTime**: 2018-04-01T00:00:00.100000Z
- Bemeneti paraméterek:
    - **operátor**: nagyobb, mint
    - **timeSpanInSeconds**: 7776000 (90 nap)
- Kimeneti jogcímek: 
    - **eredmény**: igaz

