---
title: Webes API-kat meghívó asztali alkalmazás létrehozása – Microsoft Identity platform | Azure
description: Ismerje meg, hogyan hozhat létre webes API-kat meghívó asztali alkalmazást (áttekintés)
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 545012629686e1fe3ece8a48ed852542e09e54fe
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74965517"
---
# <a name="scenario-desktop-app-that-calls-web-apis"></a>Forgatókönyv: webes API-kat meghívó asztali alkalmazás

A webes API-kat meghívó asztali alkalmazások létrehozásához szükséges tudnivalók

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Bevezetés

Ha még nem tette meg, hozza létre első alkalmazását a .NET Desktop rövid útmutatójának, a UWP rövid útmutatójának vagy a macOS natív alkalmazásának rövid útmutatójának használatával:

> [!div class="nextstepaction"]
> [Gyors útmutató: token beszerzése és Microsoft Graph API meghívása egy Windowsos asztali alkalmazásból](./quickstart-v2-windows-desktop.md)


> [!div class="nextstepaction"]
> [Gyors útmutató: token beszerzése és Microsoft Graph API meghívása egy UWP-alkalmazásból](./quickstart-v2-uwp.md)

> [!div class="nextstepaction"]
> [Gyors útmutató: token beszerzése és Microsoft Graph API meghívása macOS natív alkalmazásból](./quickstart-v2-ios.md)

## <a name="overview"></a>Áttekintés

Írhat egy asztali alkalmazást, és a felhasználók bejelentkezhetnek az alkalmazásba, és meghívhatják a webes API-kat, például a Microsoft Graph, más Microsoft API-kat vagy a saját webes API-ját. Több lehetősége van:

- Használhatja az interaktív jogkivonat-beszerzést:

  - Ha az asztali alkalmazás támogatja a grafikus vezérlőket, például egy Windows. Form alkalmazást, egy WPF alkalmazást vagy egy macOS natív alkalmazást.
  - Ha ez egy .NET Core-alkalmazás, és Ön vállalja, hogy az Azure AD-vel való hitelesítés interakcióba lép a rendszerböngészőben

- A Windowsban üzemeltetett alkalmazásokhoz a Windows-tartományhoz vagy HRE csatlakoztatott számítógépeken futó alkalmazások esetében is lehetséges a jogkivonat csendes beszerzése az integrált Windows-hitelesítés használatával.
- Végül, és bár nem ajánlott, a felhasználónevet és a jelszót is használhatja a nyilvános ügyfélalkalmazások számára. Bizonyos helyzetekben (például a DevOps-ban) továbbra is szükség van rá, de ügyeljen arra, hogy a használatával korlátozásokat fog alkalmazni az alkalmazására. Például nem tud bejelentkezni a többtényezős hitelesítést (feltételes hozzáférés) végző felhasználót. Az alkalmazás nem használhatja az egyszeri bejelentkezést (SSO).

  Emellett a modern hitelesítés alapelvei is megtalálhatók, és csak az örökölt okok miatt biztosítható.

  ![Asztali alkalmazás](media/scenarios/desktop-app.svg)

- Ha hordozható parancssori eszközt ír, valószínűleg egy Linux vagy Mac rendszerű .NET Core-alkalmazást használ, és ha elfogadja, hogy a hitelesítés a rendszerböngészőhöz lesz delegálva, interaktív hitelesítést is használhat. (A .NET Core még nem biztosít [webböngészőt](https://aka.ms/msal-net-uses-web-browser) , ezért a hitelesítés a rendszerböngészőben történik), ellenkező esetben a legjobb megoldás az, ha az eszköz kód áramlását használja. Ezt a folyamatot böngésző nélküli alkalmazásokhoz is használják, például IoT-alkalmazásokhoz

  ![Böngészővel nem rendelkező alkalmazás](media/scenarios/device-code-flow-app.svg)

## <a name="specifics"></a>Sajátosságai

Az asztali alkalmazások számos sajátossággal rendelkeznek, amelyek főleg attól függnek, hogy az alkalmazás az interaktív hitelesítést használja-e.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Asztali alkalmazás regisztrálása](scenario-desktop-app-registration.md)
