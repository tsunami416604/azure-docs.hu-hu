---
title: A felhasználók által az éles környezetbe bejelentkező webalkalmazás áthelyezése – Microsoft Identity platform | Azure
description: Megtudhatja, hogyan hozhat létre egy webalkalmazást, amely aláírja a felhasználókat (éles környezetbe helyezi)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 47a45b52ac10a44b6efd54c41b3fec1e61a47a35
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82181630"
---
# <a name="web-app-that-signs-in-users-move-to-production"></a>Felhasználók számára bejelentkező webalkalmazás: áthelyezés éles környezetbe

Most, hogy tudja, hogyan szerezhet be jogkivonatot webes API-k hívására, megtudhatja, hogyan helyezheti át az éles környezetbe.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>További lépések

### <a name="troubleshooting"></a>Hibaelhárítás

> [!NOTE]
> Amikor a felhasználók első alkalommal jelentkeznek be a webalkalmazásba, hozzá kell járulniuk. Egyes szervezeteknél azonban a felhasználók a következőhöz hasonló üzenetet tekinthetnek meg:
>
> *A AppName engedélyre van szüksége a szervezet erőforrásaihoz való hozzáféréshez, melyeket csak rendszergazda adhat meg. Kérje meg a rendszergazdát, hogy adjon engedélyt az alkalmazásnak, mielőtt használni lehetne.*
>
> Ennek az az oka, hogy a bérlői rendszergazdája **letiltotta** a felhasználók beleegyező jogosultságát. Ebben az esetben kapcsolatba kell lépnie a bérlői rendszergazdákkal, hogy az alkalmazás által igényelt hatókörökhöz rendszergazdai jogosultságot adjanak.

### <a name="same-site"></a>Ugyanazon a helyen

Győződjön meg arról, hogy megértette a Chrome böngésző új verzióival kapcsolatos lehetséges problémákat

> [!div class="nextstepaction"]
> [A SameSite-cookie-változások kezelése a Chrome böngészőben](howto-handle-samesite-cookie-changes-chrome-browser.md)

A Microsoft. Identity. Web NuGet csomag kezeli a leggyakoribb SameSite problémákat.

### <a name="scenario-for-calling-web-apis"></a>Forgatókönyv a webes API-k meghívásához

Miután a webalkalmazás bejelentkezett a felhasználókba, a webes API-kat hívhat a bejelentkezett felhasználók nevében. A webes API-k hívása a webalkalmazásból a következő forgatókönyv objektuma:

> [!div class="nextstepaction"]
> [Webes API-kat hívó webalkalmazás](scenario-web-app-call-api-overview.md)

## <a name="deep-dive-aspnet-core-web-app-tutorial"></a>Deep Dive: ASP.NET Core webalkalmazás-oktatóanyag

További információ a felhasználók bejelentkezésének egyéb módjairól a ASP.NET Core oktatóanyagban: 

> [!div class="nextstepaction"]
> [A webalkalmazások bejelentkezhetnek a felhasználókba, és meghívhatják az API-kat a Microsoft Identity platform for Developers szolgáltatással](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) 

Ez a progresszív oktatóanyag éles használatra kész kóddal rendelkezik a webalkalmazásokhoz, többek között a bejelentkezéshez a következő fiókokkal:

- A szervezet
- Több szervezet
- Munkahelyi vagy iskolai fiókok, vagy személyes Microsoft-fiókok
- [Azure AD B2C](https://aka.ms/aadb2c)
- Nemzeti felhők

## <a name="sample-code-java-web-app"></a>Mintakód: Java-webalkalmazás

További információ a Java-webalkalmazásról ebből a mintából a GitHubon: 

> [!div class="nextstepaction"]
> [Egy Java-webalkalmazás, amely a Microsoft Identity platformmal és a hívások Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp)
