---
title: Egyoldalas alkalmazás áthelyezése éles környezetbe – Microsoft identity platform | Azure
description: Az egyoldalas alkalmazások létrehozásának megismerkedése (ugrás éles környezetbe)
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
ms.openlocfilehash: 01b923e0d013fab1815456e55eac6036ded772bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76701841"
---
# <a name="single-page-application-move-to-production"></a>Egyoldalas alkalmazás: Ugrás éles környezetbe

Most, hogy már tudja, hogyan szerezhet be egy jogkivonatot webes API-k hívásához, ismerje meg, hogyan helyezheti át éles környezetbe.

## <a name="improve-your-app"></a>Az alkalmazás fejlesztése

[Engedélyezze](msal-logging.md) a naplózást, hogy az alkalmazás éles környezete készen álljon.

## <a name="test-your-integration"></a>Az integráció tesztelése

Tesztelje az integrációt a [Microsoft identitásplatform-integrációs ellenőrzőlistájának](identity-platform-integration-checklist.md)követésével.

## <a name="next-steps"></a>További lépések

A rövid útmutató minta részletes merülése, amely ismerteti a felhasználók bejelentkezésének és a Microsoft Graph API MSAL.js használatával történő hívásához tartozó hozzáférési jogkivonat beszerzésének kódját:

> [!div class="nextstepaction"]
> [JavaScript SPA oktatóanyag](./tutorial-v2-javascript-spa.md)

Minta, amely bemutatja, hogyan szerezhet le jogkivonatokat a saját háttérwebes API-hoz az MSAL.js használatával:

> [!div class="nextstepaction"]
> [SPA ASP.NET háttérvéggel](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2)

Minta, amely bemutatja, hogyan használhatja az MSAL.js-t a felhasználók bejelentkezéséhez az Azure Active Directory B2C (Azure AD B2C) szolgáltatásban regisztrált alkalmazásban:

> [!div class="nextstepaction"]
> [SPA az Azure AD B2C-vel](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)
