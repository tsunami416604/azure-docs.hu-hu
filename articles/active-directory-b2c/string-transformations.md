---
title: Karakterlánc-jogcímek átalakítása példák az identitás élmény keretrendszer sémát az Azure Active Directory B2C |} A Microsoft Docs
description: Karakterlánc jogcímek átalakítása példák az identitás élmény keretrendszer sémát az Azure Active Directory B2C a.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: ce2ad3e699b930f801ad47083d6cfcf6a7937a5c
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/28/2018
ms.locfileid: "47433446"
---
# <a name="string-claims-transformations"></a>Karakterlánc jogcím-átalakítás

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ez a cikk példákat ad az Azure Active Directory (Azure AD) B2C-ben a karakterlánc a jogcímek átalakítása az identitás-kezelőfelületi keretrendszer séma használatával. További információkért lásd: [ClaimsTransformations](claimstransformations.md).

## <a name="assertstringclaimsareequal"></a>AssertStringClaimsAreEqual 

Hasonlítsa össze a két jogcímeket, és ha azok nem egyenlő a megadott összehasonlító inputClaim1, inputClaim2 és stringComparison alapján kivételt.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| Bemeneti jogcím | inputClaim1 | sztring | Első jogcím típusa, amelyet össze kell hasonlítani. |
| Bemeneti jogcím | inputClaim2 | sztring | Második jogcím típusa, ami kell összehasonlítani. |
| InputParameter | stringComparison | sztring | karakterlánc-összehasonlítási, az értékek egyike: sorszámát, OrdinalIgnoreCase. |

A **AssertStringClaimsAreEqual** jogcímek átalakítását a rendszer mindig futtatja a egy [ellenőrzési technikai profil](validation-technical-profile.md) , hívja meg a [önálló kiszolgáló által megerősített technikai profil](self-asserted-technical-profile.md). A **UserMessageIfClaimsTransformationStringsAreNotEqual** önellenőrzött technikai profil metaadataiban szabályozza a hibaüzenet a felhasználó számára látható.

![AssertStringClaimsAreEqual végrehajtása](./media/string-transformations/assert-execution.png)

Használhatja a jogcím-átalakítás győződjön meg arról, hogy, két ClaimTypes ugyanazzal az értékkel rendelkezik. Ha nem, akkor egy hibaüzenet fordul elő. Az alábbi példa ellenőrzi, hogy a **strongAuthenticationEmailAddress** takar egyenlő **e-mail** takar. Ellenkező esetben hibaüzenet fordul elő. 

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


A **bejelentkezési nem interaktív** érvényesítési technikai profil hívások a **AssertEmailAndStrongAuthenticationEmailAddressAreEqual** jogcím-átalakítás.
```XML
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertEmailAndStrongAuthenticationEmailAddressAreEqual" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

Az önellenőrzött technikai profilban meghívja az érvényesítés **bejelentkezési nem interaktív** technikai profil.

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

- A bemeneti jogcímek:
    - **inputClaim1**: someone@contoso.com
    - **inputClaim2**: someone@outlook.com
 - Bemeneti paraméterek:
    - **stringComparison**: ordinalIgnoreCase
- Eredmény: Hiba lépett fel

## <a name="changecase"></a>ChangeCase 

Módosítja az eset a megadott jogcím csökkentheti, vagy a nagybetűs függően az operátor.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| Bemeneti jogcím | inputClaim1 | sztring | A takar, amelyek kell módosítani. |
| InputParameter | toCase | sztring | A következő értékek egyikét: `LOWER` vagy `UPPER`. |
| kimeneti jogcím | kimeneti jogcím | sztring | Az a jogcím-átalakítás után előállított takar meghívása. |

Bármilyen karakterlánc takar csökkentheti, vagy a nagybetűs módosításához használja a jogcím-átalakítást.  

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

- A bemeneti jogcímek:
    - **e-mailek**: SomeOne@contoso.com
- Bemeneti paraméterek:
    - **toCase**: ALACSONYABB
- Kimeneti jogcímek:
    - **e-mailek**: someone@contoso.com

## <a name="createstringclaim"></a>CreateStringClaim 

A házirendben megadott bemeneti paraméterből egy karakterlánc jogcímet hoz létre.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
|----- | ----------------------- | --------- | ----- |
| InputParameter | érték | sztring | A karakterláncot kell beállítani |
| kimeneti jogcím | createdClaim | sztring | Az a jogcím-átalakítás után előállított takar meghívása a bemeneti paraméterben megadott értékkel. |

A jogcím-átalakítás való használatát egy karakterlánc takar érték beállítása.

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

- A bemeneti paraméter:
    - **érték**: Contoso használati feltételei...
- Kimeneti jogcímek:
    - **createdClaim**: A TOS takar a "Contoso használati szolgáltatás..." értéket tartalmaz.

## <a name="compareclaims"></a>CompareClaims

Megállapításához, hogy egy karakterlánc jogcím egy másik egyenlő. Az eredmény egy új logikai takar értékkel `true` vagy `false`.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| Bemeneti jogcím | inputClaim1 | sztring | Először jogcím típusa, amelyet össze kell hasonlítani. |
| Bemeneti jogcím | inputClaim2 | sztring | A második jogcím típusa, amelyet össze kell hasonlítani. |
| InputParameter | Operátor | sztring | A lehetséges értékek: `EQUAL` vagy `NOT EQUAL`. |
| InputParameter | ignoreCase | logikai | Itt adhatja meg, hogy ez az összehasonlítás figyelmen kívül hagyja a kis-és a két összehasonlított karakterlánc. |
| kimeneti jogcím | kimeneti jogcím | logikai | Az a jogcím-átalakítás után előállított takar meghívása. |

A jogcím-átalakítás annak ellenőrzéséhez, hogy a jogcím megegyezik egy másik jogcímek használatát. Például a következő jogcím-e átalakítási ellenőrzések Ha értékét a **e-mail** jogcím megegyezik a **Verified.Email** jogcím.

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

- A bemeneti jogcímek:
    - **inputClaim1**: someone@contoso.com
    - **inputClaim2**: someone@outlook.com
- Bemeneti paraméterek:
    - **operátor**: nem egyenlő
    - **ignoreCase**: igaz
- Kimeneti jogcímek:
    - **kimeneti jogcím**: igaz

## <a name="compareclaimtovalue"></a>CompareClaimToValue

Meghatározza, hogy a jogcím értéke megegyezik a bemeneti paraméter értéke.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| Bemeneti jogcím | inputClaim1 | sztring | A jogcím típusa, amelyet össze kell hasonlítani. |
| InputParameter | Operátor | sztring | A lehetséges értékek: `EQUAL` vagy `NOT EQUAL`. |
| InputParameter | compareto metódus végrehajtása | sztring | karakterlánc-összehasonlítási, az értékek egyike: sorszámát, OrdinalIgnoreCase. |
| InputParameter | ignoreCase | logikai | Itt adhatja meg, hogy ez az összehasonlítás figyelmen kívül hagyja a kis-és a két összehasonlított karakterlánc. |
| kimeneti jogcím | kimeneti jogcím | logikai | Az a jogcím-átalakítás után előállított takar meghívása. |

Használhatja a jogcím-átalakítás, ellenőrizze, hogy egy jogcímet a megadott érték egyenlő. Például a következő jogcím-e átalakítási ellenőrzések Ha értékét a **termsOfUseConsentVersion** jogcím egyenlő `v1`.

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
- A bemeneti jogcímek:
    - **inputClaim1**: v1
- Bemeneti paraméterek:
    - **compareto metódus végrehajtása**: V1
    - **operátor**: egyenlő 
    - **ignoreCase**: igaz
- Kimeneti jogcímek:
    - **kimeneti jogcím**: igaz

## <a name="createrandomstring"></a>CreateRandomString

Létrehoz egy véletlenszerű karakterlánc a véletlenszám-generátor használatával. Ha a véletlenszám-generátor típusú `integer`, igény szerint elhagyható rekordparaméterrel kezdőérték paraméter és a egy maximális számát. Egy nem kötelező karakterlánc-formátum paraméter lehetővé teszi, hogy a kimeneti formázott használja azt, és a egy nem kötelező base64 paraméter határozza meg, hogy a kimenet base64-kódolású randomGeneratorType [guid, egész] kimeneti jogcím (karakterlánc).

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputParameter | randomGeneratorType | sztring | Adja meg a generált, véletlenszerű értéket `GUID` (globálisan egyedi azonosító) vagy `INTEGER` (szám). |
| InputParameter | stringFormat | sztring | [Opcionális] A véletlenszerű értéket formátumban. |
| InputParameter | Base64 | logikai | [Opcionális] A véletlenszerű értéket átalakítása base64. Ha karakterláncként van érvényben, az érték karakterlánc-formátum után kódolása base64. |
| InputParameter | maximumNumber | int | [Opcionális] A `INTEGER` randomGeneratorType csak. Adja meg az maximute. |
| InputParameter | Kezdőérték  | int | [Opcionális] A `INTEGER` randomGeneratorType csak. Adja meg a kezdőérték véletlenszerű értéket. Megjegyzés: az azonos kezdőérték poskytne véletlenszerű számból álló ugyanabban a sorrendben. |
| kimeneti jogcím | kimeneti jogcím | sztring | Az a jogcím-átalakítás után előállított ClaimTypes meghívása. A véletlenszerű értéket. |

A következő példa létrehoz egy globális egyedi. A jogcím-átalakítást a véletlenszerű UPN (egyszerű felhasználónév) létrehozására használatos.

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
    - **kimeneti jogcím**: bc8bedd2-aaa3-411e-bdee-2f1810b73dfc

Alábbi példa egy egész számot 0 és 1000 közötti véletlenszerű értéket állít elő. Az érték az olvashatóság érdekében OTP_ {véletlenszerű értéket}.

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
    - **Base64**: False (hamis)
- Kimeneti jogcímek: 
    - **kimeneti jogcím**: OTP_853


## <a name="formatstringclaim"></a>FormatStringClaim

Jogcím alapján megadott karakterlánc-formátum. Az átalakítás használja a C# `String.Format` metódust.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| Bemeneti jogcím | Bemeneti jogcím |sztring |A ClaimType, amely a karakterlánc-formátum {0} paraméter. |
| InputParameter | stringFormat | sztring | A karakterlánc-formátum, beleértve a {0} paraméter. |
| kimeneti jogcím | kimeneti jogcím | sztring | Az a jogcím-átalakítás után előállított takar meghívása. |

Használja a jogcím-átalakítás bármelyik egy paraméterrel karakterlánc formátumba {0}. A következő példában létrehozunk egy **userPrincipalName**. Összes közösségi szolgáltató technikai profil, például `Facebook-OAUTH` hívások a **CreateUserPrincipalName** létrehozni egy **userPrincipalName**.   

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

- A bemeneti jogcímek:
    - **bemeneti jogcím**: 5164db16-3eee-4629-bfda-dcc3326790e9
- Bemeneti paraméterek:
    - **stringFormat**: cpim_{0}@{RelyingPartyTenantId}
- Kimeneti jogcímek:
    - **kimeneti jogcím**: cpim_5164db16-3eee-4629-bfda-dcc3326790e9@b2cdemo.onmicrosoft.com

## <a name="formatstringmultipleclaims"></a>FormatStringMultipleClaims

A megadott formázó karakterlánc megfelelően két jogcímek formátumban. Az átalakítás használja a C# **String.Format** metódust.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| Bemeneti jogcím | Bemeneti jogcím |sztring | A ClaimType, amely a karakterlánc-formátum {0} paraméter. |
| Bemeneti jogcím | Bemeneti jogcím | sztring | A ClaimType, amely a karakterlánc-formátum {1} paraméter. |
| InputParameter | stringFormat | sztring | A karakterlánc-formátum, beleértve a {0} és {1} paramétereket. |
| kimeneti jogcím | kimeneti jogcím | sztring | Az a jogcím-átalakítás után előállított takar meghívása. |

Használja a jogcím-átalakítás két paramétert bármelyik karakterlánc formátumba {0} és {1}. A következő példában létrehozunk egy **displayName** a megadott formátummal:

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

- A bemeneti jogcímek:
    - **inputClaim1**: János
    - **inputClaim2**: Fernando
- Bemeneti paraméterek:
    - **stringFormat**: {0} {1}
- Kimeneti jogcímek:
    - **kimeneti jogcím**: János Fernando

## <a name="getmappedvaluefromlocalizedcollection"></a>GetMappedValueFromLocalizedCollection

Egy elem keresésekor a jogcím **korlátozás** gyűjtemény.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| Bemeneti jogcím | mapFromClaim | sztring | A jogcímet, amely keresett szöveget tartalmazza a **restrictionValueClaim** a jogcímeket a **korlátozás** gyűjtemény.  |
| kimeneti jogcím | restrictionValueClaim | sztring | A jogcímet, amely tartalmazza a **korlátozás** gyűjtemény. A jogcímek átalakításáról meghívása után a jogcím értékét tartalmazza a kijelölt elem értékét. |

Az alábbi példa a hibaüzenet leírása a hiba a kulcs alapján keres. A **responseMsg** jogcím elérhető, hogy a felhasználó vagy a függő entitás küldendő hibaüzenetek gyűjteményét tartalmazza.

```XML
<ClaimType Id="responseMsg">
  <DisplayName>Error message: </DisplayName>
  <DataType>string</DataType>
  <UserInputType>Paragraph</UserInputType>
  <Restriction>
    <Enumeration Text="B2C_V1_90001" Value="You cant sign in because you are a minor" />
    <Enumeration Text="B2C_V1_90002" Value="This action can only be performed by gold members" />
    <Enumeration Text="B2C_V1_90003" Value="You have not been enabled for this operation" />
  </Restriction>
</ClaimType>
```
A jogcímek átalakításáról kikeresi az elem szövegét, és annak értékét adja vissza. Ha a korlátozás van lokalizálva használatával `<LocalizedCollection>`, a jogcímek átalakításáról honosított értékét adja vissza.

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

- A bemeneti jogcímek:
    - **mapFromClaim**: B2C_V1_90001
- Kimeneti jogcímek:
    - **restrictionValueClaim**: átállítást jelentkezzen be, mert Ön egy kisebb.

## <a name="lookupvalue"></a>LookupValue

A jogcím értéke keresnie egy másik jogcím értéke alapján értékek listáját.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| Bemeneti jogcím | inputParameterId | sztring | A keresési értéket tartalmazó jogcímet |
| InputParameter | |sztring | InputParameters gyűjteménye. |
| InputParameter | errorOnFailedLookup | logikai | Annak szabályozása, hogy hibát ad vissza, ha nincs egyező keresési. |
| kimeneti jogcím | inputParameterId | sztring | Az a jogcím-átalakítás után előállított ClaimTypes meghívása. Az egyező azonosító értékét |

Az alábbi példában szereplő egyik inpuParameters gyűjtemény keres. A jogcímek átalakítását a tartomány nevét az azonosító és az értékét (az alkalmazás azonosítója) visszaadása.

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

- A bemeneti jogcímek:
    - **inputParameterId**: proba.com
- Bemeneti paraméterek:
    - **a contoso.com**: 13c15f79-8fb1-4e29-a6c9-be0d36ff19f1
    - **Microsoft.com**: 0213308f-17cb-4398-b97e-01da7bd4804e
    - **proba.com**: c7026f88-4299-4cdb-965d-3f166464b8a9
    - **errorOnFailedLookup**: False (hamis)
- Kimeneti jogcímek:
    - **kimeneti jogcím**: c7026f88-4299-4cdb-965d-3f166464b8a9

## <a name="nullclaim"></a>NullClaim

Tisztítsa meg egy adott jogcím értékét.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| kimeneti jogcím | claim_to_null | sztring | A jogcím értéke null. |

A jogcím-átalakítást használatával távolítsa el a jogcímek tulajdonságcsomagjait szükségtelen adatokat. A munkamenet-cookie-t, így kisebb lesz. A következő példa eltávolítja az értékét a `TermsOfService` jogcím típusa.

```XML
<ClaimsTransformation Id="SetTOSToNull" TransformationMethod="NullClaim">
  <OutputClaims>
  <OutputClaim ClaimTypeReferenceId="TermsOfService" TransformationClaimType="claim_to_null" />
  </OutputClaims>
</ClaimsTransformation>
```

- A bemeneti jogcímek:
    - **kimeneti jogcím**: üdvözli a Contoso-alkalmazást. Ha továbbra is megkeresheti, és használja ezt a webhelyet, elfogadja megfelelnek, és a következő feltételek kötelezőek...
- Kimeneti jogcímek:
    - **kimeneti jogcím**: NULL

## <a name="parsedomain"></a>ParseDomain

Lekérdezi egy e-mail-cím tartományt jelölő része.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| Bemeneti jogcím | E-mail cím | sztring | A ClaimType, amely tartalmazza az e-mail-cím. |
| kimeneti jogcím | tartomány | sztring | Az a jogcím-átalakítás után előállított takar meghívása – a tartományhoz. |

Használja a jogcím-átalakítás elemzése után a tartomány nevét a @ karaktert a felhasználó. Ez a személyes azonosításra alkalmas adatokat (PII) eltávolítása a naplózási adatok hasznos lehet. A következő jogcímek átalakítását bemutatja, hogyan elemezhető a tartomány nevét egy **e-mail** jogcím.

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

- A bemeneti jogcímek:
    - **e-mail cím**: joe@outlook.com
- Kimeneti jogcímek:
    - **tartomány**: Outlook.com-os

## <a name="setclaimsifstringsareequal"></a>SetClaimsIfStringsAreEqual

Ellenőrzi, hogy egy karakterlánc jogcím és `matchTo` bemeneti paraméter egyenlő, és beállítja a kimeneti jogcímek található értékkel `stringMatchMsg` és `stringMatchMsgCode` bemeneti paraméterek, együtt kell beállítani: hasonlítsa össze eredmény kimeneti jogcímek `true` vagy `false` összehasonlítás eredménye alapján.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| Bemeneti jogcím | Bemeneti jogcím | sztring | A jogcím típusa, amelyet össze kell hasonlítani. |
| InputParameter | matchTo | sztring | Az összehasonlítandó karakterlánc `inputClaim`. |
| InputParameter | stringComparison | sztring | A lehetséges értékek: `Ordinal` vagy `OrdinalIgnoreCase`. |
| InputParameter | stringMatchMsg | sztring | Első érték kell beállítani, ha karakterláncok egyenlő. |
| InputParameter | stringMatchMsgCode | sztring | Második értéket kell beállítani, ha karakterláncok egyenlő. |
| kimeneti jogcím | outputClaim1 | sztring | Ha karakterláncokat egyenlő, a kimeneti jogcím értékét tartalmazza `stringMatchMsg` bemeneti paraméter. |
| kimeneti jogcím | outputClaim2 | sztring | Ha karakterláncokat egyenlő, a kimeneti jogcím értékét tartalmazza `stringMatchMsgCode` bemeneti paraméter. |
| kimeneti jogcím | stringCompareResultClaim | logikai | Az összehasonlítás eredménye kimeneti jogcím típusa, kell beállítani amely `true` vagy `false` összehasonlítás eredménye alapján. |

Használhatja a jogcím-átalakítás, ellenőrizze, hogy egy jogcímet a megadott érték egyenlő. Például a következő jogcím-e átalakítási ellenőrzések Ha értékét a **termsOfUseConsentVersion** jogcím egyenlő `v1`. Ha igen, módosítsa az értéket `v2`. 

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

- A bemeneti jogcímek:
    - **bemeneti jogcím**: v1
- Bemeneti paraméterek:
    - **matchTo**: V1
    - **stringComparison**: ordinalIgnoreCase 
    - **stringMatchMsg**: B2C_V1_90005
    - **stringMatchMsgCode**: v2 verzióra frissíti a TOS
- Kimeneti jogcímek:
    - **outputClaim1**: B2C_V1_90005
    - **outputClaim2**: v2 verzióra frissíti a TOS
    - **stringCompareResultClaim**: igaz

## <a name="setclaimsifstringsmatch"></a>SetClaimsIfStringsMatch

Ellenőrzi, hogy egy karakterlánc jogcím és `matchTo` bemeneti paraméter egyenlő, és beállítja a kimeneti jogcímek található értékkel `outputClaimIfMatched` bemeneti paramétert, együtt kell beállítani: hasonlítsa össze eredmény kimeneti jogcímek `true` vagy `false` alapján a összehasonlítás eredménye.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| Bemeneti jogcím | claimToMatch | sztring | A jogcím típusa, amelyet össze kell hasonlítani. |
| InputParameter | matchTo | sztring | A bemeneti jogcím értékével összehasonlítandó karakterlánc. |
| InputParameter | stringComparison | sztring | A lehetséges értékek: `Ordinal` vagy `OrdinalIgnoreCase`. |
| InputParameter | outputClaimIfMatched | sztring | Az értéket kell beállítani, ha karakterláncok egyenlő. |
| kimeneti jogcím | kimeneti jogcím | sztring | Ha karakterláncokat egyenlő, a kimeneti jogcím értékét tartalmazza `outputClaimIfMatched` bemeneti paraméter. Vagy null, ha a karakterlánc nem egyezik. |
| kimeneti jogcím | stringCompareResultClaim | logikai | Az összehasonlítás eredménye kimeneti jogcím típusa, kell beállítani amely `true` vagy `false` összehasonlítás eredménye alapján. |

Például a következő jogcím-e átalakítási ellenőrzések Ha értékét **korcsoport** jogcím egyenlő `Minor`. Ha igen, lépjen vissza az értéket `B2C_V1_90001`. 

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

- A bemeneti jogcímek:
    - **claimToMatch**: kisebb
- Bemeneti paraméterek:
    - **matchTo**: kisebb
    - **stringComparison**: ordinalIgnoreCase 
    - **outputClaimIfMatched**: B2C_V1_90001
- Kimeneti jogcímek:
    - **isMinorResponseCode**: B2C_V1_90001
    - **isMinor**: igaz

