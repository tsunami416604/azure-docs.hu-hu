---
title: Webes API-kat megnevező asztali alkalmazás létrehozása – Microsoft identity platform | Azure
description: Megtudhatja, hogy miként hozhat létre webes API-kat meghívja asztali alkalmazást (áttekintés)
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
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 196102769c1f5f68df316918a63079b09baad32d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76702147"
---
# <a name="scenario-desktop-app-that-calls-web-apis"></a>Eset: Webes API-kat meghívja ópadi alkalmazás

Ismerje meg mindazt, amire szüksége van egy webes API-t meghívjatartalmazó asztali alkalmazás létrehozásához.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="get-started"></a>Bevezetés

Ha még nem tette meg, hozza létre az első alkalmazást a .NET asztali rövid útmutató, az Univerzális Windows platform (UWP) rövid útmutatója vagy a macOS natív alkalmazás rövid útmutatója szerint:

> [!div class="nextstepaction"]
> [Rövid útmutató: Jogkivonat lekérése és a Microsoft Graph API meghívása egy Windows asztali alkalmazásból](./quickstart-v2-windows-desktop.md)


> [!div class="nextstepaction"]
> [Rövid útmutató: Jogkivonat beszerzése és Microsoft Graph API hívása UWP-alkalmazásból](./quickstart-v2-uwp.md)

> [!div class="nextstepaction"]
> [Rövid útmutató: Jogkivonat beszerzése és microsoft graph API hívása macOS natív alkalmazásból](./quickstart-v2-ios.md)

## <a name="overview"></a>Áttekintés

Asztali alkalmazást ír, és be szeretne jelentkezni a felhasználókba az alkalmazásba, és webes API-kat, például a Microsoft Graphot, más Microsoft API-kat vagy a saját webes API-kat szeretne meghívni. Számos lehetősége van:

- Használhatja az interaktív token beszerzés:

  - Ha az asztali alkalmazás támogatja a grafikus vezérlőket, például ha Windows.Form alkalmazásról, WPF-alkalmazásról vagy macOS natív alkalmazásról van szó.
  - Vagy ha ez egy .NET Core alkalmazás, és beleegyezik abba, hogy az Azure Active Directoryval (Azure AD) a rendszerböngészőben történjen a hitelesítési interakció.

- Windows-üzemeltetésű alkalmazások esetén a Windows-tartományhoz vagy az Azure AD-hez csatlakozott számítógépeken futó alkalmazások is beszerezhetnek egy jogkivonatot az integrált Windows-hitelesítés használatával.
- Végül, és bár ez nem ajánlott, használhatja a felhasználónevet és a jelszót a nyilvános ügyfélalkalmazásokban. Bizonyos forgatókönyvekben, például a DevOps-hoz továbbra is szükség van. Használata korlátozza az alkalmazást. Például nem tud bejelentkezni egy olyan felhasználóba, akinek többtényezős hitelesítést (feltételes hozzáférést) kell végrehajtania. Emellett az alkalmazás nem részesül egyszeri bejelentkezés (SSO).

  Ez is ellentétes a modern hitelesítés elveivel, és csak örökölt okokból biztosított.

  ![Asztali alkalmazás](media/scenarios/desktop-app.svg)

- Ha hordozható parancssori eszközt ír, valószínűleg egy Linuxvagy Mac rendszeren futó .NET Core alkalmazást, és elfogadja, hogy a hitelesítés delegálásra kerül a rendszerböngészőben, interaktív hitelesítést használhat. A .NET Core nem biztosít [webböngészőt,](https://aka.ms/msal-net-uses-web-browser)így a hitelesítés a rendszerböngészőben történik. Ellenkező esetben a legjobb megoldás ebben az esetben az eszköz kódfolyamatának használata. Ez a folyamat böngésző nélküli alkalmazásokhoz is használható, például az IoT-alkalmazásokhoz.

  ![Böngésző nélküli alkalmazás](media/scenarios/device-code-flow-app.svg)

## <a name="specifics"></a>Sajátosságai

Asztali alkalmazások számos sajátosságait. Ezek elsősorban attól függ, hogy az alkalmazás interaktív hitelesítést használ-e vagy sem.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Asztali alkalmazás: Alkalmazásregisztráció](scenario-desktop-app-registration.md)
