---
title: Egyoldalas alkalmazás áthelyezése az éles környezetbe – Microsoft Identity platform | Azure
description: Ismerje meg, hogyan hozhat létre egy egyoldalas alkalmazást (áttérés éles környezetbe)
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: ba41b29cfcea2d9dfcab2cd552040eaffa4e90ff
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74965279"
---
# <a name="single-page-application-move-to-production"></a>Egyoldalas alkalmazás: áthelyezés éles környezetbe

Most, hogy már tudja, hogyan szerezzen be tokent a webes API-k hívásához, Ismerje meg, hogyan helyezheti át az éles környezetbe.

## <a name="improve-your-app"></a>Az alkalmazás fejlesztése

[Engedélyezze a naplózást](msal-logging.md) , hogy az alkalmazás előállítása készen álljon.

## <a name="test-your-integration"></a>Az integráció tesztelése

Tesztelje az integrációt a [Microsoft Identity platform Integration ellenőrzőlista](identity-platform-integration-checklist.md)követésével.

## <a name="next-steps"></a>Következő lépések

A gyors üzembe helyezési minta részletes bemutatása, amely leírja, hogyan lehet bejelentkezni a felhasználókba, és hogyan kérhet hozzáférési jogkivonatot a Microsoft Graph API MSAL. js használatával történő meghívásához:

> [!div class="nextstepaction"]
> [JavaScript SPA – oktatóanyag](./tutorial-v2-javascript-spa.md)

Minta, amely bemutatja, hogyan szerezhet jogkivonatokat a saját háttérbeli webes API-hoz a MSAL. js használatával:

> [!div class="nextstepaction"]
> [SPA ASP.NET-háttérrel](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2)

Ez a minta bemutatja, hogyan lehet a MSAL. js használatával bejelentkezni a felhasználókba Azure Active Directory B2C (Azure AD B2C) szolgáltatásban regisztrált alkalmazásokban:

> [!div class="nextstepaction"]
> [SPA Azure AD B2C](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)
