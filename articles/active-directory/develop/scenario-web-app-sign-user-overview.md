---
title: Felhasználók bejelentkezése webalkalmazásból – Microsoft identity platform | Azure
description: További információ a felhasználókban bejelentkező webalkalmazás létrehozásáról (áttekintés)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 65d254cec5735c54e19f5adfde57fb6aed776a2c
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80881485"
---
# <a name="scenario-web-app-that-signs-in-users"></a>Eset: A felhasználókban bejelentkező webalkalmazás

Ismerje meg, hogy miként hozhat létre egy olyan webalkalmazást, amely a Microsoft identitásplatformot használja a felhasználók bejelentkezéséhez.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Első lépések

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Ha az első hordozható (ASP.NET Core) webalkalmazást szeretné létrehozni, amely bejelentkezik a felhasználókba, kövesse az alábbi rövid útmutatót:

> [!div class="nextstepaction"]
> [Rövid útmutató: ASP.NET Core webalkalmazás, amely bejelentkezik a felhasználók](quickstart-v2-aspnet-core-webapp.md)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Ha szeretné megtudni, hogyan adhat hozzá bejelentkezést egy meglévő ASP.NET webalkalmazáshoz, próbálkozzon az alábbi rövid útmutatóval:

> [!div class="nextstepaction"]
> [Rövid útmutató: ASP.NET webalkalmazás, amely bejelentkezik a felhasználók](quickstart-v2-aspnet-webapp.md)

# <a name="java"></a>[Java](#tab/java)

Ha Ön Java-fejlesztő, próbálkozzon az alábbi rövid útmutatóval:

> [!div class="nextstepaction"]
> [Rövid útmutató: Bejelentkezés hozzáadása a Microsofttal egy Java webalkalmazáshoz](quickstart-v2-java-webapp.md)

# <a name="python"></a>[Python](#tab/python)

Ha a Pythonnal dolgozik, próbálkozzon az alábbi rövid útmutatóval:

> [!div class="nextstepaction"]
> [Rövid útmutató: Bejelentkezés hozzáadása a Microsofttal egy Python-webalkalmazáshoz](quickstart-v2-python-webapp.md)

---

## <a name="overview"></a>Áttekintés

Hitelesítést ad hozzá a webalkalmazáshoz, hogy bejelentkezhessen a felhasználókhoz. A hitelesítés hozzáadása lehetővé teszi, hogy a webalkalmazás korlátozott profiladatokhoz férjen hozzá a felhasználók felhasználói élményének testreszabása érdekében. 

A webalkalmazások hitelesítik a felhasználót egy webböngészőben. Ebben a forgatókönyvben a webalkalmazás irányítja a felhasználó böngészőjét, hogy jelentkezzen be az Azure Active Directoryba (Azure AD). Az Azure AD egy bejelentkezési választ ad vissza a felhasználó böngészőjében keresztül, amely egy biztonsági jogkivonatban tartalmazza a felhasználóra vonatkozó jogcímeket. A felhasználók bejelentkezése kihasználja az [Open ID Connect](./v2-protocols-oidc.md) szabványprotokollt, amelyet a köztes [szoftverkönyvtárak](scenario-web-app-sign-user-app-configuration.md#libraries-for-protecting-web-apps)egyszerűsítettek.

![Webalkalmazás-bejelentkezések a felhasználókban](./media/scenario-webapp/scenario-webapp-signs-in-users.svg)

Második fázisként engedélyezheti, hogy az alkalmazás webes API-kat hívjon meg a bejelentkezett felhasználó nevében. A következő fázis egy másik forgatókönyv, amely a [webes API-kat meghívja webappokban](scenario-web-app-call-api-overview.md)található.

> [!NOTE]
> A webalkalmazásba való bejelentkezés a webalkalmazás védelméről és a felhasználói jogkivonat érvényesítéséről szól, amit a **köztes szoftvertárak** csinálnak. A .NET esetében ez a forgatókönyv még nem igényli a Microsoft authentication library (MSAL) könyvtárat, amely a védett API-k hívásához szükséges jogkivonat beszerzéséről szól. A hitelesítési kódtárak a nyomon követési forgatókönyvben kerülnek bevezetésre, amikor a webalkalmazásnak webes API-kat kell hívnia.

## <a name="specifics"></a>Sajátosságai

- Az alkalmazás regisztrációja során meg kell adnia egy vagy több (ha az alkalmazást több helyen telepíti) választ kell adnia az URI-knak. Bizonyos esetekben (ASP.NET és ASP.NET Core), engedélyeznie kell az azonosító jogkivonatot. Végül érdemes beállítani egy kijelentkezési URI-t, hogy az alkalmazás reagáljon a kijelentkező felhasználókra.
- Az alkalmazás kódjában meg kell adnia azt a jogosultságot, amelyhez a webalkalmazás delegálja a bejelentkezést. Előfordulhat, hogy testre szeretné szabni a jogkivonat-érvényesítést (különösen a partneri forgatókönyvekben).
- A webalkalmazások bármilyen fióktípust támogatnak. További információt a [Támogatott fióktípusok](v2-supported-account-types.md)című témakörben talál.

## <a name="next-steps"></a>További lépések

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [Alkalmazásregisztráció](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=aspnetcore)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [Alkalmazásregisztráció](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=aspnet)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Alkalmazásregisztráció](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=java)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Alkalmazásregisztráció](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=python)

---
