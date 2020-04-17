---
title: Hogyan kezeljük a SameSite cookie-változásokat a Chrome böngészőben | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogyan kezelhető a SameSite cookie-k változásai a Chrome böngészőben.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 01/27/2020
ms.author: jmprieur
ms.reviewer: kkrishna
ms.custom: aaddev
ms.openlocfilehash: f28d3722d56582bd925d31b43b4a0219bca2ae30
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81534601"
---
# <a name="handle-samesite-cookie-changes-in-chrome-browser"></a>SameSite-cookie-k változásainak kezelése a Chrome böngészőben

## <a name="what-is-samesite"></a>Mi az a SameSite?

`SameSite`egy olyan tulajdonság, amely beállítható a HTTP cookie-kban, hogy megakadályozza a webhelyek közötti kérelem hamisítás (CSRF) támadásokat a webes alkalmazásokban:

- Ha `SameSite` a **rendszer Lax,** a cookie-t küldi kérelmek ugyanazon a helyszínen, és a GET kérelmek más oldalakon. Nem küldi el a GET-kérelmek, amelyek domainek közötti.
- A **Strict** értéke biztosítja, hogy a cookie-kat csak ugyanazon a webhelyen küldje el a rendszer.

Alapértelmezés szerint `SameSite` az érték nincs beállítva a böngészőkben, és ezért nincsenek korlátozások a kérésekben küldött cookie-kra. Egy alkalmazásnak a CSRF-védelemre való jelentkezést a **Lax** vagy **a Strict** beállításával kell engedélyeznie.

## <a name="samesite-changes-and-impact-on-authentication"></a>SameSite változások és hatása a hitelesítésre

A [SameSite szabványainak](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00) legújabb frissítései az alkalmazások védelmét `SameSite` javasolják azáltal, hogy az alapértelmezett viselkedést, ha nincs értéke lax. Ez a megoldás azt jelenti, hogy a cookie-k at a HTTP-kérések korlátozzák, kivéve a más webhelyekről származó GET-t. Ezenkívül a **Nincs** érték érték kerül bevezetésre az elküldött cookie-kra vonatkozó korlátozások megszüntetéséhez. Ezek a frissítések hamarosan megjelennek a Chrome böngésző közelgő verziójában.

Amikor a webalkalmazások a "form_post" válaszmód használatával hitelesítik magukat a Microsoft Identity platformmal, a bejelentkezési kiszolgáló http-post használatával válaszol az alkalmazásra a tokenek vagy az auth kód elküldéséhez. Mivel ez a kérés egy domainek közötti kérés `login.microsoftonline.com` `https://contoso.com/auth`(például a tartományodból ), az alkalmazás által beállított cookie-k most már a Chrome új szabályai alá tartoznak. A cookie-kat, amelyeket használni kell a több webhely forgatókönyvek cookie-kat, amelyek az *állam* és *a nonce* értékeket, amelyek szintén küldött a bejelentkezési kérelmet. Vannak más cookie-k eldobott Az Azure AD a munkamenet megtartása.

Ha nem frissíti a webalkalmazásokat, ez az új viselkedés hitelesítési hibákat eredményez.

## <a name="mitigation-and-samples"></a>Mérséklés és minták

A hitelesítési hibák leküzdése érdekében a Microsoft identity `SameSite` platformmal hitelesítő webalkalmazások beállíthatják a tulajdont a tartományok közötti helyzetekben használt `None` cookie-khoz, amikor a Chrome böngészőben futnak.
Más böngészők [(lásd itt](https://www.chromium.org/updates/same-site/incompatible-clients) a teljes listát) `SameSite` követik a korábbi `SameSite=None` viselkedését, és nem tartalmazza a cookie-kat, ha be van állítva.
Ezért a több böngészőhitelesítés támogatásához a webalkalmazásoknak `SameSite` `None` csak a Chrome-on kell beállítaniuk az értéket, és üresen kell hagyniuk az értéket más böngészőkben.

Ezt a megközelítést az alábbi kódmintáink mutatják be.

# <a name="net"></a>[.NET](#tab/dotnet)

Az alábbi táblázat bemutatja a lekéréses kérelmeket, amelyek a SameSite változások körül működtek a ASP.NET és ASP.NET Core mintákban.

| Sample | Lekéréses kérelem |
| ------ | ------------ |
|  [ASP.NET Core webalkalmazás növekményes oktatóanyaga](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)  |  [Ugyanaz a webhely cookie fix #261](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/pull/261)  |
|  [ASP.NET MVC webalkalmazás mintája](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)  |  [Ugyanaz a webhely cookie fix #35](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/pull/35)  |
|  [active-directory-dotnet-admin-restricted-scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2)  |  [Ugyanaz az oldal cookie fix #28](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2/pull/28)  |

A SameSite cookie-k ASP.NET és ASP.NET Core-ban való kezelésével kapcsolatos részletekért lásd még:

- [SameSite cookie-k ASP.NET Core.](https://docs.microsoft.com/aspnet/core/security/samesite)
- [ASP.NET Blog a SameSite kérdés](https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/)

# <a name="python"></a>[Python](#tab/python)

| Sample |
| ------ |
|  [ms-identity-python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp)  |

# <a name="java"></a>[Java](#tab/java)

| Sample | Lekéréses kérelem |
| ------ | ------------ |
|  [ms-identity-java-webapp](https://github.com/Azure-Samples/ms-identity-java-webapp)  | [Ugyanaz az oldal cookie fix #24](https://github.com/Azure-Samples/ms-identity-java-webapp/pull/24)
|  [ms-identity-java-webapi](https://github.com/Azure-Samples/ms-identity-java-webapi)  | [Ugyanaz a webhely cookie fix #4](https://github.com/Azure-Samples/ms-identity-java-webapi/pull/4)

---

## <a name="next-steps"></a>További lépések

További információ a SameSite és a webalkalmazás forgatókönyvéről:

> [!div class="nextstepaction"]
> [A Google Chrome GYIK ugyanazon webhelyen](https://www.chromium.org/updates/same-site/faq)

> [!div class="nextstepaction"]
> [Chromium SameSite oldal](https://www.chromium.org/updates/same-site)

> [!div class="nextstepaction"]
> [Eset: A felhasználókban bejelentkező webalkalmazás](scenario-web-app-sign-user-overview.md)