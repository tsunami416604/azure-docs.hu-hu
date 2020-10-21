---
title: Egyszeri bejelentkezés és jogkivonat-Testreszabás kezelése egyéni szabályzatok használatával
titleSuffix: Azure AD B2C
description: Ismerje meg, hogyan kezelheti az egyszeri bejelentkezést és a tokenek testreszabását a Azure Active Directory B2C egyéni házirendjeivel.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/21/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e72bd04bb41537546191b8ceb320c0722bd10146
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/21/2020
ms.locfileid: "92340291"
---
# <a name="manage-sso-and-token-customization-using-custom-policies-in-azure-active-directory-b2c"></a>Az egyszeri bejelentkezés és a jogkivonatok testreszabásának kezelése egyéni házirendek használatával Azure Active Directory B2C

Ez a cikk azt ismerteti, hogyan kezelheti a tokeneket, a munkameneteket és az egyszeri bejelentkezési (SSO) konfigurációkat a Azure Active Directory B2C (Azure AD B2C) [Egyéni házirendjeivel](custom-policy-overview.md) .

## <a name="jwt-token-lifetimes-and-claims-configuration"></a>JWT-jogkivonat élettartamai és jogcímek konfigurálása

Ha módosítani szeretné a jogkivonat-élettartamok beállításait, vegyen fel egy [ClaimsProviders](claimsproviders.md) elemet a befolyásolni kívánt szabályzat függő entitásában található fájlban.  A **ClaimsProviders** elem a [TrustFrameworkPolicy](trustframeworkpolicy.md) elem gyermeke.

Szúrja be a ClaimsProviders elemet a BasePolicy elem és a függő entitás fájljának RelyingParty eleme között.

A-ben be kell állítania a token élettartamait érintő adatokat. Az XML a következő példához hasonlít:

```xml
<ClaimsProviders>
  <ClaimsProvider>
    <DisplayName>Token Issuer</DisplayName>
    <TechnicalProfiles>
      <TechnicalProfile Id="JwtIssuer">
        <Metadata>
          <Item Key="token_lifetime_secs">3600</Item>
          <Item Key="id_token_lifetime_secs">3600</Item>
          <Item Key="refresh_token_lifetime_secs">1209600</Item>
          <Item Key="rolling_refresh_token_lifetime_secs">7776000</Item>
          <Item Key="IssuanceClaimPattern">AuthorityAndTenantGuid</Item>
          <Item Key="AuthenticationContextReferenceClaimPattern">None</Item>
        </Metadata>
      </TechnicalProfile>
    </TechnicalProfiles>
  </ClaimsProvider>
</ClaimsProviders>
```

Az előző példában az alábbi értékek vannak megadva:

- **Hozzáférési jogkivonat élettartamai** – a hozzáférési jogkivonat élettartamának értéke **token_lifetime_secs** metaadat-elemmel van beállítva. Az alapértelmezett érték 3600 másodperc (60 perc).
- **Azonosító jogkivonat élettartama** – az azonosító jogkivonat élettartamának értéke a **id_token_lifetime_secs** metaadat-elemmel van beállítva. Az alapértelmezett érték 3600 másodperc (60 perc).
- **Frissítési jogkivonat élettartama** – a frissítési jogkivonat élettartamának értéke a **refresh_token_lifetime_secs** metaadat-elemmel van beállítva. Az alapértelmezett érték 1209600 másodperc (14 nap).
- **Frissítési jogkivonat ablakának élettartama** – ha a frissítési tokenhez be szeretné állítani a csúszó ablak élettartamát, állítsa be **rolling_refresh_token_lifetime_secs** metaadat-elem értékét. Az alapértelmezett érték 7776000 (90 nap). Ha nem szeretné kikényszeríteni a csúszó ablak élettartamát, cserélje le az elemeket a következőre: `<Item Key="allow_infinite_rolling_refresh_token">True</Item>` .
- **Kiállítói (ISS) jogcím** – a kibocsátó (ISS) jogcím a **IssuanceClaimPattern** metaadat-elemmel van beállítva. A megfelelő értékek: `AuthorityAndTenantGuid` és `AuthorityWithTfp` .
- **Házirend-azonosítót jelölő jogcím beállítása** – az érték beállításának beállításai `TFP` (megbízhatósági keretrendszer-házirend) és `ACR` (hitelesítési környezetre vonatkozó hivatkozás). `TFP` az ajánlott érték. Állítsa be a **AuthenticationContextReferenceClaimPattern** értéket a értékkel `None` .

    A **ClaimsSchema** elemben adja hozzá a következő elemet:

    ```xml
    <ClaimType Id="trustFrameworkPolicy">
      <DisplayName>Trust framework policy name</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    ```

    A **OutputClaims** elemben adja hozzá a következő elemet:

    ```xml
    <OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />
    ```

    Az ACR esetében távolítsa el a **AuthenticationContextReferenceClaimPattern** -tételt.

- **Tulajdonos (Sub) jogcím** – ez a beállítás alapértelmezés szerint a ObjectId, ha ezt a beállítást szeretné váltani `Not Supported` , cserélje le a következő sort:

    ```xml
    <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
    ```

    Ezzel a sorral:

    ```xml
    <OutputClaim ClaimTypeReferenceId="sub" />
    ```

> [!NOTE]
> Az engedélyezési programkódot használó egylapos alkalmazások esetében a PKCE mindig 24 órás frissítési jogkivonat élettartama szükséges. [További információ a frissítési tokenek biztonsági vonatkozásairól a böngészőben](../active-directory/develop/reference-third-party-cookies-spas.md#security-implications-of-refresh-tokens-in-the-browser).

## <a name="provide-optional-claims-to-your-app"></a>Opcionális jogcímek megadása az alkalmazás számára

A [függő entitásra vonatkozó házirend technikai profiljának](relyingparty.md#technicalprofile) kimeneti jogcímei az alkalmazásnak visszaadott értékek. A kimeneti jogcímek hozzáadásával a jogcímeket a rendszer sikeres felhasználói út után kiállítja a jogkivonatba, és a rendszer elküldi az alkalmazásnak. Módosítsa a technikai profil elemet a függő entitás szakaszban a kívánt jogcímek kimeneti jogcímként való hozzáadásához.

1. Nyissa meg az egyéni házirend-fájlt. Például SignUpOrSignin.xml.
1. Keresse meg a OutputClaims elemet. Adja hozzá a tokenbe felvenni kívánt OutputClaim. 
1. Állítsa be a kimeneti jogcímek attribútumait. 

A következő példa hozzáadja a `accountBalance` jogcímet. Az accountBalance-jogcímet egyensúlyt kell elküldeni az alkalmazásnak. 

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surname" />
      <OutputClaim ClaimTypeReferenceId="email" />
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="identityProvider" />
      <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      <!--Add the optional claims here-->
      <OutputClaim ClaimTypeReferenceId="accountBalance" DefaultValue="" PartnerClaimType="balance" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

A OutputClaim elem a következő attribútumokat tartalmazza:

  - **ClaimTypeReferenceId** – egy jogcím típusának azonosítója már definiálva van a [ClaimsSchema](claimsschema.md) szakaszban a házirend fájl vagy a szülő házirend fájljában.
  - **PartnerClaimType** – lehetővé teszi a jogcím nevének módosítását a jogkivonatban. 
  - **DefaultValue** – alapértelmezett érték. Beállíthatja az alapértelmezett értéket is egy jogcím- [feloldóhoz](claim-resolver-overview.md), például a bérlő azonosítóját.
  - **AlwaysUseDefaultValue** – az alapértelmezett érték használatának kényszerítése.

## <a name="next-steps"></a>Következő lépések

- További információ a [Azure ad B2C-munkamenetről](session-overview.md).
- Megtudhatja, hogyan [konfigurálhatja a munkamenet viselkedését az egyéni házirendekben](session-behavior-custom-policy.md).
- Hivatkozás: [JwtIssuer](jwt-issuer-technical-profile.md).
