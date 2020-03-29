---
title: JavaScript egyoldalas alkalmazásforgatókönyv – Microsoft identitásplatform | Azure
description: Megtudhatja, hogyan hozhat létre egy egyoldalas alkalmazást (forgatókönyv áttekintése) a Microsoft identitásplatform használatával.
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
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: b430778bed811656b5c8aadc75ba3cf35917f737
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76701875"
---
# <a name="scenario-single-page-application"></a>Eset: Egyoldalas alkalmazás

Ismerje meg mindazt, amire szüksége van egy egyoldalas alkalmazás (SPA) létrehozásához.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Első lépések

Az első alkalmazást a JavaScript SPA rövid útmutatójának követésével hozhatja létre:

> [!div class="nextstepaction"]
> [Rövid útmutató: Egyoldalas alkalmazás](./quickstart-v2-javascript.md)

## <a name="overview"></a>Áttekintés

Számos modern webes alkalmazás ügyféloldali egyoldalas alkalmazásként épül fel. A fejlesztők JavaScript vagy SPA keretrendszer, például Az Angular, a Vue.js és a React.js használatával írják őket. Ezek az alkalmazások webböngészőn futnak, és a hagyományos kiszolgálóoldali webalkalmazásoktól eltérő hitelesítési jellemzőkkel rendelkeznek. 

A Microsoft identity platform lehetővé teszi, hogy az egyoldalas alkalmazások bejelentkezhessenek a felhasználókba, és jogkivonatokat szerezzenek be a háttérszolgáltatásokhoz vagy webes API-khoz az [OAuth 2.0 implicit folyamat](./v2-oauth2-implicit-grant-flow.md)használatával. Az implicit folyamat lehetővé teszi, hogy az alkalmazás azonosító jogkivonatokat kapjon a hitelesített felhasználó nak, és a védett API-k hívásához szükséges jogkivonatokat is.

![Egyoldalas alkalmazások](./media/scenarios/spa-app.svg)

Ez a hitelesítési folyamat nem tartalmazza az alkalmazásforgatókönyveket, amelyek platformfüggetlen JavaScript-keretrendszereket használnak, például electron és React-Native. További képességekre van szükségük a natív platformokkal való interakcióhoz.

## <a name="specifics"></a>Sajátosságai

Ahhoz, hogy engedélyezze ezt a forgatókönyvet az alkalmazáshoz, a következőkre van szüksége:

* Alkalmazásregisztráció az Azure Active Directoryval (Azure AD). Ez a regisztráció magában foglalja az implicit folyamat engedélyezését és egy átirányítási URI beállítását, amelyre a jogkivonatokat visszaadja.
* Alkalmazáskonfiguráció a regisztrált alkalmazástulajdonságokkal, például az alkalmazásazonosítóval.
* A Microsoft authentication library (MSAL) segítségével végezze el a hitelesítési folyamatot a bejelentkezéshez és a jogkivonatok beszerzéséhez.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Alkalmazásregisztráció](scenario-spa-app-registration.md)
