---
title: Webes alkalmazás, amely képes bejelentkeztetni a felhasználókat (áttekintés) – a Microsoft identity platform
description: Ismerje meg, hogyan hozhat létre egy webalkalmazást, amely képes bejelentkeztetni a felhasználókat (áttekintés)
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
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b46df5e4ec8352b47f744a507fad702c37aa5fba
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075100"
---
# <a name="scenario-web-app-that-signs-in-users"></a>Forgatókönyv: Webes alkalmazás, amely képes bejelentkeztetni a felhasználókat

Ismerje meg, hozhat létre egy webalkalmazást, hogy a Microsoft identity platform használatával jelentkezik be felhasználókat kell.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Első lépések

Ha azt szeretné, hozhat létre az első hordozható (az ASP.NET Core) webalkalmazásokat, bejelentkezhetnek a felhasználók, kövesse az ebben a rövid útmutatóban:

> [!div class="nextstepaction"]
> [Rövid útmutató: ASP.NET Core webes alkalmazást, hogy jelentkezik be felhasználókat](quickstart-v2-aspnet-core-webapp.md)

Ha inkább az ASP.NET-tel marad, próbálja ki a következő oktatóanyaggal:

> [!div class="nextstepaction"]
> [Rövid útmutató: ASP.NET Core webes alkalmazást, hogy jelentkezik be felhasználókat](quickstart-v2-aspnet-webapp.md)

## <a name="overview"></a>Áttekintés

Hitelesítés hozzáadása a webalkalmazáshoz, hogy a felhasználók bejelentkezhetnek. Hitelesítés hozzáadása lehetővé teszi, hogy korlátozott profil adatai eléréséhez, és például testre szabhatja a felhasználói élményt kínál a felhasználóknak, hogy a webalkalmazás. Web apps egy webböngészőben a felhasználó hitelesítéséhez. Ebben a forgatókönyvben a webes alkalmazás a felhasználó böngészőben jelentkezzen be őket az Azure AD irányítja. Az Azure AD választ jelentkezzen be a felhasználó böngészőjében, amely tartalmazza a biztonsági jogkivonat a felhasználóval kapcsolatos jogcímek keresztül. Felhasználói bejelentkezés a [Open ID Connect](./v2-protocols-oidc.md) szabványos protokoll magát a közbenső szoftver használatával egyszerűsített [kódtárak](scenario-web-app-sign-user-app-configuration.md#libraries-used-to-protect-web-apps).

![Webes alkalmazás bejelentkezik felhasználók](./media/scenario-webapp/scenario-webapp-signs-in-users.svg)

A második fázis, a webes API-k hívása a bejelentkezett felhasználó nevében az alkalmazás is engedélyezheti. A következő fázis más-más forgatókönyvet, amely a [webalkalmazás meghívja a webes API-k](scenario-web-app-call-api-overview.md)

> [!NOTE]
> Bejelentkezés hozzáadása egy webes alkalmazás a web app védelméről, és egy felhasználói jogkivonat érvényesítése, melyik milyen **közbenső** kódtárak tegye. Ez a forgatókönyv nem igényel, de a Microsoft hitelesítési tárak (MSAL), amely készül a meghívni egy token beszerzése a védett API-k. A hitelesítési tárak csak jelenik meg a követési forgatókönyvben, amikor a webalkalmazás webes API-k meghívásához szükséges.

## <a name="specifics"></a>Tulajdonságairól

- Az alkalmazás regisztrációja során meg kell adnia egy vagy több (ha az alkalmazás üzembe helyezése több helyre) válasz URI-k. Bizonyos esetekben (ASP.NET/ASP.NET mag) akkor engedélyeznie kell a IDToken. Végül érdemes beállítani a kijelentkezési URI-t, hogy a felhasználók aláírási kibővített reagál az alkalmazás.
- Az alkalmazás kódjában kell adja meg a szolgáltatót, amelyhez webes alkalmazás delegáltak jelentkezzen be. Előfordulhat, hogy testre szeretné szabni a jogkivonat érvényesítésére (különösen a független Szoftverszállító forgatókönyvek).
- Webes alkalmazások minden fióktípus esetében támogatja. További információ: [támogatott fióktípusok](v2-supported-account-types.md).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Alkalmazásregisztráció](scenario-web-app-sign-user-app-registration.md)
