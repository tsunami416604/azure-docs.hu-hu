---
title: Webes API-kat megnevező mobilalkalmazás létrehozása | Azure
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogy miként hozhat létre webes API-kat meghívja mobilalkalmazást (áttekintés)
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
ms.openlocfilehash: 6675d67299091325fcc3e12572a906716bf5b88d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132416"
---
# <a name="scenario-mobile-application-that-calls-web-apis"></a>Eset: Webes API-kat meghívjaó mobilalkalmazás

További információ a webes API-kat meghívjaó mobilalkalmazás létrehozásáról.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Első lépések

Hozza létre első mobilalkalmazását, és próbálja ki a rövid útmutatót.

> [!div class="nextstepaction"]
> [Rövid útmutató: Jogkivonat beszerzése és Microsoft Graph API hívása Android-alkalmazásból](./quickstart-v2-android.md)
>
> [Rövid útmutató: Jogkivonat beszerzése és microsoft graph API hívása iOS-alkalmazásból](./quickstart-v2-ios.md)
>
> [Rövid útmutató: Token beszerzése és a Microsoft Graph API hívása Xamarin iOS és Android alkalmazásból](https://github.com/Azure-Samples/active-directory-xamarin-native-v2)

## <a name="overview"></a>Áttekintés

A mobilalkalmazások számára elengedhetetlen a személyre szabott, zökkenőmentes felhasználói élmény.  A Microsoft identity platform lehetővé teszi a mobilfejlesztők számára, hogy ezt a felhasználói élményt iOS és Android felhasználók számára hozzák létre. Az alkalmazás az Azure Active Directory (Azure AD) felhasználóiban, a személyes Microsoft-fiók-felhasználókban és az Azure AD B2C-felhasználókban is bejelentkezhet. Azt is beszerezheti a jogkivonatokat, hogy hívja meg a webes API-t a nevükben. Ezeknek a folyamatoknak a megvalósításához a Microsoft Authentication Library (MSAL) programot használjuk. Az MSAL megvalósítja az [OAuth2.0 engedélyezési kód folyamatát.](v2-oauth2-auth-code-flow.md)

![Démonalkalmazások](./media/scenarios/mobile-app.svg)

A mobilalkalmazásokkal kapcsolatos szempontok:

- **A felhasználói élmény kulcsfontosságú:** Lehetővé teheti a felhasználók számára, hogy a bejelentkezés előtt lássák az alkalmazás értékét. Csak a szükséges engedélyeket kérje.
- **Az összes felhasználói konfiguráció támogatása:** Sok mobilüzleti felhasználónak be kell tartania a feltételes hozzáférési szabályzatokat és az eszközmegfelelőségi szabályzatokat. Ügyeljen arra, hogy támogassa ezeket a kulcsfontosságú forgatókönyveket.
- **Egyszeri bejelentkezés (SSO) megvalósítása:** Az MSAL és a Microsoft identity platform használatával engedélyezheti az egyszeri bejelentkezést az eszköz böngészőjében vagy a Microsoft Authenticatoron (és az Intune Vállalati portálon Androidon).

## <a name="specifics"></a>Sajátosságai

A Microsoft identitásplatformon mobilalkalmazás létrehozásakor tartsa szem előtt az alábbi szempontokat:

- A platformtól függően előfordulhat, hogy a felhasználók első bejelentkezésekor bizonyos felhasználói beavatkozásra van szükség. Az iOS-nek például meg kell mutatnia a felhasználói beavatkozást, amikor először használják az Egyszeri bejelentkezést a Microsoft Authenticator (és az Android Intune Vállalati portálon) keresztül.
- IOS és Android rendszeren az MSAL külső böngészőt használhat a felhasználók bejelentkezéséhez. Előfordulhat, hogy a külső böngésző az alkalmazás tetején jelenik meg. Testreszabhatja a konfigurációt, hogy inkább az alkalmazáson belüli WebViews-t használja.
- Soha ne használjon titkos kulcsot mobilalkalmazásban. Ezekben az alkalmazásokban a titkos kulcsok minden felhasználó számára elérhetők.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Alkalmazásregisztráció](scenario-mobile-app-registration.md)
