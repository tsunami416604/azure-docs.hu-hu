---
title: Fiókok eltávolítása a jogkivonat-gyorsítótárból a kijelentkezéskor – Microsoft Identity platform | Azure
description: Ismerje meg, hogyan távolíthat el egy fiókot a jogkivonat-gyorsítótárból a kijelentkezéskor
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: b7f59f235f4baa270b36b01cc4532227ab23fbc8
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/10/2020
ms.locfileid: "94442531"
---
# <a name="a-web-app-that-calls-web-apis-remove-accounts-from-the-token-cache-on-global-sign-out"></a>Webes API-kat meghívó webalkalmazás: fiókok eltávolítása a jogkivonat-gyorsítótárból a globális kijelentkezéskor

Megtanulta, hogyan adhat hozzá bejelentkezést a webalkalmazáshoz a webalkalmazásban, amely bejelentkezik [a felhasználókba: bejelentkezés és](scenario-web-app-sign-user-sign-in.md)kijelentkezés.

A webes API-kat meghívó webalkalmazások esetében a kijelentkezés nem egyezik. Ha a felhasználó kijelentkezik az alkalmazásból, vagy bármely alkalmazásból, el kell távolítania az adott felhasználóhoz társított jogkivonatokat a jogkivonat-gyorsítótárból.

## <a name="intercept-the-callback-after-single-sign-out"></a>Visszahívás elfogása az egyszeri kijelentkezés után

Ha törölni szeretné a kijelentkezett fiókhoz társított jogkivonat-gyorsítótár bejegyzést, az alkalmazás feltartóztathatja az `logout` eseményt. A Web Apps tárolja a hozzáférési jogkivonatokat a jogkivonat-gyorsítótárban lévő egyes felhasználók számára. A `logout` visszahívást követően a webalkalmazás el tudja távolítani a felhasználót a gyorsítótárból.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

A Microsoft. Identity. Web gondoskodik a kijelentkezés megkezdéséről. További részletek: [Microsoft. Identity. Web forrás kódja](https://github.com/AzureAD/microsoft-identity-web/blob/c29f1a7950b940208440bebf0bcb524a7d6bee22/src/Microsoft.Identity.Web/WebAppExtensions/WebAppCallsWebApiAuthenticationBuilderExtensions.cs#L168-L176)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

A ASP.NET minta nem távolítja el a fiókokat a gyorsítótárból a globális kijelentkezéskor.

# <a name="java"></a>[Java](#tab/java)

A Java-minta nem távolítja el a fiókokat a gyorsítótárból a globális kijelentkezéskor.

# <a name="python"></a>[Python](#tab/python)

A Python-minta nem távolítja el a fiókokat a gyorsítótárból a globális kijelentkezéskor.

---

## <a name="next-steps"></a>További lépések

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Ebben a forgatókönyvben a következő cikkre léphet be, amely a [webalkalmazás tokenjét szerzi](./scenario-web-app-call-api-acquire-token.md?tabs=aspnetcore)be.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Ebben a forgatókönyvben a következő cikkre léphet be, amely a [webalkalmazás tokenjét szerzi](./scenario-web-app-call-api-acquire-token.md?tabs=aspnet)be.

# <a name="java"></a>[Java](#tab/java)

Ebben a forgatókönyvben a következő cikkre léphet be, amely a [webalkalmazás tokenjét szerzi](./scenario-web-app-call-api-acquire-token.md?tabs=java)be.

# <a name="python"></a>[Python](#tab/python)

Ebben a forgatókönyvben a következő cikkre léphet be, amely a [webalkalmazás tokenjét szerzi](./scenario-web-app-call-api-acquire-token.md?tabs=python)be.

---