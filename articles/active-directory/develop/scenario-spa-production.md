---
title: Egyoldalas alkalmazás (Váltás az éles környezetben) – a Microsoft identity platform
description: Ismerje meg, hogyan hozhat létre egy egyoldalas alkalmazás (Váltás az éles környezetben)
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
ms.openlocfilehash: e6a04f5d62ec750cfbe44765e833552bd694654d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65075010"
---
# <a name="single-page-application---move-to-production"></a>Egyoldalas alkalmazás – áthelyezése éles környezetbe

Most, hogy tudja, hogyan webes API-k hívása a tokenek beszerzésére, megtudhatja, hogyan helyezze át az éles környezetbe.

## <a name="improve-your-app"></a>Az alkalmazás fejlesztéséhez

Kövesse a lépéseket, hogy készen áll az alkalmazás éles szükséges.

- [Naplózás engedélyezése](msal-logging.md) az alkalmazásban.

## <a name="test-your-integration"></a>A-integráció tesztelése

- Az integráció teszteléséhez a következő a [a Microsoft identity platform integrációs ellenőrzőlista](identity-platform-integration-checklist.md).

## <a name="next-steps"></a>További lépések

Az alábbiakban néhány egyéb minták és oktatóanyagok:

- A rövid útmutató részletes bemutatása mintát, amely leírja, hogy a felhasználók és az MSAL.js használatával az MS Graph API meghívása a hozzáférési jogkivonatot kapjon a kódot

    > [!div class="nextstepaction"]
    > [JavaScript SPA-oktatóanyag](./tutorial-v2-javascript-spa.md)

- A minta bemutatja, hogyan lehet a saját háttérrendszer webes API-khoz MSAL.js használatával tokenekhez

     > [!div class="nextstepaction"]
     > [Egy ASP.NET-háttérrendszerrel SPA-ALKALMAZÁSOKBA](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2)

- Minta MSAL.js használatával jelentkezzen be az Azure AD B2C-vel regisztrált egy alkalmazást a felhasználók megjelenítése

    > [!div class="nextstepaction"]
    > [SPA-ALKALMAZÁSOKBA az Azure AD B2C-vel](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)
