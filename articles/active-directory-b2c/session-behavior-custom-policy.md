---
title: Munkamenet-viselkedés konfigurálása egyéni házirendek használatával – Azure Active Directory B2C | Microsoft Docs
description: A munkamenet viselkedésének konfigurálása egyéni házirendek használatával Azure Active Directory B2Cban.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/07/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a2f20a4521efe2806c4bc66e4612b99caf84382a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85385263"
---
# <a name="configure-session-behavior-using-custom-policies-in-azure-active-directory-b2c"></a>Munkamenet-viselkedés konfigurálása egyéni házirendek használatával Azure Active Directory B2C

Az [egyszeri bejelentkezés (SSO) munkamenet](session-overview.md) -kezelője Azure Active Directory B2C (Azure ad B2C) lehetővé teszi a rendszergazda számára, hogy a felhasználó már hitelesített hitelesítése után vezérelje a felhasználókkal való interakciót. A rendszergazda például megadhatja, hogy megjelenjen-e az identitás-szolgáltatók kiválasztása, vagy hogy meg kell-e adni a fiók adatait. Ez a cikk a Azure AD B2C egyszeri bejelentkezéses beállításainak konfigurálását ismerteti.

## <a name="session-behavior-properties"></a>Munkamenet viselkedési tulajdonságai

A webalkalmazás-munkamenetek kezeléséhez a következő tulajdonságokat használhatja:

- **Webalkalmazás-munkamenet élettartama (perc)** – sikeres hitelesítés után a felhasználó böngészőjében tárolt Azure ad B2C's-munkamenet-cookie élettartama.
    - Alapértelmezett = 86400 másodperc (1440 perc).
    - Minimum (inkluzív) = 900 másodperc (15 perc).
    - Maximum (inkluzív) = 86400 másodperc (1440 perc).
- **Webalkalmazás-munkamenet időtúllépése** – a [munkamenet lejárati típusa](session-overview.md#session-expiry-type), *működés közbeni*vagy *abszolút*értéke. 
- **Egyszeri bejelentkezés konfigurálása** – az egyszeri bejelentkezés (SSO) viselkedési [hatóköre](session-overview.md#session-scope) több alkalmazás és felhasználói folyamat között a Azure ad B2C-bérlőben. 

## <a name="configure-the-properties"></a>A tulajdonságok konfigurálása

A munkamenet-viselkedés és az SSO-konfigurációk módosításához adjon hozzá egy **UserJourneyBehaviors** elemet a [RelyingParty](relyingparty.md) elemen belül.  A **UserJourneyBehaviors** elemnek azonnal követnie kell a **DefaultUserJourney**. A **UserJourneyBehavors** elemnek a következő példához hasonlóan kell kinéznie:

```xml
<UserJourneyBehaviors>
   <SingleSignOn Scope="Application" />
   <SessionExpiryType>Absolute</SessionExpiryType>
   <SessionExpiryInSeconds>86400</SessionExpiryInSeconds>
</UserJourneyBehaviors>
```

## <a name="single-sign-out"></a>Egyszeri kijelentkezés

### <a name="configure-the-applications"></a>Alkalmazások konfigurálása

Amikor átirányítja a felhasználót a Azure AD B2C kijelentkezési végpontra (a OAuth2 és az SAML protokollok esetében egyaránt), Azure AD B2C törli a felhasználó munkamenetét a böngészőből.  Az [egyszeri kijelentkezés](session-overview.md#single-sign-out)engedélyezéséhez állítsa be az `LogoutUrl` alkalmazást a Azure Portal:

1. Navigáljon a [Azure Portal](https://portal.azure.com).
1. A lap jobb felső sarkában található fiókra kattintva válassza ki a Azure AD B2C könyvtárat.
1. A bal oldali menüben válassza a **Azure ad B2C**lehetőséget, válassza a **Alkalmazásregisztrációk**elemet, majd válassza ki az alkalmazást.
1. Válassza a **Beállítások**, majd a **Tulajdonságok**elemet, majd keresse meg a **kijelentkezési URL-címet** szövegmezőt. 

### <a name="configure-the-token-issuer"></a>A jogkivonat-kiállító konfigurálása 

Az egyszeri kijelentkezés támogatásához a token kiállítói technikai profiljainak mind a JWT, mind az SAML esetében meg kell adniuk a következőket:

- A protokoll neve, például`<Protocol Name="OpenIdConnect" />`
- A munkamenet technikai profiljára mutató hivatkozás, például: `UseTechnicalProfileForSessionManagement ReferenceId="SM-OAuth-issuer" />` .

Az alábbi példa a JWT és az SAML-jogkivonat kiállítóit mutatja be egyszeri kijelentkezéssel:

```xml
<ClaimsProvider>
  <DisplayName>Local Account SignIn</DisplayName>
  <TechnicalProfiles>
    <!-- JWT Token Issuer -->
    <TechnicalProfile Id="JwtIssuer">
      <DisplayName>JWT token Issuer</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputTokenFormat>JWT</OutputTokenFormat>
      ...    
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-OAuth-issuer" />
    </TechnicalProfile>

    <!-- Session management technical profile for OIDC based tokens -->
    <TechnicalProfile Id="SM-OAuth-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.OAuthSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    </TechnicalProfile>

    <!--SAML token issuer-->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>SAML token issuer</DisplayName>
      <Protocol Name="SAML2" />
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      ...
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-issuer" />
    </TechnicalProfile>

    <!-- Session management technical profile for SAML based tokens -->
    <TechnicalProfile Id="SM-Saml-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="next-steps"></a>További lépések

- További információ a [Azure ad B2C-munkamenetről](session-overview.md).
