---
title: Webes alkalmazás, hogy jelentkezik be felhasználókat (Váltás az éles környezetben) – a Microsoft identity platform
description: Ismerje meg, hogyan hozhat létre egy webalkalmazást, amely képes bejelentkeztetni a felhasználókat (Váltás az éles környezetben)
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
ms.openlocfilehash: d41ad2518f885bbaa02dda3b01f0c02e9fc1d217
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074710"
---
# <a name="web-app-that-signs-in-users---move-to-production"></a>Webes alkalmazás, amely a felhasználók – bejelentkezik átállni éles üzemre

Most, hogy tudja, hogyan webes API-k hívása a tokenek beszerzésére, megtudhatja, hogyan helyezze át az éles környezetbe.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>További lépések

### <a name="calling-web-apis-scenario"></a>Hívása a webes API-k forgatókönyv

Miután meghívhatja a webes alkalmazás jelentkezik be felhasználókat, webes API-k a bejelentkezett felhasználó nevében. Hívása webes API-k és a webes alkalmazás közötti olyan objektum, a következő forgatókönyv:

> [!div class="nextstepaction"]
> [Webalkalmazás, hogy a hívások webes API-k](scenario-web-app-call-api-overview.md)

### <a name="deep-dive---web-app-tutorial"></a>Deep dive - alapú webappokról szóló oktatóanyagunkat

Jelentkezzen be az ASP.NET Core-oktatóanyag felhasználók egyéb módjaival kapcsolatos további tudnivalók: [ms-identity-aspnetcore-webapp-tutorial](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial). Ez az éles kész kód egy webalkalmazás, beleértve a hozzáadásáról bejelentkezési fokozatos oktatóanyaga.

<!--- Removed the diagram as it's already shown in the above link to GitHub

![Tutorial overview](media/scenarios/aspnetcore-webapp-tutorial.svg)

--->
