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
ms.date: 09/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: e138b3513b42dda47b0a114d866d657e18e3e393
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "82181647"
---
# <a name="a-web-app-that-calls-web-apis-remove-accounts-from-the-token-cache-on-global-sign-out"></a>Webes API-kat meghívó webalkalmazás: fiókok eltávolítása a jogkivonat-gyorsítótárból a globális kijelentkezéskor

Megtanulta, hogyan adhat hozzá bejelentkezést a webalkalmazáshoz a webalkalmazásban, amely bejelentkezik [a felhasználókba: bejelentkezés és](scenario-web-app-sign-user-sign-in.md)kijelentkezés.

A webes API-kat meghívó webalkalmazások esetében a kijelentkezés nem egyezik. Ha a felhasználó kijelentkezik az alkalmazásból, vagy bármely alkalmazásból, el kell távolítania az adott felhasználóhoz társított jogkivonatokat a jogkivonat-gyorsítótárból.

## <a name="intercept-the-callback-after-single-sign-out"></a>Visszahívás elfogása az egyszeri kijelentkezés után

Ha törölni szeretné a kijelentkezett fiókhoz társított jogkivonat-gyorsítótár bejegyzést, az alkalmazás feltartóztathatja az `logout` eseményt. A Web Apps tárolja a hozzáférési jogkivonatokat a jogkivonat-gyorsítótárban lévő egyes felhasználók számára. A `logout` visszahívást követően a webalkalmazás el tudja távolítani a felhasználót a gyorsítótárból.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

A Microsoft. Identity. Web gondoskodik a kijelentkezés megkezdéséről.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

A ASP.NET minta nem távolítja el a fiókokat a gyorsítótárból a globális kijelentkezéskor.

# <a name="java"></a>[Java](#tab/java)

A Java-minta nem távolítja el a fiókokat a gyorsítótárból a globális kijelentkezéskor.

# <a name="python"></a>[Python](#tab/python)

A Python-minta nem távolítja el a fiókokat a gyorsítótárból a globális kijelentkezéskor.

---

## <a name="next-steps"></a>További lépések

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [Jogkivonat beszerzése a webalkalmazáshoz](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=aspnetcore)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [Jogkivonat beszerzése a webalkalmazáshoz](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=aspnet)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Jogkivonat beszerzése a webalkalmazáshoz](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=java)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Jogkivonat beszerzése a webalkalmazáshoz](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=python)

---
