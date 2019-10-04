---
title: Webes alkalmazás, amely bejelentkezik a felhasználók számára (éles környezetbe kerül) – Microsoft Identity platform
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
ms.openlocfilehash: bd6717c132d32d54c16e7678695a09e35181a057
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71086553"
---
# <a name="web-app-that-signs-in-users---move-to-production"></a>Felhasználók számára bejelentkező webalkalmazás – áttérés éles környezetbe

Most, hogy már tudja, hogyan lehet jogkivonatot beszerezni webes API-k hívásához, megtudhatja, hogyan helyezheti át az éles környezetbe.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>További lépések

### <a name="calling-web-apis-scenario"></a>Webes API-k meghívása forgatókönyv

Ha a webalkalmazás bejelentkezik a felhasználókba, a webes API-kat hívhat a bejelentkezett felhasználók nevében. A webes API-k hívása a webalkalmazásból a következő forgatókönyv objektuma:

> [!div class="nextstepaction"]
> [Webes API-kat hívó webalkalmazás](scenario-web-app-call-api-overview.md)

### <a name="deep-dive---aspnet-core-web-app-tutorial"></a>Deep Dive – ASP.NET Core webalkalmazás-oktatóanyag

Ismerje meg a bejelentkezési felhasználók egyéb módszereit az ASP.NET Core oktatóanyaggal: [MS-Identity-aspnetcore-WebApp-tutorial](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial). Ez a példa egy olyan progresszív oktatóanyag, amely éles használatra kész kóddal rendelkezik egy webalkalmazáshoz, beleértve a bejelentkezés fiókkal való hozzáadását a következőben:

- szervezete,
- több szervezet,
- munkahelyi vagy iskolai fiókok vagy személyes Microsoft-fiók,
- [Azure ad B2C](https://aka.ms/aadb2c),
- vagy az országos felhőkben.

### <a name="sample-code---java-web-app"></a>Mintakód – Java-webalkalmazás

További információ a Java-webalkalmazásról a GitHubon található minta alapján: [Egy Java-webalkalmazás, amely a Microsoft Identity platformmal és a hívások Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp)
