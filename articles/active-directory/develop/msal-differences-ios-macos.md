---
title: MSAL iOS & macOS különbségekhez | Azure
titleSuffix: Microsoft identity platform
description: A cikk a Microsoft Authentication Library (MSAL) iOS és macOS közötti használati különbségeit ismerteti.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: 62b79ee7398286b8e6c8ed8612bd001595e1f6ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084978"
---
# <a name="microsoft-authentication-library-for-ios-and-macos-differences"></a>Microsoft Authentication Library iOS és macOS rendszerekre

Ez a cikk az iOS és a macOS rendszerhez szükséges Microsoft authentication library (MSAL) közötti működési különbségeket ismerteti.

> [!NOTE]
> Macen az MSAL csak a macOS alkalmazásokat támogatja.

## <a name="general-differences"></a>Általános különbségek

A macOS msal az iOS-hez elérhető funkciók egy része.

A macOS msal nem támogatja:

- különböző böngészőtípusok, `ASWebAuthenticationSession` `SFAuthenticationSession`mint `SFSafariViewController`például a , , .
- a Microsoft Authenticator alkalmazáson keresztül közvetített hitelesítés nem támogatott a macOS rendszerben.

Az azonos közzétevőtől származó alkalmazások közötti kulcskarika-megosztás korlátozottabb a macOS 10.14-es és korábbi verziója intőbb. A [hozzáférés-vezérlési listák segítségével](https://developer.apple.com/documentation/security/keychain_services/access_control_lists?language=objc) megadhatja a kulcskarikát megosztó alkalmazások elérési útjait. A felhasználó további kulcskarika-utasításokat is láthat.

A macOS 10.15+ rendszerben az MSAL viselkedése megegyezik az iOS és a macOS között. Az MSAL [kulcskarika-hozzáférési csoportokat](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc) használ a kulcskarika-megosztáshoz. 

### <a name="conditional-access-authentication-differences"></a>Feltételes hozzáférés hitelesítési különbségei

Feltételes hozzáférési forgatókönyvek esetén kevesebb felhasználói kérdés jelenik meg az MSAL iOS-hez való használatakor. Ennek az az oka, hogy az iOS a bróker alkalmazást (Microsoft Authenticator) használja, amely bizonyos esetekben tagadja a felhasználó rákérdezésének szükségességét.

### <a name="project-setup-differences"></a>A projektek beállítási különbségei

**Macos**

- Amikor macOS rendszeren állítja be a projektet, győződjön meg arról, hogy az alkalmazás érvényes fejlesztési vagy gyártási tanúsítvánnyal van aláírva. Az MSAL továbbra is az aláíratlan módban működik, de a gyorsítótár-megőrzés tekintetében másképp fog működni. Az alkalmazást csak aláíratlanul kell futtatni hibakeresési célokra. Ha az alkalmazást aláíratlanul terjeszti, a következőket teszi:
1. 10.14-kor és korábban az MSAL minden alkalommal kéri a felhasználótól a kulcskarika jelszavát, amikor újraindítja az alkalmazást.
2. A 10.15+, MSAL kérni fogja a felhasználó hitelesítő adatokat minden jogkivonat-beszerzés. 

- A macOS-alkalmazásoknak nem kell megvalósítaniuk az AppDelegate-hívást.

**iOS**

- További lépések a projekt beállítása a hitelesítési közvetítői folyamat támogatásához. A lépések hívják ki a tutorial.
- Az iOS-projekteknek egyéni sémákat kell regisztrálniuk az info.plist oldalon. Ez nem kötelező a macOS rendszeren.
