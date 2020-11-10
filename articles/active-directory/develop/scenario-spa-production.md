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
ms.openlocfilehash: 0a51442870fb72e2b3cd93d9f03736d2c679ed06
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/10/2020
ms.locfileid: "94442820"
---
# <a name="single-page-application-move-to-production"></a>Egyoldalas alkalmazás: áthelyezés éles környezetbe

Most, hogy már tudja, hogyan szerezzen be tokent a webes API-k hívásához, Ismerje meg, hogyan helyezheti át az éles környezetbe.

## <a name="improve-your-app"></a>Az alkalmazás fejlesztése

[Engedélyezze a naplózást](msal-logging.md) , hogy az alkalmazás előállítása készen álljon.

## <a name="test-your-integration"></a>Az integráció tesztelése

Tesztelje az integrációt a [Microsoft Identity platform Integration ellenőrzőlista](identity-platform-integration-checklist.md)követésével.

## <a name="deploy-your-app"></a>Az alkalmazás üzembe helyezése

Tekintse meg az [üzembe helyezési mintát](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnet-webapi-multitenant/tree/master/Chapter3) , amelyből megtudhatja, hogyan helyezheti üzembe a Spa-és webes API-projektjeit az Azure Storage és az Azure app Services használatával. 

## <a name="next-steps"></a>További lépések

- A gyors üzembe helyezési minta részletes bemutatása, amely leírja, hogyan lehet bejelentkezni a felhasználókba, és hogyan kérhet hozzáférési jogkivonatot a **Microsoft Graph API** meghívásához **MSAL.js** : [JavaScript Spa oktatóanyag](./tutorial-v2-javascript-spa.md)használatával.

- Ez a minta azt mutatja be, hogyan lehet jogkivonatokat beolvasni a saját háttérbeli webes API-hoz (ASP.NET Core) a **MSAL.js** : [SPA és egy ASP.net-háttér](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnetcore-webapi)használatával.

- Minta, amely bemutatja, hogyan lehet érvényesíteni a háttérbeli webes API-hoz (Node.js) tartozó hozzáférési jogkivonatokat a **Passport-Azure-ad** : [Node.js web API (Azure ad](https://github.com/Azure-Samples/active-directory-javascript-nodejs-webapi-v2)) használatával.

- Ez a minta azt mutatja be, hogyan használhatók a **MSAL.js** a **Azure Active Directory B2C** (Azure ad B2C) szolgáltatásban regisztrált alkalmazásokban lévő felhasználók bejelentkezéséhez: [Azure ad B2C](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp).

- Minta, amely bemutatja, hogyan használható a **Passport-Azure-ad** a **Azure Active Directory B2C** (Azure ad B2C) szolgáltatásban regisztrált alkalmazások hozzáférési jogkivonatának érvényesítéséhez: [Node.js web API (Azure ad B2C)](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi).
