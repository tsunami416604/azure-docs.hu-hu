---
title: AD FS támogatás a Microsoft-hitelesítési tár .NET-es kódtárával |} Az Azure
description: Ismerje meg az Active Directory összevonási szolgáltatások (AD FS) támogatása a Microsoft-hitelesítési tár .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/03/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5963c5e83b5af3848edd934328caa1f095bd184
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66676728"
---
# <a name="active-directory-federation-services-support-in-msalnet"></a>Active Directory összevonási szolgáltatások támogatják az MSAL.NET
Az Active Directory összevonási szolgáltatások (AD FS) a Windows Server lehetővé teszi hozzáadni az OpenID Connect és az OAuth 2.0-alapú hitelesítés és -jához való alkalmazásokat fejleszt, és ezeket az alkalmazásokat kell hitelesíteni tudja a felhasználókat közvetlenül az AD FS. További információkért olvassa el [AD FS-forgatókönyvek fejlesztőknek](/windows-server/identity/ad-fs/overview/ad-fs-scenarios-for-developers).

Microsoft-hitelesítési tár .NET (MSAL.NET) két eseteket támogatja az AD FS hitelesítést:

- Az Azure Active Directoryhoz, mely maga MSAL.NET előadások van *összevont* az AD FS-sel.
- MSAL.NET előadások *közvetlenül* egy AD FS-szolgáltatónak, ahol az AD FS verziója OpenID Connect megfelelő (az AD FS a 2019-től). Az AD FS való közvetlen csatlakozás lehetőségét lehetővé teszi, hogy hitelesíteni futó alkalmazások MSAL.NET [Azure Stack](https://azure.microsoft.com/overview/azure-stack/).

## <a name="msal-connects-to-azure-ad-which-is-federated-with-ad-fs"></a>Az MSAL csatlakozik az Azure AD-ben össze van vonva az AD FS-sel
MSAL.NET támogatja az Azure AD-ben, amely felügyelt – a felhasználók (a felhasználó által felügyelt Azure AD-ben) be- vagy összevont felhasználók (a felhasználó egy másik identitásszolgáltató, például az AD FS kezeli). MSAL.NET nem tudja a tény, hogy összevont felhasználók kapcsolatban. Szerint van szó, ismerteti az Azure ad-hez.

A [hatóság](msal-client-application-configuration.md#authority) , ebben az esetben a rendszer a szokásos hatóság (gazdagép neve + bérlő, közös vagy szervezetek) használatát.

### <a name="acquiring-a-token-interactively"></a>Interaktív módon tokenbeolvasás
Meghívásakor a `AcquireTokenInteractive` módszer, a felhasználói élmény a következő általában:

1. A felhasználó megadja az azonosítót.
2. Az Azure AD rövid ideig a "Oldalának megnyitása a szervezet" jelenik meg.
3. Az identitásszolgáltató bejelentkezési oldala a rendszer átirányítja a felhasználót. A bejelentkezési oldalon az embléma a szervezet az általában testreszabott.

Ebben a forgatókönyvben összevont támogatott az AD FS-verziók a következők az AD FS v2-ben az AD FS v3-as (a Windows Server 2012 R2), és a v4 Active Directory összevonási szolgáltatások (AD FS 2016).

### <a name="acquiring-a-token-using-acquiretokenbyintegratedauthentication-or-acquiretokenbyusernamepassword"></a>AcquireTokenByIntegratedAuthentication vagy AcquireTokenByUsernamePassword egy token beszerzése
Való beszerzéséhez segítségével a `AcquireTokenByIntegratedAuthentication` vagy `AcquireTokenByUsernamePassword` módszerek MSAL.NET lekérdezi az identitásszolgáltató kapcsolatba felhasználónév alapján.  MSAL.NET kap egy [SAML 1.1-es jogkivonat](reference-saml-tokens.md) után vegye fel a kapcsolatot az identitásszolgáltató.  Az Azure AD egy felhasználó helyességi feltétel, majd biztosít az SAML-jogkivonat MSAL.NET (hasonló a [a alapú meghatalmazásos folyamat](msal-authentication-flows.md#on-behalf-of)) visszatéréshez jwt-t.

## <a name="msal-connects-directly-to-ad-fs"></a>Az MSAL közvetlenül kapcsolódik az AD FS
MSAL.NET támogatja az AD FS 2019, amely Open ID Connect megfelelő. Az AD FS való közvetlen csatlakozás lehetőségét, amikor létrehozza az alkalmazást használni szeretné a szolgáltató hasonlít a `https://mysite.contoso.com/adfs/`.

Jelenleg nem tervezzük támogatja a közvetlen kapcsolatot létesít az AD FS 2016 és az AD FS v2 (amelyek nem OpenID Connect megfelelő). Ha az AD FS 2016 közvetlen kapcsolatot igénylő forgatókönyvek támogatására van szüksége, használja legújabb verziójának [Azure Active Directory Authentication Library](active-directory-authentication-libraries.md#microsoft-supported-client-libraries). Az AD FS 2019 frissítette a helyszíni rendszer, amikor fogja tudni használni az MSAL.NET.
