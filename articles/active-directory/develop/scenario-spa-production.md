---
title: Egyoldalas alkalmazás áthelyezése az éles környezetbe – Microsoft Identity platform | Azure
description: Ismerje meg, hogyan hozhat létre egy egyoldalas alkalmazást (áttérés éles környezetbe)
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 21ba0193c3f1e19ffc74452aaceee34759c7e606
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2020
ms.locfileid: "88949014"
---
# <a name="single-page-application-move-to-production"></a>Egyoldalas alkalmazás: áthelyezés éles környezetbe

Most, hogy már tudja, hogyan szerezzen be tokent a webes API-k hívásához, Ismerje meg, hogyan helyezheti át az éles környezetbe.

## <a name="improve-your-app"></a>Az alkalmazás fejlesztése

[Engedélyezze a naplózást](msal-logging.md) , hogy az alkalmazás előállítása készen álljon.

## <a name="test-your-integration"></a>Az integráció tesztelése

Tesztelje az integrációt a [Microsoft Identity platform Integration ellenőrzőlista](identity-platform-integration-checklist.md)követésével.

## <a name="deploy-your-app"></a>Az alkalmazás üzembe helyezése

Tekintse meg az [üzembe helyezési mintát](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnet-webapi-multitenant/tree/master/Chapter3) , amelyből megtudhatja, hogyan helyezheti üzembe a Spa-és webes API-projektjeit az Azure Storage és az Azure app Services használatával. 

## <a name="next-steps"></a>Következő lépések

A gyors üzembe helyezési minta részletes bemutatása, amely leírja, hogyan lehet bejelentkezni a felhasználókba, és hogyan kérhet hozzáférési jogkivonatot a **Microsoft Graph API** meghívásához **MSAL.js**használatával:

> [!div class="nextstepaction"]
> [JavaScript SPA – oktatóanyag](./tutorial-v2-javascript-spa.md)

Minta, amely bemutatja, hogyan szerezhet jogkivonatokat a saját háttérbeli webes API-hoz (ASP.NET Core) a **MSAL.js**használatával:

> [!div class="nextstepaction"]
> [SPA ASP.NET-háttérrel](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnetcore-webapi)

Minta, amely bemutatja, hogyan lehet érvényesíteni a háttérbeli webes API-hoz (Node.js) tartozó hozzáférési jogkivonatokat a **Passport-Azure-ad**használatával.

> [!div class="nextstepaction"]
> [Node.js webes API (Azure AD)](https://github.com/Azure-Samples/active-directory-javascript-nodejs-webapi-v2)

Ez a minta azt mutatja be, hogyan használhatók a **MSAL.js** a **Azure Active Directory B2C** (Azure ad B2C) szolgáltatásban regisztrált alkalmazásokban lévő felhasználók bejelentkezéséhez:

> [!div class="nextstepaction"]
> [SPA Azure AD B2C](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)

Minta, amely bemutatja, hogyan használható a **Passport-Azure-ad** a **Azure Active Directory B2C** (Azure ad B2C) szolgáltatásban regisztrált alkalmazások hozzáférési jogkivonatának ellenőrzéséhez

> [!div class="nextstepaction"]
> [Node.js webes API (Azure AD B2C)](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi)
