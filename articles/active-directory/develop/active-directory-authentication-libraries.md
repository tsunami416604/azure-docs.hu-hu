---
title: Az Azure Active Directory hitelesítési Kódtárai |} A Microsoft Docs
description: Az Azure AD Authentication Library (ADAL) lehetővé teszi az ügyfél segítségével könnyedén hitelesítheti a felhasználókat a felhőbe az alkalmazásfejlesztők vagy a helyszíni Active Directory (AD), és ezután szerezze be a hozzáférési jogkivonatok az API-hívások biztonságossá tételéhez.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 2e4fc79a-0285-40be-8c77-65edee408a22
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/13/2018
ms.author: celested
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: d3f34ac3d284d61caf79cbd4929689106fdf7999
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46980644"
---
# <a name="azure-active-directory-authentication-libraries"></a>Az Azure Active Directory hitelesítési Kódtárai

Az Azure Active Directory Authentication Library (ADAL) 1.0-s verzió lehetővé teszi az alkalmazásfejlesztők a felhőalapú felhasználók hitelesítéséhez és a helyszíni Active Directory (AD), és szerezze be jogkivonatok az API-hívások biztonságossá tételéhez. ADAL hitelesítési egyszerűbbé teszi a funkciók révén a fejlesztők például:

- Konfigurálható jogkivonatok gyorsítótárát, hogy a tárolók hozzáférési jogkivonatokat, és frissítési jogkivonatok
- Automatikus token frissítést, ha a hozzáférési jogkivonat lejár, és a egy frissítési jogkivonat érhető el
- Aszinkron metódust hívja támogatása

> [!NOTE]
> Az Azure AD v2.0-kódtárak (MSAL) keres? Kivétel a [MSAL könyvtár útmutató](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries).
>
>

## <a name="microsoft-supported-client-libraries"></a>A Microsoft által támogatott Ügyfélkódtárak

| Platform | Részletes ismertetés | Letöltés | Forráskód | Sample | Leírások
| --- | --- | --- | --- | --- | --- |
| .NET-ügyfél Windows Store, az UWP, Xamarin iOS és Android rendszerhez |ADAL .NET v3 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) |[GitHubon](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet) | [Asztali alkalmazás](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-dotnet) |[Referencia](https://docs.microsoft.com/dotnet/api/?view=identitymodelclientsad-3.13.9) |
| .NET-ügyfél Windows Store, Windows Phone 8.1 |ADAL .NET v2 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/2.28.4) |[GitHubon](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/releases/tag/v2.28.4) | [Asztali alkalmazás](https://github.com/AzureADQuickStarts/NativeClient-DotNet/releases/tag/v2.X) | |
| JavaScript |ADAL.js |[GitHubon](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[GitHubon](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[Egyoldalas alkalmazás](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) | |
| iOS, macOS |ADAL |[GitHubon](https://github.com/AzureAD/azure-activedirectory-library-for-objc/releases) |[GitHubon](https://github.com/AzureAD/azure-activedirectory-library-for-objc) |[iOS-alkalmazás](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-ios) | [Referencia](http://cocoadocs.org/docsets/ADAL/2.5.1/)|
| Android |ADAL |[A központi tárházban](http://search.maven.org/remotecontent?filepath=com/microsoft/aad/adal/) |[GitHubon](https://github.com/AzureAD/azure-activedirectory-library-for-android) |[Android-alkalmazás](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-android) | [JavaDocs](http://javadoc.io/doc/com.microsoft.aad/adal/)|
| Node.js |ADAL |[npm](https://www.npmjs.com/package/adal-node) |[GitHubon](https://github.com/AzureAD/azure-activedirectory-library-for-nodejs) | [Node.Js-webalkalmazás](https://github.com/Azure-Samples/active-directory-node-webapp-openidconnect)|[Referencia](https://docs.microsoft.com/javascript/api/adal-node/?view=azure-node-latest) |
| Java |ADAL4J |[Maven 3](http://search.maven.org/#search%7Cga%7C1%7Ca%3Aadal4j%20g%3Acom.microsoft.azure) |[GitHubon](https://github.com/AzureAD/azure-activedirectory-library-for-java) |[Java webalkalmazások](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect) |[Referencia](http://javadoc.io/doc/com.microsoft.azure/adal4j) |
| Python |ADAL |[GitHubon](https://github.com/AzureAD/azure-activedirectory-library-for-python) |[GitHubon](https://github.com/AzureAD/azure-activedirectory-library-for-python) |[Python-webalkalmazás](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi) |[Referencia](http://adal-python.readthedocs.io/) |

## <a name="microsoft-supported-server-libraries"></a>A Microsoft által támogatott kiszolgálói kódtárak

| Platform | Részletes ismertetés | Letöltés | Forráskód | Sample | Leírások
| --- | --- | --- | --- | --- | --- |
| .NET |Az Azure ad OWIN|[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/) |[CodePlex](http://katanaproject.codeplex.com) |[MVC-alkalmazás](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-webapp-dotnet) | |
| .NET |A OpenIDConnect OWIN |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect) |[CodePlex](http://katanaproject.codeplex.com) |[Webalkalmazás](https://github.com/AzureADSamples/WebApp-OpenIDConnect-DotNet) | |
| .NET |A WS-Federation OWIN |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) |[CodePlex](http://katanaproject.codeplex.com) |[MVC-webalkalmazás](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet) | |
| .NET |A .NET 4.5-ös identitás protokoll bővítményei |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Protocol.Extensions) |[GitHubon](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| .NET |A .NET 4.5-ös JWT-kezelő |[NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt) |[GitHubon](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| Node.js |Az Azure AD-Passport |[npm](https://www.npmjs.com/package/passport-azure-ad) |[GitHubon](https://github.com/AzureAD/passport-azure-ad) | [Webes API](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-webapi-nodejs)| |

## <a name="scenarios"></a>Forgatókönyvek

Itt három általános forgatókönyv ügyfél, amely egy távoli erőforráshoz fér hozzá az adal-t a következők:

### <a name="authenticating-users-of-a-native-client-application-running-on-a-device"></a>Egy natív ügyfélalkalmazás olyan eszközön fut, a felhasználók hitelesítése

Ebben a forgatókönyvben egy fejlesztői mobilügyfél vagy asztali alkalmazás, el kell érnie a távoli erőforrás, például a webes API-k rendelkezik. A webes API-k nem engedélyezi a névtelen hívásokat, és a egy hitelesített felhasználó kontextusában kell meghívni. A webes API egy adott által kiállított hozzáférési jogkivonatok megbízható előre konfigurálva az Azure AD-bérlővel. Az Azure AD egy előre konfigurált meghatározott hozzáférési jogkivonatok kiállításához. Az ügyfél a webes API meghívásához, a fejlesztői használja az adal-t megkönnyítése érdekében az Azure AD-hitelesítés. A legbiztonságosabb adal-t használó módja, hogy a felhasználói felület (a böngésző ablakát, megjelenített) felhasználói hitelesítő adatok gyűjtésére szolgáló jelennek meg.

Adal-t egyszerűen hitelesíteni a felhasználót, egy hozzáférési jogkivonatot, és a frissítési jogkivonat beszerzése az Azure ad-ből, és ezután hívja meg a webes API-t a hozzáférési jogkivonat.

A kódminta azt mutatja be, ebben a forgatókönyvben az Azure AD-hitelesítés használatával, lásd: [natív ügyfél WPF alkalmazás webes API-nak](https://github.com/azureadsamples/nativeclient-dotnet).

### <a name="authenticating-a-confidential-client-application-running-on-a-web-server"></a>Egy webkiszolgálón futó bizalmas ügyfélalkalmazás hitelesítése

Ebben a forgatókönyvben egy fejlesztői rendelkezik, amely a távoli erőforrás, például a webes API-k hozzáférésre van szüksége a kiszolgálón futó alkalmazásokhoz. A webes API-t nem engedélyezi a névtelen hív meg, így a jogosult szolgáltatás kell meghívni. A webes API egy adott által kiállított hozzáférési jogkivonatok megbízható előre konfigurálva az Azure AD-bérlővel. Az Azure AD egy előre konfigurált egy szolgáltatás hozzáférési jogkivonatok az adott erőforráshoz kiadására (ügyfél-azonosítója és kulcsa) ügyfél-hitelesítő adatokkal. Adal-t elősegíti a hitelesítést a szolgáltatás az Azure AD vissza, amely a webes API meghívásához használható hozzáférési jogkivonat. Adal-t is kezeli a gyorsítótárazás, és szükség szerint megújítása, a hozzáférési jogkivonat élettartama kezelését. A kódminta azt mutatja be, ebben a forgatókönyvben, lásd: [démon konzol webes API-alkalmazás](https://github.com/AzureADSamples/Daemon-DotNet).

### <a name="authenticating-a-confidential-client-application-running-on-a-server-on-behalf-of-a-user"></a>A kiszolgáló egy felhasználó nevében futó bizalmas ügyfélalkalmazás hitelesítése

Ebben a forgatókönyvben egy fejlesztői rendelkezik egy olyan kiszolgálóra, amely a távoli erőforrás, például a webes API-k hozzáférésre van szüksége a futó webalkalmazás. A webes API-k nem engedélyezi a névtelen hívások, így egy jogosult szolgáltatás egy hitelesített felhasználó nevében kell meghívni. A webes API-k előre konfigurálva egy adott Azure AD által kiállított hozzáférési jogkivonatok megbízik, és az Azure AD bérlő előre konfigurálva adott erőforrásra vonatkozó hozzáférési jogkivonatainak kiadni egy szolgáltatás ügyfél-hitelesítő adatokkal. A webes alkalmazás a felhasználó hitelesítését követően az alkalmazás képes egy hozzáférési kód lekérése a felhasználó Azure AD-ből. A webes alkalmazás adal-t a hozzáférési jogkivonat beszerzésére és a frissítési jogkivonat egy engedélyezési kód és az ügyfél hitelesítő adatok használatával az Azure ad-ből az alkalmazáshoz társított felhasználó nevében használhatja. Ha a webes alkalmazásba a hozzáférési jogkivonat birtokában van, meghívhatja a webes API mindaddig, amíg a jogkivonat lejár. A jogkivonat lejár, a webes alkalmazás adal-t egy új hozzáférési jogkivonatot beszerezni a a frissítési jogkivonat korábban fogadott használhat. A kódminta azt mutatja be, ebben a forgatókönyvben, lásd: [webes API-nak a webes API a natív ügyfél](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof).

## <a name="see-also"></a>Lásd még:

- [Az Azure Active Directory fejlesztői útmutatója](azure-ad-developers-guide.md)
- [Hitelesítési forgatókönyvek az Azure Active Directoryval](authentication-scenarios.md)
- [Az Azure Active Directory-Kódminták](sample-v1-code.md)
