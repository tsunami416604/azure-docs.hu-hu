---
title: Webes API-kat meghívó mobil alkalmazás – áttekintés | Microsoft Identity platform
description: Ismerje meg, hogyan hozhat létre webes API-kat meghívó mobil alkalmazást (áttekintés)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: f67f063ab374dec8bac3905ea46f1dfa9d8ed1fb
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68852603"
---
# <a name="scenario-mobile-application-that-calls-web-apis"></a>Forgatókönyv: Webes API-kat meghívó mobil alkalmazás

Ismerje meg a webes API-kat meghívó Mobile-alkalmazások készítéséhez szükséges tudnivalókat.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Első lépések

Hozza létre az első mobil alkalmazást, és próbálja ki a gyors üzembe helyezést!

> [!div class="nextstepaction"]
> [Rövid útmutató: Token beszerzése és Microsoft Graph API meghívása Android-alkalmazásból](./quickstart-v2-android.md)
>
> [Rövid útmutató: Token beszerzése és Microsoft Graph API meghívása iOS-alkalmazásból](./quickstart-v2-ios.md)
>
> [Rövid útmutató: Token beszerzése és Microsoft Graph API meghívása egy Xamarin iOS-& Android-alkalmazásból](https://github.com/Azure-Samples/active-directory-xamarin-native-v2)

## <a name="overview"></a>Áttekintés

A mobil alkalmazások esetében elengedhetetlen a személyre szabott, zökkenőmentes felhasználói élmény.  A Microsoft Identity platform lehetővé teszi a mobil fejlesztők számára, hogy az iOS-és Android-felhasználók számára is létrehozzák az élményt. Az alkalmazás bejelentkezhet Azure Active Directory (Azure AD) felhasználókba, személyes Microsoft-fiók felhasználókba és Azure AD B2C felhasználókba, és jogkivonatokat kérhet a webes API-k nevében történő meghívásához. Ezeknek a folyamatoknak a megvalósításához a Microsoft Authentication Library (MSAL) eszközt fogjuk használni, amely megvalósítja az iparági szabványnak megfelelő [OAuth 2.0](v2-oauth2-auth-code-flow.md)-s engedélyezési programkódot.

![Démonalkalmazások](./media/scenarios/mobile-app.svg)

A Mobile apps szempontjai:

- **A felhasználói élmény kulcsfontosságú**: Annak engedélyezése, hogy a felhasználók megtekintsék az alkalmazás értékét, mielőtt bejelentkeznek a bejelentkezésre, és csak a szükséges engedélyeket kérik.
- **Az összes felhasználói konfiguráció támogatása**: Számos mobil üzleti felhasználó a feltételes hozzáférés és az eszköz megfelelőségi szabályzatai alatt található. Ügyeljen arra, hogy támogassa ezeket a kulcsfontosságú forgatókönyveket.
- **Egyszeri bejelentkezés (SSO) implementálása**: A MSAL és a Microsoft Identity platform lehetővé teszi az egyszeri bejelentkezés egyszerű engedélyezését az eszköz böngészőjén vagy a Microsoft Authenticatoron (és az Androidon Intune Céges portál).

## <a name="specifics"></a>Sajátosságai

Vegye figyelembe ezeket a szempontokat, amikor a Microsoft Identity platformon létrehoz egy Mobile alkalmazást:

- A platformtól függően előfordulhat, hogy egyes felhasználói beavatkozásokra szükség lehet az első alkalommal, amikor a felhasználók bejelentkeznek. Az iOS esetében például az alkalmazások számára a felhasználói interakció megjelenítésére van szükség, amikor az egyszeri bejelentkezést használja Microsoft Authenticator (és Intune Céges portál az Android rendszeren).
- Az iOS és az Android rendszereken a MSAL külső böngészőt használhatnak (amely az alkalmazás tetején jelenhet meg), hogy bejelentkezzen a felhasználókba. A konfigurációt testre szabhatja alkalmazáson belüli webnézetekben való használatra.
- Soha ne használjon titkos kulcsot a mobil alkalmazásokban. Minden felhasználó számára elérhető lesz.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Alkalmazásregisztráció](scenario-mobile-app-registration.md)
