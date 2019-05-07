---
title: Mobilalkalmazás, amely meghívja a webes API-k – áttekintés |} A Microsoft identity platform
description: Ismerje meg a mobilalkalmazások, hogy a hívások webes API-k (áttekintés)
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb0acd1534bab11eb57a7aa0e695f192b5999ed2
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65076495"
---
# <a name="scenario-mobile-application-that-calls-web-apis"></a>Forgatókönyv: A mobilalkalmazás, hogy a hívások webes API-k

Ismerje meg a mobilalkalmazások, amely meghívja a webes API-kat kell.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Első lépések

Az első mobile alkalmazás létrehozása, és próbálja ki a rövid útmutató!

> [!div class="nextstepaction"]
> [Rövid útmutató: A token beszerzéséhez és a Microsoft Graph API hívása Androidos alkalmazásokból](./quickstart-v2-android.md)
>
> [Rövid útmutató: A token beszerzéséhez és a Microsoft Graph API meghívása iOS-alkalmazások](./quickstart-v2-ios.md)
>
> [Rövid útmutató: A token beszerzéséhez és a Microsoft Graph API hívása egy Xamarin iOS és Android-alkalmazás](https://github.com/Azure-Samples/active-directory-xamarin-native-v2)

## <a name="overview"></a>Áttekintés

Mobilalkalmazások készítése, során egy személyre szabott, gördülékeny végfelhasználói élmény elengedhetetlen.  A Microsoft identity platform lehetővé teszi, hogy az iOS és Android-felhasználók pontosan ezt a mobilos fejlesztésekhez. Az alkalmazás Azure AD-ben a személyes Microsoft-fiókot, bejelentkezhet, és az Azure AD B2C-felhasználókat és szerzi be a jogkivonatokat a webes API-hívás a felhasználók nevében. Ezek a folyamatok megvalósításához, a Microsoft hitelesítési tár (MSAL) az iparági szabvány, amely fogjuk használni [OAuth2.0 hitelesítési kódfolyamat](v2-oauth2-auth-code-flow.md).

![Démonalkalmazások](./media/scenarios/mobile-app.svg)

Mobilalkalmazás szempontok:

- ***Felhasználói környezet***: Lehetővé teszi a felhasználók számára az alkalmazás értéke bejelentkezés kérése előtt, és csak a szükséges engedélyekkel kérése.
- ***Támogatja az összes felhasználói konfiguráció***: Számos mobil üzleti felhasználók a feltételes hozzáférés és az eszközmegfelelőségi szabályzatok alatt találhatók. Győződjön meg arról, ezek a legfontosabb forgatókönyvek támogatásához.
- ***Egyszeri bejelentkezés (SSO) megvalósítása***: MSAL és a Microsoft identity platform révén egyszeri bejelentkezés engedélyezése egyszerűen elvégezhető a böngésző vagy a Microsoft Authenticator (és az Intune vállalati portál Android rendszeren).

## <a name="specifics"></a>Tulajdonságairól

Mobilalkalmazások létrehozását, a Microsoft identity platform, a teljes körű felhasználói élményt rendelkezik néhány megfontolandó szempont:

- A platformtól függően jelentkezik be a felhasználói beavatkozás nélkül nem lehetséges az az első bejelentkezésekor. iOS-, például használata alkalmazások megjelenítése a felhasználói beavatkozás, egyszeri bejelentkezés az első alkalommal a Microsoft Authenticator (és az Intune vállalati portál Android rendszeren) beolvasásakor.
- Az iOS és Android az MSAL a felhasználók felhasználhatja egy külső böngészők (amely felett az alkalmazás jelenhet meg). Ez a testre szabható használja helyette az alkalmazáson belüli Webnézeteket.
- Soha ne használja a titkos kód mobilalkalmazás, minden felhasználó számára elérhető lesz.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Alkalmazásregisztráció](scenario-mobile-app-registration.md)
