---
title: Webes API-kat meghívó Mobile-alkalmazás létrehozása | Azure
titleSuffix: Microsoft identity platform
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
ms.openlocfilehash: c6ebeed4a7806c013205d01621107ef65655e753
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74965466"
---
# <a name="scenario-mobile-application-that-calls-web-apis"></a>Forgatókönyv: webes API-kat meghívó mobil alkalmazás

Megtudhatja, hogyan hozhat létre webes API-kat meghívó mobil alkalmazást.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Bevezetés

Hozza létre az első mobil alkalmazást, és próbálja ki a gyors üzembe helyezést.

> [!div class="nextstepaction"]
> [Gyors útmutató: token beszerzése és Microsoft Graph API meghívása Android-alkalmazásból](./quickstart-v2-android.md)
>
> [Gyors útmutató: token beszerzése és Microsoft Graph API meghívása iOS-alkalmazásból](./quickstart-v2-ios.md)
>
> [Gyors útmutató: token beszerzése és Microsoft Graph API meghívása egy Xamarin iOS-& Android-alkalmazásból](https://github.com/Azure-Samples/active-directory-xamarin-native-v2)

## <a name="overview"></a>Áttekintés

A mobil alkalmazások esetében elengedhetetlen a személyre szabott, zökkenőmentes felhasználói élmény.  A Microsoft Identity platform lehetővé teszi a mobil fejlesztők számára, hogy az iOS-és Android-felhasználók számára is létrehozzák az élményt. Az alkalmazás bejelentkezhet Azure Active Directory (Azure AD) felhasználókba, személyes Microsoft-fiók felhasználókba és Azure AD B2C felhasználókba, és jogkivonatokat kérhet a webes API-k nevében történő meghívásához. Ezeknek a folyamatoknak a megvalósításához a Microsoft Authentication Library (MSAL) eszközt fogjuk használni, amely megvalósítja az iparági szabványnak megfelelő [OAuth 2.0-s engedélyezési programkódot](v2-oauth2-auth-code-flow.md).

![Démonalkalmazások](./media/scenarios/mobile-app.svg)

A Mobile apps szempontjai:

- A **felhasználói élmény kulcsfontosságú**: lehetővé teszi, hogy a felhasználók a bejelentkezés előtt megtekintsék az alkalmazás értékét, és csak a szükséges engedélyeket kérjenek.
- **Az összes felhasználói konfiguráció támogatása**: sok mobil üzleti felhasználó a feltételes hozzáférés és az eszköz megfelelőségi szabályzatai alatt található. Ügyeljen arra, hogy támogassa ezeket a kulcsfontosságú forgatókönyveket.
- **Egyszeri bejelentkezés (SSO) implementálása**: a MSAL és a Microsoft Identity platform lehetővé teszi az egyszeri bejelentkezés engedélyezését az eszköz böngészőjében vagy a Microsoft Authenticator (és Intune céges portál Android rendszeren).

## <a name="specifics"></a>Sajátosságai

Vegye figyelembe ezeket a szempontokat, amikor a Microsoft Identity platformon létrehoz egy Mobile alkalmazást:

- A platformtól függően előfordulhat, hogy egyes felhasználói beavatkozásokra szükség lehet az első alkalommal, amikor a felhasználók bejelentkeznek. Az iOS esetében például az alkalmazások számára a felhasználói interakció megjelenítésére van szükség, amikor az egyszeri bejelentkezést használja Microsoft Authenticator (és Intune Céges portál az Android rendszeren).
- Az iOS és az Android rendszereken a MSAL külső böngészőt használhatnak (amely az alkalmazás tetején jelenhet meg), hogy bejelentkezzen a felhasználókba. A konfigurációt testre szabhatja alkalmazáson belüli webnézetekben való használatra.
- Soha ne használjon titkos kulcsot a mobil alkalmazásokban. Minden felhasználó számára elérhető lesz.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Alkalmazásregisztráció](scenario-mobile-app-registration.md)
