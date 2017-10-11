---
title: "Áttekintés – Azure AD B2C | Microsoft Docs"
description: "A felhasználók felé néző alkalmazások fejlesztése az Azure Active Directory B2C-vel"
services: active-directory-b2c
documentationcenter: 
author: saeedakhter-msft
manager: krassk
editor: parakhj
ms.assetid: c465dbde-f800-4f2e-8814-0ff5f5dae610
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 12/06/2016
ms.author: saeedakhter-msft
ms.openlocfilehash: 44d5d31d49c375c802a67511d1f962df20656559
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="azure-ad-b2c-focus-on-your-app-let-us-worry-about-sign-up-and-sign-in"></a>Azure AD B2C: Összpontosítson az alkalmazására, a regisztráció és a bejelentkezés a mi feladatunk

Az Azure AD B2C egy felhőbeli identitáskezelő megoldás a webes és mobilalkalmazásokhoz. Ez a globális szolgáltatás magas rendelkezésre állást biztosít, és akár több száz millió felhasználói identitásra is méretezhető. A vállalati szintű biztonsági platformra épülő Azure AD B2C védelmet nyújt alkalmazásainak, vállalatának és ügyfeleinek.

Az Azure AD B2C minimális konfigurálással lehetővé teszi az alkalmazásnak a következők hitelesítését:

* **Közösségi fiókok** (például Facebook, Google, LinkedIn stb.)
* **Vállalati fiókok** (nyílt szabványos protokollok, OpenID Connect vagy SAML használata esetén)
* **Helyi fiókok** (e-mail-cím és jelszó vagy felhasználónév és jelszó)

## <a name="get-started"></a>Bevezetés

Először hozzon létre egy saját bérlőt az [Azure AD B2C-bérlő létrehozása](active-directory-b2c-get-started.md) című részben ismertetett lépések segítségével.

Ezután válassza ki az alkalmazásfejlesztési forgatókönyvet:

|  |  |  |  |
| --- | --- | --- | --- |
| <center>![Mobil- és asztali appok](../active-directory/develop/media/active-directory-developers-guide/NativeApp_Icon.png)<br />Mobil- és asztali appok</center> | [Áttekintés](active-directory-b2c-reference-oauth-code.md)&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<br /><br />[iOS](https://github.com/Azure-Samples/active-directory-b2c-ios-swift-native-msal)<br /><br />[Android](https://github.com/Azure-Samples/active-directory-b2c-android-native-msal) | [.NET](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop)<br /><br />[Xamarin](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) |  |
| <center>![Web Apps](../active-directory/develop/media/active-directory-developers-guide/Web_app.png)<br />Web Apps</center> | [Áttekintés](active-directory-b2c-reference-oidc.md)<br /><br />[ASP.NET](active-directory-b2c-devquickstarts-web-dotnet-susi.md)<br /><br />[ASP.NET Core](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapp) | [Node.js](active-directory-b2c-devquickstarts-web-node.md) |  |
| <center>![Egylapos appok](../active-directory/develop/media/active-directory-developers-guide/SPA.png)<br />Egylapos appok</center> | [Áttekintés](active-directory-b2c-reference-spa.md)<br /><br />[JavaScript](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)<br /><br /> |  |  |
| <center>![Webes API-k](../active-directory/develop/media/active-directory-developers-guide/Web_API.png)<br />Webes API-k</center> | [ASP.NET](active-directory-b2c-devquickstarts-api-dotnet.md)<br /><br /> [ASP.NET Core](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi)<br /><br /> [Node.js](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) | [.NET-alapú webes API hívása](active-directory-b2c-devquickstarts-web-api-dotnet.md) |

## <a name="whats-new"></a>Újdonságok

Keresse fel rendszeresen ezt a lapot, hiszen itt szerezhet tudomást az Azure Active Directory B2C-t érintő változásokról. A frissítésekről @AzureAD néven Twitter-üzeneteket is küldünk.

* Az általánosan elérhető „Beépített szabályzatok” funkció mellett mostantól az [„Egyéni szabályzatok”](active-directory-b2c-overview-custom.md) funkció is elérhető nyilvános előzetes verzióban.  Az egyéni szabályzatokat az identitáskezelő szakemberek használhatják, akik felügyelni szeretnék az identitáskezelési feladatok összetételét.
* Mostantól nyilvános előzetes verzióban elérhető az [Access Token](https://azure.microsoft.com/en-us/blog/azure-ad-b2c-access-tokens-now-in-public-preview) funkció.
* Bejelentettük az [európai Azure AD B2C-könyvtárak](https://azure.microsoft.com/en-us/blog/azuread-b2c-ga-eu/) általános elérhetőségét.
* Tekintse meg a [kódpéldák folyamatosan bővülő könyvtárát a Githubon](https://github.com/Azure-Samples?q=b2c)!

## <a name="how-to-articles"></a>Útmutatók

Ismerje meg, hogyan használhatja az Azure Active Directory B2C egyes szolgáltatásait:

* [Facebook-](active-directory-b2c-setup-fb-app.md), [Google+-](active-directory-b2c-setup-goog-app.md), [Microsoft-](active-directory-b2c-setup-msa-app.md), [Amazon-](active-directory-b2c-setup-amzn-app.md) és [LinkedIn-](active-directory-b2c-setup-li-app.md)fiókok beállítása a felhasználók felé néző alkalmazásokban való használatra
* [Egyéni attribútumok használata a felhasználókról való adatgyűjtéshez](active-directory-b2c-reference-custom-attr.md)
* [Az Azure Multi-Factor Authentication alkalmazása a felhasználók felé néző alkalmazásokban](active-directory-b2c-reference-mfa.md)
* [Önkiszolgáló jelszóátállítás a felhasználók számára](active-directory-b2c-reference-sspr.md)
* Az Azure Active Directory B2C által üzemeltetett, [felhasználók felé irányuló regisztrációs, bejelentkezési és más lapok megjelenésének és használatának testreszabása](active-directory-b2c-reference-ui-customization.md)
* [Felhasználók programokon keresztüli létrehozása, beolvasása, frissítése és törlése az Azure Active Directory Graph API segítségével](active-directory-b2c-devquickstarts-graph-dotnet.md) az Azure Active Directory B2C-bérlőben

## <a name="next-steps"></a>Következő lépések

Az alábbi hivatkozások segítségével mélyebben is megismerheti a szolgáltatást:

* Tekintse meg az [Azure Active Directory B2C díjszabásával kapcsolatos információkat](https://azure.microsoft.com/pricing/details/active-directory-b2c/).
* Tekintse át az Azure Active Directory B2C-vel kapcsolatos [kódmintákat](https://azure.microsoft.com/en-us/resources/samples/?service=active-directory&term=b2c). 
* Keressen hasznos információkat a Stack Overflow oldalon az [azure-ad-b2c](http://stackoverflow.com/questions/tagged/azure-ad-b2c) címke segítségével.
* Ossza meg velünk gondolatait a [felhasználói visszajelzési webhelyen](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c). Szívesen veszünk minden visszajelzést.
* Tekintse át az [Azure AD B2C protokollreferenciáját](active-directory-b2c-reference-protocols.md).
* Tekintse át az [Azure AD B2C jogkivonat-referenciáját](active-directory-b2c-reference-tokens.md).
* Olvassa el az [Azure Active Directory B2C-vel kapcsolatos gyakori kérdéseket](active-directory-b2c-faqs.md).
* [Az Azure Active Directory B2C-vel kapcsolatos fájltámogatási kérések](active-directory-b2c-support.md).

## <a name="get-security-updates-for-our-products"></a>Biztonsági frissítések termékeinkhez

Javasoljuk, hogy kérjen értesítést a bekövetkező biztonsági incidensekről. Látogasson el [erre a lapra](https://technet.microsoft.com/security/dd252948), és fizessen elő a biztonsági tanácsadói riasztásokra.

