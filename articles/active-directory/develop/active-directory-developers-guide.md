---
title: "Azure Active Directory fejlesztők számára | Microsoft Docs"
description: "Ez a cikk áttekintést ad a munkahelyi és iskolai Microsoft-fiókokba az Azure Active Directory használatával történő bejelentkezésről."
services: active-directory
author: dstrockis
manager: mbaldwin
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
ms.openlocfilehash: dce813b885d492343428428056a2e8aada27b461
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="azure-active-directory-for-developers"></a>Azure Active Directory fejlesztők számára
Az Azure Active Directory egy olyan felhőalapú identitásszolgáltatás, amely lehetővé teszi a fejlesztők számára bármely munkahelyi vagy iskolai Microsoft-fiókkal rendelkező felhasználó biztonságos bejelentkeztetését.  E dokumentációban megtekintheti, hogyan történik az Azure AD-támogatás hozzáadása az alkalmazásához iparági szabvány szerinti hitelesítési protokollok (OAuth és OpenID Connect) használatával.

| | |
| --- | --- |
|[A hitelesítés alapjai](active-directory-authentication-scenarios.md) | Bevezetés az Azure AD-vel történő hitelesítés használatába |
|[Alkalmazástípusok](active-directory-authentication-scenarios.md#application-types-and-scenarios) | Áttekintés az Azure AD által támogatott hitelesítési forgatókönyvekről |                                
                                                                              
## <a name="get-started"></a>Bevezetés
Az alábbi irányított beállítási folyamatok végigvezetik az Azure Active Directory-felhasználók saját hitelesítési könyvtárának használatával történő bejelentkeztetési folyamatán.

|  |  |  |  |
| --- | --- | --- | --- |
| <center>![Mobil- és asztali appok](./media/active-directory-developers-guide/NativeApp_Icon.png)<br />Mobil- és asztali appok</center> | [Áttekintés](active-directory-authentication-scenarios.md#native-application-to-web-api)<br /><br />[iOS](active-directory-devquickstarts-ios.md)<br /><br />[Android](active-directory-devquickstarts-android.md) | [.NET](active-directory-devquickstarts-dotnet.md)<br /><br />[Windows](active-directory-devquickstarts-windowsstore.md)<br /><br />[Xamarin](active-directory-devquickstarts-xamarin.md) | [Cordova](active-directory-devquickstarts-cordova.md)<br /><br />[OAuth 2.0](active-directory-protocols-oauth-code.md) |
| <center>![Web Apps](./media/active-directory-developers-guide/Web_app.png)<br />Web Apps</center> | [Áttekintés](active-directory-authentication-scenarios.md#web-browser-to-web-application)<br /><br />[ASP.NET](active-directory-devquickstarts-webapp-dotnet.md)<br /><br />[Java](active-directory-devquickstarts-webapp-java.md) | [NodeJS](active-directory-devquickstarts-openidconnect-nodejs.md)<br /><br />[OpenID Connect 1.0](active-directory-protocols-openid-connect-code.md) |  |
| <center>![Egylapos appok](./media/active-directory-developers-guide/SPA.png)<br />Egylapos appok</center> | [Áttekintés](active-directory-authentication-scenarios.md#single-page-application-spa)<br /><br />[AngularJS](active-directory-devquickstarts-angular.md)<br /><br />[JavaScript](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |  |  |
| <center>![Webes API-k](./media/active-directory-developers-guide/Web_API.png)<br />Webes API-k</center> | [Áttekintés](active-directory-authentication-scenarios.md#web-application-to-web-api)<br /><br />[ASP.NET](active-directory-devquickstarts-webapi-dotnet.md)<br /><br />[NodeJS](active-directory-devquickstarts-webapi-nodejs.md) | &nbsp; |
| <center>![Szolgáltatások közötti](./media/active-directory-developers-guide/Service_App.png)<br />Szolgáltatások közötti</center> | [Áttekintés](active-directory-authentication-scenarios.md#daemon-or-server-application-to-web-api)<br /><br />[.NET](active-directory-code-samples.md#server-or-daemon-application-to-web-api)<br /><br />[OAuth 2.0-ügyfél hitelesítő adatai](active-directory-protocols-oauth-service-to-service.md) |  |

## <a name="guides"></a>Útmutatók
Ebből a cikkből megtudhatja, hogyan hajthat végre gyakori feladatokat az Azure Active Directory használatával.

|                                                                           |  |
|---------------------------------------------------------------------------| --- |
|[Alkalmazásregisztráció](active-directory-integrating-applications.md)           | Alkalmazás regisztrálása az Azure AD-ben |
|[Több-bérlős alkalmazások](active-directory-devhowto-multi-tenant-overview.md)    | Belépés bármely munkahelyi Microsoft-fiókba |
|[OAuth és OpenID Connect](active-directory-protocols-openid-connect-code.md)| Felhasználók beléptetése és webes API-k hívása a modern hitelesítési protokolljaink használatával |
|[További útmutatók…](active-directory-developers-guide-index.md#guides)        |     |

## <a name="reference"></a>Referencia
Ezekben a cikkekben az API-król, a protokollüzenetekről és az Azure Active Directory által használt kifejezésekről találhat részletes információkat.

|                                                                                   | |
| ----------------------------------------------------------------------------------| --- |
| [Hitelesítési tárak (ADAL)](active-directory-authentication-libraries.md)   | Áttekintés az Azure AD által támogatott tárakról és SDK-król |
| [Kódminták](active-directory-code-samples.md)                                  | Az Azure AD összes kódmintáját tartalmazó lista |
| [Szószedet](active-directory-dev-glossary.md)                                      | A jelen dokumentációban használt fogalmak terminológiája és meghatározásai |
| [További referenciaanyagok…](active-directory-developers-guide-index.md#reference)|     |

## <a name="help--support"></a>Súgó és támogatás
Az Azure Active Directoryban történő fejlesztéshez ezeken a helyeken találhat segítséget.

|  |  
|---|
|[A Stack Overflow `azure-active-directory` és `adal` címkéi](http://stackoverflow.com/questions/tagged/azure-active-directory+or+adal)      |
|[Visszajelzés az Azure Active Directoryról](https://feedback.azure.com/forums/169401-azure-active-directory/category/164757-developer-experiences)|
| [Próbálja ki a Microsoft Dev Chatet (korlátozott ideig ingyenes)](http://aka.ms/devchat) |

<br />

> [!NOTE]
> Ha szeretne bejelentkezni a személyes Microsoft-fiókjaiba, érdemes megfontolnia az [Azure AD v2.0-végpont](active-directory-appmodel-v2-overview.md) használatát.  Az Azure AD v2.0-végpont egyetlen hitelesítési rendszerben egyesíti a személyes és munkahelyi Microsoft-fiókjait (az Azure AD-ből).
