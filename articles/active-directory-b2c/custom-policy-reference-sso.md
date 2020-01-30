---
title: Egyszeri bejelentkezés munkamenet-kezelés egyéni szabályzatok használatával
titleSuffix: Azure AD B2C
description: Megtudhatja, hogyan kezelheti az egyszeri bejelentkezéses munkameneteket a Azure AD B2C egyéni házirendjeivel.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: ee32b13820cb50fc1649672b78b34e7e293d65b5
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76849083"
---
# <a name="single-sign-on-session-management-in-azure-active-directory-b2c"></a>Egyszeri bejelentkezés munkamenet-kezelés a Azure Active Directory B2C-ben

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Az egyszeri bejelentkezés (SSO) munkamenet-kezelője Azure Active Directory B2C (Azure AD B2C) lehetővé teszi a rendszergazda számára, hogy a felhasználó már hitelesített hitelesítése után vezérelje a felhasználókkal való interakciót. A rendszergazda például megadhatja, hogy megjelenjenek-e az identitás-szolgáltatók, vagy hogy meg kell-e adni a helyi fiók adatait. Ez a cikk a Azure AD B2C egyszeri bejelentkezéses beállításainak konfigurálását ismerteti.

Az egyszeri bejelentkezéses munkamenetek kezelése két részből áll. Az első a felhasználó interakcióit a Azure AD B2C és a másikkal együtt a külső felekkel, például a Facebooktal folytatott interakciókkal foglalkozik. Azure AD B2C nem bírál felül vagy mellőzi az SSO-munkameneteket, amelyeket külső felek is tárolhatnak. Ahelyett, hogy a külső fél felé irányuló, Azure AD B2C útvonalon áthaladó útvonalat "megjegyezték", a felhasználónak nem kell újrakérnie a felhasználót, hogy válassza ki a közösségi vagy vállalati identitás-szolgáltatót. A végső SSO-döntés a külső fél számára is fennáll.

Az egyszeri bejelentkezéses munkamenet-kezelés ugyanazt a szemantikai kapcsolatot használja, mint bármely más technikai profil az egyéni házirendekben. Egy előkészítési lépés végrehajtásakor a lépéshez társított technikai profil lekérdezése `UseTechnicalProfileForSessionManagement`. Ha van ilyen, a rendszer ellenőrzi a hivatkozott SSO munkamenet-szolgáltatót, hogy a felhasználó munkamenet-résztvevő-e. Ha igen, az SSO-munkamenet-szolgáltató a munkamenet újrafeltöltésére szolgál. Hasonlóképpen, ha egy előkészítési lépés végrehajtása befejeződött, a szolgáltató a munkamenetben lévő információk tárolására szolgál, ha meg van adva egy egyszeri bejelentkezéses munkamenet-szolgáltató.

Azure AD B2C több SSO munkamenet-szolgáltatót definiált:

* NoopSSOSessionProvider
* DefaultSSOSessionProvider
* ExternalLoginSSOSessionProvider
* SamlSSOSessionProvider

Az egyszeri bejelentkezéses felügyeleti osztályok a technikai profil `<UseTechnicalProfileForSessionManagement ReferenceId=“{ID}" />` eleme alapján vannak megadva.

## <a name="noopssosessionprovider"></a>NoopSSOSessionProvider

Ahogy a név diktálja, a szolgáltató nem tesz semmit. Ez a szolgáltató használható egy adott technikai profil SSO-viselkedésének letiltására.

## <a name="defaultssosessionprovider"></a>DefaultSSOSessionProvider

Ezt a szolgáltatót a jogcímek egy munkamenetben való tárolására lehet használni. Ez a szolgáltató általában a helyi fiókok kezeléséhez használt technikai profilban hivatkozik. Ha a DefaultSSOSessionProvider használatával tárolja a jogcímeket egy munkamenetben, gondoskodnia kell arról, hogy az alkalmazásnak visszaadott, az azt követő lépések során visszaadott jogcímek a munkamenetben legyenek tárolva, vagy kiegészítve legyenek a felhasználók profiljából beolvasott Directory. Ezzel biztosíthatja, hogy a hitelesítési útvonal nem fog meghiúsulni a hiányzó jogcímek esetében.

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

Ha jogcímeket szeretne felvenni a munkamenetbe, használja a technikai profil `<PersistedClaims>` elemét. Ha a szolgáltató a munkamenet újrafeltöltésére szolgál, a rendszer hozzáadja a megőrzött jogcímeket a jogcímek táskához. `<OutputClaims>` a jogcímek munkamenetből való beolvasására szolgál.

## <a name="externalloginssosessionprovider"></a>ExternalLoginSSOSessionProvider

Ez a szolgáltató a "személyazonossági szolgáltató választása" képernyő letiltására szolgál. Általában egy külső identitás-szolgáltatóhoz, például a Facebookhoz konfigurált technikai profil hivatkozik rá.

```XML
<TechnicalProfile Id="SM-SocialLogin">
    <DisplayName>Session Mananagement Provider</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.ExternalLoginSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="samlssosessionprovider"></a>SamlSSOSessionProvider

Ez a szolgáltató az alkalmazások és a külső SAML-szolgáltatók közötti SAML-munkamenetek Azure AD B2C kezelésére szolgál.

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

A technikai profilban két metaadat-elem található:

| Tétel | Alapértelmezett érték | Lehetséges értékek | Leírás
| --- | --- | --- | --- |
| IncludeSessionIndex | igaz | Igaz/hamis | Azt jelzi, hogy a szolgáltatónak tárolnia kell a munkamenet-indexet. |
| RegisterServiceProviders | igaz | Igaz/hamis | Azt jelzi, hogy a szolgáltatónak regisztrálnia kell az összes olyan SAML-szolgáltatót, amely kiállított egy állítást. |

Ha a szolgáltatót az SAML-identitás szolgáltatói munkamenetének tárolására használja, a fenti elemeknek is false értékűnek kell lenniük. A B2C SAML-munkamenet tárolására szolgáló szolgáltató használata esetén a fenti elemeknek igaznak kell lenniük, vagy el kell hagyni őket, mert az alapértelmezett értékek igazak. Az SAML-munkamenet kijelentkezéséhez szükség van a `SessionIndex`ra, és `NameID` a befejezéshez.

