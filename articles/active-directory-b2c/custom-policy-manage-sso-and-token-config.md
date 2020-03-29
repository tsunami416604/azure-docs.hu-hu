---
title: Egyszeri rendelés és tokenek testreszabásának kezelése egyéni házirendek használatával
titleSuffix: Azure AD B2C
description: Ismerje meg az egyszeri bejelentkezés és a tokenek testreszabásának kezelését az Azure Active Directory B2C egyéni szabályzatai használatával.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189293"
---
# <a name="manage-sso-and-token-customization-using-custom-policies-in-azure-active-directory-b2c"></a>Egyszeri bejelentkezés és tokenek testreszabásának kezelése egyéni szabályzatok használatával az Azure Active Directory B2C-ben

Ez a cikk arról nyújt tájékoztatást, hogyan kezelheti a jogkivonatot, a munkamenetet és az egyszeri bejelentkezési (Egyszeri bejelentkezés) konfigurációkat az Azure Active Directory B2C (Azure AD B2C) [egyéni szabályzatok](custom-policy-overview.md) használatával.

## <a name="token-lifetimes-and-claims-configuration"></a>A jogkivonat élettartama és jogcímkonfigurációja

A jogkivonat élettartamának beállításainak módosításához adjon hozzá egy [ClaimsProviders](claimsproviders.md) elemet a módosítani kívánt szabályzat függő entitásfájljában.  A **ClaimsProviders** elem a [TrustFrameworkPolicy](trustframeworkpolicy.md) elem gyermeke.

Szúrja be a ClaimsProviders elemet a BasePolicy elem és a függő entitásfájl RelyingParty eleme közé.

Belül, akkor el kell helyeznie az információt, amely befolyásolja a token élettartamát. Az XML így néz ki:

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

Az előző példában a következő értékek vannak beállítva:

- **Hozzáférési jogkivonat élettartama** – A hozzáférési jogkivonat élettartama érték van beállítva **token_lifetime_secs** metaadat-elem. Az alapértelmezett érték 3600 másodperc (60 perc).
- **Azonosító token élettartama** – Az azonosító token élettartama érték van beállítva a **id_token_lifetime_secs** metaadat-elem. Az alapértelmezett érték 3600 másodperc (60 perc).
- **Frissítési jogkivonat élettartama** – A frissítési jogkivonat élettartama érték a **refresh_token_lifetime_secs** metaadat-elem. Az alapértelmezett érték 1209600 másodperc (14 nap).
- **Token csúsztatási ablak élettartamának frissítése** – Ha a csúszóablak élettartamát a frissítési jogkivonatra szeretné beállítani, állítsa be **rolling_refresh_token_lifetime_secs** metaadatelem értékét. Az alapértelmezett érték 7776000 (90 nap). Ha nem szeretné kikényszeríteni a csúszó ablak élettartamát, cserélje ki az elemet a mezőre. `<Item Key="allow_infinite_rolling_refresh_token">True</Item>`
- **Kiállítói (iss) jogcím** – a kiállítói (iss) jogcím az **IssuanceClaimPattern** metaadat-elemével van beállítva. A vonatkozó `AuthorityAndTenantGuid` értékek `AuthorityWithTfp`a és a.
- **Házirend-azonosítót képviselő jogcím beállítása** – `TFP` Az érték beállításának `ACR` beállításai a következők: (megbízhatósági keretházirend) és (hitelesítési környezet hivatkozása). `TFP`az ajánlott érték. Állítsa be az **AuthenticationContextReferenceClaimPattern** értéket a értékével. `None`

    A **ClaimsSchema** elemben adja hozzá ezt az elemet:

    ```XML
    <ClaimType Id="trustFrameworkPolicy">
      <DisplayName>Trust framework policy name</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    ```

    A **OutputClaims** elemben adja hozzá ezt az elemet:

    ```XML
    <OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />
    ```

    ACR esetén távolítsa el az **AuthenticationContextReferenceClaimPattern** elemet.

- **Tárgy (al) jogcím** – Ez a beállítás alapértelmezés szerint az ObjectID, ha szeretné átváltani ezt a `Not Supported`beállítást, cserélje ki ezt a sort:

    ```XML
    <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
    ```

    ezzel a sokkal:

    ```XML
    <OutputClaim ClaimTypeReferenceId="sub" />
    ```

## <a name="session-behavior-and-sso"></a>Munkamenet-viselkedés és sso

A munkamenet viselkedésének és az SSO-konfigurációk módosításához adjon hozzá egy **UserJourneyBehaviors** elemet a [RelyingParty](relyingparty.md) elemben.  A **UserJourneyBehaviors** elemnek azonnal követnie kell a **DefaultUserJourney elemet.** A **UserJourneyBehavors** elem belseje így kell kinéznie:

```XML
<UserJourneyBehaviors>
   <SingleSignOn Scope="Application" />
   <SessionExpiryType>Absolute</SessionExpiryType>
   <SessionExpiryInSeconds>86400</SessionExpiryInSeconds>
</UserJourneyBehaviors>
```

Az előző példában a következő értékek vannak konfigurálva:

- **Egyszeri bejelentkezés (SSO)** - Egyszeri bejelentkezés van konfigurálva a **SingleSignOn.** A vonatkozó `Tenant`értékek `Application` `Policy`a `Suppressed`, , és .
- **Webapp-munkamenet időbeli meghosszabbítása** – A webalkalmazás munkamenet-időmegadása a **SessionExpiryType elemmel** van beállítva. A vonatkozó `Absolute` értékek `Rolling`a és a.
- **Webalkalmazás-munkamenet élettartama** – A webalkalmazás munkamenetének élettartama a **SessionExpiryInSeconds** összetevővel van beállítva. Az alapértelmezett érték 86400 másodperc (1440 perc).
