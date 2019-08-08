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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 95aeeacfd85dd79453bff4e365e5b050039f77b9
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68852466"
---
# <a name="scenario-web-app-that-signs-in-users"></a>Forgatókönyv: Felhasználót bejelentkeztető webalkalmazás

Ismerje meg a Microsoft Identity platformot használó webalkalmazások létrehozásához szükséges összes információt.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Első lépések

Ha az első olyan hordozható (ASP.NET Core) webalkalmazást szeretné létrehozni, amely felhasználói bejelentkezést végez, kövesse az alábbi rövid útmutatót:

> [!div class="nextstepaction"]
> [Rövid útmutató: Bejelentkező felhasználók ASP.NET Core webalkalmazás](quickstart-v2-aspnet-core-webapp.md)

Ha inkább a ASP.NET-t szeretné megtekinteni, próbálja ki a következő oktatóanyagot:

> [!div class="nextstepaction"]
> [Rövid útmutató: ASP.NET webalkalmazás, amely bejelentkezik a felhasználók számára](quickstart-v2-aspnet-webapp.md)

## <a name="overview"></a>Áttekintés

A hitelesítést a webalkalmazáshoz adja hozzá, hogy bejelentkezzen a felhasználókba. A hitelesítés hozzáadása lehetővé teszi a webalkalmazás számára a korlátozott profilokra vonatkozó információk elérését, és például testreszabhatja a felhasználók számára kínált felhasználói élményt. A webalkalmazások a felhasználókat egy böngészőben hitelesítik. Ebben az esetben a webalkalmazás irányítja a felhasználó böngészőjében, hogy bejelentkezzen az Azure AD-be. Az Azure AD egy bejelentkezési választ ad vissza a felhasználó böngészőjén keresztül, amely a felhasználóhoz tartozó jogcímeket tartalmaz egy biztonsági jogkivonatban. A bejelentkezett felhasználók a [nyílt azonosító](./v2-protocols-oidc.md) összekapcsolására szolgáló protokollt is egyszerűsítik, a köztes [kódtárak](scenario-web-app-sign-user-app-configuration.md#libraries-used-to-protect-web-apps)használatával.

![Webalkalmazás-bejelentkezési felhasználók](./media/scenario-webapp/scenario-webapp-signs-in-users.svg)

Második fázisként azt is engedélyezheti, hogy az alkalmazás a bejelentkezett felhasználó nevében meghívja a webes API-kat. Ez a következő fázis egy másik forgatókönyv, amelyet a web [app calls web API](scenario-web-app-call-api-overview.md) -kon talál

> [!NOTE]
> Ha bejelentkezik a webalkalmazásba, a webalkalmazás védelme és a felhasználói token érvényesítése is megtörténik. Ehhez a forgatókönyvhöz még nem szükséges a Microsoft Authentication librarys (MSAL), amely a védett API-k meghívására szolgáló token beszerzésére szolgál. A hitelesítési kódtárak csak akkor lesznek bevezetve a követési forgatókönyvben, amikor a webalkalmazásnak webes API-kat kell meghívnia.

## <a name="specifics"></a>Sajátosságai

- Az alkalmazás regisztrációja során meg kell adnia egy vagy több (ha az alkalmazást több helyen helyezi üzembe) válasz URI-k. Bizonyos esetekben (ASP.NET/ASP.NET Core) engedélyeznie kell a IDToken. Végül egy kijelentkezési URI-t kell beállítania, hogy az alkalmazás a felhasználók számára kijelentkezést váltson ki.
- Az alkalmazás kódjában meg kell adnia azt a szolgáltatót, amelyhez a webalkalmazás a bejelentkezést delegálja. Előfordulhat, hogy testre szeretné szabni a jogkivonat-ellenőrzést (különösen az ISV-forgatókönyvekben).
- A webalkalmazások bármilyen fióktípus használatát támogatják. További információ: [támogatott fióktípus](v2-supported-account-types.md).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Alkalmazásregisztráció](scenario-web-app-sign-user-app-registration.md)
