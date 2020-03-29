---
title: A felhasználókat bejelentkező webalkalmazás áthelyezése éles környezetbe – Microsoft identity platform | Azure
description: Megtudhatja, hogyan hozhat létre olyan webalkalmazást, amely bejelentkezik a felhasználókba (ugrás éles környezetbe)
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
ms.openlocfilehash: 6c486e59f32afd09a9934ae2298172ccb4ee2414
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76768107"
---
# <a name="web-app-that-signs-in-users-move-to-production"></a>A felhasználókban bejelentkező webalkalmazás: Ugrás éles környezetbe

Most, hogy már tudja, hogyan kaphat egy jogkivonatot webes API-k hívásához, ismerje meg, hogyan helyezheti át éles környezetbe.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>További lépések

### <a name="same-site"></a>Ugyanaz a webhely

Győződjön meg arról, hogy megértette a Chrome böngésző új verzióival kapcsolatos lehetséges problémákat

> [!div class="nextstepaction"]
> [A SameSite cookie-k változásainak kezelése a Chrome böngészőben](howto-handle-samesite-cookie-changes-chrome-browser.md)

### <a name="scenario-for-calling-web-apis"></a>Webes API-k hívásának forgatókönyve

Miután a webalkalmazás bejelentkezik a felhasználók, hívhatja webes API-k a bejelentkezett felhasználók nevében. A webes API-k webappból való hívása a következő forgatókönyv tárgyát képezi:

> [!div class="nextstepaction"]
> [Webes API-kat hívó webalkalmazás](scenario-web-app-call-api-overview.md)

## <a name="deep-dive-aspnet-core-web-app-tutorial"></a>Mély merülés: ASP.NET Core web app bemutató

További információ arról, hogyan jelentkeztethat be a felhasználók ebbe ASP.NET Core oktatóanyag: 

> [!div class="nextstepaction"]
> [A webalkalmazások bejelentkezésének és API-k hívásának engedélyezése a Fejlesztőknek készült Microsoft-identitásplatformmal](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) 

Ez a progresszív oktatóanyag éles használatra kész kódot kapott egy webalkalmazáshoz, többek között azt is, hogyan adhat hozzá bejelentkezést a fiókokkal:

- Az Ön szervezete
- Több szervezet
- Munkahelyi vagy iskolai fiókok, illetve személyes Microsoft-fiókok
- [Azure AD B2C](https://aka.ms/aadb2c)
- Nemzeti felhők

## <a name="sample-code-java-web-app"></a>Mintakód: Java webalkalmazás

Tudjon meg többet a Java webappról a GitHubon található mintából: 

> [!div class="nextstepaction"]
> [Java webalkalmazás, amely a Microsoft identity platformmal rendelkező felhasználókat jelentkezik be, és meghívja a Microsoft Graph-ot](https://github.com/Azure-Samples/ms-identity-java-webapp)
