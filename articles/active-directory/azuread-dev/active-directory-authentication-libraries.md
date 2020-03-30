---
title: Azure Active Directory hitelesítési könyvtárak | Microsoft dokumentumok
description: Az Azure AD authentication library (ADAL) lehetővé teszi az ügyfélalkalmazás-fejlesztők számára, hogy egyszerűen hitelesítsék a felhasználókat a felhőbe vagy a helyszíni Active Directoryba (AD), majd hozzáférési jogkivonatokat szerezzenbe az API-hívások védelméhez.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: conceptual
ms.workload: identity
ms.date: 12/01/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 32375c14d95dc9e100cbf496c8550f0a195a11dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154610"
---
# <a name="azure-active-directory-authentication-libraries"></a>Az Azure Active Directory hitelesítési kódtárai

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Az Azure Active Directory hitelesítési könyvtár (ADAL) v1.0 lehetővé teszi az alkalmazásfejlesztők számára, hogy hitelesítsék a felhasználókat a felhőbe vagy a helyszíni Active Directory (AD), és szerezzen be jogkivonatokat az API-hívások biztonságossá tétele. Az ADAL megkönnyíti a hitelesítést a fejlesztők számára az olyan funkciókkal, mint például:

- Konfigurálható jogkivonat-gyorsítótár, amely hozzáférési jogkivonatokat és frissítési jogkivonatokat tárol
- Automatikus jogkivonat-frissítés, ha egy hozzáférési jogkivonat lejár, és egy frissítési jogkivonat elérhető
- Aszinkron metódushívások támogatása

> [!NOTE]
> Az Azure AD v2.0-s kódtárak (MSAL)? Pénztár a [MSAL könyvtár útmutató](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries).
>
>

## <a name="microsoft-supported-client-libraries"></a>Microsoft által támogatott ügyféltárak

| Platform | Erőforrástár | Letöltés | Forráskód | Sample | Referencia
| --- | --- | --- | --- | --- | --- |
| .NET ügyfél, Windows Áruház, UWP, Xamarin iOS és Android |ADAL .NET v3 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) |[Github](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet) | [Asztali alkalmazás](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-dotnet) |[Referencia](https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.clients.activedirectory?view=azure-dotnet) |
| JavaScript |ADAL.js |[Github](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[Github](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[Egyoldalas alkalmazás](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) | |
| iOS, macOS |ADAL |[Github](https://github.com/AzureAD/azure-activedirectory-library-for-objc/releases) |[Github](https://github.com/AzureAD/azure-activedirectory-library-for-objc) |[iOS-alkalmazás](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-ios) | [Referencia](http://cocoadocs.org/docsets/ADAL/2.5.1/)|
| Android |ADAL |[Maven](https://search.maven.org/search?q=g:com.microsoft.aad+AND+a:adal&core=gav) |[Github](https://github.com/AzureAD/azure-activedirectory-library-for-android) |[Android-alkalmazás](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-android) | [JavaDocs](https://javadoc.io/doc/com.microsoft.aad/adal/)|
| Node.js |ADAL |[npm](https://www.npmjs.com/package/adal-node) |[Github](https://github.com/AzureAD/azure-activedirectory-library-for-nodejs) | [Node.Js-webalkalmazás](https://github.com/Azure-Samples/active-directory-node-webapp-openidconnect)|[Referencia](https://docs.microsoft.com/javascript/api/overview/azure/activedirectory) |
| Java |ADAL4J között |[Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3Aadal4j%20g%3Acom.microsoft.azure) |[Github](https://github.com/AzureAD/azure-activedirectory-library-for-java) |[Java webalkalmazások](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect) |[Referencia](https://javadoc.io/doc/com.microsoft.azure/adal4j) |
| Python |ADAL |[Github](https://github.com/AzureAD/azure-activedirectory-library-for-python) |[Github](https://github.com/AzureAD/azure-activedirectory-library-for-python) |[Python-webalkalmazás](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi) |[Referencia](https://adal-python.readthedocs.io/) |

## <a name="microsoft-supported-server-libraries"></a>Microsoft által támogatott kiszolgálókönyvtárak

| Platform | Erőforrástár | Letöltés | Forráskód | Sample | Referencia
| --- | --- | --- | --- | --- | --- |
| .NET |OWIN az AzureAD-hoz|[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/) |[Github](https://github.com/aspnet/AspNetKatana/tree/dev/src/Microsoft.Owin.Security.ActiveDirectory) |[MVC alkalmazás](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-webapp-dotnet) | |
| .NET |OWIN az OpenIDConnect-hez |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect) |[Github](https://github.com/aspnet/AspNetKatana/tree/dev/src/Microsoft.Owin.Security.OpenIdConnect) |[Webalkalmazás](https://github.com/AzureADSamples/WebApp-OpenIDConnect-DotNet) | |
| .NET |OWIN a WS-Federation-hez |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) |[Github](https://github.com/aspnet/AspNetKatana/tree/dev/src/Microsoft.Owin.Security.WsFederation) |[MVC webalkalmazás](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet) | |
| .NET |Identity Protocol extensions for .NET 4.5 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Protocol.Extensions) |[Github](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| .NET |JWT-kezelő a .NET 4.5-höz |[NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt) |[Github](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| Node.js |Azure Hirdetési passport |[npm](https://www.npmjs.com/package/passport-azure-ad) |[Github](https://github.com/AzureAD/passport-azure-ad) | [Webes API](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-webapi-nodejs)| |

## <a name="scenarios"></a>Forgatókönyvek

Az alábbiakban három gyakori forgatókönyv et olvashat az ADAL távoli erőforrásokhoz hozzáférő ügyfélben való használatára:

### <a name="authenticating-users-of-a-native-client-application-running-on-a-device"></a>Eszközön futó natív ügyfélalkalmazás felhasználóinak hitelesítése

Ebben a forgatókönyvben a fejlesztő rendelkezik egy mobil ügyfél vagy asztali alkalmazás, amely egy távoli erőforrás eléréséhez, például egy webes API-t. A webes API nem engedélyezi a névtelen hívásokat, ezért egy hitelesített felhasználó környezetében kell meghívni. A webes API előre konfigurálva van egy adott Azure AD-bérlő által kiadott hozzáférési jogkivonatok megbízhatóságához. Az Azure AD előre konfigurálva van, hogy az adott erőforráshoz hozzáférési jogkivonatokat adjon ki. A webes API meghívásához az ügyféltől, a fejlesztő adal használatával megkönnyíti az Azure AD hitelesítését. Az ADAL használatának legbiztonságosabb módja, ha a felhasználói hitelesítő adatok gyűjtésére szolgáló felhasználói felületet jeleníti meg (böngészőablakként jelenik meg).

Az ADAL megkönnyíti a felhasználó hitelesítését, egy hozzáférési jogkivonat beszerzése és a frissítési jogkivonat az Azure AD-től, majd hívja meg a webes API-t a hozzáférési jogkivonat használatával.

A kódminta, amely bemutatja ezt a forgatókönyvet az Azure AD hitelesítésével, lásd: [Natív ügyfél WPF alkalmazás web API.For](https://github.com/azureadsamples/nativeclient-dotnet)a code sample that demonstrates this scenario using authentication to Azure AD, see Native Client WPF Application to Web API .

### <a name="authenticating-a-confidential-client-application-running-on-a-web-server"></a>Webkiszolgálón futó bizalmas ügyfélalkalmazás hitelesítése

Ebben az esetben a fejlesztő egy olyan kiszolgálón futó alkalmazással rendelkezik, amelynek hozzá kell férnie egy távoli erőforráshoz, például egy webes API-hoz. A webes API nem engedélyezi a névtelen hívásokat, ezért azt egy hivatalos szolgáltatásból kell meghívni. A webes API előre konfigurálva van egy adott Azure AD-bérlő által kiadott hozzáférési jogkivonatok megbízhatóságához. Az Azure AD előre konfigurálva van, hogy az adott erőforráshoz hozzáférési jogkivonatokat adjon ki egy ügyfélhitelesítőadatokkal (ügyfélazonosítóval és titkos) rendelkező szolgáltatásra. Az ADAL megkönnyíti a szolgáltatás hitelesítését az Azure AD-vel, amely egy hozzáférési jogkivonatot ad vissza, amely a webes API-hívásához használható. Az ADAL a hozzáférési jogkivonat élettartamának kezelését is kezeli a gyorsítótárazásával és szükség szerint megújításával. A forgatókönyvet szemléltető kódmintát a [Daemon konzolalkalmazás webAPI-ra (Daemon console Application to Web API) (Démonkonzol alkalmazás webAPI-nak) témakörben](https://github.com/AzureADSamples/Daemon-DotNet)talál.

### <a name="authenticating-a-confidential-client-application-running-on-a-server-on-behalf-of-a-user"></a>Kiszolgálón, felhasználó nevében futó bizalmas ügyfélalkalmazás hitelesítése

Ebben a forgatókönyvben a fejlesztő egy webalkalmazás fut a kiszolgálón, amely nek szüksége van egy távoli erőforrás, például egy webes API eléréséhez. A webes API nem engedélyezi a névtelen hívásokat, ezért azt egy hitelesített felhasználó nevében egy hivatalos szolgáltatásból kell meghívni. A webes API előre konfigurálva van egy adott Azure AD-bérlő által kiadott hozzáférési jogkivonatok megbízhatóságára, és az Azure AD előre konfigurálva van, hogy az adott erőforráshoz hozzáférési jogkivonatokat adjon ki egy ügyfélhitelesítő adatokkal rendelkező szolgáltatásnak. Miután a felhasználó hitelesítést kapott a webalkalmazásban, az alkalmazás letud kérni egy engedélyezési kódot a felhasználó számára az Azure AD-ből. A webalkalmazás ezután az ADAL használatával hozzáférési jogkivonatot kaphat, és frissítheti a jogkivonatot egy felhasználó nevében az Azure AD-ből az alkalmazáshoz társított engedélyezési kód és ügyfélhitelesítő adatok használatával. Miután a webalkalmazás birtokában van a hozzáférési jogkivonat, meghívhatja a webes API-t, amíg a jogkivonat lejár. Amikor a jogkivonat lejár, a webalkalmazás az ADAL használatával új hozzáférési jogkivonatot kaphat a korábban kapott frissítési jogkivonat használatával. A forgatókönyvet szemléltető kódmintát a [Natív ügyfél web API-web API-ban](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof)talál.

## <a name="see-also"></a>Lásd még:

- [Az Azure Active Directory fejlesztői útmutatója](v1-overview.md)
- [Az Azure Active Directory hitelesítési forgatókönyvei](v1-authentication-scenarios.md)
- [Az Azure Active Directory kódmintái](sample-v1-code.md)
