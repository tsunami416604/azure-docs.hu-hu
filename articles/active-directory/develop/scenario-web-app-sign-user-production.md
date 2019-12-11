---
title: A felhasználók által az éles környezetbe bejelentkező webalkalmazás áthelyezése – Microsoft Identity platform | Azure
description: Megtudhatja, hogyan hozhat létre egy webalkalmazást, amely aláírja a felhasználókat (éles környezetbe helyezi)
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
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c49782a6e1e86320b508875e2bf931cc1cc19b4e
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74964770"
---
# <a name="web-app-that-signs-in-users-move-to-production"></a>Felhasználók számára bejelentkező webalkalmazás: áthelyezés éles környezetbe

Most, hogy tudja, hogyan szerezhet be jogkivonatot webes API-k hívására, megtudhatja, hogyan helyezheti át az éles környezetbe.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Következő lépések

### <a name="scenario-for-calling-web-apis"></a>Forgatókönyv a webes API-k meghívásához

Miután a webalkalmazás bejelentkezett a felhasználókba, a webes API-kat hívhat a bejelentkezett felhasználók nevében. A webes API-k hívása a webalkalmazásból a következő forgatókönyv objektuma:

> [!div class="nextstepaction"]
> [Webes API-kat hívó webalkalmazás](scenario-web-app-call-api-overview.md)

### <a name="deep-dive-aspnet-core-web-app-tutorial"></a>Deep Dive: ASP.NET Core webalkalmazás-oktatóanyag

További információ a felhasználók bejelentkezésének egyéb módjairól a ASP.NET Core oktatóanyagban: 

> [!div class="nextstepaction"]
> [A webalkalmazások bejelentkezhetnek a felhasználókba, és meghívhatják az API-kat a Microsoft Identity platform for Developers szolgáltatással](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) 

Ez a progresszív oktatóanyag éles használatra kész kóddal rendelkezik a webalkalmazásokhoz, többek között a bejelentkezéshez a következő fiókokkal:

- A szervezet
- Több szervezet
- Munkahelyi vagy iskolai fiókok, vagy személyes Microsoft-fiókok
- [Azure AD B2C](https://aka.ms/aadb2c)
- Nemzeti felhők

### <a name="sample-code-java-web-app"></a>Mintakód: Java-webalkalmazás

További információ a Java-webalkalmazásról ebből a mintából a GitHubon: 

> [!div class="nextstepaction"]
> [Egy Java-webalkalmazás, amely a Microsoft Identity platformmal és a hívások Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp)
