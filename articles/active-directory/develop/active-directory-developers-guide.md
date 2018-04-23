---
title: Azure Active Directory fejlesztők számára | Microsoft Docs
description: Ez a cikk áttekintést ad a munkahelyi és iskolai Microsoft-fiókokba való, Azure Active Directory használatával végzett bejelentkezésről.
services: active-directory
author: dstrockis
manager: mtillman
editor: ''
ms.assetid: 5c872c89-ef04-4f4c-98de-bc0c7460c7c2
ms.service: active-directory
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/07/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: 580dc7414baea80ef005826d27fb5f2d35ee8895
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
---
# <a name="azure-active-directory-for-developers"></a>Azure Active Directory fejlesztők számára
Az Azure Active Directory (Azure AD) egy olyan felhőalapú identitásszolgáltatás, amely lehetővé teszi a fejlesztők számára az olyan alkalmazások létrehozását, amelyek biztosítják a munkahelyi vagy iskolai Microsoft-fiókkal rendelkező felhasználók biztonságos bejelentkeztetését. Az Azure AD egyaránt támogatja egybérlős, üzletági (LOB) alkalmazások, illetve a több-bérlős alkalmazások fejlesztését. Az alapszintű bejelentkezés mellett az Azure AD lehetővé teszi az alkalmazások számára, hogy Microsoft API-kat (pl. [Microsoft Graph](https://developer.microsoft.com/en-us/graph/docs/concepts/overview)) és az Azure AD platformon alapuló egyéni API-kat egyaránt hívhassanak.  Ebben a dokumentációban megtekintheti, hogyan történik az Azure AD-támogatás hozzáadása az alkalmazásához iparági szabvány szerinti protokollok (pl. OAuth2.0 és OpenID Connect) használatával.

> [!NOTE]
> Az oldal tartalmának nagy része az Azure AD v1-végpontra összpontosít, amely csak a Microsoft munkahelyi vagy iskolai fiókokat támogatja. Ha végfelhasználói és személyes Microsoft-fiókokat is be szeretne jelentkeztetni, tekintse meg az [Azure AD v2.0-végpontokra](active-directory-appmodel-v2-overview.md) vonatkozó további információkat. Az Azure AD v2.0-végpont egységesített fejlesztői élményt biztosít az alkalmazásokhoz, amelyek Azure AD- (munkahelyi és iskolai) és személyes Microsoft-fiókokat egyaránt be szeretnének jelentkeztetni.

| | |
| --- | --- |
|[Hitelesítési alapok](active-directory-authentication-scenarios.md) | Bevezetés az Azure AD-vel történő hitelesítés használatába. |
|[Alkalmazástípusok](active-directory-authentication-scenarios.md#application-types-and-scenarios) | Áttekintés az Azure AD által támogatott hitelesítési forgatókönyvekről. |                                

## <a name="get-started"></a>Bevezetés
Az alábbi irányított beállítások végigvezetik az alkalmazásoknak a kívánt platformon, az Azure Active Directory Library (ADAL) SDK használatával történő létrehozásán. A Microsoft Authentication Libraryvel (MSAL) kapcsolatos további információért tekintse meg az [Azure AD v2.0-végpontra](active-directory-appmodel-v2-overview.md) vonatkozó dokumentációnkat.

|  |  |  |  |
| --- | --- | --- | --- |
| <center>![Mobil- és asztali alkalmazások](./media/active-directory-developers-guide/NativeApp_Icon.png)<br />Mobil- és asztali alkalmazások</center> | [Áttekintés](active-directory-authentication-scenarios.md#native-application-to-web-api)<br /><br />[iOS](active-directory-devquickstarts-ios.md)<br /><br />[Android](active-directory-devquickstarts-android.md) | [.NET (WPF)](active-directory-devquickstarts-dotnet.md)<br /><br />[.NET (UWP)](active-directory-devquickstarts-windowsstore.md)<br /><br />[Xamarin](active-directory-devquickstarts-xamarin.md) | [Cordova](active-directory-devquickstarts-cordova.md) |
| <center>![Webalkalmazások](./media/active-directory-developers-guide/Web_app.png)<br />Webalkalmazások</center> | [Áttekintés](active-directory-authentication-scenarios.md#web-browser-to-web-application)<br /><br />[ASP.NET](active-directory-devquickstarts-webapp-dotnet.md)<br /><br />[Java](active-directory-devquickstarts-webapp-java.md) | [Python](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi)<br/><br/> [Node.js](active-directory-devquickstarts-openidconnect-nodejs.md) | |
| <center>![Egylapos alkalmazások](./media/active-directory-developers-guide/SPA.png)<br />Egylapos alkalmazások</center> | [Áttekintés](active-directory-authentication-scenarios.md#single-page-application-spa)<br /><br />[AngularJS](active-directory-devquickstarts-angular.md)<br /><br />[JavaScript](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |  |  |
| <center>![Webes API-k](./media/active-directory-developers-guide/Web_API.png)<br />Webes API-k</center> | [Áttekintés](active-directory-authentication-scenarios.md#web-application-to-web-api)<br /><br />[ASP.NET](active-directory-devquickstarts-webapi-dotnet.md)<br /><br />[Node.js](active-directory-devquickstarts-webapi-nodejs.md) | &nbsp; |
| <center>![Szolgáltatások közötti](./media/active-directory-developers-guide/Service_App.png)<br />Szolgáltatások közötti</center> | [Áttekintés](active-directory-authentication-scenarios.md#daemon-or-server-application-to-web-api)<br /><br />[.NET](active-directory-code-samples.md#server-or-daemon-application-to-web-api)|  |

## <a name="how-to-guides"></a>Útmutatók
Az alábbi útmutatók végigvezetik az Azure AD néhány gyakori feladatán.

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


[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
