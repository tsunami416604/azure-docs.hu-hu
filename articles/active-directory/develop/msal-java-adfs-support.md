---
title: AD FS támogatás (MSAL Java-hoz)
titleSuffix: Microsoft identity platform
description: Információ az Active Directory összevonási szolgáltatások (AD FS) támogatásáról a Microsoft Authentication Library for Java (MSAL4j) alkalmazásban.
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/21/2019
ms.author: sagonzal
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 3d834a8d1524595304c22fed9897094622dfd93f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696214"
---
# <a name="active-directory-federation-services-support-in-msal-for-java"></a>Az Active Directory összevonási szolgáltatások támogatása az MSAL for Java szolgáltatásban

A Windows Server Active Directory összevonási szolgáltatásai (AD FS) lehetővé teszik az OpenID Connect és az OAuth 2.0 alapú hitelesítés és engedélyezés hozzáadását a Microsoft Authentication Library for Java (MSAL for Java) alkalmazáshoz. Az integrálás után az alkalmazás hitelesítheti a felhasználókat az AD FS-ben, az Azure AD-n keresztül összevont módon. A forgatókönyvekről az [AD FS-forgatókönyvek fejlesztőknek](/windows-server/identity/ad-fs/ad-fs-development)című témakörben talál további információt.

Az MSAL java-hoz használó alkalmazás az Azure Active Directoryval (Azure AD) fog beszélni, amely ezután az AD FS-be kerül.

Az MSAL for Java az Azure AD-hez csatlakozik, amely az Azure AD-ben (felügyelt felhasználók) vagy egy másik identitásszolgáltató, például az AD FS (összevont felhasználók) által felügyelt felhasználókat ír alá. Az MSAL java-hoz nem tudja, hogy a felhasználó össze van-e véve. Egyszerűen beszél az Azure AD.It simply talks to Azure AD.

Ebben [authority](msal-client-application-configuration.md#authority) az esetben a szokásos jogosultságot használja (a hatóság gazdagépneve + bérlő, közös vagy szervezet).

## <a name="acquire-a-token-interactively-for-a-federated-user"></a>Jogkivonat interaktív beszerzése összevont felhasználó számára

Amikor `ConfidentialClientApplication.AcquireToken()` telefonál, `PublicClientApplication.AcquireToken()` `AuthorizationCodeParameters` vagy `DeviceCodeParameters`a vagy, a felhasználói élmény általában:

1. A felhasználó megadja a fiókazonosítóját.
2. Az Azure AD röviden megjeleníti a "A szervezet oldalára vétele" lehetőséget, és a felhasználó átlesz irányítva az identitásszolgáltató bejelentkezési oldalára. A bejelentkezési lap általában a szervezet emblémájával van testreszabva.

Ebben az összevont forgatókönyvben a támogatott AD FS-verziók a következők:
- Active Directory összevonási szolgáltatások FS v2
- Active Directory összevonási szolgáltatások 3.
- Active Directory összevonási szolgáltatások 4- es v4-es (AD FS 2016)

## <a name="acquire-a-token-via-username-and-password"></a>Token beszerzése felhasználónévvel és jelszóval

Amikor beszerez egy `ConfidentialClientApplication.AcquireToken()` `PublicClientApplication.AcquireToken()` jogkivonatot a használatával `IntegratedWindowsAuthenticationParameters` `UsernamePasswordParameters`vagy a , MSAL java lekéri az identitásszolgáltató t a felhasználónév alapján. MSAL Java lekérést kap egy [SAML 1.1 jogkivonatot](reference-saml-tokens.md) az identitásszolgáltatótól, amelyezután biztosítja az Azure AD, amely visszaadja a JSON webtoken (JWT).

## <a name="next-steps"></a>További lépések

Az összevont esetről az [Azure Active Directory bejelentkezési viselkedésének konfigurálása egy alkalmazáshoz home realm felderítési szabályzat használatával című](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-authentication-for-federated-users-portal) témakörben található.