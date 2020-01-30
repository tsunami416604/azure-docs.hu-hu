---
title: A SameSite-cookie-változások kezelése a Chrome böngészőben | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogyan kezelheti a SameSite-cookie-módosításokat a Chrome böngészőben.
services: active-directory
documentationcenter: ''
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
ms.openlocfilehash: 8fc1fab89a89fbf7e20414f292a1b02f77ac7907
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2020
ms.locfileid: "76776364"
---
# <a name="handle-samesite-cookie-changes-in-chrome-browser"></a>SameSite-cookie-változások kezelése a Chrome böngészőben

## <a name="what-is-samesite"></a>Mi az a SameSite?

a `SameSite` egy olyan tulajdonság, amely HTTP-cookie-kon állítható be, hogy megakadályozza a helyek közötti kérelmek hamisításának (CSRF) támadásait a webalkalmazásokban:

- Ha `SameSite` a **LAX**értékre van állítva, a cookie-t a rendszer ugyanazon a helyen és a más helyekről érkező kérésekben fogadja. A rendszer nem fogadja el a tartományok közötti GET kérésekben.
- A **szigorú** érték biztosítja, hogy a cookie-t csak ugyanazon a helyen belül küldje el a rendszer a kérelmekben.

Alapértelmezés szerint a `SameSite` érték nincs beállítva a böngészőkben, ezért a rendszer nem korlátozza a kérésekben a cookie-k küldésére vonatkozó korlátozásokat. Egy alkalmazásnak a CSRF-védelemre kell kattintania a **LAX** vagy **szigorú** beállításával.

## <a name="samesite-changes-and-impact-on-authentication"></a>SameSite változások és a hitelesítés hatása

A [SameSite vonatkozó szabványok legutóbbi frissítései](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00) a `SameSite` alapértelmezett viselkedését javasolják, ha nincs beállítva érték a LAX értékre. Ez a megoldás azt jelenti, hogy a cookie-k a HTTP-kérelmekre korlátozottak lesznek, kivéve a más helyekről történő letöltést. Emellett a **none** érték nem lesz bevezetve az elküldött cookie-k korlátozásának eltávolításához. Ezek a frissítések hamarosan a Chrome böngésző egy későbbi verziójában jelennek meg.

Ha a webalkalmazások a "form_post" válasz mód használatával hitelesítik a Microsoft Identity platformot, a bejelentkezési kiszolgáló egy HTTP-POST használatával válaszol az alkalmazásra, hogy elküldje a jogkivonatokat vagy az hitelesítési kódot. Mivel ez a kérelem tartományon kívüli kérelem (`login.microsoftonline.com`ról a tartományra – például https://contoso.com/auth) az alkalmazás által beállított cookie-k már a Chrome új szabályai alá tartoznak. A helyek közötti forgatókönyvekben használandó cookie-k az *állapotot* *és az* egyszer használatos értékeket tartalmazó cookie-k, amelyeket a rendszer a bejelentkezési kérelemben is elküld. Az Azure AD más cookie-kat is eldobott a munkamenet tárolására.

Ha nem frissíti a webalkalmazásokat, ez az új viselkedés a hitelesítési hibákhoz vezet.

## <a name="mitigation-and-samples"></a>Enyhítés és minták

A hitelesítési hibák elhárítása érdekében a Microsoft Identity platformmal hitelesítő webalkalmazások a `SameSite` tulajdonságot úgy állíthatják be, hogy `None` a tartományok közötti forgatókönyvekben használt cookie-k esetén, amikor a Chrome böngészőben futnak.
Más böngészők [(lásd a](https://www.chromium.org/updates/same-site/incompatible-clients) teljes listát) kövesse a `SameSite` korábbi viselkedését, és ha `SameSite=None` van beállítva, a cookie-kat nem fogja tartalmazni.
Ezért a több böngészőből álló webalkalmazások hitelesítésének támogatásához a `SameSite` értéket kell megadnia, hogy csak a Chrome-on `None`, és más böngészőkben ne hagyja üresen az értéket.

Ezt a módszert az alábbi kódrészletek szemléltetik.

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

Az alábbi táblázat azokat a lekéréses kérelmeket mutatja be, amelyek a SameSite megváltoztak a ASP.NET és a ASP.NET Core mintákban.

| Minta | Lekéréses kérelem |
| ------ | ------------ |
|  [ASP.NET Core Web App növekményes oktatóanyaga](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)  |  [Ugyanazon a helyen lévő cookie-javítás #261](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/pull/261)  |
|  [ASP.NET MVC webalkalmazás minta](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)  |  [Ugyanazon a helyen lévő cookie-javítás #35](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/pull/35)  |
|  [Active-Directory-DotNet-rendszergazda-korlátozott-hatókörök-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2)  |  [Ugyanazon a helyen lévő cookie-javítás #28](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2/pull/28)  |

a SameSite-cookie-k ASP.NET és ASP.NET Core való kezelésével kapcsolatos részletekért lásd még:

- [SameSite-cookie-k használata ASP.net Coreban](https://docs.microsoft.com/aspnet/core/security/samesite) .
- [ASP.NET blog a SameSite-probléma megoldásához](https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/)

# <a name="pythontabpython"></a>[Python](#tab/python)

| Minta |
| ------ |
|  [MS-Identity-Python-WebApp](https://github.com/Azure-Samples/ms-identity-python-webapp)  |

# <a name="javatabjava"></a>[Java](#tab/java)

| Minta | Lekéréses kérelem |
| ------ | ------------ |
|  [MS-Identity-Java-WebApp](https://github.com/Azure-Samples/ms-identity-java-webapp)  | [Ugyanazon a helyen lévő cookie-javítás #24](https://github.com/Azure-Samples/ms-identity-java-webapp/pull/24)
|  [MS-Identity-Java-webapi](https://github.com/Azure-Samples/ms-identity-java-webapi)  | [Ugyanazon a helyen lévő cookie-javítás #4](https://github.com/Azure-Samples/ms-identity-java-webapi/pull/4)

---

## <a name="next-steps"></a>Következő lépések

További információ a SameSite és a webalkalmazási forgatókönyvről:

> [!div class="nextstepaction"]
> [Google Chrome GYIK a SameSite-on](https://www.chromium.org/updates/same-site/faq)

> [!div class="nextstepaction"]
> [Króm SameSite lapja](https://www.chromium.org/updates/same-site)

> [!div class="nextstepaction"]
> [Forgatókönyv: a felhasználók által bejelentkezett webalkalmazás](scenario-web-app-sign-user-overview.md)