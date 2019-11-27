---
title: Felhasználók számára bejelentkező webes alkalmazás (áttekintés) – Microsoft Identity platform
description: Megtudhatja, hogyan hozhat létre olyan webalkalmazást, amely aláírja a felhasználókat (áttekintés)
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
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 316ab055a077b251e88421ab26997f8556a6e31f
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2019
ms.locfileid: "74482499"
---
# <a name="scenario-web-app-that-signs-in-users"></a>Forgatókönyv: a felhasználók által bejelentkezett webalkalmazás

Mindent megtudhat a Microsoft Identity platformot használó webalkalmazások létrehozásához a felhasználói bejelentkezéshez.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Első lépések

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Ha az első olyan hordozható (ASP.NET Core) webalkalmazást szeretné létrehozni, amely a felhasználók számára jelentkezik be, kövesse az alábbi rövid útmutatót:

> [!div class="nextstepaction"]
> [Gyors útmutató: ASP.NET Core webalkalmazás, amely bejelentkezik a felhasználók számára](quickstart-v2-aspnet-core-webapp.md)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Ha szeretné megismerni, hogyan adhat hozzá bejelentkezést egy meglévő ASP.NET-webalkalmazáshoz, próbálja meg a következő rövid útmutatót:

> [!div class="nextstepaction"]
> [Rövid útmutató: ASP.NET-webalkalmazás, amely bejelentkezik a felhasználók számára](quickstart-v2-aspnet-webapp.md)

# <a name="javatabjava"></a>[Java](#tab/java)

Ha Ön Java-fejlesztő, próbálja ki a következő rövid útmutatót:

> [!div class="nextstepaction"]
> [Gyors útmutató: bejelentkezés felvétele a Microsofttal egy Java-webalkalmazásba](quickstart-v2-java-webapp.md)

# <a name="pythontabpython"></a>[Python](#tab/python)

Ha Python-fejlesztést végez, próbálja ki a következő rövid útmutatót:

> [!div class="nextstepaction"]
> [Gyors útmutató: bejelentkezés felvétele a Microsofttal egy Python-webalkalmazásba](quickstart-v2-python-webapp.md)

---

## <a name="overview"></a>Áttekintés

A webalkalmazáshoz olyan hitelesítést adhat hozzá, amellyel bejelentkezhet a felhasználókba. A hitelesítés hozzáadásával a webalkalmazás elérheti a korlátozott profilokra vonatkozó információkat, így testre szabhatja a felhasználói élményt. 

A webalkalmazások a felhasználókat egy böngészőben hitelesítik. Ebben az esetben a webalkalmazás irányítja a felhasználó böngészőjében, hogy bejelentkezzen a Azure Active Directoryba (Azure AD). Az Azure AD egy bejelentkezési választ ad vissza a felhasználó böngészőjén keresztül, amely a felhasználóhoz tartozó jogcímeket tartalmaz egy biztonsági jogkivonatban. A felhasználók bejelentkezésekor a rendszer kihasználja a [nyílt ID-kapcsolat](./v2-protocols-oidc.md) szabványos protokolljának előnyeit, amelyet a middleware- [kódtárak](scenario-web-app-sign-user-app-configuration.md#libraries-for-protecting-web-apps)használatával egyszerűsített.

![Webalkalmazás-jelek a felhasználókban](./media/scenario-webapp/scenario-webapp-signs-in-users.svg)

Második fázisként engedélyezheti, hogy az alkalmazás a bejelentkezett felhasználó nevében hívja meg a webes API-kat. Ez a következő fázis egy másik forgatókönyv, amelyet a [webes API-kat meghívó webalkalmazásban](scenario-web-app-call-api-overview.md)talál.

> [!NOTE]
> Ha bejelentkezik a webalkalmazásba, a webalkalmazás védelme és a felhasználói jogkivonat érvényesítése is megtörténik. .NET esetén ehhez a forgatókönyvhöz még nincs szükség a Microsoft Authentication Library (MSAL) szolgáltatásra, amely a védett API-k meghívására szolgáló jogkivonat beszerzésére vonatkozik. A hitelesítési kódtárak a követési forgatókönyvben lesznek bevezetve, amikor a webalkalmazásnak webes API-kat kell meghívnia.

## <a name="specifics"></a>Sajátosságai

- Az alkalmazás regisztrációja során meg kell adnia egy vagy több (ha több helyen helyezi üzembe az alkalmazást) válasz URI-k. Bizonyos esetekben (ASP.NET és ASP.NET Core) engedélyeznie kell az azonosító tokent. Végezetül egy kijelentkezési URI-t kell beállítania, hogy az alkalmazás feliratkozik a felhasználókra.
- Az alkalmazás kódjában meg kell adnia azt a szolgáltatót, amelyhez a webalkalmazás bejelentkezett. Előfordulhat, hogy testre szeretné szabni a jogkivonat-ellenőrzést (különösen a partneri forgatókönyvekben).
- A webalkalmazások bármilyen fióktípus használatát támogatják. További információ: [támogatott fióktípus](v2-supported-account-types.md).

## <a name="next-steps"></a>Következő lépések

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [Alkalmazásregisztráció](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=aspnetcore)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [Alkalmazásregisztráció](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=aspnet)

# <a name="javatabjava"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Alkalmazásregisztráció](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=java)

# <a name="pythontabpython"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Alkalmazásregisztráció](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=python)

---
