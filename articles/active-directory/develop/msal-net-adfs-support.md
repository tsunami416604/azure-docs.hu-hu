---
title: AD FS támogatás a .NET-hez készült Microsoft Authentication Library-ben
titleSuffix: Microsoft identity platform
description: Ismerje meg a .NET-hez készült Microsoft Authentication Library (MSAL.NET) Active Directory összevonási szolgáltatások (AD FS) (AD FS) támogatását.
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/16/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 24f2d43455ff15089accc2463db83349abcea564
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802880"
---
# <a name="active-directory-federation-services-support-in-msalnet"></a>Active Directory összevonási szolgáltatások (AD FS) támogatás a MSAL.NET-ben
A Windows Server Active Directory összevonási szolgáltatások (AD FS) (AD FS) lehetővé teszi az OpenID Connect és a OAuth 2,0-alapú hitelesítés és engedélyezés hozzáadását a fejleszthető alkalmazásokhoz. Ezek az alkalmazások közvetlenül a AD FSon keresztül hitelesítik a felhasználókat. További információkért olvassa el [AD FS fejlesztői forgatókönyvek](/windows-server/identity/ad-fs/overview/ad-fs-scenarios-for-developers)című témakört.

A .NET-hez készült Microsoft Authentication Library (MSAL.NET) két forgatókönyvet támogat a AD FS való hitelesítéshez:

- A MSAL.NET Azure Active Directory, amely maga a AD FS *összevont* .
- A MSAL.NET **közvetlenül** egy ADFS-szolgáltatóhoz beszélnek. Ez csak AD FS 2019-es és újabb verziók esetén támogatott. Az alábbi forgatókönyvek egyike [Azure stack](https://azure.microsoft.com/overview/azure-stack/) támogatás


## <a name="msal-connects-to-azure-ad-which-is-federated-with-ad-fs"></a>A MSAL csatlakozik az Azure AD-hez, amely összevont AD FS
A MSAL.NET támogatja az Azure AD-hez való csatlakozást, amely a felügyelt felhasználók (az Azure AD-ben felügyelt felhasználók) vagy összevont felhasználók (más identitás-szolgáltató, például AD FS) által felügyelt felhasználók számára jelentkezik. A MSAL.NET nem tudja, hogy a felhasználók összevontak-e. A szóban forgó módon az Azure AD-t beszéli.

Az ebben az esetben [használt szolgáltató a](msal-client-application-configuration.md#authority) szokásos hatóság (szolgáltatói állomásnév + bérlő, közös vagy szervezet).

### <a name="acquiring-a-token-interactively"></a>Token interaktív beszerzése
A `AcquireTokenInteractive` metódus meghívásakor a felhasználói élmény általában:

1. A felhasználó megadja a fiók AZONOSÍTÓját.
2. Az Azure AD röviden megjeleníti a "saját szervezet lapja" üzenetet.
3. A rendszer átirányítja a felhasználót az Identitáskezelő bejelentkezési oldalára. A bejelentkezési oldal általában a szervezet emblémájának megfelelően van testreszabva.

Az összevont forgatókönyvben támogatott AD FS verziók a következők: AD FS v2, AD FS v3 (Windows Server 2012 R2) és AD FS v4 (AD FS 2016).

### <a name="acquiring-a-token-using-acquiretokenbyintegratedauthentication-or-acquiretokenbyusernamepassword"></a>Token beszerzése a AcquireTokenByIntegratedAuthentication vagy a AcquireTokenByUsernamePassword használatával
Ha a `AcquireTokenByIntegratedAuthentication` vagy `AcquireTokenByUsernamePassword` metódusok használatával szerez be tokent, a MSAL.NET a Felhasználónév alapján megkeresi az identitás-szolgáltatót.  A MSAL.NET [SAML 1,1-tokent](reference-saml-tokens.md) kap az identitás-szolgáltatóval való kapcsolatfelvétel után.  A MSAL.NET ezt követően az SAML-jogkivonatot az Azure AD-nek felhasználói kijelentésként (a [folyamathoz](msal-authentication-flows.md#on-behalf-of)hasonlóan) adja vissza egy JWT visszaszerzéséhez.

## <a name="msal-connects-directly-to-ad-fs"></a>A MSAL közvetlenül csatlakozik AD FS
A MSAL.NET támogatja a AD FS 2019-hez való csatlakozást, amely a nyitott azonosító csatlakozásnak megfelelő, és ismeri a PKCE és a hatóköröket. Ehhez a támogatáshoz a [4490481](https://support.microsoft.com/en-us/help/4490481/windows-10-update-kb4490481) -es szervizcsomagot kell alkalmazni a Windows Serverre. Közvetlenül a AD FShoz való csatlakozáskor az alkalmazás létrehozásához használni kívánt szolgáltató a `https://mysite.contoso.com/adfs/`hoz hasonló.

Jelenleg nincsenek a közvetlen kapcsolódást támogató csomagok:

- AD FS 16, mivel nem támogatja a PKCE, és továbbra is erőforrásokat használ, nem pedig a hatókört
- AD FS v2, amely nem OIDC-kompatibilis.

 Ha olyan forgatókönyveket kell támogatnia, amelyek közvetlen kapcsolódást igényelnek a AD FS 2016-hez, használja a [Azure Active Directory Authentication Library](active-directory-authentication-libraries.md#microsoft-supported-client-libraries)legújabb verzióját. Ha a helyszíni rendszert a 2019-es AD FSra frissítette, akkor használhatja a MSAL.NET-t.

## <a name="see-also"></a>Lásd még:

Az összevont esetért lásd: [Azure Active Directory bejelentkezési viselkedésének konfigurálása alkalmazáshoz egy otthoni tartomány felderítési házirendjének használatával](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-authentication-for-federated-users-portal)
