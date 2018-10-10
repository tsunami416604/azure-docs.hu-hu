---
title: Felügyelete egyszeri Bejelentkezéssel és egyéni szabályzatok használatával az Azure Active Directory B2C jogkivonat testreszabása |} A Microsoft Docs
description: Ismerje meg az egyszeri Bejelentkezést és a jogkivonat testreszabása az Azure Active Directory B2C-vel egyéni szabályzatok használatával.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: c7ba1f87b877466ff4d9d11e4b3b5a6567e7ae06
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2018
ms.locfileid: "48902634"
---
# <a name="manage-sso-and-token-customization-using-custom-policies-in-azure-active-directory-b2c"></a>Egyszeri bejelentkezés és egyéni szabályzatok használatával az Azure Active Directory B2C jogkivonat testreszabása kezelése

Ez a cikk ismerteti, hogyan kezelheti a jogkivonat, munkamenet és egyszeri bejelentkezés (SSO) konfigurációk használatával [egyéni szabályzatok](active-directory-b2c-overview-custom.md) Azure Active Directory (Azure AD) B2C-ben.

## <a name="token-lifetimes-and-claims-configuration"></a>Jogkivonat élettartama, és a jogcímek konfigurációja

A beállításokat módosítani a jogkivonatok élettartamának, hozzá kell adnia egy [ClaimsProviders](claimsproviders.md) elem befolyásolhatja a kívánt házirendet, a függő entitás fájlban.  A **ClaimsProviders** elem gyermeke a [TrustFrameworkPolicy](trustframeworkpolicy.md) elemet. Belső kell helyezni, amely befolyásolja a jogkivonatok élettartamának. Az XML-fájl az alábbihoz hasonlít:

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

A következő értékeket az előző példában vannak beállítva:

- **Hozzáférési jogkivonatok élettartamának** – az értéke a hozzáférési jogkivonat élettartama **token_lifetime_secs** metaadatelem. Az alapértelmezett érték: 3600 másodperc (60 perc).
- **Azonosító jogkivonat élettartama** – az értéke az azonosító jogkivonat élettartama a **id_token_lifetime_secs** metaadatelem. Az alapértelmezett érték: 3600 másodperc (60 perc).
- **Frissítési jogkivonat élettartama** – a frissítési jogkivonat élettartama érték van beállítva a **refresh_token_lifetime_secs** metaadatelem. Az alapértelmezett érték: 1209600 másodperc (14 nap).
- **Frissítési jogkivonat csúszóablak-élettartama** – Ha egy csúszóablak-élettartama beállítása a frissítési jogkivonatot, az értékét állítsa be szeretné **rolling_refresh_token_lifetime_secs** metaadatelem. Az alapértelmezett érték: 7776000 (90 nap). Ha nem szeretné kényszeríteni a csúszóablak-élettartama, cserélje le a cikk a `<Item Key="allow_infinite_rolling_refresh_token">True</Item>`.
- **Kibocsátói (iss) jogcím** – a kibocsátói (iss) jogcím van beállítva a **IssuanceClaimPattern** metaadatelem. Az érvényes értékek a következők `AuthorityAndTenantGuid` és `AuthorityWithTfp`.
- **Beállítás a házirend Azonosítóját jelölő jogcím** -érték a lehetőségeket vannak `TFP` (megbízhatósági keretrendszer házirend) és `ACR` (hitelesítési környezeti hivatkozás). `TFP` az ajánlott érték. Állítsa be **AuthenticationContextReferenceClaimPattern** értékét `None`. Az a **OutputClaims** elemet, adja hozzá ezt az elemet:
    
    ```XML
    <OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />
    ```

    Az ACR-hez, távolítsa el a **AuthenticationContextReferenceClaimPattern** elemet.

- **Tárgy (sub) jogcím** – Ez a beállítás alapértelmezett értéke ObjectID, ha szeretné, ezt a beállítást átállítja `Not Supported`, cserélje le ezt a sort: 

    ```XML
    <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
    ```
    
    az ezt a sort:
    
    ```XML
    <OutputClaim ClaimTypeReferenceId="sub" />
    ```

## <a name="session-behavior-and-sso"></a>Munkamenet-viselkedés, és az egyszeri bejelentkezés

Ha módosítani szeretné a munkamenet-viselkedés, és az egyszeri bejelentkezés konfigurációkkal, adjon hozzá egy **UserJourneyBehaviors** elemben található a [RelyingParty](relyingparty.md) elemet.  A **UserJourneyBehaviors** elem azonnal kell követnie a **DefaultUserJourney**. Belső a **UserJourneyBehavors** elem példához hasonlóan kell kinéznie:

```XML
<UserJourneyBehaviors>
   <SingleSignOn Scope="Application" />
   <SessionExpiryType>Absolute</SessionExpiryType>
   <SessionExpiryInSeconds>86400</SessionExpiryInSeconds>
</UserJourneyBehaviors>
```

A következő értékeket az előző példában vannak konfigurálva:

- **Egyszeri bejelentkezés (SSO)** – egyszeri bejelentkezés van konfigurálva a **SingleSignOn**. Az érvényes értékek a következők `Tenant`, `Application`, `Policy`, és `Suppressed`. 
- **Webalkalmazás munkamenet élettartama (perc)** – a web app-munkamenet élettartama van beállítva a **SessionExpiryInSeconds** elemet. Az alapértelmezett érték 86 400 másodperc (1440 perc).
- **Webes alkalmazás munkamenet időtúllépésének** – a webes alkalmazás munkamenet időkorlátja van beállítva a **SessionExpiryType** elemet. Az érvényes értékek a következők `Absolute` és `Rolling`.
