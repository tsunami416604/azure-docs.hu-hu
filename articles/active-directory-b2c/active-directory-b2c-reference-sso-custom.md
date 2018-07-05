---
title: Egyszeri bejelentkezés munkamenet-kezelés az Azure Active Directory B2C-vel egyéni szabályzatok használatával |} A Microsoft Docs
description: Ismerje meg, hogyan kezelheti az Azure AD B2C-vel egyéni szabályzatok használatával egyszeri bejelentkezési munkamenetek.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/20/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 351b48f2e2766b4974a5a41b5e95acfbd63dbfc9
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37443222"
---
# <a name="azure-ad-b2c-single-sign-on-sso-session-management"></a>Az Azure AD B2C: Egyszeri bejelentkezéses (SSO) munkamenet-kezelés

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Az Azure AD B2C lehetővé teszi, hogy az Azure AD B2C-vel és együttműködését a felhasználó a felhasználó már rendelkezik hitelesítése után a rendszergazda. Ez az egyszeri bejelentkezés munkamenet-management szolgáltatáson keresztül történik. Ha például a rendszergazda szabályozhatja jelenik-e a választott Identitásszolgáltatók meg, vagy hogy helyi fiók adatainak kell újra megadni. Ez a cikk ismerteti az Azure AD B2C az egyszeri bejelentkezési beállításainak konfigurálására.

## <a name="overview"></a>Áttekintés

Egyszeri bejelentkezés munkamenet-kezelés két részből áll. Az első közvetlenül az Azure AD B2C a felhasználói interakció, és a külső felek, például a Facebookhoz a felhasználói interakció más ügyletek foglalkozik. Az Azure AD B2C nem bírálja felül, és egyszeri Bejelentkezést olyan munkamenetek, amelyek a külső felek által menthető megkerülése. Inkább az útvonal keresztül az Azure AD B2C-vel a külső fél lekérése "megőrzése", nem kell a felhasználót, hogy válassza ki a közösségi vagy vállalati identitásszolgáltatóval reprompt. A végső SSO döntést a külső fél maradnak.

## <a name="how-does-it-work"></a>Hogyan működik?

Egyszeri bejelentkezés munkamenet-kezelés azonos használja, mint az egyéni házirendek egyéb technikai profil. Egy vezénylési lépés végrehajtásakor a technikai profil a lépéshez kapcsolódó lekérik a egy `UseTechnicalProfileForSessionManagement` hivatkozást. Ha van ilyen, akkor az egyszeri bejelentkezés munkamenet hivatkozott szolgáltató majd ellenőrzi, hogy a felhasználói munkamenet résztvevő-e. Ha tehát az egyszeri bejelentkezés munkamenet-szolgáltató segítségével tudja kezelni a munkamenetet. Hasonlóképpen, egy vezénylési lépés végrehajtásának befejeződése után a szolgáltatót használja a információkat tárolja a munkamenet, ha az egyszeri bejelentkezés munkamenet-szolgáltató van megadva.

Az Azure AD B2C az egyszeri bejelentkezés munkamenet-szolgáltatói használható számos definiálva van:

* NoopSSOSessionProvider
* DefaultSSOSessionProvider
* ExternalLoginSSOSessionProvider
* SamlSSOSessionProvider

Egyszeri bejelentkezés felügyeleti osztályainak használatával vannak megadva a `<UseTechnicalProfileForSessionManagement ReferenceId=“{ID}" />` technikai profil elemhez.

### <a name="noopssosessionprovider"></a>NoopSSOSessionProvider

Előírja a neve, ahogy ez a szolgáltató nem csinál semmit. Ez a szolgáltató egy adott technikai profil egyszeri bejelentkezés működése újrainduljanak használható.

### <a name="defaultssosessionprovider"></a>DefaultSSOSessionProvider

Ez a szolgáltató munkamenetben jogcímek tárolására használható. Ez a szolgáltató általában egy helyi felhasználói fiókok felügyeletének technikai profil hivatkozik. 

> [!NOTE]
> A DefaultSSOSessionProvider jogcímek tárolni a munkamenet használatakor győződjön meg arról, hogy a jogcímeket, az alkalmazásba visszaküldött vagy a későbbi lépésekben előtti feltételei által használt kell tárolni a munkamenet vagy a felhasználói profilt a forrásszint kiegészítve kell a könyvtár. Ez biztosítja, hogy a hitelesítési világába nem fog meghiúsulni hiányzó jogcímek.

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

### <a name="externalloginssosessionprovider"></a>ExternalLoginSSOSessionProvider

Ez a szolgáltató le a "Identitásszolgáltató kiválasztása" képernyő szolgál. Egy technikai profilban konfigurált külső identitásszolgáltató, például a Facebookhoz általában hivatkozik rá. 

```XML
<TechnicalProfile Id="SM-SocialLogin">
    <DisplayName>Session Mananagement Provider</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.ExternalLoginSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

### <a name="samlssosessionprovider"></a>SamlSSOSessionProvider

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

A szolgáltató egy SAML-identity provider munkamenetek tárolására szolgáló használatakor a fenti mindkét értéke csak hamis lehet. A szolgáltató a B2C SAML-munkamenetek tárolására szolgáló használatakor a fenti kell igaz vagy nincs megadva az alapértelmezett beállításokat is igaz.

>[!NOTE]
> SAML-munkamenet kijelentkeztetése igényel a `SessionIndex` és `NameID` befejezéséhez.

## <a name="next-steps"></a>További lépések

Mi a kedvenc vélemények és tanácsok! Ha ez a témakör a tapasztalnak, közzététel a Stack Overflow címke használatával ["azure-ad-b2c"](https://stackoverflow.com/questions/tagged/azure-ad-b2c). Szolgáltatással kapcsolatos kéréseit, szavazzon azok esetében a [Visszajelzési fórum](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).

