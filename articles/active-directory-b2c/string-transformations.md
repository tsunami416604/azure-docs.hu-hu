---
title: Karakterlánc-jogcím-átalakítási példák egyéni házirendekhez
titleSuffix: Azure AD B2C
description: Karakterlánc-jogcím-átalakítási példák a Azure Active Directory B2C Identity Experience Framework (IEF) sémájához.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/24/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 4a5d0908842c20e15fdf7b336b9e244c4bafb345
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79264296"
---
# <a name="string-claims-transformations"></a>Karakterlánc-jogcímek átalakítása

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ez a cikk példákat tartalmaz a Azure Active Directory B2C (Azure AD B2C) Identity Experience Framework-sémájának karakterlánc-jogcímek átalakítására. További információ: [ClaimsTransformations](claimstransformations.md).

## <a name="assertstringclaimsareequal"></a>AssertStringClaimsAreEqual

Hasonlítsa össze a két jogcímet, és kivételt képez, ha a megadott összehasonlító inputClaim1, a inputClaim2 és a Stringcomparison argumentummal szerint nem egyeznek.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | inputClaim1 | sztring | Az első jogcím típusa, amelyet össze kell hasonlítani. |
| inputClaim | inputClaim2 | sztring | A második jogcím típusa, amelyet össze kell hasonlítani. |
| InputParameter | Stringcomparison argumentummal | sztring | karakterlánc-összehasonlítás, az értékek egyike: sorszám, OrdinalIgnoreCase. |

Az **AssertStringClaimsAreEqual** jogcím-átalakítás mindig egy [önérvényesített technikai profil](self-asserted-technical-profile.md)vagy egy [DisplayConrtol](display-controls.md)által hívott [érvényesítési műszaki profilból](validation-technical-profile.md) történik. Az önellenőrzött technikai profilok `UserMessageIfClaimsTransformationStringsAreNotEqual` metaadatai a felhasználó számára megjelenített hibaüzenetet vezérlik.


![AssertStringClaimsAreEqual-végrehajtás](./media/string-transformations/assert-execution.png)

A jogcímek átalakításával gondoskodhat arról, hogy két ClaimTypes ugyanazzal az értékkel rendelkezzen. Ha nem, a rendszer hibaüzenetet küld. A következő példa ellenőrzi, hogy a **strongAuthenticationEmailAddress** claimType egyenlő **-e az e-mail-** claimType. Ellenkező esetben a rendszer hibaüzenetet küld.

```XML
<ClaimsTransformation Id="AssertEmailAndStrongAuthenticationEmailAddressAreEqual" TransformationMethod="AssertStringClaimsAreEqual">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="strongAuthenticationEmailAddress" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="stringComparison" DataType="string" Value="ordinalIgnoreCase" />
  </InputParameters>
</ClaimsTransformation>
```


A **bejelentkezés – nem interaktív** érvényesítő technikai profil meghívja a **AssertEmailAndStrongAuthenticationEmailAddressAreEqual** jogcím-átalakítást.
```XML
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertEmailAndStrongAuthenticationEmailAddressAreEqual" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

Az önérvényesített technikai profil meghívja az érvényesítési **bejelentkezést nem interaktív** technikai profilt.

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <Metadata>
    <Item Key="UserMessageIfClaimsTransformationStringsAreNotEqual">Custom error message the email addresses you provided are not the same.</Item>
  </Metadata>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="login-NonInteractive" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```

### <a name="example"></a>Példa

- Bemeneti jogcímek:
  - **inputClaim1**: someone@contoso.com
  - **inputClaim2**: someone@outlook.com
- Bemeneti paraméterek:
  - **stringcomparison argumentummal**: ordinalIgnoreCase
- Eredmény: feldobott hiba

## <a name="changecase"></a>ChangeCase

A megadott jogcím esetét az operátortól függően az alacsonyabb vagy a nagybetűs értékre módosítja.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | inputClaim1 | sztring | A módosítandó ClaimType. |
| InputParameter | toCase | sztring | A következő értékek egyike: `LOWER` vagy `UPPER`. |
| outputClaim | outputClaim | sztring | A jogcím-átalakítás után létrehozott ClaimType meghívása megtörtént. |

Használja ezt a jogcím-átalakítást, ha bármilyen karakterláncot ClaimType, amely kisebb vagy nagybetűs lehet.

```XML
<ClaimsTransformation Id="ChangeToLower" TransformationMethod="ChangeCase">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim1" />
  </InputClaims>
<InputParameters>
  <InputParameter Id="toCase" DataType="string" Value="LOWER" />
</InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Példa

- Bemeneti jogcímek:
  - **e-mail**: SomeOne@contoso.com
- Bemeneti paraméterek:
    - **toCase**: alacsonyabb
- Kimeneti jogcímek:
  - **e-mail**: someone@contoso.com

## <a name="createstringclaim"></a>CreateStringClaim

Létrehoz egy karakterlánc-jogcímet a megadott bemeneti paraméterből az átalakításban.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
|----- | ----------------------- | --------- | ----- |
| InputParameter | érték | sztring | A beállítani kívánt karakterlánc. Ez a bemeneti paraméter támogatja a [karakterlánc-jogcímek átalakítási kifejezéseit](string-transformations.md#string-claim-transformations-expressions). |
| outputClaim | createdClaim | sztring | A jogcím-átalakítást követően létrehozott ClaimType a bemeneti paraméterben megadott értékkel lett meghívva. |

A jogcím-átalakítás használatával ClaimType értéket állíthat be.

```XML
<ClaimsTransformation Id="CreateTermsOfService" TransformationMethod="CreateStringClaim">
  <InputParameters>
    <InputParameter Id="value" DataType="string" Value="Contoso terms of service..." />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="TOS" TransformationClaimType="createdClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Példa

- Bemeneti paraméter:
    - **érték**: contoso szolgáltatási feltételek...
- Kimeneti jogcímek:
    - **createdClaim**: a TOS claimType tartalmazza a "contoso szolgáltatási feltételeket..." érték.

## <a name="compareclaims"></a>CompareClaims

Annak megállapítása, hogy egy karakterlánc-jogcím egyenlő-e egy másikkal. Az eredmény egy új, `true` vagy `false`értékű logikai ClaimType.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | inputClaim1 | sztring | Az első jogcím típusa, amelyet össze kell hasonlítani. |
| inputClaim | inputClaim2 | sztring | Második jogcím típusa, amelyet össze kell hasonlítani. |
| InputParameter | operator | sztring | Lehetséges értékek: `EQUAL` vagy `NOT EQUAL`. |
| InputParameter | ignoreCase | logikai | Meghatározza, hogy az összehasonlítás figyelmen kívül hagyja-e az összehasonlított karakterláncok esetét. |
| outputClaim | outputClaim | logikai | A jogcím-átalakítás után létrehozott ClaimType meghívása megtörtént. |

Ezzel a jogcím-átalakítással ellenőrizhető, hogy egy jogcím egy másik jogcímnek felel-e meg. Például a következő jogcím-átalakítás ellenőrzi, hogy az **e-mail-** jogcím értéke megegyezik-e az **ellenőrzött. e-mail** -jogcím értékével.

```XML
<ClaimsTransformation Id="CheckEmail" TransformationMethod="CompareClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="Email" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="Verified.Email" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="operator" DataType="string" Value="NOT EQUAL" />
    <InputParameter Id="ignoreCase" DataType="string" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="SameEmailAddress" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Példa

- Bemeneti jogcímek:
  - **inputClaim1**: someone@contoso.com
  - **inputClaim2**: someone@outlook.com
- Bemeneti paraméterek:
    - **operátor**: nem egyenlő
    - **ignoreCase**: true
- Kimeneti jogcímek:
    - **outputClaim**: true

## <a name="compareclaimtovalue"></a>CompareClaimToValue

Meghatározza, hogy a jogcím értéke megegyezik-e a bemeneti paraméter értékével.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | inputClaim1 | sztring | A jogcím típusa, amelyet össze kell hasonlítani. |
| InputParameter | operator | sztring | Lehetséges értékek: `EQUAL` vagy `NOT EQUAL`. |
| InputParameter | Compareto metódus végrehajtása | sztring | karakterlánc-összehasonlítás, az értékek egyike: sorszám, OrdinalIgnoreCase. |
| InputParameter | ignoreCase | logikai | Meghatározza, hogy az összehasonlítás figyelmen kívül hagyja-e az összehasonlított karakterláncok esetét. |
| outputClaim | outputClaim | logikai | A jogcím-átalakítás után létrehozott ClaimType meghívása megtörtént. |

A jogcímek átalakításával ellenőrizhető, hogy egy jogcím egyenlő-e a megadott értékkel. Például a következő jogcím-átalakítás ellenőrzi, hogy a **termsOfUseConsentVersion** jogcím értéke `v1`-e.

```XML
<ClaimsTransformation Id="IsTermsOfUseConsentRequiredForVersion" TransformationMethod="CompareClaimToValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="termsOfUseConsentVersion" TransformationClaimType="inputClaim1" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="compareTo" DataType="string" Value="V1" />
    <InputParameter Id="operator" DataType="string" Value="not equal" />
    <InputParameter Id="ignoreCase" DataType="string" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="termsOfUseConsentRequired" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Példa
- Bemeneti jogcímek:
    - **inputClaim1**: v1
- Bemeneti paraméterek:
    - **compareto metódus végrehajtása**: v1
    - **operátor**: egyenlő
    - **ignoreCase**: true
- Kimeneti jogcímek:
    - **outputClaim**: true

## <a name="createrandomstring"></a>CreateRandomString

Egy véletlenszerű karakterláncot hoz létre a véletlenszám-generátor használatával. Ha a véletlenszám-generátor `integer`típusú, opcionálisan egy vetőmag-paramétert is meg lehet adni. Egy opcionális karakterlánc-formátum paraméterrel a kimenet formázható, és egy opcionális Base64-paraméter azt határozza meg, hogy a kimenet Base64 kódolású randomGeneratorType [GUID, integer] outputClaim (karakterlánc).

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputParameter | randomGeneratorType | sztring | Meghatározza a létrehozandó véletlenszerű értéket, `GUID` (globális egyedi azonosító) vagy `INTEGER` (egy szám). |
| InputParameter | stringFormat | sztring | Választható A véletlenszerű érték formázása. |
| InputParameter | Base64 | logikai | Választható Alakítsa át a véletlenszerű értéket Base64-re. Ha a karakterlánc formátuma van alkalmazva, akkor az érték a karakterlánc formátuma után Base64-re van kódolva. |
| InputParameter | maximumNumber | int | Választható Csak `INTEGER` randomGeneratorType. A maximális szám megadására. |
| InputParameter | kezdőérték  | int | Választható Csak `INTEGER` randomGeneratorType. A véletlenszerű értékhez tartozó magot határozza meg. Megjegyzés: ugyanaz a mag a véletlenszerűen kiválasztott számok azonos sorrendjét eredményezi. |
| outputClaim | outputClaim | sztring | A jogcím-átalakítás meghívása után előállított ClaimTypes. A véletlenszerű érték. |

A következő példa globális egyedi azonosítót hoz létre. A jogcím-átalakítás a véletlenszerű UPN (felhasználói elv neve) létrehozásához használatos.

```XML
<ClaimsTransformation Id="CreateRandomUPNUserName" TransformationMethod="CreateRandomString">
  <InputParameters>
    <InputParameter Id="randomGeneratorType" DataType="string" Value="GUID" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="upnUserName" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```
### <a name="example"></a>Példa

- Bemeneti paraméterek:
    - **randomGeneratorType**: GUID
- Kimeneti jogcímek:
    - **outputClaim**: bc8bedd2-AAA3-411e-bdee-2f1810b73dfc

Az alábbi példa egy 0 és 1000 közötti egész számú véletlenszerű értéket hoz létre. Az érték OTP_ {Random Value} formátumra van formázva.

```XML
<ClaimsTransformation Id="SetRandomNumber" TransformationMethod="CreateRandomString">
  <InputParameters>
    <InputParameter Id="randomGeneratorType" DataType="string" Value="INTEGER" />
    <InputParameter Id="maximumNumber" DataType="int" Value="1000" />
    <InputParameter Id="stringFormat" DataType="string" Value="OTP_{0}" />
    <InputParameter Id="base64" DataType="boolean" Value="false" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="randomNumber" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Példa

- Bemeneti paraméterek:
    - **randomGeneratorType**: egész szám
    - **maximumNumber**: 1000
    - **stringFormat**: OTP_{0}
    - **Base64**: hamis
- Kimeneti jogcímek:
    - **outputClaim**: OTP_853


## <a name="formatstringclaim"></a>FormatStringClaim

Jogcím formázása a megadott formátumú karakterláncnak megfelelően. Ez a transzformáció a C# `String.Format` metódust használja.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | inputClaim |sztring |Az a ClaimType, amely karakterlánc-formátumot {0} paraméterként viselkedik. |
| InputParameter | stringFormat | sztring | A karakterlánc formátuma, beleértve a {0} paramétert. Ez a bemeneti paraméter támogatja a [karakterlánc-jogcímek átalakítási kifejezéseit](string-transformations.md#string-claim-transformations-expressions).  |
| outputClaim | outputClaim | sztring | A jogcím-átalakítás után létrehozott ClaimType meghívása megtörtént. |

Ezzel a jogcím-átalakítással formázhat bármely karakterláncot egy paraméterrel {0}. A következő példa egy **userPrincipalName**hoz létre. Az összes közösségi identitás-szolgáltató technikai profilja, például a `Facebook-OAUTH` meghívja a **CreateUserPrincipalName** egy **userPrincipalName**létrehozásához.

```XML
<ClaimsTransformation Id="CreateUserPrincipalName" TransformationMethod="FormatStringClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="upnUserName" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="stringFormat" DataType="string" Value="cpim_{0}@{RelyingPartyTenantId}" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="userPrincipalName" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Példa

- Bemeneti jogcímek:
    - **inputClaim**: 5164db16-3eee-4629-bfda-dcc3326790e9
- Bemeneti paraméterek:
    - **stringFormat**: cpim_{0}@ {RelyingPartyTenantId}
- Kimeneti jogcímek:
  - **outputClaim**: cpim_5164db16-3eee-4629-bfda-dcc3326790e9@b2cdemo.onmicrosoft.com

## <a name="formatstringmultipleclaims"></a>FormatStringMultipleClaims

Két jogcím formázása a megadott formátumú karakterláncnak megfelelően. Ez a transzformáció a C# `String.Format` metódust használja.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | inputClaim |sztring | Az a ClaimType, amely karakterlánc-formátumot {0} paraméterként viselkedik. |
| inputClaim | inputClaim | sztring | Az a ClaimType, amely karakterlánc-formátumot {1} paraméterként viselkedik. |
| InputParameter | stringFormat | sztring | A karakterlánc formátuma, beleértve a {0} és {1} paramétereket. Ez a bemeneti paraméter támogatja a [karakterlánc-jogcímek átalakítási kifejezéseit](string-transformations.md#string-claim-transformations-expressions).   |
| outputClaim | outputClaim | sztring | A jogcím-átalakítás után létrehozott ClaimType meghívása megtörtént. |

Ezzel a jogcím-átalakítással formázhat bármely karakterláncot két paraméterrel, {0} és {1}. A következő példa egy **DisplayName** paramétert hoz létre a megadott formátumban:

```XML
<ClaimsTransformation Id="CreateDisplayNameFromFirstNameAndLastName" TransformationMethod="FormatStringMultipleClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="surName" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="stringFormat" DataType="string" Value="{0} {1}" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="displayName" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Példa

- Bemeneti jogcímek:
    - **inputClaim1**: Joe
    - **inputClaim2**: Fernando
- Bemeneti paraméterek:
    - **stringFormat**: {0} {1}
- Kimeneti jogcímek:
    - **outputClaim**: Joe Fernando

## <a name="getlocalizedstringstransformation"></a>GetLocalizedStringsTransformation

Honosított karakterláncokat helyez át jogcímekre.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| outputClaim | A honosított karakterlánc neve | sztring | A jogcímek átalakításának meghívása után létrehozott jogcím-típusok listája. |

A GetLocalizedStringsTransformation jogcím-átalakítás használata:

1. Definiáljon egy [honosító karakterláncot](localization.md) , és társítsa azt egy [önérvényesített – technikai profilhoz](self-asserted-technical-profile.md).
1. A `LocalizedString` elem `ElementType`át `GetLocalizedStringsTransformationClaimType`értékre kell beállítani.
1. A `StringId` egy egyedi azonosító, amelyet Ön határoz meg, és később is használhatja a jogcím-átalakításban.
1. A jogcím-átalakítás lapon adja meg a honosított karakterlánccal beállítani kívánt jogcímek listáját. A `ClaimTypeReferenceId` egy, a szabályzat ClaimsSchema szakaszában már definiált ClaimType mutató hivatkozás. A `TransformationClaimType` a honosított karakterlánc neve, amelyet a `LocalizedString` elem `StringId` határoz meg.
1. Egy [önérvényesített technikai profilban](self-asserted-technical-profile.md), vagy egy [megjelenítési vezérlő](display-controls.md) bemeneti vagy kimeneti jogcímek átalakításával hivatkozhat a jogcímek átalakítására.

![GetLocalizedStringsTransformation](./media/string-transformations/get-localized-strings-transformation.png)

A következő példa megkeresi az e-mail-tárgyat, a törzset, a kód üzenetét, valamint az e-mailek aláírását a honosított karakterláncokból. Ezeket a jogcímeket később az egyéni e-mail-ellenőrző sablon használja.

Honosított karakterláncok definiálása angol (alapértelmezett) és spanyol nyelven.

```XML
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="Append">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
   </SupportedLanguages>

  <LocalizedResources Id="api.localaccountsignup.en">
    <LocalizedStrings>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Contoso account email verification code</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Thanks for verifying your account!</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Your code is</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Sincerely</LocalizedString>
     </LocalizedStrings>
   </LocalizedResources>
   <LocalizedResources Id="api.localaccountsignup.es">
     <LocalizedStrings>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Código de verificación del correo electrónico de la cuenta de Contoso</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Gracias por comprobar la cuenta de </LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Su código es</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Atentamente</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
</Localization>
```

A jogcím-átalakítás beállítja a *jogcím típusának* értékét a `StringId` *email_subject*értékével.

```XML
<ClaimsTransformation Id="GetLocalizedStringsForEmail" TransformationMethod="GetLocalizedStringsTransformation">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="subject" TransformationClaimType="email_subject" />
    <OutputClaim ClaimTypeReferenceId="message" TransformationClaimType="email_message" />
    <OutputClaim ClaimTypeReferenceId="codeIntro" TransformationClaimType="email_code" />
    <OutputClaim ClaimTypeReferenceId="signature" TransformationClaimType="email_signature" />
   </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Példa

- Kimeneti jogcímek:
  - **subject**: contoso-fiók e-mail ellenőrző kódja
  - **üzenet**: Köszönjük a fiók ellenőrzését!
  - **codeIntro**: a kód
  - **aláírás**: őszintén


## <a name="getmappedvaluefromlocalizedcollection"></a>GetMappedValueFromLocalizedCollection

Elem keresése jogcím- **korlátozási** gyűjteményből.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | mapFromClaim | sztring | Az a jogcím, amely tartalmazza a **restrictionValueClaim** jogcímek által a **korlátozási** gyűjteményben felkeresni kívánt szöveget.  |
| outputClaim | restrictionValueClaim | sztring | A **korlátozási** gyűjteményt tartalmazó jogcím. A jogcímek átalakításának meghívása után a jogcím értéke a kijelölt elem értékét tartalmazza. |

A következő példa a hibaüzenet alapján megkeresi a hibaüzenet leírását. Az **responseMsg** jogcím olyan hibaüzenetek gyűjteményét tartalmazza, amelyek a végfelhasználónak vagy a függő entitásnak küldendőek.

```XML
<ClaimType Id="responseMsg">
  <DisplayName>Error message: </DisplayName>
  <DataType>string</DataType>
  <UserInputType>Paragraph</UserInputType>
  <Restriction>
    <Enumeration Text="B2C_V1_90001" Value="You cannot sign in because you are a minor" />
    <Enumeration Text="B2C_V1_90002" Value="This action can only be performed by gold members" />
    <Enumeration Text="B2C_V1_90003" Value="You have not been enabled for this operation" />
  </Restriction>
</ClaimType>
```
A jogcím-átalakítás megkeresi az elemek szövegét, és visszaadja az értékét. Ha a korlátozás honosítva van a `<LocalizedCollection>`használatával, a jogcím-átalakítás a honosított értéket adja vissza.

```XML
<ClaimsTransformation Id="GetResponseMsgMappedToResponseCode" TransformationMethod="GetMappedValueFromLocalizedCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="responseCode" TransformationClaimType="mapFromClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="responseMsg" TransformationClaimType="restrictionValueClaim" />        
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Példa

- Bemeneti jogcímek:
    - **mapFromClaim**: B2C_V1_90001
- Kimeneti jogcímek:
    - **restrictionValueClaim**: nem lehet bejelentkezni, mert Ön kisebb.

## <a name="lookupvalue"></a>LookupValue

Keressen egy jogcím értékét egy másik jogcím értéke alapján.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | inputParameterId | sztring | A keresési értéket tartalmazó jogcím |
| InputParameter | |sztring | A inputParameters gyűjteménye. |
| InputParameter | errorOnFailedLookup | logikai | Annak ellenőrzése, hogy a rendszer hibát ad-e vissza, ha nincs megfelelő keresés. |
| outputClaim | inputParameterId | sztring | A jogcím-átalakítás meghívása után előállított ClaimTypes. A megfelelő `Id`értéke. |

A következő példa az egyik inputParameters-gyűjteményben keresi a tartománynevet. A jogcím-átalakítás megkeresi a tartománynevet az azonosítóban, és visszaadja az értékét (alkalmazás-azonosító).

```XML
 <ClaimsTransformation Id="DomainToClientId" TransformationMethod="LookupValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="domainName" TransformationClaimType="inputParameterId" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="contoso.com" DataType="string" Value="13c15f79-8fb1-4e29-a6c9-be0d36ff19f1" />
    <InputParameter Id="microsoft.com" DataType="string" Value="0213308f-17cb-4398-b97e-01da7bd4804e" />
    <InputParameter Id="test.com" DataType="string" Value="c7026f88-4299-4cdb-965d-3f166464b8a9" />
    <InputParameter Id="errorOnFailedLookup" DataType="boolean" Value="false" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="domainAppId" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Példa

- Bemeneti jogcímek:
    - **inputParameterId**: test.com
- Bemeneti paraméterek:
    - **contoso.com**: 13c15f79-8fb1-4e29-a6c9-be0d36ff19f1
    - **Microsoft.com**: 0213308f-17cb-4398-b97e-01da7bd4804e
    - **test.com**: c7026f88-4299-4cdb-965d-3f166464b8a9
    - **errorOnFailedLookup**: hamis
- Kimeneti jogcímek:
    - **outputClaim**: c7026f88-4299-4cdb-965d-3f166464b8a9

Ha a `errorOnFailedLookup` bemeneti paraméter értéke `true`, a rendszer mindig az **LookupValue** jogcím-átalakítást hajtja végre egy olyan [érvényesítési technikai profilból](validation-technical-profile.md) , amelyet egy [önérvényesített technikai profil](self-asserted-technical-profile.md)vagy egy [DisplayConrtol](display-controls.md)hívnak. Az önellenőrzött technikai profilok `LookupNotFound` metaadatai a felhasználó számára megjelenített hibaüzenetet vezérlik.

![AssertStringClaimsAreEqual-végrehajtás](./media/string-transformations/assert-execution.png)

A következő példa az egyik inputParameters-gyűjteményben keresi a tartománynevet. A jogcím-átalakítás megkeresi a tartománynevet az azonosítóban, és visszaadja az értékét (egy alkalmazás AZONOSÍTÓját), vagy hibaüzenetet jelenít meg.

```XML
 <ClaimsTransformation Id="DomainToClientId" TransformationMethod="LookupValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="domainName" TransformationClaimType="inputParameterId" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="contoso.com" DataType="string" Value="13c15f79-8fb1-4e29-a6c9-be0d36ff19f1" />
    <InputParameter Id="microsoft.com" DataType="string" Value="0213308f-17cb-4398-b97e-01da7bd4804e" />
    <InputParameter Id="test.com" DataType="string" Value="c7026f88-4299-4cdb-965d-3f166464b8a9" />
    <InputParameter Id="errorOnFailedLookup" DataType="boolean" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="domainAppId" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Példa

- Bemeneti jogcímek:
    - **inputParameterId**: Live.com
- Bemeneti paraméterek:
    - **contoso.com**: 13c15f79-8fb1-4e29-a6c9-be0d36ff19f1
    - **Microsoft.com**: 0213308f-17cb-4398-b97e-01da7bd4804e
    - **test.com**: c7026f88-4299-4cdb-965d-3f166464b8a9
    - **errorOnFailedLookup**: true
- Hiba:
    - Nem található egyezés a bemeneti jogcím értékéhez a bemeneti paraméterek azonosítóinak és errorOnFailedLookup listájában.


## <a name="nullclaim"></a>NullClaim

Egy adott jogcím értékének megtisztítása.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| outputClaim | claim_to_null | sztring | A jogcím értéke NULL értékre van állítva. |

Ezzel a jogcím-átalakítással eltávolíthatók a szükségtelen adatok a jogcím-tárolóból, így a munkamenet-cookie kisebb lesz. Az alábbi példa eltávolítja a `TermsOfService` jogcím típusának értékét.

```XML
<ClaimsTransformation Id="SetTOSToNull" TransformationMethod="NullClaim">
  <OutputClaims>
  <OutputClaim ClaimTypeReferenceId="TermsOfService" TransformationClaimType="claim_to_null" />
  </OutputClaims>
</ClaimsTransformation>
```

- Bemeneti jogcímek:
    - **outputClaim**: üdvözli a contoso-alkalmazás. Ha továbbra is megkeresi és használja ezt a webhelyet, Ön vállalja, hogy betartja a következő feltételeket és kikötéseket...
- Kimeneti jogcímek:
    - **outputClaim**: NULL

## <a name="parsedomain"></a>ParseDomain

Egy e-mail-cím tartomány részét kapja meg.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | emailAddress | sztring | Az e-mail-címet tartalmazó ClaimType. |
| outputClaim | tartományi | sztring | A jogcímek átalakítását követően létrehozott ClaimType – a tartomány. |

Ezzel a jogcím-átalakítással elemezheti a tartománynevet a felhasználó @ szimbóluma után. A következő jogcím-átalakítás azt mutatja be, hogyan elemezheti a tartománynevet egy **e-mail-** jogcím alapján.

```XML
<ClaimsTransformation Id="SetDomainName" TransformationMethod="ParseDomain">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="emailAddress" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="domainName" TransformationClaimType="domain" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Példa

- Bemeneti jogcímek:
  - **emailAddress**: joe@outlook.com
- Kimeneti jogcímek:
    - **tartomány**: Outlook.com

## <a name="setclaimsifregexmatch"></a>SetClaimsIfRegexMatch

Ellenőrzi, hogy egy karakterlánc-jogcím `claimToMatch` és `matchTo` bemeneti paraméter egyenlő-e, és beállítja a kimeneti jogcímeket a `outputClaimIfMatched` bemeneti paraméterben szereplő értékkel együtt, az eredmény kimeneti jogcímek összehasonlításával, amely az összehasonlítás eredményétől függően `true`ként vagy `false`ként lesz beállítva.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | claimToMatch | sztring | A jogcím típusa, amelyet össze kell hasonlítani. |
| InputParameter | matchTo | sztring | Az egyeztetendő reguláris kifejezés. |
| InputParameter | outputClaimIfMatched | sztring | Az a érték, amelyet a karakterláncok egyenlőként kell megadni. |
| outputClaim | outputClaim | sztring | Ha a reguláris kifejezés egyezik, a kimeneti jogcím `outputClaimIfMatched` bemeneti paraméter értékét tartalmazza. Vagy Null, ha nincs egyezés. |
| outputClaim | regexCompareResultClaim | logikai | A reguláris kifejezés egyezik az eredmény kimeneti jogcímek típusával, amelyet a rendszer a megfeleltetés eredménye alapján `true`ként vagy `false`ként kell beállítani. |

Például ellenőrzi, hogy a megadott telefonszám érvényes-e, a telefonszám reguláris kifejezési mintája alapján.

```XML
<ClaimsTransformation Id="SetIsPhoneRegex" TransformationMethod="setClaimsIfRegexMatch">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phone" TransformationClaimType="claimToMatch" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="matchTo" DataType="string" Value="^[0-9]{4,16}$" />
    <InputParameter Id="outputClaimIfMatched" DataType="string" Value="isPhone" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="validationResult" TransformationClaimType="outputClaim" />
    <OutputClaim ClaimTypeReferenceId="isPhoneBoolean" TransformationClaimType="regexCompareResultClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Példa

- Bemeneti jogcímek:
    - **claimToMatch**: "64854114520"
- Bemeneti paraméterek:
    - **matchTo**: "^ [0-9]{4,16}$"
    - **outputClaimIfMatched**: "isPhone"
- Kimeneti jogcímek:
    - **outputClaim**: "isPhone"
    - **regexCompareResultClaim**: true

## <a name="setclaimsifstringsareequal"></a>SetClaimsIfStringsAreEqual

Ellenőrzi, hogy egy karakterlánc-jogcím és `matchTo` bemeneti paraméter egyenlő-e, és beállítja-e a kimeneti jogcímeket a `stringMatchMsg` és a `stringMatchMsgCode` bemeneti paraméterekben szereplő értékkel együtt, az eredmény kimeneti jogcímek összehasonlításával, amelyet a rendszer az összehasonlítás eredményeképpen `true`ként vagy `false`ként kell beállítani.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | inputClaim | sztring | A jogcím típusa, amelyet össze kell hasonlítani. |
| InputParameter | matchTo | sztring | A `inputClaim`összevetni kívánt karakterlánc. |
| InputParameter | Stringcomparison argumentummal | sztring | Lehetséges értékek: `Ordinal` vagy `OrdinalIgnoreCase`. |
| InputParameter | stringMatchMsg | sztring | Az első érték, amely akkor lesz beállítva, ha a karakterláncok egyenlőek. |
| InputParameter | stringMatchMsgCode | sztring | Az a második érték, amelyet a karakterláncok egyenlőként kell megadni. |
| outputClaim | outputClaim1 | sztring | Ha a karakterláncok egyenlőek, ez a kimeneti jogcím `stringMatchMsg` bemeneti paraméter értékét tartalmazza. |
| outputClaim | outputClaim2 | sztring | Ha a karakterláncok egyenlőek, ez a kimeneti jogcím `stringMatchMsgCode` bemeneti paraméter értékét tartalmazza. |
| outputClaim | stringCompareResultClaim | logikai | Az eredményül kapott kimeneti jogcím típusának összehasonlítása, amelyet `true`ként vagy `false`ként kell beállítani az összehasonlítás eredményétől függően. |

A jogcímek átalakításával ellenőrizhető, hogy a jogcímek értéke megegyezik-e a megadott értékkel. Például a következő jogcím-átalakítás ellenőrzi, hogy a **termsOfUseConsentVersion** jogcím értéke `v1`-e. Ha igen, módosítsa az értéket `v2`ra.

```XML
<ClaimsTransformation Id="CheckTheTOS" TransformationMethod="SetClaimsIfStringsAreEqual">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="termsOfUseConsentVersion" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="matchTo" DataType="string" Value="v1" />
    <InputParameter Id="stringComparison" DataType="string" Value="ordinalIgnoreCase" />
    <InputParameter Id="stringMatchMsg" DataType="string" Value="B2C_V1_90005" />
    <InputParameter Id="stringMatchMsgCode" DataType="string" Value="The TOS is upgraded to v2" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="termsOfUseConsentVersion" TransformationClaimType="outputClaim1" />
    <OutputClaim ClaimTypeReferenceId="termsOfUseConsentVersionUpgradeCode" TransformationClaimType="outputClaim2" />
    <OutputClaim ClaimTypeReferenceId="termsOfUseConsentVersionUpgradeResult" TransformationClaimType="stringCompareResultClaim" />
  </OutputClaims>
</ClaimsTransformation>
```
### <a name="example"></a>Példa

- Bemeneti jogcímek:
    - **inputClaim**: v1
- Bemeneti paraméterek:
    - **matchTo**: v1
    - **stringcomparison argumentummal**: ordinalIgnoreCase
    - **stringMatchMsg**: B2C_V1_90005
    - **stringMatchMsgCode**: a TOS frissítése a v2-re
- Kimeneti jogcímek:
    - **outputClaim1**: B2C_V1_90005
    - **outputClaim2**: a TOS frissítése a v2-re
    - **stringCompareResultClaim**: true

## <a name="setclaimsifstringsmatch"></a>SetClaimsIfStringsMatch

Ellenőrzi, hogy egy karakterlánc-jogcím és `matchTo` bemeneti paraméter egyenlő-e, és beállítja-e a kimeneti jogcímeket a `outputClaimIfMatched` bemeneti paraméterben szereplő értékkel együtt, az eredmény kimeneti jogcímek összehasonlításával, amelyet `true`ként vagy `false`ként kell beállítani az összehasonlítás eredményének megfelelően.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | claimToMatch | sztring | A jogcím típusa, amelyet össze kell hasonlítani. |
| InputParameter | matchTo | sztring | A inputClaim összehasonlítandó karakterlánc. |
| InputParameter | Stringcomparison argumentummal | sztring | Lehetséges értékek: `Ordinal` vagy `OrdinalIgnoreCase`. |
| InputParameter | outputClaimIfMatched | sztring | Az a érték, amelyet a karakterláncok egyenlőként kell megadni. |
| outputClaim | outputClaim | sztring | Ha a karakterláncok egyenlőek, ez a kimeneti jogcím `outputClaimIfMatched` bemeneti paraméter értékét tartalmazza. Vagy Null, ha a karakterláncok nem egyeznek. |
| outputClaim | stringCompareResultClaim | logikai | Az eredményül kapott kimeneti jogcím típusának összehasonlítása, amelyet `true`ként vagy `false`ként kell beállítani az összehasonlítás eredményétől függően. |

Például a következő jogcím-átalakítás ellenőrzi, hogy a **beszerzésimennyiség** jogcím értéke `Minor`-e. Ha igen, az értéket az `B2C_V1_90001`értékre kell visszaadnia.

```XML
<ClaimsTransformation Id="SetIsMinor" TransformationMethod="SetClaimsIfStringsMatch">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="ageGroup" TransformationClaimType="claimToMatch" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="matchTo" DataType="string" Value="Minor" />
    <InputParameter Id="stringComparison" DataType="string" Value="ordinalIgnoreCase" />
    <InputParameter Id="outputClaimIfMatched" DataType="string" Value="B2C_V1_90001" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isMinor" TransformationClaimType="outputClaim" />
    <OutputClaim ClaimTypeReferenceId="isMinorResponseCode" TransformationClaimType="stringCompareResultClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Példa

- Bemeneti jogcímek:
    - **claimToMatch**: kisebb
- Bemeneti paraméterek:
    - **matchTo**: kisebb
    - **stringcomparison argumentummal**: ordinalIgnoreCase
    - **outputClaimIfMatched**: B2C_V1_90001
- Kimeneti jogcímek:
    - **isMinorResponseCode**: B2C_V1_90001
    - **isMinor**: true


## <a name="stringcontains"></a>StringContains

Annak megállapítása, hogy a megadott alkarakterlánc a bemeneti jogcímen belülre esik-e. Az eredmény egy új, `true` vagy `false`értékű logikai ClaimType. `true`, ha az érték paraméter a karakterláncon belül történik, ellenkező esetben `false`.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | inputClaim | sztring | A keresendő jogcím típusa. |
|InputParameter|tartalmaz|sztring|A keresendő érték.|
|InputParameter|ignoreCase|sztring|Meghatározza, hogy az összehasonlítás figyelmen kívül hagyja-e az összehasonlított karakterlánc esetét.|
| outputClaim | outputClaim | sztring | A ClaimsTransformation után létrehozott ClaimType meghívása megtörtént. Logikai jelző, ha az alkarakterlánc a bemeneti jogcímen belül van. |

Ezzel a jogcím-átalakítással ellenőrizhető, hogy egy karakterlánc típusú jogcím tartalmaz-e alkarakterláncot. A következő példa ellenőrzi, hogy a `roles` karakterlánc-jogcím típusa tartalmazza-e a **rendszergazda**értéket.

```XML
<ClaimsTransformation Id="CheckIsAdmin" TransformationMethod="StringContains">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="roles" TransformationClaimType="inputClaim"/>
  </InputClaims>
  <InputParameters>
    <InputParameter  Id="contains" DataType="string" Value="admin"/>
    <InputParameter  Id="ignoreCase" DataType="string" Value="true"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isAdmin" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Példa

- Bemeneti jogcímek:
    - **inputClaim**: "adminisztrátor, jóváhagyó, szerkesztő"
- Bemeneti paraméterek:
    - a következőket **tartalmazza**: "admin"
    - **ignoreCase**: true
- Kimeneti jogcímek:
    - **outputClaim**: true

## <a name="stringsubstring"></a>StringSubstring

Egy karakterlánc-jogcím típusának kibontása a megadott pozíción kezdődő karaktertől kezdve, és a megadott számú karaktert adja vissza.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | inputClaim | sztring | A jogcím típusa, amely tartalmazza a karakterláncot. |
| InputParameter | startIndex | int | Ebben a példányban egy alsztring nulla alapú kiindulási karakterének pozíciója. |
| InputParameter | length | int | Az alkarakterláncban szereplő karakterek száma. |
| outputClaim | outputClaim | logikai | Egy olyan karakterlánc, amely megegyezik az ebben a példányban található startIndex megjelenő hosszúsági karakterlánccal, vagy üres, ha a startIndex a példány hosszával egyenlő, és a hossz nulla. |

Például szerezze be a telefonszám országának előtagját.


```XML
<ClaimsTransformation Id="GetPhonePrefix" TransformationMethod="StringSubstring">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="inputClaim" />
  </InputClaims>
<InputParameters>
  <InputParameter Id="startIndex" DataType="int" Value="0" />
  <InputParameter Id="length" DataType="int" Value="2" />
</InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="phonePrefix" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```
### <a name="example"></a>Példa

- Bemeneti jogcímek:
    - **inputClaim**: "+ 1644114520"
- Bemeneti paraméterek:
    - **startIndex**: 0
    - **Hossz**: 2
- Kimeneti jogcímek:
    - **outputClaim**: "+ 1"

## <a name="stringreplace"></a>StringReplace

Egy adott értékhez tartozó jogcím típusú karakterláncot keres, és egy új jogcím típusú karakterláncot ad vissza, amelyben az aktuális karakterláncban szereplő adott sztring összes előfordulása egy másik megadott karakterlánccal van lecserélve.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | inputClaim | sztring | A jogcím típusa, amely tartalmazza a karakterláncot. |
| InputParameter | oldValue | sztring | A keresendő karakterlánc. |
| InputParameter | newValue | sztring | Az `oldValue` összes előfordulását lecserélő sztring |
| outputClaim | outputClaim | logikai | Az aktuális karakterlánccal egyenértékű karakterlánc, kivéve, hogy a oldValue összes példánya le van cserélve a newValue. Ha a oldValue nem található az aktuális példányban, a metódus változatlanul visszaadja az aktuális példányt. |

Például egy telefonszám normalizálása a `-` karakterek eltávolításával


```XML
<ClaimsTransformation Id="NormalizePhoneNumber" TransformationMethod="StringReplace">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="inputClaim" />
  </InputClaims>
<InputParameters>
  <InputParameter Id="oldValue" DataType="string" Value="-" />
  <InputParameter Id="newValue" DataType="string" Value="" />
</InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```
### <a name="example"></a>Példa

- Bemeneti jogcímek:
    - **inputClaim**: "+ 164-411-452-054"
- Bemeneti paraméterek:
    - **OldValue**: "-"
    - **Hossz**: ""
- Kimeneti jogcímek:
    - **outputClaim**: "+ 164411452054"

## <a name="stringjoin"></a>StringJoin

Egy megadott karakterlánc-gyűjteményi jogcím elemeinek összefűzése az egyes elemek vagy tagok közötti megadott elválasztó használatával.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | inputClaim | StringCollection stb | Egy gyűjtemény, amely az összefűzni kívánt karakterláncokat tartalmazza. |
| InputParameter | elválasztó | sztring | Az elválasztóként használandó karakterlánc, például vessző `,`. |
| outputClaim | outputClaim | sztring | A `inputClaim` karakterlánc-gyűjtemény tagjaiból álló karakterlánc, amelyet a `delimiter` bemeneti paraméter választ. |

Az alábbi példa felhasználói szerepkörök karakterlánc-gyűjteményét veszi át, és egy vesszővel elválasztó karakterlánccá alakítja át. Ezt a módszert használhatja egy karakterlánc-gyűjtemény Azure AD-beli felhasználói fiókban való tárolására. Később, amikor beolvassa a fiókot a címtárból, a `StringSplit` használatával alakítsa át a vesszővel elválasztó sztringet karakterlánc-gyűjteményre.

```XML
<ClaimsTransformation Id="ConvertRolesStringCollectionToCommaDelimiterString" TransformationMethod="StringJoin">
  <InputClaims>
   <InputClaim ClaimTypeReferenceId="roles" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter DataType="string" Id="delimiter" Value="," />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="rolesCommaDelimiterConverted" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Példa

- Bemeneti jogcímek:
  - **inputClaim**: ["admin", "author", "Reader"]
- Bemeneti paraméterek:
  - **határolójel**: ","
- Kimeneti jogcímek:
  - **outputClaim**: "admin, szerző, olvasó"


## <a name="stringsplit"></a>StringSplit

Egy olyan karakterlánc-tömböt ad vissza, amely tartalmazza az ebben a példányban lévő alsztringeket, amelyeket egy adott karakterlánc elemei határoznak meg.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | inputClaim | sztring | Egy karakterlánc-jogcím típusa, amely a felosztani kívánt alkarakterláncokat tartalmazza. |
| InputParameter | elválasztó | sztring | Az elválasztóként használandó karakterlánc, például vessző `,`. |
| outputClaim | outputClaim | StringCollection stb | Egy karakterlánc-gyűjtemény, amelynek elemei tartalmazzák az ebben a karakterláncban található azon alsztringeket, amelyeket a `delimiter` bemeneti paramétere tagol. |

A következő példa a felhasználói szerepkörök vesszővel elválasztó karakterláncát veszi át, és átalakítja egy karakterlánc-gyűjteménybe.

```XML
<ClaimsTransformation Id="ConvertRolesToStringCollection" TransformationMethod="StringSplit">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="rolesCommaDelimiter" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
  <InputParameter DataType="string" Id="delimiter" Value="," />
    </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="roles" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Példa

- Bemeneti jogcímek:
  - **inputClaim**: "admin, szerző, olvasó"
- Bemeneti paraméterek:
  - **határolójel**: ","
- Kimeneti jogcímek:
  - **outputClaim**: ["admin", "author", "Reader"]

## <a name="string-claim-transformations-expressions"></a>Karakterlánc-jogcím átalakítása kifejezés
A jogcím-átalakítási kifejezések Azure AD B2C egyéni házirendekben környezeti információkat biztosítanak a bérlői AZONOSÍTÓról és a technikai profil AZONOSÍTÓról.

  | Kifejezés | Leírás | Példa |
 | ----- | ----------- | --------|
 | `{TechnicalProfileId}` | A technikai profileId neve. | Facebook – OAUTH |
 | `{RelyingPartyTenantId}` | A függő entitás házirendjének bérlői azonosítója. | your-tenant.onmicrosoft.com |
 | `{TrustFrameworkTenantId}` | A megbízhatósági keretrendszer bérlői azonosítója. | your-tenant.onmicrosoft.com |
