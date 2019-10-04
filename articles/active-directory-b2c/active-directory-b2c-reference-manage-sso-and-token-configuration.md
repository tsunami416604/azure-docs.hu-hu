---
title: Az egyszeri bejelentkezés és a token testreszabásainak kezelése egyéni szabályzatokkal Azure Active Directory B2Cban | Microsoft Docs
description: Ismerje meg, hogyan kezelheti az egyszeri bejelentkezést és a tokenek testreszabását a Azure Active Directory B2C egyéni házirendjeivel.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 966386bfed5f94556f145afab1c665eb3c90546a
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71065553"
---
# <a name="manage-sso-and-token-customization-using-custom-policies-in-azure-active-directory-b2c"></a>Az egyszeri bejelentkezés és a jogkivonatok testreszabásának kezelése egyéni házirendek használatával Azure Active Directory B2C

Ez a cikk azt ismerteti, hogyan kezelheti a tokeneket, a munkameneteket és az egyszeri bejelentkezési (SSO) konfigurációkat a Azure Active Directory B2C (Azure AD B2C) [Egyéni házirendjeivel](active-directory-b2c-overview-custom.md) .

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
- **Frissítési jogkivonat ablakának élettartama** – ha egy csúszó ablak élettartamát szeretné beállítani a frissítési jogkivonat számára, állítsa be a **rolling_refresh_token_lifetime_secs** metaadat-elem értékét. Az alapértelmezett érték 7776000 (90 nap). Ha nem szeretné kikényszeríteni a csúszó ablak élettartamát, cserélje le az `<Item Key="allow_infinite_rolling_refresh_token">True</Item>`elemeket a következőre:.
- **Kiállítói (ISS) jogcím** – a kibocsátó (ISS) jogcím a **IssuanceClaimPattern** metaadat-elemmel van beállítva. A megfelelő értékek `AuthorityAndTenantGuid` : és `AuthorityWithTfp`.
- **Házirend-azonosítót jelölő jogcím beállítása** – az érték `TFP` beállításának beállításai (megbízhatósági keretrendszer-házirend) `ACR` és (hitelesítési környezetre vonatkozó hivatkozás). `TFP`az ajánlott érték. Állítsa be a **AuthenticationContextReferenceClaimPattern** értéket a `None`értékkel.

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

- **Egyszeri bejelentkezés (SSO)** – az egyszeri bejelentkezés a **SingleSignon**van konfigurálva. A `Tenant`megfelelő értékek `Application` `Suppressed`:,, és. `Policy`
- **Webalkalmazás-munkamenet élettartama (perc)** – a webalkalmazás-munkamenet élettartama a **SessionExpiryInSeconds** elemmel van beállítva. Az alapértelmezett érték 86400 másodperc (1440 perc).
- **Webalkalmazás-munkamenet** időkorlátja – a webalkalmazás-munkamenet időtúllépése a **SessionExpiryType** elemmel van beállítva. A megfelelő értékek `Absolute` : és `Rolling`.
