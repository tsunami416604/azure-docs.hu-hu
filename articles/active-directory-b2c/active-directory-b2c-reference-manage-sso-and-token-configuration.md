---
title: Egyszeri bejelentkezés és egyéni házirendekkel token testreszabási kezelése |} Microsoft Docs
description: Ismerje meg az egyszeri bejelentkezés és token testreszabási egyéni házirendek kezelése.
services: active-directory-b2c
documentationcenter: ''
author: davidmu
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 05/02/2017
ms.author: davidmu
ms.openlocfilehash: c9eb7f7711a8987945b8aeaee8d6798b7a6b8284
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-manage-sso-and-token-customization-with-custom-policies"></a>Az Azure Active Directory B2C: Egyszeri Bejelentkezéssel és token testreszabási egyéni házirendek kezelése
Egyéni házirendek használatával teszi lehetővé a jogkivonatot, munkamenet és egyszeri bejelentkezés (SSO) konfigurációk ugyanarra a vezérlőre beépített házirendek segítségével.  Ha szeretné megtudni, hogy egyes beállítások funkciója, a dokumentáció [Itt](#active-directory-b2c-token-session-sso).

## <a name="token-lifetimes-and-claims-configuration"></a>Jogkivonat élettartamát és a jogcímek konfiguráció
A jogkivonat élettartamát a beállítások módosításához meg kell adni egy `<ClaimsProviders>` befolyásolja a kívánt házirendet, a függő entitás fájlban.  A `<ClaimsProviders>` elem gyermeke a `<TrustFrameworkPolicy>`.  Belül, amely befolyásolja a jogkivonat élettartamát helyezni lesz szüksége.  Az XML-fájl így néz ki:

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

**Hozzáférési jogkivonat élettartamát** értéket módosításával módosíthatja a hozzáférési jogkivonat élettartamát a `<Item>` kulccsal = "token_lifetime_secs" másodpercben.  A beépített alapértelmezett érték 3600 másodperc (60 perc).

**A jogkivonatok élettartama azonosító** jogkivonat élettartamát az azonosító értéket módosításával módosítható a `<Item>` kulccsal = "id_token_lifetime_secs" másodpercben.  A beépített alapértelmezett érték 3600 másodperc (60 perc).

**Frissítse a jogkivonatok élettartama** értéket módosításával módosíthatja a frissítési jogkivonat élettartamát a `<Item>` kulccsal = "refresh_token_lifetime_secs" másodpercben.  A beépített alapértelmezett érték 1209600 másodperc (14 nap).

**Frissítési jogkivonat mozgó ablak élettartamát** Ha azt szeretné beállítani a mozgó ablak élettartama a frissítési jogkivonat, módosítsa az értéket `<Item>` kulccsal = "rolling_refresh_token_lifetime_secs" másodpercben.  A beépített alapértelmezett érték 7776000 (90 nap).  Ha nem szeretné enfore egy késleltetett ablak élettartamát, cserélje le a sort:
```XML
<Item Key="allow_infinite_rolling_refresh_token">True</Item>
```

**A jogcím kiállítójának (iss)** Ha a kibocsátó (iss) jogcím módosítani kívánja, módosítsa az értéket a `<Item>` kulccsal = "IssuanceClaimPattern".  A megfelelő értékek `AuthorityAndTenantGuid` és `AuthorityWithTfp`.

**Beállítás jogcím házirend-azonosító képviselő** érték a beállítások a következők TFP (megbízhatósági keretrendszer házirend) és ACR (hitelesítési környezeti hivatkozás).  
Javasoljuk, hogy ezt a TFP beállítást, ehhez ellenőrizze a `<Item>` és a kulcs = "AuthenticationContextReferenceClaimPattern" létezik, és az érték `None`.
Az a `<OutputClaims>` cikkhez, ez az elem hozzáadása:
```XML
<OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />
```
ACR, távolítsa el a `<Item>` kulccsal = "AuthenticationContextReferenceClaimPattern".

**Tulajdonos (rész-) jogcím** ObjectID, hogy alapértelmezett ezt a beállítást, ha azt szeretné, a kapcsolóhoz `Not Supported`, tegye a következőket:

Cserélje le ezt a sort 
```XML
<OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
```
Ehhez a sorhoz:
```XML
<OutputClaim ClaimTypeReferenceId="sub" />
```

## <a name="session-behavior-and-sso"></a>Munkamenet viselkedést és az egyszeri bejelentkezés
A munkamenet viselkedést és egyszeri Bejelentkezéssel konfigurációk módosítása, meg kell adni egy `<UserJourneyBehaviors>` elemben található a `<RelyingParty>` elemet.  A `<UserJourneyBehaviors>` elem közvetlenül követnie kell a `<DefaultUserJourney>`.  Belső a `<UserJourneyBehavors>` elem kell kinéznie:

```XML
<UserJourneyBehaviors>
   <SingleSignOn Scope="Application" />
   <SessionExpiryType>Absolute</SessionExpiryType>
   <SessionExpiryInSeconds>86400</SessionExpiryInSeconds>
</UserJourneyBehaviors>
```
**Egyszeri bejelentkezés (SSO) konfigurációs** való egyszeri bejelentkezés beállításainak módosítása esetén módosítania kell a értékének `<SingleSignOn>`.  A megfelelő értékek `Tenant`, `Application`, `Policy` és `Disabled`. 

**Webalkalmazás munkamenet élettartama (perc)** módosítása a webalkalmazás munkamenetek élettartamát, módosítania kell értékének a `<SessionExpiryInSeconds>` elemet.  Az alapértelmezett beépített házirendek értéke 86400 másodperc (1440 perc).

**Webes alkalmazás munkamenet időkorlátja** Ha módosítani szeretné a webes alkalmazás munkamenet időkorlátja, módosítania kell a értékének `<SessionExpiryType>`.  A megfelelő értékek `Absolute` és `Rolling`.
