---
title: Egyszeri bejelentkezés és jogkivonat-Testreszabás kezelése egyéni szabályzatok használatával
titleSuffix: Azure AD B2C
description: Ismerje meg, hogyan kezelheti az egyszeri bejelentkezést és a tokenek testreszabását a Azure Active Directory B2C egyéni házirendjeivel.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 0b460d5a3c5535b74e349fa46c6a2ad55fc3a8d8
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82966570"
---
# <a name="manage-sso-and-token-customization-using-custom-policies-in-azure-active-directory-b2c"></a>Az egyszeri bejelentkezés és a jogkivonatok testreszabásának kezelése egyéni házirendek használatával Azure Active Directory B2C

Ez a cikk azt ismerteti, hogyan kezelheti a tokeneket, a munkameneteket és az egyszeri bejelentkezési (SSO) konfigurációkat a Azure Active Directory B2C (Azure AD B2C) [Egyéni házirendjeivel](custom-policy-overview.md) .

## <a name="jtw-token-lifetimes-and-claims-configuration"></a>JTW-jogkivonat élettartamai és jogcímek konfigurálása

Ha módosítani szeretné a jogkivonat-élettartamok beállításait, vegyen fel egy [ClaimsProviders](claimsproviders.md) elemet a befolyásolni kívánt szabályzat függő entitásában található fájlban.  A **ClaimsProviders** elem a [TrustFrameworkPolicy](trustframeworkpolicy.md) elem gyermeke.

Szúrja be a ClaimsProviders elemet a BasePolicy elem és a függő entitás fájljának RelyingParty eleme között.

A-ben be kell állítania a token élettartamait érintő adatokat. Az XML a következő példához hasonlít:

```XML
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
- **Frissítési jogkivonat ablakának élettartama** – ha a frissítési tokenhez be szeretné állítani a csúszó ablak élettartamát, állítsa be **rolling_refresh_token_lifetime_secs** metaadat-elem értékét. Az alapértelmezett érték 7776000 (90 nap). Ha nem szeretné kikényszeríteni a csúszó ablak élettartamát, cserélje le az `<Item Key="allow_infinite_rolling_refresh_token">True</Item>`elemeket a következőre:.
- **Kiállítói (ISS) jogcím** – a kibocsátó (ISS) jogcím a **IssuanceClaimPattern** metaadat-elemmel van beállítva. A megfelelő értékek: `AuthorityAndTenantGuid` és `AuthorityWithTfp`.
- **Házirend-azonosítót jelölő jogcím beállítása** – az érték beállításának beállításai `TFP` (megbízhatósági keretrendszer-házirend) `ACR` és (hitelesítési környezetre vonatkozó hivatkozás). `TFP`az ajánlott érték. Állítsa be a **AuthenticationContextReferenceClaimPattern** értéket a `None`értékkel.

    A **ClaimsSchema** elemben adja hozzá a következő elemet:

    ```XML
    <ClaimType Id="trustFrameworkPolicy">
      <DisplayName>Trust framework policy name</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    ```

    A **OutputClaims** elemben adja hozzá a következő elemet:

    ```XML
    <OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />
    ```

    Az ACR esetében távolítsa el a **AuthenticationContextReferenceClaimPattern** -tételt.

- **Tulajdonos (Sub) jogcím** – ez a beállítás alapértelmezés szerint a ObjectId, ha ezt a `Not Supported`beállítást szeretné váltani, cserélje le a következő sort:

    ```XML
    <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
    ```

    Ezzel a sorral:

    ```XML
    <OutputClaim ClaimTypeReferenceId="sub" />
    ```

## <a name="next-steps"></a>További lépések

- További információ a [Azure ad B2C-munkamenetről](session-overview.md).
- Megtudhatja, hogyan [konfigurálhatja a munkamenet viselkedését az egyéni házirendekben](session-behavior-custom-policy.md).
- Hivatkozás: [JwtIssuer](jwt-issuer-technical-profile.md).