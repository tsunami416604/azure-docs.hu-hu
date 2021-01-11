---
title: Webes API-kat meghívó Mobile-alkalmazás létrehozása | Azure
titleSuffix: Microsoft identity platform | Azure
description: Ismerje meg, hogyan hozhat létre webes API-kat meghívó mobil alkalmazást (áttekintés)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: f60e21f98092df65ef01e68fe9c2e64833a24855
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2021
ms.locfileid: "98063212"
---
# <a name="scenario-mobile-application-that-calls-web-apis"></a>Forgatókönyv: webes API-kat meghívó mobil alkalmazás

Megtudhatja, hogyan hozhat létre webes API-kat meghívó mobil alkalmazást.

## <a name="getting-started"></a>Első lépések

Ha még nem tette meg, hozza létre első alkalmazását egy rövid útmutató elvégzésével:

- [Gyors útmutató: token beszerzése és Microsoft Graph API meghívása Android-alkalmazásból](./quickstart-v2-android.md)
- [Gyors útmutató: token beszerzése és Microsoft Graph API meghívása iOS-alkalmazásból](./quickstart-v2-ios.md)
- Gyors útmutató [: token beszerzése és Microsoft Graph API meghívása egy Xamarin iOS-és Android-alkalmazásból](https://github.com/Azure-Samples/active-directory-xamarin-native-v2) (GitHub)

## <a name="overview"></a>Áttekintés

A mobil alkalmazások esetében elengedhetetlen a személyre szabott, zökkenőmentes felhasználói élmény.  A Microsoft Identity platform lehetővé teszi a mobil fejlesztők számára, hogy az iOS-és Android-felhasználók számára is létrehozzák az élményt. Az alkalmazás bejelentkezhet Azure Active Directory (Azure AD) felhasználókba, személyes Microsoft-fiók felhasználókba és Azure AD B2C felhasználókba. Emellett jogkivonatokat is beszerezzen a webes API-k nevében történő meghívásához. Ezeknek a folyamatoknak a megvalósításához a Microsoft hitelesítési függvénytárát (MSAL) fogjuk használni. A MSAL az iparági szabványnak megfelelő [OAuth 2.0 engedélyezési kódját](v2-oauth2-auth-code-flow.md)valósítja meg.

![Démonalkalmazások](./media/scenarios/mobile-app.svg)

A Mobile apps szempontjai:

- A **felhasználói élmény kulcsfontosságú**: lehetővé teszi, hogy a felhasználók a bejelentkezés előtt lássák az alkalmazás értékét. Csak a szükséges engedélyeket kérje.
- **Az összes felhasználói konfiguráció támogatása**: sok mobil üzleti felhasználónak be kell tartania a feltételes hozzáférési házirendeket és az eszköz-megfelelőségi szabályzatokat. Ügyeljen arra, hogy támogassa ezeket a kulcsfontosságú forgatókönyveket.
- **Egyszeri bejelentkezés (SSO) implementálása**: a MSAL és a Microsoft Identity platform használatával engedélyezheti az egyszeri bejelentkezést az eszköz böngészőjében vagy Microsoft Authenticator (és az Androidon is Intune céges portál).
- **Megosztott eszköz mód implementálása**: lehetővé teszi, hogy alkalmazása megosztott eszközökön, például kórházakban, a gyártásban, a kereskedelemben és a pénzügyben is használható legyen. [További információ a megosztott eszköz üzemmódjának támogatásáról](msal-shared-devices.md).

## <a name="specifics"></a>Sajátosságai

Vegye figyelembe a következő szempontokat, amikor a Microsoft Identity platformon létrehoz egy Mobile alkalmazást:

- A platformtól függően előfordulhat, hogy egyes felhasználói beavatkozásokra akkor van szükség, amikor a felhasználók először jelentkeznek be. Az iOS esetében például az alkalmazások számára a felhasználói interakció megjelenítésére van szükség, amikor az egyszeri bejelentkezést az első alkalommal használja Microsoft Authenticator (és Intune Céges portál az Android rendszeren).
- Az iOS és az Android rendszereken a MSAL külső böngésző használatával jelentkezhet be a felhasználókba. Előfordulhat, hogy a külső böngésző az alkalmazás tetején jelenik meg.
- Soha ne használjon titkos kulcsot a mobil alkalmazásokban. Ezekben az alkalmazásokban a titkok minden felhasználó számára elérhetők.

## <a name="recommended-reading"></a>Ajánlott olvasás

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>Következő lépések

Lépjen tovább a forgatókönyv következő cikkére, amely az [alkalmazás regisztrálását](scenario-mobile-app-registration.md)ismerteti.
