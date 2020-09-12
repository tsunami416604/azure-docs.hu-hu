---
title: JavaScript egyoldalas alkalmazás forgatókönyve – Microsoft Identity platform | Azure
description: Ismerje meg, hogyan hozhat létre egy egyoldalas alkalmazást a Microsoft Identity platform használatával (forgatókönyv áttekintése).
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 60e4ca80faa2c8787a13d87ab06cad9243299e50
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/02/2020
ms.locfileid: "89291950"
---
# <a name="scenario-single-page-application"></a>Forgatókönyv: egyoldalas alkalmazás

Ismerje meg az egyoldalas alkalmazások (SPA) létrehozásához szükséges tudnivalókat.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Első lépések

Az első alkalmazást az alábbi JavaScript SPA-útmutató segítségével hozhatja létre:

> [!div class="nextstepaction"]
> [Rövid útmutató: egyoldalas alkalmazás](./quickstart-v2-javascript.md)

## <a name="overview"></a>Áttekintés

Számos modern webalkalmazás úgy van kialakítva, mint az ügyféloldali egyoldalas alkalmazások. A fejlesztők a JavaScript vagy a SPA-keretrendszer, például a szögletes, a Vue és a reakciók használatával írhatják őket. Ezek az alkalmazások egy webböngészőben futnak, és különböző hitelesítési jellemzőkkel rendelkeznek, mint a hagyományos kiszolgálóoldali webes alkalmazások. 

A Microsoft Identity platform **két** lehetőséget kínál, amelyek lehetővé teszik egyoldalas alkalmazások bejelentkezését a felhasználókba, és jogkivonatok beszerzését a háttérbeli szolgáltatások vagy webes API-k eléréséhez:

- [OAuth 2,0 engedélyezési kód folyamata (PKCE)](./v2-oauth2-auth-code-flow.md). Az engedélyezési kód folyamatábrája lehetővé teszi, hogy az alkalmazás hitelesítő kódot cseréljen az **azonosító** jogkivonatok számára a védett API-k meghívásához szükséges hitelesített felhasználói és **hozzáférési** jogkivonatok megjelenítéséhez. Emellett visszaadja azokat a **frissítési** jogkivonatokat, amelyek hosszú távú hozzáférést biztosítanak az erőforrásokhoz a felhasználók nevében anélkül, hogy ezekkel a felhasználókkal való interakcióra lenne szükség. Ez az **ajánlott** módszer.

![Egyoldalas alkalmazások – hitelesítés](./media/scenarios/spa-app-auth.svg)

- [OAuth 2,0 implicit folyamat](./v2-oauth2-implicit-grant-flow.md). Az implicit engedélyezési folyamat lehetővé teszi, hogy az alkalmazás **azonosítót** és **hozzáférési** jogkivonatokat kapjon. Az engedélyezési kód folyamatával ellentétben az implicit engedélyezési folyamat nem ad vissza **frissítési jogkivonatot**.

![Egyoldalas alkalmazások – implicit](./media/scenarios/spa-app.svg)

Ez a hitelesítési folyamat nem tartalmaz olyan alkalmazási helyzeteket, amelyek platformfüggetlen JavaScript-keretrendszereket használnak, például az Electron-et és a reakciós Natívt. További képességeket igényelnek a natív platformokkal való interakcióhoz.

## <a name="specifics"></a>Sajátosságai

Ha ezt a forgatókönyvet szeretné engedélyezni az alkalmazásához, a következőkre lesz szüksége:

* Az alkalmazás regisztrálása Azure Active Directory (Azure AD) szolgáltatással. A regisztrációs lépések eltérnek az implicit engedélyezési folyamat és az engedélyezési kód folyamata között.
* Alkalmazás konfigurációja a regisztrált alkalmazás tulajdonságaival, például az alkalmazás azonosítójával.
* A Microsoft Authentication Library for JavaScript (MSAL.js) használata a hitelesítési folyamat a bejelentkezéshez és a jogkivonatok beszerzéséhez.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Alkalmazásregisztráció](scenario-spa-app-registration.md)
