---
title: Egyszeri bejelentkezés és jogkivonat testreszabása az Azure Active Directory B2C-vel egyéni szabályzatok kezeléséhez |} A Microsoft Docs
description: Ismerje meg az egyszeri Bejelentkezést és a jogkivonat testreszabása a egyéni szabályzatait kezelése.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/02/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 811fb8b2de59c9d324ab4acb8b0f51b4cec80aee
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37441797"
---
# <a name="azure-active-directory-b2c-manage-sso-and-token-customization-with-custom-policies"></a>Az Azure Active Directory B2C: Felügyelete egyszeri Bejelentkezéssel és egyéni házirendekkel jogkivonat testreszabása
Egyéni szabályzatok használatával biztosít a jogkivonat, munkamenet és egyszeri bejelentkezést (SSO) konfigurációk azonos felett, a beépített szabályzatokon keresztül.  Megtudhatja, mi minden beállítás, a dokumentációt [Itt](#active-directory-b2c-token-session-sso).

## <a name="token-lifetimes-and-claims-configuration"></a>Jogkivonat élettartama, és a jogcímek konfigurációja
A jogkivonatok élettartamának a beállítások módosításához kell hozzáadnia egy `<ClaimsProviders>` elem befolyásolhatja a kívánt házirendet, a függő entitás fájlban.  A `<ClaimsProviders>` elem gyermeke a `<TrustFrameworkPolicy>`.  Belső kell helyezni, amely befolyásolja a jogkivonatok élettartamának.  Az XML-fájl az alábbihoz hasonlít:

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

**Hozzáférési jogkivonatok élettartamának** -közötti értéket módosításával módosíthatja a hozzáférési jogkivonat élettartama a `<Item>` kulccsal = "token_lifetime_secs" másodpercek alatt.  A beépített alapértelmezett értéke 3600 másodperc (60 perc).

**Azonosító jogkivonat élettartama** -közötti értéket módosításával módosíthatja az azonosító jogkivonat élettartama a `<Item>` kulccsal = "id_token_lifetime_secs" másodpercek alatt.  A beépített alapértelmezett értéke 3600 másodperc (60 perc).

**Frissítési jogkivonat élettartama** -közötti értéket módosításával módosíthatja a frissítési jogkivonat élettartama a `<Item>` kulccsal = "refresh_token_lifetime_secs" másodpercek alatt.  A beépített alapértelmezett értéke 1209600 másodperc (14 nap).

**Frissítési jogkivonat csúszóablak-élettartama** – Ha szeretne egy csúszóablak-élettartama beállítása a frissítési jogkivonatot, módosítsa az értéket belül `<Item>` kulccsal = "rolling_refresh_token_lifetime_secs" másodpercek alatt.  A beépített alapértelmezett értéke 7776000 (90 nap).  Ha nem szeretné kényszeríteni a csúszóablak-élettartama, cserélje le a sort:
```XML
<Item Key="allow_infinite_rolling_refresh_token">True</Item>
```

**Kibocsátói (iss) jogcím** – Ha szeretné módosítani a kibocsátói (iss) jogcím, közötti értéket módosítsa a `<Item>` kulccsal = "IssuanceClaimPattern".  Az érvényes értékek a következők `AuthorityAndTenantGuid` és `AuthorityWithTfp`.

**Beállítás a házirend Azonosítóját jelölő jogcím** -érték a lehetőségeket TFP (bizalmi keretrendszer házirend) és az ACR (hitelesítési környezeti hivatkozás).  
Ehhez biztosítania kell, javasoljuk, hogy ezt a TFP beállítást a `<Item>` és a kulcs = "AuthenticationContextReferenceClaimPattern" létezik, és az érték `None`.
Az a `<OutputClaims>` elemet, adja hozzá ezt az elemet:
```XML
<OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />
```
Az ACR-hez, távolítsa el a `<Item>` kulccsal = "AuthenticationContextReferenceClaimPattern".

**Tárgy (sub) jogcím** – Ez a beállítás alapértelmezés szerint az objektumazonosító felett, ha szeretne váltani, ezt a `Not Supported`, tegye a következőket:

Cserélje le ezt a sort 
```XML
<OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
```
az ezt a sort:
```XML
<OutputClaim ClaimTypeReferenceId="sub" />
```

## <a name="session-behavior-and-sso"></a>Munkamenet-viselkedés, és az egyszeri bejelentkezés

Ha módosítani szeretné a munkamenet-viselkedés, és az egyszeri bejelentkezés konfigurációkkal, hozzá kell egy `<UserJourneyBehaviors>` elemben található a `<RelyingParty>` elemet.  A `<UserJourneyBehaviors>` elem azonnal kell követnie a `<DefaultUserJourney>`.  Belső a `<UserJourneyBehavors>` elem kell kinéznie:

```XML
<UserJourneyBehaviors>
   <SingleSignOn Scope="Application" />
   <SessionExpiryType>Absolute</SessionExpiryType>
   <SessionExpiryInSeconds>86400</SessionExpiryInSeconds>
</UserJourneyBehaviors>
```
**Egyszeri bejelentkezés (SSO) beállítása** – egyszeri bejelentkezési konfigurációjának módosítása, értékét módosítani kell `<SingleSignOn>`.  Az érvényes értékek a következők `Tenant`, `Application`, `Policy` és `Disabled`. 

**Webalkalmazás munkamenet élettartama (perc)** – módosíthatja a webalkalmazás munkamenet élettartamára, értékét módosítani kell a `<SessionExpiryInSeconds>` elemet.  Beépített szabályzatok alapértelmezett érték 86 400 másodperc (1440 perc).

**Webes alkalmazás munkamenet időtúllépésének** – a webalkalmazás munkamenet-időkorlátja, módosítsa az értékét módosítani kell `<SessionExpiryType>`.  Az érvényes értékek a következők `Absolute` és `Rolling`.
