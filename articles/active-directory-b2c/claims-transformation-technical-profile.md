---
title: Jogcímátalakítási technikai profil definiálása
titleSuffix: Azure AD B2C
description: Az Azure Active Directory B2C-ben egy egyéni szabályzatban definiáljon jogcímátalakítási technikai profilt.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/13/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 84c1cf798e88e4067da8a495c1591143d2ee1bd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189786"
---
# <a name="define-a-claims-transformation-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Jogcímátalakítási technikai profil definiálása az Azure Active Directory B2C egyéni szabályzatában

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A jogcím-átalakítási technikai profil lehetővé teszi, hogy a kimeneti jogcímek átalakítása a jogcímértékek kezeléséhez, jogcímek érvényesítéséhez vagy a kimeneti jogcímek készletalapértelmezett értékeinek beállításához.

## <a name="protocol"></a>Protocol (Protokoll)

A **Protokoll** elem **Name** attribútumát a `Proprietary`parancsra kell állítani. A **kezelő** attribútumnak tartalmaznia kell az Azure AD B2C által használt `Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`protokollkezelő szerelvény teljesen minősített nevét: .

A következő példa egy jogcímátalakítási technikai profilt mutat be:

```XML
<TechnicalProfile Id="Facebook-OAUTH-UnLink">
    <DisplayName>Unlink Facebook</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="output-claims"></a>Kimeneti jogcímek

A **OutputClaims** elem kötelező. Legalább egy, a technikai profil által visszaadott kimeneti jogcímet meg kell adnia. A következő példa bemutatja, hogyan állíthat be alapértelmezett értékeket a kimeneti jogcímekben:

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="ageGroup" DefaultValue="Undefined" />
  <OutputClaim ClaimTypeReferenceId="ageGroupValueChanged" DefaultValue="false" />
</OutputClaims>
```

## <a name="output-claims-transformations"></a>Kimeneti jogcímek átalakítása

A **OutputClaimsTransformations** elem tartalmazhat **OutputClaimsTransformations** elemek gyűjteményét, amelyek a jogcímek módosítására vagy újak létrehozására szolgálnak. A következő technikai profil meghívja az **RemoveAlternativeSecurityIdByIdentityProvider** jogcímek átalakítását. Ez a követelések átalakítása eltávolítja a társadalmi azonosítót a gyűjtemény **ből AlternativeSecurityIds**. A technikai profil kimeneti jogcímek: **identityProvider2**, amely be van állítva `facebook.com`, és **AlternativeSecurityIds**, amely tartalmazza a felhasználóhoz társított közösségi identitások listáját, miután facebook.com identitás eltávolítása.

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

A jogcímek átalakítása technikai profil lehetővé teszi, hogy bármely felhasználói út vezénylési lépésjogátalakítása hajtson végre jogcímátalakítást. A következő példában a vezénylési lépés meghívja az egyik leválasztás technikai profilok, például **UnLink-Facebook-OAUTH**. Ez a technikai profil meghívja a jogcímek átalakítása technikai profil **RemoveAlternativeSecurityIdByIdentityProvider**, amely létrehoz egy új **AlternativeSecurityIds2** jogcím, amely tartalmazza a felhasználói közösségi identitások listáját, miközben eltávolítja a Facebook identitást a gyűjtemények.

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

## <a name="metadata"></a>Metaadatok

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| IncludeClaimResolvingInClaimsHandling  | Nem | Bemeneti és kimeneti jogcímek esetén megadja, hogy a [jogcímek feloldása](claim-resolver-overview.md) szerepel-e a technikai profilban. Lehetséges értékek: `true` `false`  vagy (alapértelmezett). Ha a technikai profilban jogcímfeloldót szeretne `true`használni, állítsa ezt a beállításra. |

## <a name="use-a-validation-technical-profile"></a>Érvényesítési technikai profil használata

A jogcímátalakítási technikai profil az információk érvényesítéséhez használható. A következő példában a **LocalAccountSignUpWithLogonEmail** nevű [saját érvényesítési technikai profil](self-asserted-technical-profile.md) kétszer kéri meg a felhasználót, hogy adja meg az e-mailt, majd felhívja az [érvényesítési technikai profilt](validation-technical-profile.md) Validate-Email néven az **e-mailek** érvényesítéséhez. A **Validate-Email** technikai profil felhívja a jogcímek átalakítása **AssertEmailAreEqual** összehasonlítani a két jogcím **e-mail** és **emailRepeat**, és dobjon kivételt, ha azok nem egyenlőek szerint a megadott összehasonlítást.

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

A jogcímek átalakítása technikai profil meghívja az **AssertEmailAreEqual** jogcímek átalakítását, amely azt állítja, hogy a felhasználó által biztosított e-mailek azonosak.

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

Egy saját érvényesítésű technikai profil meghívhatja az érvényesítési technikai profilt, és megjelenítheti a **UserMessageIfClaimsTransformationStringsAreNotEqual** metaadatokban megadott hibaüzenetet.

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
