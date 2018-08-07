---
title: Azure Active Directory fejlesztők számára | Microsoft Docs
description: Ez a cikk áttekintést ad a munkahelyi és iskolai Microsoft-fiókokba való, Azure Active Directory használatával végzett bejelentkezésről.
services: active-directory
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 5c872c89-ef04-4f4c-98de-bc0c7460c7c2
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/30/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 41fdf1a61538e43ef17eee2937273f56a4538b76
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39577203"
---
# <a name="azure-active-directory-for-developers"></a>Azure Active Directory fejlesztők számára

Az Azure Active Directory (Azure AD) egy olyan felhőalapú identitásszolgáltatás, amely lehetővé teszi a fejlesztők számára az olyan alkalmazások létrehozását, amelyek biztosítják a munkahelyi vagy iskolai Microsoft-fiókkal rendelkező felhasználók biztonságos bejelentkeztetését. Az Azure AD egyaránt támogatja egybérlős, üzletági (LOB) alkalmazások, illetve a több-bérlős alkalmazások fejlesztését. Az alapszintű bejelentkezés mellett az Azure AD lehetővé teszi az alkalmazások számára, hogy Microsoft API-kat (pl. [Microsoft Graph](https://developer.microsoft.com/en-us/graph/docs/concepts/overview)) és az Azure AD platformon alapuló egyéni API-kat egyaránt hívhassanak. Ebben a dokumentációban megtekintheti, hogyan történik az Azure AD-támogatás hozzáadása az alkalmazásához iparági szabvány szerinti protokollok (pl. OAuth2.0 és OpenID Connect) használatával.

> [!NOTE]
> Az oldal tartalmának nagy része az Azure AD v1.0-végpontra összpontosít, amely csak a Microsoft munkahelyi vagy iskolai fiókokat támogatja. Ha végfelhasználói és személyes Microsoft-fiókokat is be szeretne jelentkeztetni, tekintse meg az [Azure AD v2.0-végpontokra](active-directory-appmodel-v2-overview.md) vonatkozó információkat. Az Azure AD v2.0-végpont egységesített fejlesztői élményt biztosít az alkalmazásokhoz, amelyek Azure AD- (munkahelyi és iskolai) és személyes Microsoft-fiókokat egyaránt be szeretnének jelentkeztetni.

| | |
| --- | --- |
|[Hitelesítési alapok](authentication-scenarios.md) | Bevezetés az Azure AD-vel történő hitelesítés használatába. |
|[Alkalmazástípusok](authentication-scenarios.md#application-types-and-scenarios) | Áttekintés az Azure AD által támogatott hitelesítési forgatókönyvekről. |      
| | |

## <a name="get-started"></a>Bevezetés
Az alábbi irányított beállítások végigvezetik az alkalmazásoknak a kívánt platformon, az Azure AD Authentication Library (ADAL) SDK használatával történő létrehozásán. A Microsoft Authentication Libraryvel (MSAL) kapcsolatos további információért tekintse meg az [Azure AD v2.0-végpontra](active-directory-appmodel-v2-overview.md) vonatkozó dokumentációnkat.

|  |  |  |  |
| --- | --- | --- | --- |
| <center>![Mobil- és asztali alkalmazások](./media/azure-ad-developers-guide/NativeApp_Icon.png)<br />Mobil- és asztali alkalmazások</center> | [Áttekintés](authentication-scenarios.md#native-application-to-web-api)<br /><br />[iOS](quickstart-v1-ios.md)<br /><br />[Android](quickstart-v1-android.md) | [.NET (WPF)](quickstart-v1-dotnet.md)<br /><br />[Xamarin](quickstart-v1-xamarin.md) |
| <center>![Webalkalmazások](./media/azure-ad-developers-guide/Web_app.png)<br />Webalkalmazások</center> | [Áttekintés](authentication-scenarios.md#web-browser-to-web-application)<br /><br />[ASP.NET](quickstart-v1-aspnet-webapp.md)<br /><br />[Java](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect) | [Python](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi)<br/><br/> [Node.js](quickstart-v1-openid-connect-code.md) |
| <center>![Egylapos alkalmazások](./media/azure-ad-developers-guide/SPA.png)<br />Egylapos alkalmazások</center> | [Áttekintés](authentication-scenarios.md#single-page-application-spa)<br /><br />[AngularJS](quickstart-v1-angularjs-spa.md)<br /><br />[JavaScript](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |  |
| <center>![Webes API-k](./media/azure-ad-developers-guide/Web_API.png)<br />Webes API-k</center> | [Áttekintés](authentication-scenarios.md#web-application-to-web-api)<br /><br />[ASP.NET](quickstart-v1-dotnet-webapi.md)<br /><br />[Node.js](quickstart-v1-nodejs-webapi.md) | &nbsp; |
| <center>![Szolgáltatások közötti](./media/azure-ad-developers-guide/Service_App.png)<br />Szolgáltatások közötti</center> | [Áttekintés](authentication-scenarios.md#daemon-or-server-application-to-web-api)<br /><br />[.NET](sample-v1-code.md#daemon-applications-accessing-web-apis-with-the-applications-identity)|  |
|  |  |  |  |  |

## <a name="how-to-guides"></a>Útmutatók
Az alábbi útmutatók végigvezetik az Azure AD néhány gyakori feladatán.

|                                                                           |  |
|---------------------------------------------------------------------------| --- |
|[Alkalmazásregisztráció](quickstart-v1-integrate-apps-with-azure-ad.md)           | Alkalmazás regisztrálása az Azure AD-ben. |
|[Több-bérlős alkalmazások](howto-convert-app-to-be-multi-tenant.md)    | Bejelentkezés bármely munkahelyi Microsoft-fiókba. |
|[OAuth és OpenID Connect protokollok](v1-protocols-openid-connect-code.md)| Felhasználók bejelentkeztetése és webes API-k hívása a Microsoft hitelesítési protokolljaival. |
|  |  |

## <a name="reference-topics"></a>Referencia-témakörök
A következő cikkekben az API-król, a protokollüzenetekről és az Azure AD által használt kifejezésekről találhat részletes információkat.

|                                                                                   | |
| ----------------------------------------------------------------------------------| --- |
| [Hitelesítési tárak (ADAL)](active-directory-authentication-libraries.md)   | Áttekintés az Azure AD által támogatott kódtárakról és SDK-król. |
| [Kódminták](sample-v1-code.md)                                  | Az Azure AD összes kódmintáját tartalmazó lista. |
| [Szószedet](active-directory-dev-glossary.md)                                      | A jelen dokumentációban használt fogalmak terminológiája és meghatározásai. |
|  |  |


[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
