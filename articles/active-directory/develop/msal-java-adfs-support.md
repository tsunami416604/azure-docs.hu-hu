---
title: AD FS támogatás (MSAL Javához)
titleSuffix: Microsoft identity platform
description: Ismerkedjen meg Active Directory összevonási szolgáltatások (AD FS) (AD FS) támogatással a Javához készült Microsoft Authentication Library (MSAL4j) szolgáltatásban.
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
ms.custom: aaddev, devx-track-java
ms.openlocfilehash: 5d01d1143563637e21aaa06a3f997c1507e4d8f1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88114778"
---
# <a name="active-directory-federation-services-support-in-msal-for-java"></a>Active Directory összevonási szolgáltatások (AD FS) támogatás a MSAL Javához

A Windows Server Active Directory összevonási szolgáltatások (AD FS) (AD FS) lehetővé teszi az OpenID Connect és a OAuth 2,0-alapú hitelesítés hozzáadását, valamint a Java (MSAL for Java) alkalmazáshoz való Microsoft-hitelesítési kódtár engedélyezését. Az integrációt követően az alkalmazás az Azure AD-n keresztül összevont AD FS felhasználók hitelesítésére is képes. További információ a forgatókönyvekről: [AD FS forgatókönyvek fejlesztőknek](/windows-server/identity/ad-fs/ad-fs-development).

A Javához készült MSAL-t használó alkalmazások Azure Active Directory (Azure AD) szolgáltatással kommunikálnak, amely aztán a AD FS összevonja.

A MSAL for Java az Azure ad-hez csatlakozik, amely az Azure AD-ben (felügyelt felhasználók) felügyelt felhasználók vagy más identitás-szolgáltató (például AD FS (összevont felhasználók) által felügyelt felhasználók számára jelentkezik be. A Java-MSAL nem tudja, hogy a felhasználó összevont. Egyszerűen beszél az Azure AD-ről.

Az ebben az esetben [használt szolgáltató a](msal-client-application-configuration.md#authority) szokásos hatóság (szolgáltatói állomásnév + bérlő, közös vagy szervezet).

## <a name="acquire-a-token-interactively-for-a-federated-user"></a>Jogkivonat interaktív beszerzése egy összevont felhasználó számára

A (z `ConfidentialClientApplication.AcquireToken()` `PublicClientApplication.AcquireToken()` ) vagy a vagy a (z) hívásakor `AuthorizationCodeParameters` `DeviceCodeParameters` a felhasználói élmény általában:

1. A felhasználó megadja a fiók AZONOSÍTÓját.
2. Az Azure AD röviden megjeleníti a "a szervezet oldalát", a felhasználó pedig átirányítja az identitás-szolgáltató bejelentkezési lapjára. A bejelentkezési oldal általában a szervezet emblémájának megfelelően van testreszabva.

Az összevont forgatókönyvben támogatott AD FS verziók a következők:
- Active Directory összevonási szolgáltatások (AD FS) FS v2
- Active Directory összevonási szolgáltatások (AD FS) v3 (Windows Server 2012 R2)
- Active Directory összevonási szolgáltatások (AD FS) v4 (AD FS 2016)

## <a name="acquire-a-token-via-username-and-password"></a>Token beszerzése Felhasználónév és jelszó használatával

Ha a vagy a vagy a használatával szerez be tokent `ConfidentialClientApplication.AcquireToken()` `PublicClientApplication.AcquireToken()` , a MSAL for Java beolvassa `IntegratedWindowsAuthenticationParameters` `UsernamePasswordParameters` az identitás-szolgáltatót a Felhasználónév alapján. A MSAL for Java egy [SAML 1,1 token](reference-saml-tokens.md) -tokent kap az identitás-szolgáltatótól, amelyet aztán az Azure ad-nek ad vissza, amely visszaadja a JSON web token (JWT).

## <a name="next-steps"></a>Következő lépések

Az összevont esetért lásd: [Azure Active Directory bejelentkezési viselkedésének konfigurálása alkalmazáshoz egy otthoni tartomány felderítési házirendjének használatával](../manage-apps/configure-authentication-for-federated-users-portal.md)