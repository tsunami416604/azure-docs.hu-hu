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
ms.date: 10/09/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ff9f57af92c50c0df6f628113bd9490ca83e1310
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78189293"
---
# <a name="manage-sso-and-token-customization-using-custom-policies-in-azure-active-directory-b2c"></a>Az egyszeri bejelentkezés és a jogkivonatok testreszabásának kezelése egyéni házirendek használatával Azure Active Directory B2C

Ez a cikk azt ismerteti, hogyan kezelheti a tokeneket, a munkameneteket és az egyszeri bejelentkezési (SSO) konfigurációkat a Azure Active Directory B2C (Azure AD B2C) [Egyéni házirendjeivel](custom-policy-overview.md) .

## <a name="token-lifetimes-and-claims-configuration"></a>Jogkivonat-élettartamok és jogcímek konfigurálása

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
- **Frissítési jogkivonat ablakának élettartama** – ha a frissítési tokenhez be szeretné állítani a csúszó ablak élettartamát, állítsa be **rolling_refresh_token_lifetime_secs** metaadat-elem értékét. Az alapértelmezett érték 7776000 (90 nap). Ha nem szeretné kényszeríteni a csúszó ablak élettartamának kiváltását, cserélje le az elemeket `<Item Key="allow_infinite_rolling_refresh_token">True</Item>`ra.
- **Kiállítói (ISS) jogcím** – a kibocsátó (ISS) jogcím a **IssuanceClaimPattern** metaadat-elemmel van beállítva. A megfelelő értékek: `AuthorityAndTenantGuid` és `AuthorityWithTfp`.
- **Házirend-azonosítót jelölő jogcím beállítása** – az érték beállításának beállításai a következők: `TFP` (megbízhatósági keretrendszer házirendje) és `ACR` (hitelesítési környezet referenciája). `TFP` a javasolt érték. A **AuthenticationContextReferenceClaimPattern** beállítása `None`értékével.

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

- **Tulajdonos (Sub) jogcím** – ez a beállítás alapértelmezés szerint a ObjectId, ha ezt a beállítást `Not Supported`re szeretné váltani, cserélje le a következő sort:

    ```XML
    <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
    ```

    Ezzel a sorral:

    ```XML
    <OutputClaim ClaimTypeReferenceId="sub" />
    ```

## <a name="session-behavior-and-sso"></a>Munkamenet-viselkedés és SSO

A munkamenet-viselkedés és az SSO-konfigurációk módosításához adjon hozzá egy **UserJourneyBehaviors** elemet a [RelyingParty](relyingparty.md) elemen belül.  A **UserJourneyBehaviors** elemnek azonnal követnie kell a **DefaultUserJourney**. A **UserJourneyBehavors** elem belsejében a következő példához hasonlóan kell kinéznie:

```XML
<UserJourneyBehaviors>
   <SingleSignOn Scope="Application" />
   <SessionExpiryType>Absolute</SessionExpiryType>
   <SessionExpiryInSeconds>86400</SessionExpiryInSeconds>
</UserJourneyBehaviors>
```

Az előző példában az alábbi értékek vannak konfigurálva:

- **Egyszeri bejelentkezés (SSO)** – az egyszeri bejelentkezés a **SingleSignon**van konfigurálva. A megfelelő értékek: `Tenant`, `Application`, `Policy`és `Suppressed`.
- **Webalkalmazás-munkamenet** időkorlátja – a webalkalmazás-munkamenet időtúllépése a **SessionExpiryType** elemmel van beállítva. A megfelelő értékek: `Absolute` és `Rolling`.
- **Webalkalmazás-munkamenet élettartama** – a webalkalmazás-munkamenet élettartama a **SessionExpiryInSeconds** elemmel van beállítva. Az alapértelmezett érték 86400 másodperc (1440 perc).
