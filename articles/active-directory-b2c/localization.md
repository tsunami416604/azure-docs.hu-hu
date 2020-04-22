---
title: Honosítás – Azure Active Directory B2C
description: Adja meg egy egyéni szabályzat honosítási elemét az Azure Active Directory B2C-ben.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 94ff7ddda41f2df2634d927a7dbf8a5a0d4fc1d8
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681417"
---
# <a name="localization"></a>Honosítás

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A **Honosítás** i elem lehetővé teszi, hogy több területi beállítás vagy nyelv támogatását is lehetővé tegye a felhasználói utak házirendjében. A házirendek honosítási támogatása lehetővé teszi a következőket:

- Állítsa be a támogatott nyelvek explicit listáját egy házirendben, és válasszon egy alapértelmezett nyelvet.
- Adjon meg nyelvspecifikus karakterláncokat és gyűjteményeket.

```XML
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
  </SupportedLanguages>
  <LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedResources Id="api.localaccountsignup.es">
  ...
```

A **Honosítási** elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Engedélyezve | Nem | Lehetséges `true` értékek: `false`vagy . |

A **Honosítási** elem a következő XML-elemeket tartalmazza

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| Támogatott nyelvek | 1:n | A támogatott nyelvek listája. |
| Honosított erőforrások | 0:n | Honosított erőforrások listája. |

## <a name="supportedlanguages"></a>Támogatott nyelvek

A **SupportedLanguages** elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| DefaultLanguage | Igen | A honosított erőforrások alapértelmezett nyelve. |
| MergeBehavior (Körviselkedés) | Nem | Az azonos azonosítóval rendelkező szülőházirendben található bármely ClaimType-al egyesített értékek felsorolási értékei. Ezt az attribútumot akkor használja, ha felülírja az alapházirendben megadott jogcímet. Lehetséges `Append`értékek: `Prepend`, `ReplaceAll`vagy . Az `Append` érték azt adja meg, hogy a jelen lévő adatok gyűjtését hozzá kell fűzni a szülőházirendben megadott gyűjtemény végéhez. Az `Prepend` érték azt adja meg, hogy a jelen lévő adatok gyűjtését a szülőházirendben megadott gyűjtemény előtt hozzá kell adni. Az `ReplaceAll` érték azt adja meg, hogy a szülőházirendben definiált adatok gyűjtését figyelmen kívül kell hagyni, ehelyett az aktuális házirendben meghatározott adatokat használva. |

### <a name="supportedlanguages"></a>Támogatott nyelvek

A **SupportedLanguages** elem a következő elemeket tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| Támogatott nyelv | 1:n | Az 5646-os számú RFC-dokumentumra vonatkozó nyelvi címkének megfelelő tartalmat jelenít meg – Címkék a nyelvek azonosítására. |

## <a name="localizedresources"></a>Honosított erőforrások

A **LocalizedResources** elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Azonosító | Igen | A honosított erőforrások egyedi azonosítására használt azonosító. |

A **Honosított erőforrások** elem a következő elemeket tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| Honosított gyűjtemények | 0:n | Meghatározza a teljes gyűjtemények különböző kultúrákban. A gyűjtemény különböző számú elemet és különböző karakterláncokat tartalmazhat a különböző kultúrákhoz. Gyűjtemények például a jogcímtípusokban megjelenő felsorolások. Például egy ország/régió lista jelenik meg a felhasználó számára egy legördülő listában. |
| Honosított karakterláncok | 0:n | Meghatározza az összes karakterláncot, kivéve azokat a karakterláncokat, amelyek különböző kultúrákban jelennek meg. |

### <a name="localizedcollections"></a>Honosított gyűjtemények

A **LocalizedCollections** elem a következő elemeket tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| Honosítottgyűjtemény | 1:n | A támogatott nyelvek listája. |

#### <a name="localizedcollection"></a>Honosítottgyűjtemény

A **LocalizedCollection** elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Elemtípusa | Igen | A házirendfájlban egy ClaimType elemre vagy egy felhasználói felületelemre hivatkozik. |
| ElementId | Igen | Olyan karakterlánc, amely a ClaimsSchema szakaszban már definiált jogcímtípusra mutató hivatkozást tartalmaz, ha az **ElementType** jogcímtípusra van állítva. |
| Célgyűjtemény | Igen | A célgyűjtemény. |

A **LocalizedCollection** elem a következő elemeket tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| Elem | 0:n | Meghatározza azt a rendelkezésre álló lehetőséget, amelyet a felhasználó a felhasználói felületen egy jogcímhez, például egy legördülő lista értékéhez választhat. |

Az **Elem** elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Szöveg | Igen | A felhasználóbarát megjelenítési karakterlánc, amelyet a felhasználónak meg kell jelenítenie a felhasználói felületen ehhez a beállításhoz. |
| Érték | Igen | A beállítás kiválasztásához társított karakterlánc-jogcímérték. |
| SelectByDefault (Alapértelmezett beállítás) | Nem | Azt jelzi, hogy ezt a beállítást alapértelmezés szerint be kell-e jelölni a felhasználói felületen. Lehetséges értékek: Igaz vagy Hamis. |

A következő példa a **LocalizedCollections** elem használatát mutatja be. Két **LocalizedCollection** elemet tartalmaz, az egyik az angol, a másik a spanyol. Mindkettő a `Gender` **jogcím korlátozási** gyűjteményét angol és spanyol nyelvű elemek listájával állítja be.

```XML
<LocalizedResources Id="api.selfasserted.en">
 <LocalizedCollections>
   <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
      <Item Text="Female" Value="F" />
      <Item Text="Male" Value="M" />
    </LocalizedCollection>
</LocalizedCollections>

<LocalizedResources Id="api.selfasserted.es">
 <LocalizedCollections>
   <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
      <Item Text="Femenino" Value="F" />
      <Item Text="Masculino" Value="M" />
    </LocalizedCollection>
</LocalizedCollections>
```

### <a name="localizedstrings"></a>Honosított karakterláncok

A **LocalizedStrings** elem a következő elemeket tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| Honosított karakterlánc | 1:n | Honosított karakterlánc. |

A **LocalizedString** elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Elemtípusa | Igen | Lehetséges értékek: [ClaimsProvider](#claimsprovider), [ClaimType](#claimtype), [ErrorMessage](#errormessage), [GetLocalizedStringsTransformationClaimType](#getlocalizedstringstransformationclaimtype), [Predikátum](#predicate), [InputValidation](#inputvalidation)vagy [UxElement](#uxelement).   | 
| ElementId | Igen | Ha az **ElementType** `Predicate`beállítása `InputValidation` `ClaimType`a , vagy , ez az elem a ClaimsSchema szakaszban már definiált jogcímtípusra mutató hivatkozást tartalmaz. |
| Karakterlánc-azonosító | Igen | Ha az **ElementType** `ClaimType`beállítása, akkor ez az elem egy jogcímtípusú attribútumra mutató hivatkozást tartalmaz. Lehetséges `DisplayName`értékek: `AdminHelpText`, `PatternHelpText`vagy . Az `DisplayName` érték a jogcím megjelenítési nevének beállítására szolgál. Az `AdminHelpText` érték a jogcímfelhasználó súgószövegnevének beállítására szolgál. Az `PatternHelpText` érték a jogcímminta súgószövegének beállítására szolgál. Ha az **ElementType** beállítása `UxElement`a beállítás, akkor ez az elem egy felhasználói felületelem attribútumára mutató hivatkozást tartalmaz. Ha az **ElementType** `ErrorMessage`érték, akkor ez az elem egy hibaüzenet azonosítóját adja meg. Az azonosítók teljes listáját a [Honosítási karakterlánc-azonosítók](localization-string-ids.md) című témakörben található. `UxElement`|

## <a name="elementtype"></a>Elemtípusa

Az ElementType hivatkozás a honosítandó házirend ben egy jogcímtípusra, egy jogcímátalakításra vagy egy felhasználói felületelemre.

| Honosításra keresztelt elem | Elemtípusa | ElementId |Karakterlánc-azonosító |
| --------- | -------- | ----------- |----------- |
| Identitásszolgáltató neve |`ClaimsProvider`| | A ClaimsExchange elem azonosítója|
| Jogcímtípus-attribútumok|`ClaimType`|A jogcímtípus neve| A honosítandó jogcím attribútuma. Lehetséges `AdminHelpText`értékek: `DisplayName` `PatternHelpText`, `UserHelpText`, és .|
|Hibaüzenet|`ErrorMessage`||A hibaüzenet azonosítója |
|Lokalizált karakterláncok másolása jogcímekké|`GetLocalizedStringsTra nsformationClaimType`||A kimeneti jogcím neve|
|Predikátumfelhasználói üzenet|`Predicate`|Az állítmány neve| A lokalizálandó predikátum attribútuma. Lehetséges értékek: `HelpText`.|
|Predikátumcsoport felhasználói üzenete|`InputValidation`|A PredicateValidation elem azonosítója.|A PredicateGroup elem azonosítója. Az alapcsoportnak az ElementId-ben meghatározott predikátum validációs elem gyermekének kell lennie.|
|Felhasználó felület elemei |`UxElement` | | A honizálandó felhasználói felületelem azonosítója.|

## <a name="examples"></a>Példák

### <a name="claimsprovider"></a>Jogcímszolgáltató

A ClaimsProvider érték a jogcímszolgáltatók egyikének megjelenítendő nevének honosítására szolgál. 

```xml
<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAUTH" />
  </ClaimsExchanges>
</OrchestrationStep>

```

A következő példa bemutatja, hogyan honosíthatja a jogcímszolgáltatók megjelenítendő nevét.

```xml
<LocalizedString ElementType="ClaimsProvider" StringId="FacebookExchange">Facebook</LocalizedString>
<LocalizedString ElementType="ClaimsProvider" StringId="GoogleExchange">Google</LocalizedString>
<LocalizedString ElementType="ClaimsProvider" StringId="LinkedInExchange">LinkedIn</LocalizedString>
```

### <a name="claimtype"></a>Jogcímtípus

A ClaimType érték a jogcímattribútumok egyikének honosítására szolgál. 

```xml
<ClaimType Id="email">
  <DisplayName>Email Address</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Email address that can be used to contact you.</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

A következő példa bemutatja, hogyan honalkazhatja az e-mail jogcímtípus DisplayName, UserHelpText és PatternHelpText attribútumait.

```XML
<LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email</LocalizedString>
<LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Please enter your email</LocalizedString>
<LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Please enter a valid email address</LocalizedString>
```

### <a name="errormessage"></a>ErrorMessage üzenet

Az ErrorMessage érték az egyik rendszerhibaüzenet honosítására szolgál. 

```xml
<TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">true</Item>
    <Item Key="UserMessageIfClaimsPrincipalAlreadyExists">You are already registered, please press the back button and sign in instead.</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

A következő példa bemutatja, hogyan honalizálhatja a UserMessageIfClaimsPrincipalAlreadyExists hibaüzenetet.


```XML
<LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">The account you are trying to create already exists, please sign-in.</LocalizedString>
```

### <a name="getlocalizedstringstransformationclaimtype"></a>GetLocalizedStringsTransformationClaimType

A GetLocalizedStringsTransformationClaimType érték a honosított karakterláncok jogcímekbe másolására szolgál. További információ: [GetLocalizedStringsTransformation jogcímek átalakítása](string-transformations.md#getlocalizedstringstransformation)


```xml
<ClaimsTransformation Id="GetLocalizedStringsForEmail" TransformationMethod="GetLocalizedStringsTransformation">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="subject" TransformationClaimType="email_subject" />
    <OutputClaim ClaimTypeReferenceId="message" TransformationClaimType="email_message" />
    <OutputClaim ClaimTypeReferenceId="codeIntro" TransformationClaimType="email_code" />
    <OutputClaim ClaimTypeReferenceId="signature" TransformationClaimType="email_signature" />
   </OutputClaims>
</ClaimsTransformation>
```

A következő példa bemutatja, hogyan honosíthatja a GetLocalizedStringsTransformation jogcímek átalakításának kimeneti jogcímeket.

```xml
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Contoso account email verification code</LocalizedString>
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Thanks for verifying your account!</LocalizedString>
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Your code is</LocalizedString>
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Sincerely</LocalizedString>
```

### <a name="predicate"></a>Predikátum

A Predikátum érték az egyik [predikátumhibaüzenet honosítására](predicates.md) szolgál. 

```xml
<Predicates>
  <Predicate Id="LengthRange" Method="IsLengthRange"  HelpText="The password must be between 6 and 64 characters.">
    <Parameters>
      <Parameter Id="Minimum">6</Parameter>
      <Parameter Id="Maximum">64</Parameter>
    </Parameters>
  </Predicate>
  <Predicate Id="Lowercase" Method="IncludesCharacters" HelpText="a lowercase letter">
    <Parameters>
      <Parameter Id="CharacterSet">a-z</Parameter>
    </Parameters>
  </Predicate>
  <Predicate Id="Uppercase" Method="IncludesCharacters" HelpText="an uppercase letter">
    <Parameters>
      <Parameter Id="CharacterSet">A-Z</Parameter>
    </Parameters>
  </Predicate>
</Predicates>
```

A következő példa bemutatja, hogyan honosíthatja a predikátumok súgószövegét.

```xml
<LocalizedString ElementType="Predicate" ElementId="LengthRange" StringId="HelpText">The password must be between 6 and 64 characters.</LocalizedString>
<LocalizedString ElementType="Predicate" ElementId="Lowercase" StringId="HelpText">a lowercase letter</LocalizedString>
<LocalizedString ElementType="Predicate" ElementId="Uppercase" StringId="HelpText">an uppercase letter</LocalizedString>
```

### <a name="inputvalidation"></a>InputValidation (InputValidation)

Az InputValidation érték a [PredicateValidation](predicates.md) csoport egyik hibaüzenetének honosítására szolgál. 

```xml
<PredicateValidations>
  <PredicateValidation Id="CustomPassword">
    <PredicateGroups>
      <PredicateGroup Id="LengthGroup">
        <PredicateReferences MatchAtLeast="1">
          <PredicateReference Id="LengthRange" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="CharacterClasses">
        <UserHelpText>The password must have at least 3 of the following:</UserHelpText>
        <PredicateReferences MatchAtLeast="3">
          <PredicateReference Id="Lowercase" />
          <PredicateReference Id="Uppercase" />
          <PredicateReference Id="Number" />
          <PredicateReference Id="Symbol" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>
</PredicateValidations>
```

A következő példa bemutatja, hogyan honosíthatja a predikátum ellenőrzési csoport súgószöveg.

```XML
<LocalizedString ElementType="InputValidation" ElementId="CustomPassword" StringId="CharacterClasses">The password must have at least 3 of the following:</LocalizedString>
```

### <a name="uxelement"></a>UxElem

Az UxElement érték a felhasználói felület egyik elemének honosítására szolgál. A következő példa bemutatja, hogyan honalkazhatja a folytatást és a megszakításgombot.

```XML
<LocalizedString ElementType="UxElement" StringId="button_continue">Create new account</LocalizedString>
<LocalizedString ElementType="UxElement" StringId="button_cancel">Cancel</LocalizedString>
```

## <a name="next-steps"></a>További lépések

A honosítási példákat az alábbi cikkekben talál:

- [Nyelvi testreszabás egyéni szabályzattal az Azure Active Directory B2C-ben](custom-policy-localization.md)
- [Nyelvi testreszabás felhasználói folyamatokkal az Azure Active Directory B2C-ben](user-flow-language-customization.md)
