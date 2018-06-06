---
title: Egyszeri bejelentkezés és egyéni házirendek az Azure Active Directory B2C token testreszabási kezelése |} Microsoft Docs
description: Ismerje meg az egyszeri bejelentkezés és token testreszabási egyéni házirendek kezelése.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 05/02/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 43e392979c50d340a10575898edb25b119e1410b
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34712232"
---
# <a name="azure-active-directory-b2c-manage-sso-and-token-customization-with-custom-policies"></a>Az Azure Active Directory B2C: Egyszeri Bejelentkezéssel és token testreszabási egyéni házirendek kezelése
Egyéni házirendek használatával teszi lehetővé a jogkivonatot, munkamenet és egyszeri bejelentkezés (SSO) konfigurációk ugyanarra a vezérlőre beépített házirendek segítségével.  Ismerje meg, mi minden beállítás, olvassa el a dokumentációt [Itt](#active-directory-b2c-token-session-sso).

## <a name="token-lifetimes-and-claims-configuration"></a>Jogkivonat élettartamát és a jogcímek konfiguráció
A jogkivonat élettartamát a beállítások módosításához meg kell adni egy `<ClaimsProviders>` befolyásolja a kívánt házirendet, a függő entitás fájlban.  A `<ClaimsProviders>` elem gyermeke a `<TrustFrameworkPolicy>`.  Belül akkor kell helyezni, amely befolyásolja a jogkivonat élettartamát.  Az XML-fájl ebben a példában néz ki:

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

**Hozzáférési jogkivonat élettartamát** -értéket módosításával módosíthatja a hozzáférési jogkivonat élettartamát a `<Item>` kulccsal = "token_lifetime_secs" másodpercben.  A beépített alapértelmezett érték 3600 másodperc (60 perc).

**A jogkivonatok élettartama azonosító** -jogkivonat élettartamát az azonosító értéket módosításával módosítható a `<Item>` kulccsal = "id_token_lifetime_secs" másodpercben.  A beépített alapértelmezett érték 3600 másodperc (60 perc).

**Frissítse a jogkivonatok élettartama** -értéket módosításával módosíthatja a frissítési jogkivonat élettartamát a `<Item>` kulccsal = "refresh_token_lifetime_secs" másodpercben.  A beépített alapértelmezett érték 1209600 másodperc (14 nap).

**Frissítési jogkivonat mozgó ablak élettartamát** – Ha meg szeretné a frissítési jogkivonat mozgó ablak élettartamának beállítása, módosítsa az értéket `<Item>` kulccsal = "rolling_refresh_token_lifetime_secs" másodpercben.  A beépített alapértelmezett érték 7776000 (90 nap).  Ha nem szeretné kényszeríteni a mozgó ablak élettartama, cserélje le a sort:
```XML
<Item Key="allow_infinite_rolling_refresh_token">True</Item>
```

**A jogcím kiállítójának (iss)** – Ha azt szeretné, hogy a kibocsátó (iss) jogcím módosításához módosítsa az értéket a `<Item>` kulccsal = "IssuanceClaimPattern".  A megfelelő értékek `AuthorityAndTenantGuid` és `AuthorityWithTfp`.

**Beállítás jogcím házirend-azonosító képviselő** -az érték közül TFP (megbízhatósági keretrendszer házirend) és ACR (hitelesítési környezeti hivatkozás).  
Javasoljuk, hogy ezt a TFP beállítást, ehhez ellenőrizze a `<Item>` és a kulcs = "AuthenticationContextReferenceClaimPattern" létezik, és az érték `None`.
Az a `<OutputClaims>` cikkhez, ez az elem hozzáadása:
```XML
<OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />
```
ACR, távolítsa el a `<Item>` kulccsal = "AuthenticationContextReferenceClaimPattern".

**Tulajdonos (rész-) jogcím** – Ez a beállítás alapértelmezett értékre ObjectID, történik, ha azt szeretné, a kapcsolóhoz `Not Supported`, tegye a következőket:

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
**Az egyszeri bejelentkezés (SSO) beállítása** – az egyszeri bejelentkezés beállításainak módosításához, módosítania kell a értékének `<SingleSignOn>`.  A megfelelő értékek `Tenant`, `Application`, `Policy` és `Disabled`. 

**Webalkalmazás munkamenet élettartama (perc)** - módosíthatja a webalkalmazás munkamenetek élettartamát, értékét módosítani kell a `<SessionExpiryInSeconds>` elemet.  Az alapértelmezett beépített házirendek értéke 86400 másodperc (1440 perc).

**Webes alkalmazás munkamenet időtúllépésének** - módosítása a webes alkalmazás munkamenet időkorlátja módosítania kell a értékének `<SessionExpiryType>`.  A megfelelő értékek `Absolute` és `Rolling`.
