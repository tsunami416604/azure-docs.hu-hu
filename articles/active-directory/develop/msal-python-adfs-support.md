---
title: Az Azure AD FS támogatása (MSAL Python)
titleSuffix: Microsoft identity platform
description: Tudnivalók az Active Directory összevonási szolgáltatások (AD FS) támogatásáról a Microsoft Authentication Library for Python szolgáltatásban
services: active-directory
author: abhidnya13
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/23/2019
ms.author: abpati
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 01d4cb626aabc83117e864b75b49eec63a6c0af0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76699546"
---
# <a name="active-directory-federation-services-support-in-msal-for-python"></a>Az Active Directory összevonási szolgáltatások támogatása az MSAL for Python ban

Az Active Directory összevonási szolgáltatások (AD FS) a Windows Server rendszerben lehetővé teszi az OpenID Connect és az OAuth 2.0 alapú hitelesítés és engedélyezés hozzáadását az alkalmazásokhoz a Microsoft Authentication Library (MSAL) for Python használatával. Az MSAL python-kódtár használatával az alkalmazás közvetlenül az AD FS-en keresztül hitelesítheti a felhasználókat. A forgatókönyvekről az [AD FS-forgatókönyvek fejlesztőknek](/windows-server/identity/ad-fs/ad-fs-development)című témakörben talál további információt.

Az AD FS-hez való hitelesítésnek általában két módja van:

- Az MSAL Python az Azure Active Directoryval beszél, amely maga is más identitásszolgáltatókkal van összeegyezve. Az összevonás az AD FS-en keresztül történik. Az MSAL Python csatlakozik az Azure AD-hez, amely az Azure AD-ben (felügyelt felhasználók) vagy egy másik identitásszolgáltató, például az AD FS (összevont felhasználók) által felügyelt felhasználókat ír alá. Az MSAL Python nem tudja, hogy a felhasználó össze van-e minősítve. Egyszerűen beszél az Azure AD.It simply talks to Azure AD. Ebben [authority](msal-client-application-configuration.md#authority) az esetben a szokásos jogosultságot használja (a hatóság gazdagépneve + bérlő, közös vagy szervezet).
- Az MSAL Python közvetlenül egy AD FS-jogosultsággal beszél. Ezt csak az AD FS 2019 és újabb verziói támogatják.

## <a name="connect-to-active-directory-federated-with-ad-fs"></a>Csatlakozás az Active Directoryhoz az AD FS szolgáltatással összevont

### <a name="acquire-a-token-interactively-for-a-federated-user"></a>Jogkivonat interaktív beszerzése összevont felhasználó számára

Az alábbiak vonatkoznak arra, hogy közvetlenül az Active Directory összevonási szolgáltatásokhoz (AD FS) vagy az Active Directoryn keresztül csatlakozik.

Amikor felhívja `acquire_token_by_authorization_code` `acquire_token_by_device_flow`a vagy a , a felhasználói élmény általában a következő:

1. A felhasználó megadja a fiókazonosítóját.
2. Az Azure AD röviden megjeleníti a "A szervezet oldalára való átirányítás" üzenetet, és a felhasználó átlesz irányítva az identitásszolgáltató bejelentkezési oldalára. A bejelentkezési lap általában a szervezet emblémájával van testreszabva.

Ebben az összevont forgatókönyvben a támogatott AD FS-verziók a következők:
- Active Directory összevonási szolgáltatások FS v2
- Active Directory összevonási szolgáltatások 3.
- Active Directory összevonási szolgáltatások 4- es v4-es (AD FS 2016)

### <a name="acquire-a-token-via-username-and-password"></a>Token beszerzése felhasználónévvel és jelszóval

Az alábbiak vonatkoznak arra, hogy közvetlenül az Active Directory összevonási szolgáltatásokhoz (AD FS) vagy az Active Directoryn keresztül csatlakozik.

Amikor beszerez egy `acquire_token_by_username_password`jogkivonatot a használatával, az MSAL Python leveszi az identitásszolgáltatót, hogy kapcsolatba lépjen a felhasználónév alapján. MSAL Python lekérést kap egy [SAML 1.1-es jogkivonatot](reference-saml-tokens.md) az identitásszolgáltatótól, amelyezután biztosítja az Azure AD, amely a JSON webtoken (JWT) ad vissza.

## <a name="connecting-directly-to-ad-fs"></a>Csatlakozás közvetlenül az AD FS-hez

Amikor a könyvtárat az AD FS-hez csatlakoztatja, az alkalmazás létrehozásához használni kívánt jogosultság a hasonló lesz:`https://somesite.contoso.com/adfs/`

Az MSAL Python támogatja az ADFS 2019-et.

Nem támogatja az ADFS 2016-hoz vagy az ADFS 2-es v2-hez való közvetlen kapcsolatot. Ha olyan forgatókönyveket kell támogatnia, amelyek közvetlen kapcsolatot igényelnek az ADFS 2016-tal, használja az ADAL Python legújabb verzióját. Miután frissítette a helyszíni rendszert az ADFS 2019-re, használhatja az MSAL Pythont.

## <a name="next-steps"></a>További lépések

- Az összevont esetről az [Azure Active Directory bejelentkezési viselkedésének konfigurálása egy alkalmazáshoz home realm felderítési szabályzat használatával című](../manage-apps/configure-authentication-for-federated-users-portal.md) témakörben található.