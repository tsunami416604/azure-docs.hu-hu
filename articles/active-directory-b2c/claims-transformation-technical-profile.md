---
title: A jogcímek átalakítása technikai profil meghatározása egy egyéni házirendek az Azure Active Directory B2C |} A Microsoft Docs
description: Adja meg az Azure Active Directory B2C egyéni házirendet egy jogcím-átalakítási technikai profil.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: fd1e2aa5162ce9263d521edf3ae11e0508353b46
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2018
ms.locfileid: "44381313"
---
# <a name="define-a-claims-transformation-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>A jogcímek átalakítása technikai profil meghatározása az Azure Active Directory B2C egyéni házirendek

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

 A jogcímek átalakításáról technikai profil teszi lehetővé kimeneti jogcímek segítségével kezelheti az átalakítások hívja jogcím-értékek, ellenőrizheti a jogcímeit, vagy a kimeneti jogcímek készletének az alapértelmezett értékeket.

## <a name="protocol"></a>Protokoll

A **neve** attribútuma a **protokoll** elemet hozzá kell beállítani `Proprietary`. A **kezelő** attribútum kell tartalmaznia a teljes nevet, amely az Azure AD B2C által használt protokoll kezelő sestavení: `Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`.

Az alábbi példa bemutatja egy jogcím-átalakítási technikai profil:

```XML
<TechnicalProfile Id="Facebook-OAUTH-UnLink">
    <DisplayName>Unlink Facebook</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...    
```

## <a name="output-claims"></a>Kimeneti jogcímek

A **OutputClaims** elem megadása kötelező. Meg kell adnia legalább egy kimeneti jogcím a technikai profil által visszaadott. Az alábbi példa bemutatja, hogyan állíthatja be az alapértelmezett értékeket a kimeneti jogcímek:

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="ageGroup" DefaultValue="Undefined" />
  <OutputClaim ClaimTypeReferenceId="ageGroupValueChanged" DefaultValue="false" />
</OutputClaims>
```

## <a name="output-claims-transformations"></a>Kimeneti jogcím-átalakítás

A **OutputClaimsTransformations** elemet tartalmazhat egy gyűjteményét **OutputClaimsTransformation** elemek, amelyek segítségével módosíthatja a jogcímeket, és hozzon létre újakat. A következő technikai profil hívások a **RemoveAlternativeSecurityIdByIdentityProvider** jogcím-átalakítás. A jogcím-átalakítási távolítja el a gyűjteményből, azonosíthatja a közösségi **AlternativeSecurityIds**. A kimeneti jogcímek a technikai profil **identityProvider2**, amelynek beállítása `facebook.com`, és **AlternativeSecurityIds**, ez társított közösségi identitások listáját tartalmazza, amely felhasználói identitás Facebook.com weboldalt eltávolítása után.

```XML
<ClaimsTransformations>
  <ClaimsTransformation Id="RemoveAlternativeSecurityIdByIdentityProvider" 
TransformationMethod="RemoveAlternativeSecurityIdByIdentityProvider">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="IdentityProvider2"
TransformationClaimType="identityProvider" />
      <InputClaim ClaimTypeReferenceId="AlternativeSecurityIds" 
TransformationClaimType="collection" />
    </InputClaims>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="AlternativeSecurityIds" 
TransformationClaimType="collection" />
    </OutputClaims>
  </ClaimsTransformation>
</ClaimsTransformations>
...
<TechnicalProfile Id="Facebook-OAUTH-UnLink">
    <DisplayName>Unlink Facebook</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="identityProvider2" DefaultValue="facebook.com" AlwaysUseDefaultValue="true" />
    </OutputClaims>
    <OutputClaimsTransformations>
        <OutputClaimsTransformation ReferenceId="RemoveAlternativeSecurityIdByIdentityProvider" />
    </OutputClaimsTransformations>
    <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

A jogcím-átalakítási technikai profil lehetővé teszi bármely felhasználói interakciósorozat vezénylési lépés hajtsa végre a jogcímek átalakítását. A következő példában a vezénylési lépés meghívja az egyiket leválasztásának technikai profil, például **leválasztásának-Facebook-OAUTH**. A technikai profil meghívja a jogcímek átalakítása technikai profil **RemoveAlternativeSecurityIdByIdentityProvider**, amely létrehoz egy új **AlternativeSecurityIds2** tartalmazó jogcímet a felhasználó közösségi identitások, a Facebook-identitás eltávolítása gyűjtemények közben listája.

```XML
<UserJourney Id="AccountUnLink">
  <OrchestrationSteps>    
    ...
    <OrchestrationStep Order="8" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="UnLinkFacebookExchange" TechnicalProfileReferenceId="UnLink-Facebook-OAUTH" />
        <ClaimsExchange Id="UnLinkMicrosoftExchange" TechnicalProfileReferenceId="UnLink-Microsoft-OAUTH" />
        <ClaimsExchange Id="UnLinkGitHubExchange" TechnicalProfileReferenceId="UnLink-GitHub-OAUTH" />
      </ClaimsExchanges>
    </OrchestrationStep>
    ...
  </OrchestrationSteps>
</UserJourney>
```


## <a name="use-a-validation-technical-profile"></a>Egy ellenőrzési technikai profil használata

A jogcímek átalakítása technikai profil használható az adatok érvényesítéséhez. A következő példában a [technikai profil önellenőrzött](self-asserted-technical-profile.md) nevű **LocalAccountSignUpWithLogonEmail** megkérdezi a felhasználót, adja meg kétszer az e-mailt, majd meghívja a [műszaki érvényesítése profil](validation-technical-profile.md) nevű **ellenőrzése – E-mail** az e-mailek ellenőrzése. A **ellenőrzése – E-mail** technikai profil meghívja a jogcímek átalakításáról **AssertEmailAreEqual** összehasonlítására két jogcímeket **e-mail** és **emailRepeat** , és ha azok nem egyenlő a megadott összehasonlító megfelelően kivételt.

```XML
<ClaimsTransformations>
  <ClaimsTransformation Id="AssertEmailAreEqual" TransformationMethod="AssertStringClaimsAreEqual">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim1" />
      <InputClaim ClaimTypeReferenceId="emailRepeat" TransformationClaimType="inputClaim2" />
    </InputClaims>
    <InputParameters>
      <InputParameter Id="stringComparison" DataType="string" Value="ordinalIgnoreCase" />
    </InputParameters>
  </ClaimsTransformation>
</ClaimsTransformations>
```

A jogcímek átalakítása technikai profil meghívja a **AssertEmailAreEqual** jogcím-átalakítást, amely használjon, esetleg imperatív állításokat, hogy a felhasználó által megadott e-mailek esetében azonos.

```XML
<TechnicalProfile Id="Validate-Email">
    <DisplayName>Unlink Facebook</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="emailRepeat" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" />
  </OutputClaims>          
  <OutputClaimsTransformations>
        <OutputClaimsTransformation ReferenceId="AssertEmailAreEqual" />
    </OutputClaimsTransformations>
    <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile> 
```

Egy önálló kiszolgáló által megerősített, technikai profil hívja az érvényesítési technikai profil, és a megadott hibaüzenet megjelenítése a **UserMessageIfClaimsTransformationStringsAreNotEqual** metaadatait.

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>User ID signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    ...
    <Item Key="UserMessageIfClaimsTransformationStringsAreNotEqual">The email addresses you provided are not the same</Item>
  </Metadata>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" />
    <OutputClaim ClaimTypeReferenceId="emailRepeat" />
    ...
  </OutputClaims>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="Validate-Email" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>  
```