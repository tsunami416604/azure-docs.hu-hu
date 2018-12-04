---
title: Jelszó erőssége az egyéni házirendek az Azure Active Directory B2C |} A Microsoft Docs
description: Hogyan kell konfigurálni a bonyolultsági feltételeknek, a jelszót az egyéni házirend.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/16/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: c6b8312a08d1d92bccf70e7d3dda5f01811b4f87
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52848527"
---
# <a name="configure-password-complexity-in-custom-policies"></a>Jelszó bonyolultsága egyéni szabályzatok konfigurálása

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ez a cikk egy speciális leírása, hogyan működik a jelszó bonyolultságát, és engedélyezve van az Azure AD B2C-vel egyéni szabályzatok használatával.

## <a name="azure-ad-b2c-configure-complexity-requirements-for-passwords"></a>Az Azure AD B2C: Konfigurálja a bonyolultsági feltételeknek, a jelszót

Az Azure Active Directory B2C (Azure AD B2C-vel) támogatja a bonyolultsági feltételeknek-fiók létrehozása során a felhasználó által megadott jelszavak módosítása.  Alapértelmezés szerint az Azure AD B2C-t használja **erős** jelszavakat.  Az Azure AD B2C-t is támogatja a konfigurációs beállítások vezérléséhez, amellyel az ügyfelek jelszavak bonyolultságát.  Ez a cikk ismerteti az egyéni házirendek jelszóösszetettség konfigurálása.  Akkor is lehet használni [állítsa a jelszó erősségét a beépített szabályzatok](active-directory-b2c-reference-password-complexity.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD B2C-bérlő egy helyi fiók regisztrálási-regisztrálási vagy bejelentkezési, végrehajtásához leírtak szerint konfigurálva [bevezetés](active-directory-b2c-get-started-custom.md).

## <a name="how-to-configure-password-complexity-in-custom-policy"></a>Jelszó bonyolultsága konfigurálása egyéni házirendek

Jelszó bonyolultsága egyéni házirendek konfigurálásához tartalmaznia kell az egyéni házirend általános szerkezete a `ClaimsSchema`, `Predicates`, és `InputValidations` elem belül `BuildingBlocks`.

```XML
  <BuildingBlocks>
    <ClaimsSchema>...</ClaimsSchema>
    <Predicates>...</Predicates>
    <InputValidations>...</InputValidations>
  </BuildingBlocks>
```

Ezek az elemek célja a következőképpen:

- Minden egyes `Predicate` elem definiálja, amely igaz vagy hamis értéket ad vissza alapszintű karakterlánc érvényességének ellenőrzése.
- A `InputValidations` elemnek legalább egy `InputValidation` elemeket.  Minden egyes `InputValidation` sorozatának használatával összeállított `Predicate` elemeket. Ez az elem lehetővé teszi, hogy hajtsa végre a logikai összesítések (hasonló `and` és `or`).
- A `ClaimsSchema` határozza meg, melyik jogcím van kell kiértékelni.  Majd definiálja, amely `InputValidation` szabály az igény ellenőrzésére szolgál.

### <a name="defining-a-predicate-element"></a>A predikátum elem meghatározása

Predikátumok kétféle módszer van: IsLengthRange vagy MatchesRegex. Tekintsük át az egyes példaként.  Először van MatchesRegex, amelyet egy adott reguláris kifejezésnek egyeznie egy példát.  Ebben a példában megfelelő számokat tartalmazó karakterlánc.

```XML
      <Predicate Id="PIN" Method="MatchesRegex" HelpText="The password must be a pin.">
        <Parameters>
          <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
        </Parameters>
      </Predicate>
```

Tovább vizsgáljuk meg IsLengthRange példát.  Ez a módszer egy minimális és maximális karakterlánchossz vesz igénybe.

```XML
      <Predicate Id="Length" Method="IsLengthRange" HelpText="The password must be between 8 and 16 characters.">
        <Parameters>
          <Parameter Id="Minimum">8</Parameter>
          <Parameter Id="Maximum">16</Parameter>
        </Parameters>
      </Predicate>
```

Használja a `HelpText` attribútum biztosít a végfelhasználók számára egy hibaüzenet, ha az ellenőrzés sikertelen.  Ez a karakterlánc honosítható használatával a [nyelvi testreszabási funkcióról](active-directory-b2c-reference-language-customization.md).

### <a name="defining-an-inputvalidation-element"></a>InputValidation elem meghatározása

Egy `InputValidation` összessége `PredicateReferences`. Minden egyes `PredicateReferences` ahhoz, hogy igaznak kell lennie a `InputValidation` sikeres.  Azonban belül a `PredicateReferences` attribútum nevű elem használata `MatchAtLeast` adja meg, hogy hány `PredicateReference` ellenőrzések igaz értéket ad vissza kell.  Szükség esetén adja meg egy `HelpText` meghatározott attribútum felülbírálhatja a hibaüzenet a `Predicate` általa hivatkozott elemeket.

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

A jogcímtípusok `newPassword` és `reenterPassword` számítanak speciális, így ne módosítsa a nevét.  A felhasználói felület érvényesíti a felhasználó megfelelően ezek alapján fiók létrehozása során a jelszó reentered `ClaimType` elemeket.  Található azonos `ClaimType` elemeket, tekintse meg a TrustFrameworkBase.xml az alapszintű csomagban.  Azt határozza meg ezeknek az elemeknek mérvadóak újdonságok ebben a példában a rendszer egy `InputValidationReference`. A `ID` attribútum ezen új elem arra mutat-e a `InputValidation` elemben meghatározott.

```XML
    <ClaimsSchema>
      <ClaimType Id="newPassword">
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
      <ClaimType Id="reenterPassword">
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
    </ClaimsSchema>
```

### <a name="putting-it-all-together"></a>Végső összeállítás

Ez a példa bemutatja, hogyan minden a helyére illeszkednek egymáshoz az űrlap egy működő házirend.  Ebben a példában használata:

1. Kövesse az előfeltételt a [bevezetés](active-directory-b2c-get-started-custom.md) letöltéséhez, konfigurálása, és töltse fel a TrustFrameworkBase.xml és TrustFrameworkExtensions.xml
1. Hozzon létre egy SignUporSignIn.xml fájlt a példa tartalom ebben a szakaszban.
1. Frissítse a SignUporSignIn.xml cseréje `yourtenant` együtt az Azure AD B2C bérlő neve.
1. Utolsó töltse fel a SignUporSignIn.xml házirendfájl.

Ebben a példában egy PIN-kód jelszavak érvényesítése és a egy erős jelszót a tartalmazza:

- Keressen `PINpassword`. Ez `InputValidation` elem érvényesíti a PIN-kód hossza.  Nincs használatban a időpontban, mert nem hivatkozik rá a `InputValidationReference` elem belül `ClaimType`. 
- Keressen `PasswordValidation`. Ez `InputValidation` elem érvényesíti a jelszó 8 – 16 karakterből, és tartalmazza a szám, nagybetű, kisbetű, 4, 3 vagy szimbólumokat.  Hivatkozik rá `ClaimType`.  Ezért ez a szabály van a házirend érvényben.

```XML
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy
  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="https://www.w3.org/2001/XMLSchema"
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
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
      <ClaimType Id="reenterPassword">
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
    </ClaimsSchema>
    <Predicates>
      <Predicate Id="Lowercase" Method="MatchesRegex" HelpText="a lowercase">
        <Parameters>
          <Parameter Id="RegularExpression">[a-z]+</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Uppercase" Method="MatchesRegex" HelpText="an uppercase">
        <Parameters>
          <Parameter Id="RegularExpression">[A-Z]+</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Number" Method="MatchesRegex" HelpText="a number">
        <Parameters>
          <Parameter Id="RegularExpression">[0-9]+</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Symbol" Method="MatchesRegex" HelpText="a symbol">
        <Parameters>
          <Parameter Id="RegularExpression">[!@#$%^*()]+</Parameter>
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
