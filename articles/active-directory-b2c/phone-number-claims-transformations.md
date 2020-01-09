---
title: Az egyéni házirendekben a telefonszámot jogcímek átalakítása
titleSuffix: Azure AD B2C
description: Egyéni házirend-hivatkozás a Azure AD B2Cban található telefonszám-jogcímek átalakításához.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: c43e3386886456eed0c58fefd0fb1212795db66c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75480163"
---
# <a name="define-phone-number-claims-transformations-in-azure-ad-b2c"></a>Telefonszám-jogcímek átalakításának meghatározása Azure AD B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ez a cikk a Azure Active Directory B2C (Azure AD B2C) identitás-keretrendszer sémájának a telefonszám-jogcímek átalakításának használatára vonatkozó hivatkozásokat és példákat tartalmaz. A jogcímek átalakításával kapcsolatos további információkért lásd: [ClaimsTransformations](claimstransformations.md).

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="convertstringtophonenumberclaim"></a>ConvertStringToPhoneNumberClaim

Ez a jogcím érvényesíti a telefonszám formátumát. Ha érvényes formátumú, módosítsa az Azure AD B2C által használt szabványos formátumra. Ha a megadott telefonszám formátuma érvénytelen, hibaüzenetet kap.

| Tétel | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | sztring | A karakterlánc-típusra való áttérés jogcíme. |
| OutputClaim | outputClaim | sztring | A jogcímek átalakításának eredménye. |

A **ConvertStringToPhoneNumberClaim** jogcímek átalakítását a rendszer mindig egy [önérvényesített technikai profil](self-asserted-technical-profile.md) vagy [megjelenítési vezérlő](display-controls.md)által hívott [érvényesítési műszaki profilból](validation-technical-profile.md) hajtja végre. A **UserMessageIfClaimsTransformationInvalidPhoneNumber** önérvényesített technikai profil metaadatai a felhasználónak megjelenített hibaüzenetet vezérlik.

![A hibaüzenet végrehajtási útvonalának ábrája](./media/phone-authentication/assert-execution.png)

A jogcím-átalakítással ellenőrizheti, hogy a megadott karakterlánc-jogcím érvényes telefonszám-e. Ha nem, a rendszer hibaüzenetet küld. A következő példa ellenőrzi, hogy a **phoneString** claimType valóban érvényes telefonszám-e, majd a telefonszámot a standard Azure ad B2C formátumban adja vissza. Ellenkező esetben a rendszer hibaüzenetet küld.

```XML
<ClaimsTransformation Id="ConvertStringToPhoneNumber" TransformationMethod="ConvertStringToPhoneNumberClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phoneString" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

A jogcímek átalakítását tartalmazó, az érvényesítési technikai profilt meghívó önérvényesített technikai profil meghatározza a hibaüzenetet.

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignup-Phone">
  <Metadata>
    <Item Key="UserMessageIfClaimsTransformationInvalidPhoneNumber">Custom error message if the phone number is not valid.</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

### <a name="example"></a>Példa

- Bemeneti jogcímek:
  - **inputClaim**: + 1 (123) 456-7890
- Kimeneti jogcímek:
  - **outputClaim**: + 11234567890

## <a name="getnationalnumberandcountrycodefromphonenumberstring"></a>GetNationalNumberAndCountryCodeFromPhoneNumberString

Ezzel kinyeri az országkód és a nemzeti számot a bemeneti jogcímen, és opcionálisan kivételt jelez, ha a megadott telefonszám érvénytelen.

| Tétel | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | Telefonszám | sztring | A telefonszám karakterlánc-jogcíme. A telefonszámnak nemzetközi formátumúnak kell lennie, és meg kell felelnie a kezdő "+" és az országkód értékének. |
| InputParameter | throwExceptionOnFailure | logikai | Választható Egy paraméter, amely azt jelzi, hogy a rendszer kivételt jelez-e, ha a telefonszám érvénytelen. Az alapértelmezett érték false (hamis). |
| InputParameter | countryCodeType | sztring | Választható Egy paraméter, amely az országkód típusát jelzi a kimeneti jogcímben. Az elérhető értékek a **CallingCode** (az ország nemzetközi hívási kódja, például + 1) vagy a **ISO3166** (a kétbetűs ISO-3166 országkód). |
| OutputClaim | nationalNumber | sztring | A telefonszám országos számára vonatkozó jogcímek száma. |
| OutputClaim | Országhívószám | sztring | A telefonszám országkódot megadó karakterlánc-jogcím. |


Ha a **GetNationalNumberAndCountryCodeFromPhoneNumberString** jogcím-átalakítás olyan [érvényesítési műszaki profilból](validation-technical-profile.md) lett végrehajtva, amelyet egy [önérvényesített technikai profil](self-asserted-technical-profile.md) vagy egy [megjelenítési vezérlő művelet hív meg](display-controls.md#display-control-actions), akkor a **UserMessageIfPhoneNumberParseFailure** önérvényesített technikai profil metaadatai a felhasználónak megjelenített hibaüzenetet vezérlik.

![A hibaüzenet végrehajtási útvonalának ábrája](./media/phone-authentication/assert-execution.png)

A jogcímek átalakításával teljes telefonszámot oszthat meg az országkód és a nemzeti szám szerint. Ha a megadott telefonszám érvénytelen, dönthet úgy, hogy eldönti, hogy van-e hibaüzenet.

A következő példában a telefonszámot a nemzeti szám és az országkód alapján kell megosztani. Ha a telefonszám érvényes, a telefonszámot a rendszer felülbírálja a nemzeti szám alapján. Ha a telefonszám érvénytelen, a rendszer nem dobja el a kivételt, és a telefonszám továbbra is az eredeti értékkel rendelkezik.

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

A jogcímek átalakítását tartalmazó, az érvényesítési technikai profilt meghívó önérvényesített technikai profil meghatározza a hibaüzenetet.

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignup-Phone">
  <Metadata>
    <Item Key="UserMessageIfPhoneNumberParseFailure">Custom error message if the phone number is not valid.</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

### <a name="example-1"></a>1\. példa

- Bemeneti jogcímek:
  - **telefonszám**: + 49 (123) 456-7890
- Bemeneti paraméterek:
  - **throwExceptionOnFailure**: hamis
  - **countryCodeType**: ISO3166
- Kimeneti jogcímek:
  - **nationalNumber**: 1234567890
  - **országhívószám**: de

### <a name="example-2"></a>2\. példa

- Bemeneti jogcímek:
  - **telefonszám**: + 49 (123) 456-7890
- Bemeneti paraméterek
  - **throwExceptionOnFailure**: hamis
  - **countryCodeType**: CallingCode
- Kimeneti jogcímek:
  - **nationalNumber**: 1234567890
  - **országhívószám**: + 49
