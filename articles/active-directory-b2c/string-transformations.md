---
title: Karakterláncjogcímek átalakítása példák egyéni házirendekhez
titleSuffix: Azure AD B2C
description: Karakterlánc jogcímek átalakítási példák az Azure Active Directory B2C Identity Experience Framework (IEF) sémája.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/21/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: f08107874598a68fb5ce2a1a8a98b6a81d7b94d4
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81756793"
---
# <a name="string-claims-transformations"></a>Karakterláncjogcímek átalakítása

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ez a cikk példákat tartalmaz az Azure Active Directory B2C (Azure AD B2C) identitáskezelési keretrendszer séma karakterlánc-jogcím-átalakításai használatára. További információ: [ClaimsTransformations](claimstransformations.md).

## <a name="assertstringclaimsareequal"></a>AssertStringclaimsAreequal

Hasonlítson össze két jogcímet, és adjon kivételt, ha azok nem egyenlőek a megadott összehasonlító inputClaim1, inputClaim2 és stringComparison szerint.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (Bemeneti jogcím) | inputClaim1 | sztring | Az első állítás típusa, amelyet össze kell hasonlítani. |
| InputClaim (Bemeneti jogcím) | inputClaim2 | sztring | A második állítás típusa, amelyet össze kell hasonlítani. |
| InputParameter (Beviteli paraméter) | karakterláncÖsszehasonlítás | sztring | karakterlánc-összehasonlítás, az egyik érték: OrdinalIgnoreCase. |

Az **AssertStringClaimsAreEqual** jogcímek átalakítása mindig egy [érvényesítési technikai profilból](validation-technical-profile.md) történik, amelyet [egy saját érvényesítésű technikai profil](self-asserted-technical-profile.md)vagy [displayconrtol](display-controls.md)hív meg . Egy `UserMessageIfClaimsTransformationStringsAreNotEqual` önérvényesítő technikai profil metaadatai vezérli a felhasználó számára megjelenő hibaüzenetet. A hibaüzenetek [honosíthatók](localization-string-ids.md#claims-transformations-error-messages).


![AssertStringClaimsAreEqual végrehajtás](./media/string-transformations/assert-execution.png)

Ezzel a jogcímátalakítással biztosíthatja, hogy két Jogcímtípus azonos értékkel rendelkezik. Ha nem, hibaüzenet jelenik meg. A következő példa ellenőrzi, hogy az **erősAuthenticationEmailAddress** ClaimType megegyezik-e **az e-mail** ClaimType-al. Ellenkező esetben hibaüzenet jelenik meg.

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


A **login-NonInteractive** érvényesítési technikai profil meghívja az **AssertEmailAndStrongAuthenticationEmailAddressAreEqual** jogcímátalakítást.
```XML
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertEmailAndStrongAuthenticationEmailAddressAreEqual" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

Az önérvényesítő technikai profil meghívja az érvényesítési **login-NonInteractive** technikai profilt.

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

- Bemeneti igények:
  - **inputClaim1**:someone@contoso.com
  - **inputClaim2**:someone@outlook.com
- Bemeneti paraméterek:
  - **stringComparison**: ordinalIgnoreCase
- Eredmény: Hiba történt

## <a name="changecase"></a>ChangeCase

A megadott jogcím kis- vagy nagybetűsre módosítja az üzemeltetőtől függően.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (Bemeneti jogcím) | inputClaim1 | sztring | A módosítandó Jogcímtípus. |
| InputParameter (Beviteli paraméter) | toCase | sztring | A következő értékek `LOWER` egyike: vagy `UPPER`. |
| OutputClaim (Kimeneti jogcím) | outputClaim | sztring | A jogcímátalakítás meghívása után létrehozott Jogcímtípus. |

Ezzel a jogcímátalakítással a Jogcímtípus karakterláncot kis- vagy nagybetűsre módosíthatja.

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

- Bemeneti igények:
  - **e-mail**:SomeOne@contoso.com
- Bemeneti paraméterek:
    - **toCase**: KISBETŰ
- Kimeneti jogcímek:
  - **e-mail**:someone@contoso.com

## <a name="createstringclaim"></a>CreateStringClaim (Karakterláncigény) létrehozása

Karakterlánc-jogcímet hoz létre a megadott bemeneti paraméterből az átalakítássorán.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
|----- | ----------------------- | --------- | ----- |
| InputParameter (Beviteli paraméter) | érték | sztring | A beállítandó karakterlánc. Ez a bemeneti paraméter támogatja a [karakterláncjogcímek átalakítási kifejezéseit.](string-transformations.md#string-claim-transformations-expressions) |
| OutputClaim (Kimeneti jogcím) | létrehozvaJog | sztring | A jogcímátalakítás meghívása után létrehozott Jogcímtípus a bemeneti paraméterben megadott értékkel. |

Ezzel a jogcímátalakítással karakterlánc-jogcímtípus-értéket állíthat be.

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
    - **érték**: Contoso szolgáltatási feltételek...
- Kimeneti jogcímek:
    - **createdClaim**: A TOS ClaimType tartalmazza a "Contoso szolgáltatási feltételeket..." Érték.

## <a name="compareclaims"></a>Jogcímek összehasonlítása

Határozza meg, hogy egy karakterlánc-jogcím egyenlő-e egy másikkal. Az eredmény egy új logikai jogcímtípus, amelynek `true` értéke vagy. `false`

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (Bemeneti jogcím) | inputClaim1 | sztring | Első jogcímtípus, amelyet össze kell hasonlítani. |
| InputClaim (Bemeneti jogcím) | inputClaim2 | sztring | Második jogcímtípus, amelyet össze kell hasonlítani. |
| InputParameter (Beviteli paraméter) | operátor | sztring | Lehetséges `EQUAL` értékek: `NOT EQUAL`vagy . |
| InputParameter (Beviteli paraméter) | ignoreCase | logikai | Itt adható meg, hogy ez az összehasonlítás figyelmen kívül hagyja-e az összehasonlított karakterláncok kis- és nagybetűs étkeztetését. |
| OutputClaim (Kimeneti jogcím) | outputClaim | logikai | A jogcímátalakítás meghívása után létrehozott Jogcímtípus. |

Ezzel a jogcímátalakítással ellenőrizheti, hogy egy jogcím megegyezik-e egy másik jogcímvel. A következő jogcímek átalakítása például ellenőrzi, hogy az **e-mail** jogcím értéke megegyezik-e a **Verified.Email** jogcímével.

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

- Bemeneti igények:
  - **inputClaim1**:someone@contoso.com
  - **inputClaim2**:someone@outlook.com
- Bemeneti paraméterek:
    - **operátor:** NEM EGYENLŐ
    - **ignoreCase**: igaz
- Kimeneti jogcímek:
    - **outputClaim**: igaz

## <a name="compareclaimtovalue"></a>CompareClaimToValue

Azt határozza meg, hogy a jogcím értéke megegyezik-e a bemeneti paraméter értékén.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (Bemeneti jogcím) | inputClaim1 | sztring | A követelés típusa, amelyet össze kell hasonlítani. |
| InputParameter (Beviteli paraméter) | operátor | sztring | Lehetséges `EQUAL` értékek: `NOT EQUAL`vagy . |
| InputParameter (Beviteli paraméter) | compareTo | sztring | karakterlánc-összehasonlítás, az egyik érték: OrdinalIgnoreCase. |
| InputParameter (Beviteli paraméter) | ignoreCase | logikai | Itt adható meg, hogy ez az összehasonlítás figyelmen kívül hagyja-e az összehasonlított karakterláncok kis- és nagybetűs étkeztetését. |
| OutputClaim (Kimeneti jogcím) | outputClaim | logikai | A jogcímátalakítás meghívása után létrehozott Jogcímtípus. |

Ezzel a jogcímátalakítással ellenőrizheti, hogy egy jogcím megegyezik-e a megadott értékkel. Például a következő jogcímek átalakítása ellenőrzi, hogy a **termsOfUseConsentVersion** jogcím értéke egyenlő-e. `v1`

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
- Bemeneti igények:
    - **inputClaim1**: v1
- Bemeneti paraméterek:
    - **compareTo**: V1
    - **operátor**: EQUAL
    - **ignoreCase**: igaz
- Kimeneti jogcímek:
    - **outputClaim**: igaz

## <a name="createrandomstring"></a>CreateRandomString

Véletlenszerű karakterláncot hoz létre a véletlenszám-generátor használatával. Ha a véletlenszám-generátor `integer`típusa van, opcionálisan meg lehet adni egy vetőmagparamétert és egy maximális számot. Egy választható karakterlánc formátum paraméter lehetővé teszi, hogy a kimenet et formázza, és egy opcionális base64 paraméter megadja, hogy a kimenet base64 kódolású randomGeneratorType [guid, egész szám) outputClaim (Karakterlánc).

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputParameter (Beviteli paraméter) | randomGeneratorType | sztring | Megadja a létrehozandó véletlenszerű értéket `GUID` (globális egyedi azonosító) vagy `INTEGER` (szám). |
| InputParameter (Beviteli paraméter) | karakterláncformátum | sztring | [Nem kötelező] Formázza a véletlenszerű értéket. |
| InputParameter (Beviteli paraméter) | base64 | logikai | [Nem kötelező] A véletlenszerű érték konvertálása base64 értékre. Ha karakterláncformátumot alkalmaz, a karakterlánc-formátum utáni érték base64-re lesz kódolva. |
| InputParameter (Beviteli paraméter) | maximumSzám | int | [Nem kötelező] Csak `INTEGER` randomGeneratorType típusesetén. Adja meg a maximális számot. |
| InputParameter (Beviteli paraméter) | Vetőmag  | int | [Nem kötelező] Csak `INTEGER` randomGeneratorType típusesetén. Adja meg a véletlenszerű érték kezdőmagját. Megjegyzés: ugyanaz a mag ugyanazt a véletlenszerű számot eredményezi. |
| OutputClaim (Kimeneti jogcím) | outputClaim | sztring | A jogcímátalakítás meghívása után előállított jogcímtípusok. A véletlenszerű érték. |

A következő példa globális egyedi azonosítót hoz létre. Ez a jogcímátalakítás a véletlenszerű egyszerű felhasználónév (felhasználói elv neve) létrehozásához használatos.

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
    - **outputClaim**: bc8bedd2-aaa3-411e-bdee-2f1810b73dfc

A következő példa 0 és 1000 közötti egész véletlen értéket hoz létre. Az érték OTP_{random value} formátumú.

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
    - **randomGeneratorType**: INTEGER
    - **maximumSzám:** 1000
    - **karakterláncformátum:** OTP_{0}
    - **base64**: hamis
- Kimeneti jogcímek:
    - **outputClaim**: OTP_853


## <a name="formatstringclaim"></a>FormatStringClaim

A jogcím formázása a megadott formátumkarakterláncnak megfelelően. Ez az átalakítás `String.Format` a C# metódust használja.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (Bemeneti jogcím) | inputClaim |sztring |A karakterlánc-formátum {0} paraméterként használó Jogcímtípus. |
| InputParameter (Beviteli paraméter) | karakterláncformátum | sztring | A karakterlánc formátuma, beleértve a {0} paramétert is. Ez a bemeneti paraméter támogatja a [karakterláncjogcímek átalakítási kifejezéseit.](string-transformations.md#string-claim-transformations-expressions)  |
| OutputClaim (Kimeneti jogcím) | outputClaim | sztring | A jogcímátalakítás meghívása után létrehozott Jogcímtípus. |

Ezzel a jogcímátalakítással formázhat {0}bármely karakterláncot egy paraméterrel. A következő példa létrehoz egy **userPrincipalName**. Minden közösségi identitásszolgáltató technikai profilja, például `Facebook-OAUTH` meghívja a **CreateUserPrincipalName nevet** egy **userPrincipalName**létrehozásához.

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

- Bemeneti igények:
    - **inputClaim**: 5164db16-3eee-4629-bfda-dcc3326790e9
- Bemeneti paraméterek:
    - **stringFormat**:{0}cpim_ @{RelyingPartyTenantId}
- Kimeneti jogcímek:
  - **outputClaim**:cpim_5164db16-3eee-4629-bfda-dcc3326790e9@b2cdemo.onmicrosoft.com

## <a name="formatstringmultipleclaims"></a>FormatStringMultiplejog

Formázza a két jogcímeket a megadott formátumkarakterláncnak megfelelően. Ez az átalakítás `String.Format` a C# metódust használja.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (Bemeneti jogcím) | inputClaim |sztring | A karakterlánc-formátum {0} paraméterként használó Jogcímtípus. |
| InputClaim (Bemeneti jogcím) | inputClaim | sztring | A karakterlánc-formátum {1} paraméterként használó Jogcímtípus. |
| InputParameter (Beviteli paraméter) | karakterláncformátum | sztring | A karakterlánc formátuma, {1} beleértve a és a {0} paramétereket. Ez a bemeneti paraméter támogatja a [karakterláncjogcímek átalakítási kifejezéseit.](string-transformations.md#string-claim-transformations-expressions)   |
| OutputClaim (Kimeneti jogcím) | outputClaim | sztring | A jogcímátalakítás meghívása után létrehozott Jogcímtípus. |

Ezzel a jogcímátalakítással formázhat bármely {0} {1}karakterláncot két paraméterrel, és . A következő példa egy **megjelenítendő nevet** hoz létre a megadott formátummal:

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

- Bemeneti igények:
    - **inputClaim1**: Joe
    - **inputClaim2**: Fernando
- Bemeneti paraméterek:
    - **stringFormat** {0} :{1}
- Kimeneti jogcímek:
    - **outputClaim**: Joe Fernando

## <a name="getlocalizedstringstransformation"></a>GetLocalizedStringsTransformation

A honosított karakterláncokat jogcímekké másolja.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| OutputClaim (Kimeneti jogcím) | A honosított karakterlánc neve | sztring | A jogcímek átalakításának meghívása után létrehozott jogcímtípusok listája. |

A GetLocalizedStringsTransformation jogcímek transzformációjának használata:

1. Definiáljon egy [honosítási karakterláncot,](localization.md) és társítsa azt egy [önérvényesített technikai profilhoz.](self-asserted-technical-profile.md)
1. Az `ElementType` `LocalizedString` elem elemét `GetLocalizedStringsTransformationClaimType`a számára kell beállítani.
1. A `StringId` egy egyedi azonosítót, amelyet ön határoz meg, és később használja a jogcímek átalakítása során.
1. A jogcímek átalakítása, adja meg a honosított karakterlánccal beállítandó jogcímek listáját. A `ClaimTypeReferenceId` hivatkozás egy olyan Jogcímtípusra, amely már definiálva van a házirend ClaimsSchema szakaszában. A `TransformationClaimType` az `StringId` `LocalizedString` elemben meghatározott honosított karakterlánc neve.
1. Egy [önérvényesítő technikai profilban,](self-asserted-technical-profile.md)vagy egy [kijelző vezérlő](display-controls.md) bemeneti vagy kimeneti jogcímek átalakítása, hivatkozzon a jogcímek átalakítása.

![GetLocalizedStringsTransformation](./media/string-transformations/get-localized-strings-transformation.png)

A következő példa megkeresi az e-mail tárgyát, törzsét, kódüzenetét és az e-mail aláírását a honosított karakterláncokból. Ezeket a jogcímeket később az egyéni e-mail-ellenőrzési sablon használta.

Adja meg a honosított karakterláncokat az angol (alapértelmezett) és a spanyol nyelvhez.

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

A jogcímátalakítás a jogcímtípus-tulajdonos értékét *subject* `StringId` a email_subject értékével állítja *be.*

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
  - **tulajdonos**: Contoso-fiók e-mail-ellenőrző kódja
  - **üzenet**: Köszönjük, hogy ellenőrizte fiókját!
  - **codeIntro**: A kód
  - **aláírás**: Tisztelettel


## <a name="getmappedvaluefromlocalizedcollection"></a>GetMappedValueFromLocalizedCollection

Egy elem kikeresegy **jogcímkorlátozási** gyűjteményből.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (Bemeneti jogcím) | térképFromClaim | sztring | Az a jogcím, amely a **restrictionValueClaim** jogcímekben a **Restriction** gyűjteményben keresendő szöveget tartalmazza.  |
| OutputClaim (Kimeneti jogcím) | korlátozásÉrtékIgény | sztring | A Korlátozás gyűjteményt tartalmazó **jogcím.** A jogcímek átalakításának meghívása után a jogcím értéke tartalmazza a kijelölt elem értékét. |

A következő példa a hibakulcs alapján keresi a hibaüzenet leírását. A **responseMsg** jogcím a végfelhasználónak vagy a függő entitásnak elküldendő hibaüzenetek gyűjteményét tartalmazza.

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
A jogcímek átalakítása megkeresi az elem szövegét, és visszaadja annak értékét. Ha a korlátozás honosított a használatával, `<LocalizedCollection>`a jogcím-átalakítás a honosított értéket adja vissza.

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

- Bemeneti igények:
    - **mapFromClaim**: B2C_V1_90001
- Kimeneti jogcímek:
    - **restrictionValueClaim**: Nem tud bejelentkezni, mert kiskorú.

## <a name="lookupvalue"></a>LookupValue értéke

Keressen meg egy jogcímértéket egy másik jogcím értéke alapján.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (Bemeneti jogcím) | inputParameterId azonosító | sztring | A kereskövetési értéket tartalmazó jogcím |
| InputParameter (Beviteli paraméter) | |sztring | InputParameters gyűjteménye. |
| InputParameter (Beviteli paraméter) | hibaFailedLookup | logikai | Annak szabályozása, hogy hiba jelenik-e meg, ha nincs megfelelő keres. |
| OutputClaim (Kimeneti jogcím) | inputParameterId azonosító | sztring | A jogcímátalakítás meghívása után előállított jogcímtípusok. Az egyezés `Id`értéke. |

A következő példa megkeresi a tartománynevet az egyik inputParameters gyűjteményben. A jogcím-átalakítás megkeresi a tartománynevet az azonosítóban, és visszaadja annak értékét (egy alkalmazásazonosítót).

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

- Bemeneti igények:
    - **inputParameterId**: test.com
- Bemeneti paraméterek:
    - **contoso.com**: 13c15f79-8fb1-4e29-a6c9-be0d36ff19f1
    - **microsoft.com**: 0213308f-17cb-4398-b97e-01da7bd4804e
    - **test.com**: c7026f88-4299-4cdb-965d-3f166464b8a9
    - **errorOnFailedLookup**: false
- Kimeneti jogcímek:
    - **outputClaim**: c7026f88-4299-4cdb-965d-3f166464b8a9

Ha `errorOnFailedLookup` a bemeneti `true`paraméter értéke , a **LookupValue** jogcímek átalakítása mindig egy [önérvényesítő technikai profil](self-asserted-technical-profile.md)vagy [DisplayConrtol](display-controls.md)által megnevezett [érvényesítési technikai profilból](validation-technical-profile.md) történik . Egy `LookupNotFound` önérvényesítő technikai profil metaadatai vezérli a felhasználó számára megjelenő hibaüzenetet.

![AssertStringClaimsAreEqual végrehajtás](./media/string-transformations/assert-execution.png)

A következő példa megkeresi a tartománynevet az egyik inputParameters gyűjteményben. A jogcím-átalakítás megkeresi a tartománynevet az azonosítóban, és visszaadja annak értékét (alkalmazásazonosító), vagy hibaüzenetet küld.

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

- Bemeneti igények:
    - **inputParameterId**: live.com
- Bemeneti paraméterek:
    - **contoso.com**: 13c15f79-8fb1-4e29-a6c9-be0d36ff19f1
    - **microsoft.com**: 0213308f-17cb-4398-b97e-01da7bd4804e
    - **test.com**: c7026f88-4299-4cdb-965d-3f166464b8a9
    - **errorOnFailedLookup**: true
- Hiba:
    - Nem található egyezés a bemeneti paraméterazonosítók listájában a bemeneti paraméter-azonosítók listájában, és az errorOnFailedLookup értéke igaz.


## <a name="nullclaim"></a>NullClaim érték

Tisztítsa meg egy adott jogcím értékét.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| OutputClaim (Kimeneti jogcím) | claim_to_null | sztring | A jogcím értéke NULL. |

Ezzel a jogcímátalakítással eltávolíthatja a szükségtelen adatokat a jogcímtulajdonság-csomagból, így a munkamenet-cookie kisebb lesz. A következő példa eltávolítja `TermsOfService` a jogcímtípus értékét.

```XML
<ClaimsTransformation Id="SetTOSToNull" TransformationMethod="NullClaim">
  <OutputClaims>
  <OutputClaim ClaimTypeReferenceId="TermsOfService" TransformationClaimType="claim_to_null" />
  </OutputClaims>
</ClaimsTransformation>
```

- Bemeneti igények:
    - **outputClaim**: Üdvözli a Contoso App. Ha továbbra is böngészni és használni ezt a weboldalt, akkor elfogadja, hogy megfeleljenek, és köti az alábbi feltételeket ...
- Kimeneti jogcímek:
    - **outputClaim**: NULL

## <a name="parsedomain"></a>Elemzési tartomány

Az e-mail cím tartományrészének beszerzése.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (Bemeneti jogcím) | e-mailcím | sztring | Az e-mail címet tartalmazó Jogcímtípus. |
| OutputClaim (Kimeneti jogcím) | domain | sztring | A jogcímátalakítás meghívása után létrehozott Jogcímtípus - a tartomány. |

Ezzel a jogcímátalakításával elemezheti a tartománynevet a @ szimbólum után. A következő jogcímek átalakítása bemutatja, hogyan elemezheti a tartománynevet egy **e-mail** jogcímből.

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

- Bemeneti igények:
  - **e-mailCím**:joe@outlook.com
- Kimeneti jogcímek:
    - **tartomány**: outlook.com

## <a name="setclaimsifregexmatch"></a>SetClaimsIfRegexMatch

Ellenőrzi, hogy `claimToMatch` egy `matchTo` karakterlánc-jogcím és bemeneti paraméter egyenlő-e, és beállítja a kimeneti jogcímeket a bemeneti paraméterben `outputClaimIfMatched` jelen lévő értékkel, valamint az összehasonlítási eredmény kimeneti jogcímet, amelyet az összehasonlítás eredményeként `true` vagy `false` az összehasonlítás eredménye alapján kell beállítani.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | jogcím | sztring | A jogcím típusa, amelyet össze kell hasonlítani. |
| InputParameter (Beviteli paraméter) | matchTo között | sztring | A szabályszerű kifejezés. |
| InputParameter (Beviteli paraméter) | outputClaimIfMatched | sztring | A karakterláncok egyenlő sége esetén beállítandó érték. |
| InputParameter (Beviteli paraméter) | extractCsoportok | logikai | [Nem kötelező] Itt adható meg, hogy a Regex egyezés kinyerje-e a csoportok értékeit. Lehetséges értékek: `true` `false` vagy (alapértelmezett). | 
| OutputClaim (Kimeneti jogcím) | outputClaim | sztring | Ha a reguláris kifejezés egyezik, `outputClaimIfMatched` ez a kimeneti jogcím a bemeneti paraméter értékét tartalmazza. Vagy null, ha nincs egyezés. |
| OutputClaim (Kimeneti jogcím) | regexCompareResultClaim | logikai | A reguláris kifejezés egyezik az eredmény `true` kimeneti jogcímtípusával, amelyet az egyeztetés eredményeként vagy `false` eredményealapján kell beállítani. |
| OutputClaim (Kimeneti jogcím)| A követelés neve| sztring | Ha a extractGroups bemeneti paraméter értéke igaz, a jogcímátalakítás meghívása után létrehozott jogcímtípusok listája. A jogcímtípus nevének meg kell egyeznie a Regex csoport nevével. | 

### <a name="example-1"></a>1. példa

Ellenőrzi, hogy a megadott telefonszám érvényes-e a telefonszám reguláris kifejezési mintája alapján.

```XML
<ClaimsTransformation Id="SetIsPhoneRegex" TransformationMethod="SetClaimsIfRegexMatch">
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

- Bemeneti igények:
    - **claimToMatch**: "64854114520"
- Bemeneti paraméterek:
    - **matchTo**: "^[0-9]{4,16}$"
    - **outputClaimIfMatched**: "isPhone"
- Kimeneti jogcímek:
    - **outputClaim**: "isPhone"
    - **regexCompareResultClaim**: true

### <a name="example-2"></a>2. példa

Ellenőrzi, hogy a megadott e-mail cím érvényes-e, és visszaadja az e-mail aliast.

```XML
<ClaimsTransformation Id="GetAliasFromEmail" TransformationMethod="SetClaimsIfRegexMatch">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="claimToMatch" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="matchTo" DataType="string" Value="(?&lt;mailAlias&gt;.*)@(.*)$" />
    <InputParameter Id="outputClaimIfMatched" DataType="string" Value="isEmail" />
    <InputParameter Id="extractGroups" DataType="boolean" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="validationResult" TransformationClaimType="outputClaim" />
    <OutputClaim ClaimTypeReferenceId="isEmailString" TransformationClaimType="regexCompareResultClaim" />
    <OutputClaim ClaimTypeReferenceId="mailAlias" />
  </OutputClaims>
</ClaimsTransformation>
```

- Bemeneti igények:
    - **claimToMatch**:emily@contoso.com" "
- Bemeneti paraméterek:
    - **matchTo :**`(?&lt;mailAlias&gt;.*)@(.*)$`
    - **outputClaimIfMatched**: "isEmail"
    - **extractGroups**: igaz
- Kimeneti jogcímek:
    - **outputClaim**: "isEmail"
    - **regexCompareResultClaim**: true
    - **mailAlias**: emily
    
## <a name="setclaimsifstringsareequal"></a>SetClaimsIfStringsAreEqual

Ellenőrzi, hogy egy `matchTo` karakterlánc-jogcím és bemeneti paraméter egyenlő-e, és beállítja a kimeneti jogcímeket a jelen lévő `stringMatchMsg` értékkel és `stringMatchMsgCode` a bemeneti paraméterekkel, valamint az összehasonlítási eredmény kimeneti jogcímet, amelyet az összehasonlítás eredményeként `true` vagy `false` alapján kell beállítani.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (Bemeneti jogcím) | inputClaim | sztring | A jogcím típusa, amelyet össze kell hasonlítani. |
| InputParameter (Beviteli paraméter) | matchTo között | sztring | A karakterláncot a- val `inputClaim`kell összehasonlítani. |
| InputParameter (Beviteli paraméter) | karakterláncÖsszehasonlítás | sztring | Lehetséges `Ordinal` értékek: `OrdinalIgnoreCase`vagy . |
| InputParameter (Beviteli paraméter) | stringMatchMsg | sztring | A karakterláncok egyenlősége esetén beállítandó első érték. |
| InputParameter (Beviteli paraméter) | stringMatchMsgCode | sztring | A karakterláncok egyenlő beállítása esetén beállítandó második érték. |
| OutputClaim (Kimeneti jogcím) | outputClaim1 | sztring | Ha a karakterláncok egyenlőek, ez a `stringMatchMsg` kimeneti jogcím a bemeneti paraméter értékét tartalmazza. |
| OutputClaim (Kimeneti jogcím) | outputClaim2 | sztring | Ha a karakterláncok egyenlőek, ez a `stringMatchMsgCode` kimeneti jogcím a bemeneti paraméter értékét tartalmazza. |
| OutputClaim (Kimeneti jogcím) | stringCompareResultClaim | logikai | Az összehasonlításeredmény kimeneti jogcímtípusa, `true` amelyet az összehasonlítás eredményeként vagy `false` alapján kell beállítani. |

Ezzel a jogcímátalakítással ellenőrizheti, hogy a jogcím megegyezik-e a megadott értékkel. Például a következő jogcímek átalakítása ellenőrzi, hogy a **termsOfUseConsentVersion** jogcím értéke egyenlő-e. `v1` Ha igen, módosítsa `v2`az értéket .

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

- Bemeneti igények:
    - **inputClaim**: v1
- Bemeneti paraméterek:
    - **matchTo**: V1
    - **stringComparison**: ordinalIgnoreCase
    - **stringMatchMsg**: B2C_V1_90005
    - **stringMatchMsgCode**: A TOS frissítve v2
- Kimeneti jogcímek:
    - **outputClaim1**: B2C_V1_90005
    - **outputClaim2**: A TOS frissítve v2
    - **stringCompareResultClaim**: true

## <a name="setclaimsifstringsmatch"></a>SetClaimsIfStringsMatch

Ellenőrzi, hogy egy `matchTo` karakterlánc-jogcím és bemeneti paraméter egyenlő-e, és beállítja a kimeneti jogcímeket a bemeneti paraméterben `outputClaimIfMatched` jelen lévő értékkel, valamint az összehasonlítási eredmény kimeneti jogcímet, amelyet az összehasonlítás eredményeként `true` vagy `false` az összehasonlítás eredménye alapján kell beállítani.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (Bemeneti jogcím) | jogcím | sztring | A jogcím típusa, amelyet össze kell hasonlítani. |
| InputParameter (Beviteli paraméter) | matchTo között | sztring | Az inputClaim-tel összehasonlítandó karakterlánc. |
| InputParameter (Beviteli paraméter) | karakterláncÖsszehasonlítás | sztring | Lehetséges `Ordinal` értékek: `OrdinalIgnoreCase`vagy . |
| InputParameter (Beviteli paraméter) | outputClaimIfMatched | sztring | A karakterláncok egyenlő sége esetén beállítandó érték. |
| OutputClaim (Kimeneti jogcím) | outputClaim | sztring | Ha a karakterláncok egyenlőek, ez a `outputClaimIfMatched` kimeneti jogcím a bemeneti paraméter értékét tartalmazza. Vagy null, ha a karakterláncok nem egyeznek. |
| OutputClaim (Kimeneti jogcím) | stringCompareResultClaim | logikai | Az összehasonlításeredmény kimeneti jogcímtípusa, `true` amelyet az összehasonlítás eredményeként vagy `false` alapján kell beállítani. |

Például a következő jogcímek átalakítása ellenőrzi, hogy az `Minor` **ageGroup** jogcím értéke megegyezik-e a. Ha igen, adja `B2C_V1_90001`vissza az értéket a értékre.

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

- Bemeneti igények:
    - **claimToMatch**: Kisebb
- Bemeneti paraméterek:
    - **matchTo**: Kisebb
    - **stringComparison**: ordinalIgnoreCase
    - **outputClaimIfMatched**: B2C_V1_90001
- Kimeneti jogcímek:
    - **isMinorResponseCode**: B2C_V1_90001
    - **isMinor**: igaz


## <a name="stringcontains"></a>Karakterlánctartalmazza

Határozza meg, hogy egy megadott részkarakterlánc történik-e a bemeneti jogcímen belül. Az eredmény egy új logikai jogcímtípus, amelynek `true` értéke vagy. `false` `true`ha az értékparaméter ebben a karakterláncban történik, `false`ellenkező esetben .

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (Bemeneti jogcím) | inputClaim | sztring | A keresett jogcímtípus. |
|InputParameter (Beviteli paraméter)|tartalmazza a következőt:|sztring|A keresni való érték.|
|InputParameter (Beviteli paraméter)|ignoreCase|sztring|Itt adható meg, hogy ez az összehasonlítás figyelmen kívül hagyja-e az összehasonlított karakterlánc esetét.|
| OutputClaim (Kimeneti jogcím) | outputClaim | sztring | A Jogcímátalakítás meghívása után létrehozott Jogcímtípus. Logikai jelző, ha a karakterláncrész a bemeneti jogcímben történik. |

Ezzel a jogcímátalakítással ellenőrizheti, hogy egy karakterlánc-jogcímtípus tartalmaz-e részkarakterláncot. A következő példa `roles` ellenőrzi, hogy a karakterlánc-jogcímtípus tartalmazza-e a **rendszergazda**értékét.

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

- Bemeneti igények:
    - **inputClaim**: "Admin, jóváhagyó, szerkesztő"
- Bemeneti paraméterek:
    - **tartalma:**"admin",
    - **ignoreCase**: igaz
- Kimeneti jogcímek:
    - **outputClaim**: igaz

## <a name="stringsubstring"></a>Karakterlánc-karakterlánc

Egy karakterlánc-jogcímtípus részeit a megadott helyen lévő karaktertől kezdődően kinyeri, és a megadott számú karaktert adja vissza.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (Bemeneti jogcím) | inputClaim | sztring | A karakterláncot tartalmazó jogcímtípus. |
| InputParameter (Beviteli paraméter) | startIndex | int | Ebben a példáulban a karakterláncrész nulla-alapú kezdőkarakter-pozíciója. |
| InputParameter (Beviteli paraméter) | hossz | int | A karakterláncrész ben lévő karakterek száma. |
| OutputClaim (Kimeneti jogcím) | outputClaim | logikai | Olyan karakterlánc, amely megegyezik a jelen példányban a startIndex-nél kezdődő hosszrészkarakterlánccal, vagy üres, ha a startIndex megegyezik a példány hosszával, és a hossz nulla. |

Például, hogy a telefonszám ország előtagja.


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

- Bemeneti igények:
    - **inputClaim**: "+1644114520"
- Bemeneti paraméterek:
    - **startIndex**: 0
    - **hossz**: 2
- Kimeneti jogcímek:
    - **outputClaim**: "+1"

## <a name="stringreplace"></a>Karakterlánccseréje

Egy jogcímtípus-karakterláncban keres egy megadott értéket, és egy új jogcímtípus-karakterláncot ad vissza, amelyben az aktuális karakterlánc ban megadott karakterlánc összes előfordulását egy másik megadott karakterlánc váltja fel.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (Bemeneti jogcím) | inputClaim | sztring | A karakterláncot tartalmazó jogcímtípus. |
| InputParameter (Beviteli paraméter) | régiérték | sztring | A keresendő karakterlánc. |
| InputParameter (Beviteli paraméter) | newValue | sztring | Az összes előfordulást felülcserélő karakterlánc`oldValue` |
| OutputClaim (Kimeneti jogcím) | outputClaim | logikai | Az aktuális karakterláncnak megfelelő karakterlánc, azzal a különbséggel, hogy a régiérték összes példányát lecseréli a newValue. Ha az oldValue nem található az aktuális példányban, a metódus változatlanul adja vissza az aktuális példányt. |

Például normalizáljon egy telefonszámot, `-` ha eltávolítja a


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

- Bemeneti igények:
    - **inputClaim**: "+164-411-452-054"
- Bemeneti paraméterek:
    - **oldValue**: "-"
    - **hossz**: ""
- Kimeneti jogcímek:
    - **outputClaim**: "+164411452054"

## <a name="stringjoin"></a>StringJoin (Karakterlánc-csatlakozás)

Egy megadott karakterlánc-gyűjteményjogcímtípus elemeinek összefűzése az egyes elemek vagy tagok között megadott elválasztó val.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (Bemeneti jogcím) | inputClaim | karakterláncgyűjtemény | Az összefűzéshez kötött karakterláncokat tartalmazó gyűjtemény. |
| InputParameter (Beviteli paraméter) | elválasztókarakter | sztring | Az elválasztóként használandó karakterlánc, például vessző `,`. |
| OutputClaim (Kimeneti jogcím) | outputClaim | sztring | A `inputClaim` karakterlánc-gyűjtemény tagjaiból álló karakterlánc, amelyet `delimiter` a bemeneti paraméter határolt. |

A következő példa felhasználói szerepkörök karakterlánc-gyűjteményét veszi fel, és vesszővel határoló karakterláncká alakítja. Ezzel a módszerrel tárolhat egy karakterlánc-gyűjteményt az Azure AD felhasználói fiókjában. Később, amikor beolvassa a fiókot `StringSplit` a könyvtárból, használja a konvertálni a vesszőhahatároló karakterlánc vissza string gyűjtemény.

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

- Bemeneti igények:
  - **inputClaim**: [ "Admin", "Szerző", "Olvasó" ]
- Bemeneti paraméterek:
  - **határoló :**","
- Kimeneti jogcímek:
  - **outputClaim**: "Admin,Szerző,Olvasó"


## <a name="stringsplit"></a>StringSplit között

Olyan karakterlánctömböt ad vissza, amely a megadott karakterlánc elemei által határolt karakterláncokat tartalmazza ebben a példányban.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (Bemeneti jogcím) | inputClaim | sztring | A felosztandó alkarakterláncokat tartalmazó karakterláncjogcím-típus. |
| InputParameter (Beviteli paraméter) | elválasztókarakter | sztring | Az elválasztóként használandó karakterlánc, például vessző `,`. |
| OutputClaim (Kimeneti jogcím) | outputClaim | karakterláncgyűjtemény | Olyan karakterláncgyűjtemény, amelynek elemei a karakterlánc ban lévő, `delimiter` a bemeneti paraméter által határolt részkarakterláncokat tartalmazzák. |

A következő példa a felhasználói szerepkörök vesszővel határoló karakterláncát veszi fel, és karakterlánc-gyűjteménysé alakítja azt.

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

- Bemeneti igények:
  - **inputClaim**: "Admin,Szerző,Olvasó"
- Bemeneti paraméterek:
  - **határoló :**","
- Kimeneti jogcímek:
  - **outputClaim**: [ "Admin", "Szerző", "Olvasó" ]

## <a name="string-claim-transformations-expressions"></a>Karakterláncjogcím-átalakítások kifejezései
Az Azure AD B2C egyéni szabályzataiban a jogcímátalakítások kifejezései a bérlői azonosítóés a technikai profil azonosítójának környezetbeli adatait biztosítják.

  | Kifejezés | Leírás | Példa |
 | ----- | ----------- | --------|
 | `{TechnicalProfileId}` | A technikai profilazonosító neve. | Facebook-OAUTH |
 | `{RelyingPartyTenantId}` | A függő entitás házirendjének bérlői azonosítója. | your-tenant.onmicrosoft.com |
 | `{TrustFrameworkTenantId}` | A megbízhatósági keretrendszer bérlői azonosítója. | your-tenant.onmicrosoft.com |
