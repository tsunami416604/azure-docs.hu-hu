---
title: Egyszeri bejelentkezési munkamenet felügyeleti egyéni házirendekkel – az Azure AD B2C |} Microsoft Docs
description: Útmutató az Azure AD B2C egyéni házirendekkel egyszeri bejelentkezési munkameneteket kezelhessen.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 10/20/2017
ms.author: davidmu
ms.openlocfilehash: 3d378d188d9acec06f37ca91e3c67e82fb31fc08
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="azure-ad-b2c-single-sign-on-sso-session-management"></a>Az Azure AD B2C: Az egyszeri bejelentkezés (SSO) munkamenet-kezelés

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Az Azure AD B2C segítségével a rendszergazda az Azure AD B2C és együttműködését a felhasználó a felhasználó már rendelkezik hitelesítése után. Ez egyszeri bejelentkezési munkamenet-kezelés segítségével történik. A rendszergazda szabályozhatja például jelenik-e a kijelölés Identitásszolgáltatók meg, vagy hogy helyi fiókadatok kell újra kell megadni. Ez a cikk ismerteti az Azure AD B2C az egyszeri bejelentkezés beállításainak konfigurálása.

## <a name="overview"></a>Áttekintés

Egyszeri bejelentkezési munkamenetek kezelésére két részből áll. Az első használhassák a felhasználó közvetlenül az Azure AD B2C és a külső felek, például a Facebookhoz a felhasználó interakciók más üzletek foglalkozik. Az Azure AD B2C nem bírálja felül, és előfordulhat, hogy külső felek kell birtokában egyszeri bejelentkezési munkamenetek megkerülése. Ahelyett, hogy az útvonal az Azure AD B2C eléréséhez a külső fél van "megjegyzett", így nem kell a felhasználó számára a társadalombiztosítási vagy vállalati identitásszolgáltató reprompt. A végső SSO döntést a külső fél maradnak.

## <a name="how-does-it-work"></a>Hogyan működik?

Egyszeri bejelentkezési munkamenet felügyeleti ugyanazon az elven az egyéni házirendek egyéb technikai tanúsítványprofilként. Ha egy vezénylési lépés végrehajtása, a lépés társított műszaki profil kérnek egy `UseTechnicalProfileForSessionManagement` hivatkozás. Ha van ilyen, az egyszeri bejelentkezési munkamenet hivatkozott szolgáltató majd rendszer ellenőrzi, hogy a felhasználónak van-e a munkamenet résztvevő. Ha tehát az egyszeri bejelentkezési munkamenet szolgáltatót használja a tudja kezelni a munkamenet. Hasonlóképpen ha az orchestration lépés végrehajtása befejeződött, a szolgáltató használatos adattárolás a munkamenet, ha egy egyszeri bejelentkezési munkamenet-szolgáltató van megadva.

Az Azure AD B2C definiálva van egy egyszeri bejelentkezési munkamenet szolgáltatók használható száma:

* NoopSSOSessionProvider
* DefaultSSOSessionProvider
* ExternalLoginSSOSessionProvider
* SamlSSOSessionProvider

Egyszeri bejelentkezés felügyeleti osztályok megadott használatával a `<UseTechnicalProfileForSessionManagement ReferenceId=“{ID}" />` műszaki profil elemének.

### <a name="noopssosessionprovider"></a>NoopSSOSessionProvider

A név szerint, a szolgáltató nincs semmi hatása. Ez a szolgáltató hogyan kell figyelmen kívül SSO viselkedését egy adott műszaki profil használható.

### <a name="defaultssosessionprovider"></a>DefaultSSOSessionProvider

Ez a szolgáltató munkamenetben jogcímek tárolására használható. Ez a szolgáltató egy helyi kezelésére használt műszaki profil általában hivatkozik. 

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

A munkamenet jogcímek hozzáadásához használja a `<PersistedClaims>` a műszaki profil elemének. Ha a szolgáltatót használja a tudja kezelni a munkamenetet, a megőrzött jogcímeket hozzáadja a jogcímek tulajdonságcsomagjait. `<OutputClaims>` a jogcímeket kér le a munkamenet szolgál.

### <a name="externalloginssosessionprovider"></a>ExternalLoginSSOSessionProvider

Ezt a szolgáltatót arra, hogy letiltsa a "Válasszon identitásszolgáltató" képernyő szolgál. Egy külső identitásszolgáltatótól, például a Facebookhoz konfigurált műszaki profil általában hivatkozik rá. 

```XML
<TechnicalProfile Id="SM-SocialLogin">
    <DisplayName>Session Mananagement Provider</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.ExternalLoginSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

### <a name="samlssosessionprovider"></a>SamlSSOSessionProvider

Ezt a szolgáltatót a Azure AD B2C SAML-munkamenetek alkalmazások, valamint a külső SAML-alapú identitás-szolgáltatóktól közötti kezelésére használt.

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

A műszaki profil két metaadat-elemek szerepelnek:

| Elem | Alapértelmezett érték | Lehetséges értékek | Leírás
| --- | --- | --- | --- |
| IncludeSessionIndex | true | Igaz/hamis | A szolgáltató jelzi, hogy a munkamenet index kell tárolni. |
| RegisterServiceProviders | true | Igaz/hamis | Azt jelzi, hogy a szolgáltató minden SAML szolgáltatók, egy helyességi feltétel kiadott kell regisztrálni. |

Amikor a szolgáltató használata a SAML-identity provider munkamenetek tárolásához, a fenti mindkét értéke csak hamis lehet. A szolgáltató használata a B2C SAML-munkamenetek tárolásához, a fenti kell igaz vagy nincs megadva az alapértelmezett beállításokat teljesül.

>[!NOTE]
> SAML-munkamenet kijelentkezési igényel a `SessionIndex` és `NameID` befejezéséhez.

## <a name="next-steps"></a>További lépések

A Microsoft kedvelt visszajelzését és javaslatait! Ha bármilyen nehézségbe ütközik az ebben a témakörben, tegye fel Stack Overflow címkével ["azure-ad-b2c"](https://stackoverflow.com/questions/tagged/azure-ad-b2c). A funkciókra vonatkozó kérések szavazzon azokat a [visszajelzési fórumon](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).

