---
title: Egyetlen bejelentkezési munkamenet-kezelés az Azure Active Directory B2C-vel egyéni szabályzatok használatával |} A Microsoft Docs
description: Ismerje meg, hogyan kezelheti az Azure AD B2C-vel egyéni szabályzatok használatával egyszeri bejelentkezési munkamenetek.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: d1d76e3ac995d4ee63e36ac3560d20f473d3ea2d
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55187216"
---
# <a name="single-sign-on-session-management-in-azure-active-directory-b2c"></a>Egyszeri bejelentkezés munkamenet-kezelés az Azure Active Directory B2C-vel

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Egyszeri bejelentkezéses (SSO) munkamenet-kezelés az Azure Active Directory (Azure AD) B2C lehetővé teszi, hogy egy felhasználói vezérlő interakció a rendszergazda a felhasználó már rendelkezik hitelesítése után. Ha például a rendszergazda szabályozhatja jelenik-e a választott Identitásszolgáltatók meg, vagy hogy helyi fiók adatainak kell újra megadni. Ez a cikk ismerteti az Azure AD B2C az egyszeri bejelentkezési beállításainak konfigurálására.

Egyszeri bejelentkezés munkamenet-kezelés két részből áll. Az első közvetlenül az Azure AD B2C a felhasználói interakció, és a külső felek, például a Facebookhoz a felhasználói interakció más ügyletek foglalkozik. Az Azure AD B2C nem bírálja felül, és egyszeri Bejelentkezést olyan munkamenetek, amelyek a külső felek által menthető megkerülése. Inkább az útvonal keresztül az Azure AD B2C-vel a külső fél lekérése "megőrzése", nem kell a felhasználót, hogy válassza ki a közösségi vagy vállalati identitásszolgáltatóval reprompt. A végső SSO döntést a külső fél maradnak.

Egyszeri bejelentkezés munkamenet-kezelés azonos használja, mint az egyéni házirendek egyéb technikai profil. Egy vezénylési lépés végrehajtásakor a technikai profil a lépéshez kapcsolódó lekérik a egy `UseTechnicalProfileForSessionManagement` hivatkozást. Ha van ilyen, akkor az egyszeri bejelentkezés munkamenet hivatkozott szolgáltató majd ellenőrzi, hogy a felhasználói munkamenet résztvevő-e. Ha tehát az egyszeri bejelentkezés munkamenet-szolgáltató segítségével tudja kezelni a munkamenetet. Hasonlóképpen, egy vezénylési lépés végrehajtásának befejeződése után a szolgáltatót használja a információkat tárolja a munkamenet, ha az egyszeri bejelentkezés munkamenet-szolgáltató van megadva.

Az Azure AD B2C az egyszeri bejelentkezés munkamenet-szolgáltatói használható számos definiálva van:

* NoopSSOSessionProvider
* DefaultSSOSessionProvider
* ExternalLoginSSOSessionProvider
* SamlSSOSessionProvider

Egyszeri bejelentkezés felügyeleti osztályainak használatával vannak megadva a `<UseTechnicalProfileForSessionManagement ReferenceId=“{ID}" />` technikai profil elemhez.

## <a name="noopssosessionprovider"></a>NoopSSOSessionProvider

Előírja a neve, ahogy ez a szolgáltató nem csinál semmit. Ez a szolgáltató egy adott technikai profil egyszeri bejelentkezés működése újrainduljanak használható.

## <a name="defaultssosessionprovider"></a>DefaultSSOSessionProvider

Ez a szolgáltató munkamenetben jogcímek tárolására használható. Ez a szolgáltató általában egy helyi felhasználói fiókok felügyeletének technikai profil hivatkozik. A DefaultSSOSessionProvider jogcímek tárolni a munkamenet használatakor győződjön meg arról, hogy a jogcímeket, az alkalmazásba visszaküldött vagy a későbbi lépésekben előtti feltételei által használt kell tárolni a munkamenet vagy a felhasználói profilt a forrásszint kiegészítve kell a könyvtár. Ez biztosítja, hogy a hitelesítési világába nem fog meghiúsulni hiányzó jogcímek.

```XML
<TechnicalProfile Id="SM-AAD">
    <DisplayName>Session Mananagement Provider</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.DefaultSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="objectId" />
        <PersistedClaim ClaimTypeReferenceId="newUser" />
        <PersistedClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" />
    </PersistedClaims>
    <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="objectIdFromSession" DefaultValue="true" />
    </OutputClaims>
</TechnicalProfile>
```

A munkamenet jogcímek hozzáadásához használja a `<PersistedClaims>` elem a technikai profil. Amikor a szolgáltató segítségével tudja kezelni a munkamenetet, a megőrzött jogcímeket a jogcímkezelési tulajdonságcsomag kerülnek. `<OutputClaims>` a munkamenet jogcímeket kér le szolgál.

## <a name="externalloginssosessionprovider"></a>ExternalLoginSSOSessionProvider

Ez a szolgáltató le a "Identitásszolgáltató kiválasztása" képernyő szolgál. Egy technikai profilban konfigurált külső identitásszolgáltató, például a Facebookhoz általában hivatkozik rá. 

```XML
<TechnicalProfile Id="SM-SocialLogin">
    <DisplayName>Session Mananagement Provider</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.ExternalLoginSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="samlssosessionprovider"></a>SamlSSOSessionProvider

Ez a szolgáltató kezelése az Azure AD B2C-vel SAML-munkamenet alkalmazásokat, valamint a külső SAML Identitásszolgáltatók között szolgál.

```XML
<TechnicalProfile Id="SM-Reflector-SAML">
    <DisplayName>Session Management Provider</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <Metadata>
        <Item Key="IncludeSessionIndex">false</Item>
        <Item Key="RegisterServiceProviders">false</Item>
    </Metadata>
</TechnicalProfile>
```

A technikai profilban találhatók két metaadat-elemek:

| Elem | Alapértelmezett érték | Lehetséges értékek | Leírás
| --- | --- | --- | --- |
| IncludeSessionIndex | true | Igaz/hamis | Azt jelzi, hogy a szolgáltató, hogy a munkamenet indexe kell tárolni. |
| RegisterServiceProviders | true | Igaz/hamis | Azt jelzi, hogy a szolgáltatót regisztrálnia kell az összes SAML szolgáltatók, kiadott egy helyességi feltétel. |

A szolgáltató egy SAML-identity provider munkamenetek tárolására szolgáló használatakor a fenti mindkét értéke csak hamis lehet. A szolgáltató a B2C SAML-munkamenetek tárolására szolgáló használatakor a fenti kell igaz vagy nincs megadva az alapértelmezett beállításokat is igaz. SAML-munkamenet kijelentkeztetése igényel a `SessionIndex` és `NameID` befejezéséhez.

