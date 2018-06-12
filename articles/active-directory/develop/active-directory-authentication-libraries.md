---
title: Az Azure Active Directory hitelesítési Kódtárai |} Microsoft Docs
description: Az Azure AD Authentication Library (ADAL) lehetővé teszi az ügyfél alkalmazásfejlesztők számára könnyedén hitelesítheti a felhasználókat a felhőalapú vagy helyszíni Active Directory (AD) és majd a védelmét biztosító API-hívások hozzáférési tokenek beszerzése érdekében.
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
ms.openlocfilehash: 27901608e5a7e76a799fb34b6f1f33773c0d8413
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35260576"
---
# <a name="azure-active-directory-authentication-libraries"></a>Az Azure Active Directory hitelesítési Kódtárai

Az Azure Active Directory Authentication Library (ADAL) 1.0-s verzió lehetővé teszi az alkalmazásfejlesztők számára a felhőalapú felhasználók hitelesítéséhez a helyszíni Active Directory (AD), illetve védelmét biztosító API-hívások tokenek beszerzése. ADAL hitelesítési egyszerűbbé teszi a fejlesztők funkciók révén, mint:

- Konfigurálható, hogy a tárolók hozzáférési jogkivonatok és frissítési jogkivonatok jogkivonat gyorsítótára
- automatikus token frissítés, ha az előző lejár, és egy frissítési jogkivonat érhető el
- aszinkron metódushívások támogatása

> [!NOTE]
> Az Azure AD v2.0 függvénytárak (MSAL) keres? Kivételt a [MSAL könyvtár útmutató](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries).
>
>

## <a name="microsoft-supported-client-libraries"></a>A Microsoft által támogatott ügyfél-függvénytárak

| Platform | Részletes ismertetés | Letöltés | Forráskód | Sample | Leírások
| --- | --- | --- | --- | --- | --- |
| .NET ügyfél, a Windows Store UWP, Xamarin iOS és Android rendszerhez |ADAL .NET v3 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) |[GitHubon](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet) | [Egy asztali alkalmazás](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-dotnet) |[Referencia](https://docs.microsoft.com/dotnet/api/?view=identitymodelclientsad-3.13.9) |
| .NET ügyfél, a Windows áruház, Windows Phone 8.1 |ADAL .NET v2 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/2.28.4) |[GitHubon](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/releases/tag/v2.28.4) | [Egy asztali alkalmazás](https://github.com/AzureADQuickStarts/NativeClient-DotNet/releases/tag/v2.X) | |
| JavaScript |ADAL.js |[GitHubon](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[GitHubon](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[Egyetlen alkalmazás](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) | |
| iOS, macOS |ADAL |[GitHubon](https://github.com/AzureAD/azure-activedirectory-library-for-objc/releases) |[GitHubon](https://github.com/AzureAD/azure-activedirectory-library-for-objc) |[iOS-alkalmazás](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-ios) | [Referencia](https://cocoadocs.org/docsets/ADAL/)|
| Android |ADAL |[A központi tárházban](http://search.maven.org/remotecontent?filepath=com/microsoft/aad/adal/) |[GitHubon](https://github.com/AzureAD/azure-activedirectory-library-for-android) |[Android-alkalmazás](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-android) | [JavaDocs](http://javadoc.io/doc/com.microsoft.aad/adal/)|
| Node.js |ADAL |[npm](https://www.npmjs.com/package/adal-node) |[GitHubon](https://github.com/AzureAD/azure-activedirectory-library-for-nodejs) | [Node.Js-webalkalmazás](https://github.com/Azure-Samples/active-directory-node-webapp-openidconnect)|[Referencia](https://docs.microsoft.com/en-us/javascript/api/adal-node/?view=azure-node-latest) |
| Java |ADAL4J |[Maven 3](http://search.maven.org/#search%7Cga%7C1%7Ca%3Aadal4j%20g%3Acom.microsoft.azure) |[GitHubon](https://github.com/AzureAD/azure-activedirectory-library-for-java) |[Java webalkalmazások](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect) |[Referencia](http://javadoc.io/doc/com.microsoft.azure/adal4j) |
| Python |ADAL |[GitHubon](https://github.com/AzureAD/azure-activedirectory-library-for-python) |[GitHubon](https://github.com/AzureAD/azure-activedirectory-library-for-python) |[Python webes alkalmazás](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi) |[Referencia](http://adal-python.readthedocs.io/) |

## <a name="microsoft-supported-server-libraries"></a>A Microsoft által támogatott Server könyvtárak

| Platform | Részletes ismertetés | Letöltés | Forráskód | Sample | Leírások
| --- | --- | --- | --- | --- | --- |
| .NET |OWIN az AzureAD|[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/) |[Codeplex webhelyen](http://katanaproject.codeplex.com) |[MVC-alkalmazáshoz](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-webapp-dotnet) | |
| .NET |A OpenIDConnect OWIN |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect) |[Codeplex webhelyen](http://katanaproject.codeplex.com) |[Webalkalmazás](https://github.com/AzureADSamples/WebApp-OpenIDConnect-DotNet) | |
| .NET |A WS-Federation OWIN |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) |[Codeplex webhelyen](http://katanaproject.codeplex.com) |[MVC webalkalmazás](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet) | |
| .NET |A .NET 4.5 identitás protokoll bővítményei |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Protocol.Extensions) |[GitHubon](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| .NET |A .NET 4.5 JWT-kezelő |[NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt) |[GitHubon](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| Node.js |Az Azure AD Passport |[npm](https://www.npmjs.com/package/passport-azure-ad) |[GitHubon](https://github.com/AzureAD/passport-azure-ad) | [Webes API](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-webapi-nodejs)| |

## <a name="scenarios"></a>Forgatókönyvek

Az alábbiakban az adal-t használó távoli erőforráshoz hozzáférő ügyfélprogram három gyakori forgatókönyvei:

### <a name="authenticating-users-of-a-native-client-application-running-on-a-device"></a>Az eszközön futó natív ügyfélalkalmazás a felhasználók hitelesítéséhez

Ebben a forgatókönyvben egy fejlesztő mobil vagy asztali alkalmazás, amelyet a távoli erőforrások, például a webes API-k eléréséhez rendelkezik. A webes API-k nem engedélyezi a névtelen hívásokat, és a hitelesített felhasználó kontextusában kell meghívni. A webes API-k előre konfigurálva történő hozzáférési jogkivonatok egy adott kiállító megbízik az Azure AD-bérlő. Az Azure AD az előre beállított hozzáférési jogkivonatok az adott erőforrás ki. Az ügyféltől a webes API meghívására, a fejlesztő adal-t az Azure ad-val hitelesítésének elősegítésére. Az adal-t használó legbiztonságosabb módja, hogy azt a felhasználói felület (böngészőablakban megjelenítve), felhasználói hitelesítő adatok gyűjtéséhez megjelenítéshez.

ADAL egyszerűen hitelesíteni a felhasználót, egy hozzáférési jogkivonatot, és a frissítési jogkivonat beszerzése az Azure AD és a webes API-t használ a hozzáférést, majd hívja token.

Azt mutatja be ezt a forgatókönyvet az Azure AD hitelesítési kódminta, lásd: [natív ügyfél WPF alkalmazás webes API](https://github.com/azureadsamples/nativeclient-dotnet).

### <a name="authenticating-a-confidential-client-application-running-on-a-web-server"></a>A webkiszolgálón futó bizalmas ügyfélalkalmazás hitelesítése

Ebben a forgatókönyvben egy fejlesztő rendelkezik, amelyet a távoli erőforrás, például a webes API-k elérésére a kiszolgálón futó alkalmazáshoz. A webes API nem engedélyezi a névtelen hívja, ezért a jogosult szolgáltatás kell meghívni. A webes API-k előre konfigurálva történő hozzáférési jogkivonatok egy adott kiállító megbízik az Azure AD-bérlő. Az Azure AD az előre beállított hozzáférési jogkivonatok az adott erőforrás kiadni egy szolgáltatás (ügyfél-Azonosítót és titkos kulcs) ügyfél-hitelesítő adatokkal. ADAL elősegíti a hitelesítést a szolgáltatás az Azure AD vissza egy jogkivonatot a webes API hívásához használható. Adal-t is kezeli, gyorsítótárazás, és szükség szerint megújítása azt a hozzáférési jogkivonat élettartamát kezelése. Azt mutatja be, ebben a forgatókönyvben kódminta, lásd: [démon console Application webes API](https://github.com/AzureADSamples/Daemon-DotNet).

### <a name="authenticating-a-confidential-client-application-running-on-a-server-on-behalf-of-a-user"></a>Egy felhasználó nevében a kiszolgálón futó bizalmas ügyfélalkalmazás hitelesítése

Ebben a forgatókönyvben egy fejlesztő rendelkezik, amely a távoli erőforrás, például a webes API-k hozzáférésre van szüksége a kiszolgálón futó webalkalmazás. A webes API-k nem engedélyezi a névtelen hívások, a jogosult szolgáltatás egy hitelesített felhasználó nevében kell meghívni. A webes API-k előre konfigurálva egy adott Azure AD által kibocsátott jogkivonatot megbízhatónak, és az Azure AD bérlő előre konfigurálva az adott erőforrás hozzáférési jogkivonatok kiadni egy szolgáltatás ügyfél-hitelesítő adatokkal. A webes alkalmazás a felhasználó hitelesítését követően az alkalmazás az Azure AD kérheti le az engedélyezési kód a felhasználó számára. A webes alkalmazás segítségével ADAL szerezze be a hozzáférési tokent, és a frissítési token engedélyezési kódot és az ügyfél hitelesítő adatok használatával az Azure ad-alkalmazáshoz kapcsolódó egy felhasználó nevében. Ha a webes alkalmazás a hozzáférési jogkivonat rendelkezik, akkor meghívhatja a webes API-t, mindaddig, amíg a jogkivonat lejár. A jogkivonat lejár, a webes alkalmazás adal-t a frissítés használatával új tokenre jogkivonatának beszerzéséhez korábban fogadott segítségével. Azt mutatja be, ebben a forgatókönyvben kódminta, lásd: [Web API webes API-t natív ügyfelet](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof).

## <a name="see-also"></a>Lásd még:

- [Az Azure Active Directory fejlesztői útmutatója](active-directory-developers-guide.md)
- [Az Azure Active directory hitelesítési forgatókönyvei](active-directory-authentication-scenarios.md)
- [Az Azure Active Directory-Kódminták](active-directory-code-samples.md)
