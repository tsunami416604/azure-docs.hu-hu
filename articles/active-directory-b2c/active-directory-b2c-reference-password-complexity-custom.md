---
title: "Egyéni házirendek – az Azure AD B2C a jelszó erősségét |} Microsoft Docs"
description: "Bonyolult jelszót egyéni házirend konfigurálása"
services: active-directory-b2c
documentationcenter: 
author: saeedakhter-msft
manager: mtillman
editor: parakhj
ms.assetid: 53ef86c4-1586-45dc-9952-dbbd62f68afc
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2017
ms.author: saeeda
ms.openlocfilehash: eb187a120399089f1c3c145a06fbe993f50fb92b
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="configure-password-complexity-in-custom-policies"></a>Állítsa be a jelszó erősségét az egyéni házirendek

> [!NOTE]
> **A funkció jelenleg előzetes verzió.**  Ügyfél [ AADB2CPreview@microsoft.com ](mailto:AADB2CPreview@microsoft.com) kell rendelkeznie a tesztbérlővel, ez a szolgáltatás engedélyezve van.  Csak akkor teszteljen Ez a termelési bérlők.

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ez a cikk egy speciális leírása a jelszó erősségét működésével, és engedélyezve van az Azure AD B2C egyéni házirendekkel.

## <a name="azure-ad-b2c-configure-complexity-requirements-for-passwords"></a>Az Azure AD B2C: Bonyolult jelszót konfigurálása

Az Azure Active Directory B2C (az Azure AD B2C) támogatja a bonyolultsági feltételeknek, egy fiók létrehozásakor a felhasználó által megadott jelszavak módosítása.  Alapértelmezés szerint az Azure AD B2C használja **erős** jelszavakat.  Az Azure AD B2C beállítások szabályozzák, hogy az ügyfelek használhatják a összetettségét is támogatja.  Ez a cikk beszél konfigurálása a jelszó erősségét a egyéni házirendekkel.  Az is lehetséges a [állítsa be a jelszó erősségét a beépített házirendek](active-directory-b2c-reference-password-complexity.md).

## <a name="prerequisites"></a>Előfeltételek

Egy helyi fiókot sign-Close-Up/sign-in befejezéséhez, a konfigurált Azure AD B2C-bérlő [bevezetés](active-directory-b2c-get-started-custom.md).

## <a name="how-to-configure-password-complexity-in-custom-policy"></a>Jelszó erőssége egyéni házirend konfigurálása

Állítsa be a jelszó erősségét egyéni házirendek, az egyéni házirend struktúrájának tartalmaznia kell egy `ClaimsSchema`, `Predicates`, és `InputValidations` elemének `BuildingBlocks`.

```XML
  <BuildingBlocks>
    <ClaimsSchema>...</ClaimsSchema>
    <Predicates>...</Predicates>
    <InputValidations>...</InputValidations>
  </BuildingBlocks>
```

Ezek az elemek célja a következőképpen:

- Minden egyes `Predicate` elem definiálja, amely igaz vagy hamis értéket ad vissza alapvető karakterlánc érvényességének ellenőrzése.
- A `InputValidations` elemnek legalább egy `InputValidation` elemek.  Minden egyes `InputValidation` sorozatának használatával összeállított `Predicate` elemek. Ez az elem lehetővé teszi logikai összesítések (hasonló `and` és `or`).
- A `ClaimsSchema` határozza meg, milyen jogcímek érvényesítésre kerül.  Majd definiálja, amely `InputValidation` szabály, hogy a jogcím ellenőrzésére szolgál.

### <a name="defining-a-predicate-element"></a>A predikátum elem meghatározása

Predikátumok kétféle módszer van: IsLengthRange vagy MatchesRegex. Tekintsük át, mindegyik egy példát.  Először azt kell MatchesRegex, amely egy reguláris kifejezésnek a feltétel teljesüléséhez példát.  Ebben a példában a megfelelő számokat tartalmazó karakterlánc.

```XML
      <Predicate Id="PIN" Method="MatchesRegex" HelpText="The password must be a pin.">
        <Parameters>
          <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
        </Parameters>
      </Predicate>
```

Következő tekintsük át, IsLengthRange példát.  Ez a módszer egy minimális és maximális hossza vesz igénybe.

```XML
      <Predicate Id="Length" Method="IsLengthRange" HelpText="The password must be between 8 and 16 characters.">
        <Parameters>
          <Parameter Id="Minimum">8</Parameter>
          <Parameter Id="Maximum">16</Parameter>
        </Parameters>
      </Predicate>
```

Használja a `HelpText` attribútumot a végfelhasználók számára megjelenik egy hibaüzenet, ha az ellenőrzés sikertelen.  Ez a karakterlánc honosítható használatával a [nyelvi testreszabási szolgáltatás](active-directory-b2c-reference-language-customization.md).

### <a name="defining-an-inputvalidation-element"></a>Egy InputValidation elem meghatározása

Egy `InputValidation` összessége `PredicateReferences`. Minden egyes `PredicateReferences` kell teljesülnie ahhoz, hogy a `InputValidation` sikeres.  Azonban belül a `PredicateReferences` attribútum nevű elem használatát `MatchAtLeast` adja meg, hogy hány `PredicateReference` ellenőrzések igaz értéket kell visszaadjon.  Szükség esetén adja meg a `HelpText` definiált bírálja felül a hibaüzenet a következő attribútumot az `Predicate` általa hivatkozott elemeket.

```XML
      <InputValidation Id="PasswordValidation">
        <PredicateReferences Id="LengthGroup" MatchAtLeast="1">
          <PredicateReference Id="Length" />
        </PredicateReferences>
        <PredicateReferences Id="3of4" MatchAtLeast="3" HelpText="You must have at least 3 of the following character classes:">
          <PredicateReference Id="Lowercase" />
          <PredicateReference Id="Uppercase" />
          <PredicateReference Id="Number" />
          <PredicateReference Id="Symbol" />
        </PredicateReferences>
      </InputValidation>
```

### <a name="defining-a-claimsschema-element"></a>Egy ClaimsSchema elem meghatározása

A jogcímtípusok `newPassword` és `reenterPassword` minősülnek különleges, ezért nem módosítható a neve.  A felhasználói felület ellenőrzi a felhasználó helytelenül reentered ezek alapján számítógépfiók létrehozása közben a jelszavát `ClaimType` elemek.  Azonos található `ClaimType` elemek, tekintse meg az alapszintű csomag TrustFrameworkBase.xml.  Újdonságok ebben a példában a rendszer, hogy a Microsoft ezeket az elemeket meghatározásához mérvadóak egy `InputValidationReference`. A `ID` az új elem attribútuma mutat a `InputValidation` elem, amely meghatározott.

```XML
    <ClaimsSchema>
      <ClaimType Id="newPassword">
        <Restriction>
          <Pattern RegularExpression="^.*$" HelpText="" />
        </Restriction>
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
      <ClaimType Id="reenterPassword">
        <Restriction>
          <Pattern RegularExpression="^.*$" HelpText="" />
        </Restriction>
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
    </ClaimsSchema>
```

### <a name="putting-it-all-together"></a>A teljes kép

A példa bemutatja, hogyan minden a illeszkednek egymáshoz az egyes működő házirend létrehozásához.  Ez a példa használata:

1. Az előfeltétel utasításait [bevezetés](active-directory-b2c-get-started-custom.md) letöltéséhez, konfigurálása, és töltse fel TrustFrameworkBase.xml és TrustFrameworkExtensions.xml
1. Hozzon létre egy SignUporSignIn.xml fájlt, például tartalom ebben a szakaszban.
1. Frissítse a SignUporSignIn.xml cseréje `yourtenant` együtt az Azure AD B2C bérlő neve.
1. Töltse fel a SignUporSignIn.xml házirendfájl utolsó.

Ez a példa egy PIN-kód jelszavak ellenőrzése és egy erős jelszót tartalmazza:

- Keressen `PINpassword`. Ez `InputValidation` elem érvényesíti a PIN-kód hossza.  Nem használható időpontjában, mert az nem hivatkozik a `InputValidationReference` elemének `ClaimType`. 
- Keressen `PasswordValidation`. Ez `InputValidation` elem érvényesíti a jelszó 8 – 16 karakterből, és tartalmaz számokat, kis- és nagybetűk, 4, 3 vagy szimbólum.  A hivatkozott `ClaimType`.  Ezért ez a szabály a házirend van érvényben.

```XML
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="http://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="yourtenant.onmicrosoft.com"
  PolicyId="B2C_1A_signup_signin"
  PublicPolicyUri="http://yourtenant.onmicrosoft.com/B2C_1A_signup_signin">
 <BasePolicy>
    <TenantId>yourtenant.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>
  <BuildingBlocks>
    <ClaimsSchema>
      <ClaimType Id="newPassword">
        <Restriction>
          <Pattern RegularExpression="^.*$" HelpText="" />
        </Restriction>
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
      <ClaimType Id="reenterPassword">
        <Restriction>
          <Pattern RegularExpression="^.*$" HelpText="" />
        </Restriction>
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
    </ClaimsSchema>
    <Predicates>
      <Predicate Id="Lowercase" Method="MatchesRegex" HelpText="a lowercase">
        <Parameters>
          <Parameter Id="RegularExpression">^[a-z]+$</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Uppercase" Method="MatchesRegex" HelpText="an uppercase">
        <Parameters>
          <Parameter Id="RegularExpression">^[A-Z]+$</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Number" Method="MatchesRegex" HelpText="a number">
        <Parameters>
          <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Symbol" Method="MatchesRegex" HelpText="a symbol">
        <Parameters>
          <Parameter Id="RegularExpression">^[!@#$%^*()]+$</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Length" Method="IsLengthRange" HelpText="The password must be between 8 and 16 characters.">
        <Parameters>
          <Parameter Id="Minimum">8</Parameter>
          <Parameter Id="Maximum">16</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="PIN" Method="MatchesRegex" HelpText="The password must be a pin.">
        <Parameters>
          <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
        </Parameters>
      </Predicate>
    </Predicates>
    <InputValidations>
      <InputValidation Id="PasswordValidation">
        <PredicateReferences Id="LengthGroup" MatchAtLeast="1">
          <PredicateReference Id="Length" />
        </PredicateReferences>
        <PredicateReferences Id="3of4" MatchAtLeast="3" HelpText="You must have at least 3 of the following character classes:">
          <PredicateReference Id="Lowercase" />
          <PredicateReference Id="Uppercase" />
          <PredicateReference Id="Number" />
          <PredicateReference Id="Symbol" />
        </PredicateReferences>
      </InputValidation>
      <InputValidation Id="PINpassword">
        <PredicateReferences Id="PINGroup">
          <PredicateReference Id="PIN" />
        </PredicateReferences>
      </InputValidation>
    </InputValidations>
  </BuildingBlocks>
  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration, DisableStrongPassword" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```
