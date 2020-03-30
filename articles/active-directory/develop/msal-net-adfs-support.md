---
title: AD FS támogatás MSAL.NET | Azure
titleSuffix: Microsoft identity platform
description: Az Active Directory összevonási szolgáltatások (AD FS) támogatásáról a Microsoft Authentication Library for .NET (MSAL.NET) alkalmazásban olvashat.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 15af18177cea217612a4d5276d130abe02d339f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77160760"
---
# <a name="active-directory-federation-services-support-in-msalnet"></a>Active Directory összevonási szolgáltatások támogatása MSAL.NET
A Windows Server Active Directory összevonási szolgáltatásai (AD FS) lehetővé teszik az OpenID Connect és az OAuth 2.0 alapú hitelesítés és engedélyezés hozzáadását a fejlesztett alkalmazásokhoz. Ezek az alkalmazások tehát közvetlenül az AD FS-en hitelesíthetik a felhasználókat. További információért olvassa el [az AD FS-forgatókönyvek fejlesztőknek című webhelyet.](/windows-server/identity/ad-fs/overview/ad-fs-openid-connect-oauth-flows-scenarios)

A Microsoft Authentication Library for .NET (MSAL.NET) két forgatókönyvet támogat az AD FS szolgáltatással való hitelesítéshez:

- MSAL.NET az Azure Active Directoryval beszél, amely maga is az AD FS-sel *van összeveteveve.*
- MSAL.NET **közvetlenül** az ADFS-hatósággal tárgyal. Ez csak az AD FS 2019-es és újabb akta. A legfontosabb forgatókönyvek egyike az [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) támogatása


## <a name="msal-connects-to-azure-ad-which-is-federated-with-ad-fs"></a>Az MSAL csatlakozik az Azure AD-hez, amely az AD FS-sel van összekötve
MSAL.NET támogatja az Azure AD-hez való csatlakozást, amely a felügyelt felhasználók (az Azure AD-ben kezelt felhasználók) vagy összevont felhasználók (egy másik identitásszolgáltató, például az AD FS által kezelt felhasználók) bejelentkezik. MSAL.NET nem tud arról a tényről, hogy a felhasználók összevannak egyezve. Ami azt illeti, az Azure AD-vel beszél.

Ebben [authority](msal-client-application-configuration.md#authority) az esetben a szokásos jogosultságot használja (a hatóság gazdagépneve + bérlő, közös vagy szervezet).

### <a name="acquiring-a-token-interactively"></a>Token interaktív beszerzése
A metódus `AcquireTokenInteractive` hívásakor a felhasználói élmény általában a következő:

1. A felhasználó megadja a fiókazonosítóját.
2. Az Azure AD röviden megjeleníti a "A szervezet oldalára viszi" üzenetet.
3. A felhasználó átlesz irányítva az identitásszolgáltató bejelentkezési oldalára. A bejelentkezési lap általában a szervezet emblémájával van testreszabva.

Ebben az összevont forgatókönyvben az AD FS v2, az AD FS v3 (Windows Server 2012 R2) és az AD FS v4 (AD FS 2016) támogatott AD FS-verziók.

### <a name="acquiring-a-token-using-acquiretokenbyintegratedauthentication-or-acquiretokenbyusernamepassword"></a>Token beolvasása AcquireTokenByIntegratedAuthentication vagy AcquireTokenByUsernamePassword használatával
Token beszerzésekor `AcquireTokenByIntegratedAuthentication` a `AcquireTokenByUsernamePassword` vagy metódusok, MSAL.NET leveszi az identitásszolgáltató kapcsolatba lépni a felhasználónév alapján.  MSAL.NET [saml 1.1-es jogkivonatot](reference-saml-tokens.md) kap, miután kapcsolatba lépett az identitásszolgáltatóval.  MSAL.NET ezután biztosítja az SAML-jogkivonatot az Azure AD-nek felhasználói állításként (hasonlóan a [folyamat nevében)](msal-authentication-flows.md#on-behalf-of)egy JWT-hez.

## <a name="msal-connects-directly-to-ad-fs"></a>Az MSAL közvetlenül csatlakozik az AD FS-hez
MSAL.NET támogatja az AD FS 2019-hez való csatlakozást, amely az Open ID Connect szabványnak megfelelő, és ismeri a PKCE-t és a hatóköröket. A támogatás hoz egy [KB 4490481](https://support.microsoft.com/en-us/help/4490481/windows-10-update-kb4490481) kb. Amikor közvetlenül csatlakozik az AD FS-hez, az alkalmazás létrehozásához `https://mysite.contoso.com/adfs/`használni kívánt jogosultság hasonló a hoz.

Jelenleg nem tervezi, hogy támogassa a közvetlen kapcsolatot:

- AD FS 16, mivel nem támogatja a PKCE-t, és továbbra is erőforrásokat használ, nem hatókört
- AD FS v2, amely nem oidc-kompatibilis.

 Ha támogatnia kell az Okat 2016-tal közvetlen kapcsolatot igénylő forgatókönyveket, használja az [Azure Active Directory hitelesítési könyvtárlegújabb](../azuread-dev/active-directory-authentication-libraries.md#microsoft-supported-client-libraries)verzióját. Miután frissítette a helyszíni rendszert az AD FS 2019-re, használhatja MSAL.NET.

## <a name="next-steps"></a>További lépések

Az összevont esetről az [Azure Active Directory bejelentkezési viselkedésének konfigurálása egy alkalmazáshoz home realm felderítési szabályzat használatával című](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-authentication-for-federated-users-portal) témakörben található.
