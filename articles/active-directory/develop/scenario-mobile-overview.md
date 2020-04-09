---
title: Webes API-kat megnevező mobilalkalmazás létrehozása | Azure
titleSuffix: Microsoft identity platform | Azure
description: Megtudhatja, hogy miként hozhat létre webes API-kat meghívja mobilalkalmazást (áttekintés)
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
ms.openlocfilehash: 1f90f7f23fbdf10b91d8dfc7cd00cca83cd32fbc
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80882573"
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
- **Megosztott eszközmód megvalósítása:** Engedélyezze az alkalmazás használható megosztott eszköz forgatókönyvek, például a kórházak, a gyártás, a kiskereskedelem és a pénzügyek. [További információ a megosztott eszközmód támogatásáról.](msal-shared-devices.md)

## <a name="specifics"></a>Sajátosságai

A Microsoft identitásplatformon mobilalkalmazás létrehozásakor tartsa szem előtt az alábbi szempontokat:

- A platformtól függően előfordulhat, hogy a felhasználók első bejelentkezésekor bizonyos felhasználói beavatkozásra van szükség. Az iOS-nek például meg kell mutatnia a felhasználói beavatkozást, amikor először használják az Egyszeri bejelentkezést a Microsoft Authenticator (és az Android Intune Vállalati portálon) keresztül.
- IOS és Android rendszeren az MSAL külső böngészőt használhat a felhasználók bejelentkezéséhez. Előfordulhat, hogy a külső böngésző az alkalmazás tetején jelenik meg.
- Soha ne használjon titkos kulcsot mobilalkalmazásban. Ezekben az alkalmazásokban a titkos kulcsok minden felhasználó számára elérhetők.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Alkalmazásregisztráció](scenario-mobile-app-registration.md)
