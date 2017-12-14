---
title: "Azure Active Directory fejlesztők számára | Microsoft Docs"
description: "Ez a cikk áttekintést ad a munkahelyi és iskolai Microsoft-fiókokba való, Azure Active Directory használatával végzett bejelentkezésről."
services: active-directory
author: dstrockis
manager: mtillman
editor: 
ms.assetid: 5c872c89-ef04-4f4c-98de-bc0c7460c7c2
ms.service: active-directory
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/07/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: c7375b91790cc7a089560bd823e99850e3b34fec
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-for-developers"></a>Azure Active Directory fejlesztők számára
Az Azure Active Directory (Azure AD) egy olyan felhőalapú identitásszolgáltatás, amely lehetővé teszi a fejlesztők számára a munkahelyi vagy iskolai Microsoft-fiókkal rendelkező felhasználók biztonságos bejelentkeztetését. Ebben a dokumentációban megtekintheti, hogyan történik az Azure AD-támogatás hozzáadása az alkalmazásához iparági szabvány szerinti protokollok (OAuth és OpenID Connect) használatával.

| | |
| --- | --- |
|[Hitelesítési alapok](active-directory-authentication-scenarios.md) | Bevezetés az Azure AD-vel történő hitelesítés használatába. |
|[Alkalmazástípusok](active-directory-authentication-scenarios.md#application-types-and-scenarios) | Áttekintés az Azure AD által támogatott hitelesítési forgatókönyvekről. |                                
                                                                              
## <a name="get-started"></a>Bevezetés
A következő irányított beállítási folyamatok végigvezetik az Azure AD-felhasználók Microsoft hitelesítési kódtárainak használatával történő bejelentkeztetési folyamatán.

|  |  |  |  |
| --- | --- | --- | --- |
| <center>![Mobil- és asztali alkalmazások](./media/active-directory-developers-guide/NativeApp_Icon.png)<br />Mobil- és asztali alkalmazások</center> | [Áttekintés](active-directory-authentication-scenarios.md#native-application-to-web-api)<br /><br />[iOS](active-directory-devquickstarts-ios.md)<br /><br />[Android](active-directory-devquickstarts-android.md) | [.NET](active-directory-devquickstarts-dotnet.md)<br /><br />[Windows](active-directory-devquickstarts-windowsstore.md)<br /><br />[Xamarin](active-directory-devquickstarts-xamarin.md) | [Cordova](active-directory-devquickstarts-cordova.md)<br /><br />[OAuth 2.0](active-directory-protocols-oauth-code.md) |
| <center>![Webalkalmazások](./media/active-directory-developers-guide/Web_app.png)<br />Webalkalmazások</center> | [Áttekintés](active-directory-authentication-scenarios.md#web-browser-to-web-application)<br /><br />[ASP.NET](active-directory-devquickstarts-webapp-dotnet.md)<br /><br />[Java](active-directory-devquickstarts-webapp-java.md) | [Node.js](active-directory-devquickstarts-openidconnect-nodejs.md)<br /><br />[OpenID Connect 1.0](active-directory-protocols-openid-connect-code.md) |  |
| <center>![Egylapos alkalmazások](./media/active-directory-developers-guide/SPA.png)<br />Egylapos alkalmazások</center> | [Áttekintés](active-directory-authentication-scenarios.md#single-page-application-spa)<br /><br />[AngularJS](active-directory-devquickstarts-angular.md)<br /><br />[JavaScript](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |  |  |
| <center>![Webes API-k](./media/active-directory-developers-guide/Web_API.png)<br />Webes API-k</center> | [Áttekintés](active-directory-authentication-scenarios.md#web-application-to-web-api)<br /><br />[ASP.NET](active-directory-devquickstarts-webapi-dotnet.md)<br /><br />[Node.js](active-directory-devquickstarts-webapi-nodejs.md) | &nbsp; |
| <center>![Szolgáltatások közötti](./media/active-directory-developers-guide/Service_App.png)<br />Szolgáltatások közötti</center> | [Áttekintés](active-directory-authentication-scenarios.md#daemon-or-server-application-to-web-api)<br /><br />[.NET](active-directory-code-samples.md#server-or-daemon-application-to-web-api)<br /><br />[OAuth 2.0-ügyfél hitelesítő adatai](active-directory-protocols-oauth-service-to-service.md) |  |

## <a name="how-to-guides"></a>Útmutatók
A következő útmutatókból megtudhatja, hogyan hajthat végre gyakori feladatokat az Azure AD használatával.

|                                                                           |  |
|---------------------------------------------------------------------------| --- |
|[Alkalmazásregisztráció](active-directory-integrating-applications.md)           | Alkalmazás regisztrálása az Azure AD-ben. |
|[Több-bérlős alkalmazások](active-directory-devhowto-multi-tenant-overview.md)    | Bejelentkezés bármely munkahelyi Microsoft-fiókba. |
|[OAuth és OpenID Connect protokollok](active-directory-protocols-openid-connect-code.md)| Felhasználók bejelentkeztetése és webes API-k hívása a Microsoft hitelesítési protokolljaival. |
|[További útmutatók](active-directory-developers-guide-index.md#guides)        |  Az Azure AD-hez elérhető útmutatók listája.   |

## <a name="reference-topics"></a>Referencia-témakörök
A következő cikkekben az API-król, a protokollüzenetekről és az Azure AD által használt kifejezésekről találhat részletes információkat.

|                                                                                   | |
| ----------------------------------------------------------------------------------| --- |
| [Hitelesítési tárak (ADAL)](active-directory-authentication-libraries.md)   | Áttekintés az Azure AD által támogatott kódtárakról és SDK-król. |
| [Kódminták](active-directory-code-samples.md)                                  | Az Azure AD összes kódmintáját tartalmazó lista. |
| [Szószedet](active-directory-dev-glossary.md)                                      | A jelen dokumentációban használt fogalmak terminológiája és meghatározásai. |
| [További referencia-témakörök](active-directory-developers-guide-index.md#reference)| Az Azure AD-hez elérhető referencia-témakörök listája.   |


> [!NOTE]
> Ha szeretne bejelentkezni a személyes Microsoft-fiókjaiba, érdemes megfontolnia az [Azure AD v2.0-végpont](active-directory-appmodel-v2-overview.md) használatát. Az Azure AD v2.0-végpont egyetlen hitelesítési rendszerben egyesíti a személyes és munkahelyi Microsoft-fiókjait (az Azure AD-ből).


[!INCLUDE  [Help and support](../../../includes/active-directory-develop-help-support-include.md)]