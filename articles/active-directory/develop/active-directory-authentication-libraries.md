---
title: Az Azure Active Directory hitelesítési Kódtárai |} Microsoft Docs
description: Az Azure AD Authentication Library (ADAL) lehetővé teszi az ügyfél alkalmazásfejlesztők számára könnyedén hitelesítheti a felhasználókat a felhőalapú vagy helyszíni Active Directory (AD) és majd a védelmét biztosító API-hívások hozzáférési tokenek beszerzése érdekében.
services: active-directory
documentationcenter: ''
author: bryanla
manager: mtillman
editor: mbaldwin
ms.assetid: 2e4fc79a-0285-40be-8c77-65edee408a22
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/25/2017
ms.author: bryanla
ms.custom: aaddev
ms.openlocfilehash: e2c77b2e47ce3dd900b1b277e802a07cfed84702
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
---
# <a name="azure-active-directory-authentication-libraries"></a>Az Azure Active Directory hitelesítési Kódtárai
Az Azure Active Directory Authentication Library (ADAL) lehetővé teszi az alkalmazásfejlesztők számára hitelesíti a felhasználókat a felhőben, vagy a helyszíni Active Directory (AD), és szerezze be jogkivonatok védelmét biztosító API-hívások. ADAL hitelesítési egyszerűbbé teszi a fejlesztők funkciók révén, mint:
 - Konfigurálható, hogy a tárolók hozzáférési jogkivonatok és frissítési jogkivonatok jogkivonat gyorsítótára
 - automatikus token frissítés, ha az előző lejár, és egy frissítési jogkivonat érhető el
 - aszinkron metódushívások támogatása
 - és további

> [!NOTE]
> Az Azure AD v2.0 függvénytárak (MSAL) keres? Kivételt a [MSAL könyvtár útmutató](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries).
>
>

### <a name="client-libraries"></a>Ügyfélkönyvtárak

| Platform | Részletes ismertetés | Letöltés | Forráskód | Sample | Leírások
| --- | --- | --- | --- | --- | --- |
| .NET ügyfél, a Windows Store UWP, Xamarin iOS és Android rendszerhez |ADAL .NET v3 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) |[GitHubon](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet) | [Egy asztali alkalmazás](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-dotnet) |[Referencia](https://docs.microsoft.com/dotnet/api/?view=identitymodelclientsad-3.13.9) |
| .NET ügyfél, a Windows áruház, Windows Phone 8.1 |ADAL .NET v2 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/2.28.4) |[GitHubon](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/releases/tag/v2.28.4) | [Egy asztali alkalmazás](https://github.com/AzureADQuickStarts/NativeClient-DotNet/releases/tag/v2.X) | |
| JavaScript |ADAL.js |[GitHubon](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[GitHubon](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[Egyetlen alkalmazás](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) | |
| iOS, macOS |ADAL |[GitHubon](https://github.com/AzureAD/azure-activedirectory-library-for-objc/releases) |[GitHubon](https://github.com/AzureAD/azure-activedirectory-library-for-objc) |[iOS-alkalmazás](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-ios) | [Referencia](https://cocoapods.org/pods/ADAL)|
| Android |ADAL |[A központi tárházban](http://search.maven.org/remotecontent?filepath=com/microsoft/aad/adal/) |[GitHubon](https://github.com/AzureAD/azure-activedirectory-library-for-android) |[Android-alkalmazás](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-android) | [JavaDocs](http://javadoc.io/doc/com.microsoft.aad/adal/)|
| Node.js |ADAL |[npm](https://www.npmjs.com/package/adal-node) |[GitHubon](https://github.com/AzureAD/azure-activedirectory-library-for-nodejs) | | |
| Java |ADAL4J |[GitHubon](https://github.com/AzureAD/azure-activedirectory-library-for-java) |[GitHubon](https://github.com/AzureAD/azure-activedirectory-library-for-java) |[Java webalkalmazások](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-webapp-java) | |
| Python |ADAL |[GitHubon](https://github.com/AzureAD/azure-activedirectory-library-for-python) |[GitHubon](https://github.com/AzureAD/azure-activedirectory-library-for-python) |[Python webes alkalmazás](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi) | |

### <a name="server-libraries"></a>Server könyvtárak

| Platform | Részletes ismertetés | Letöltés | Forráskód | Sample | Leírások
| --- | --- | --- | --- | --- | --- |
| .NET |OWIN az AzureAD|[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/) |[Codeplex webhelyen](http://katanaproject.codeplex.com) |[MVC-alkalmazáshoz](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-webapp-dotnet) | |
| .NET |A OpenIDConnect OWIN |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect) |[Codeplex webhelyen](http://katanaproject.codeplex.com) |[Webalkalmazás](https://github.com/AzureADSamples/WebApp-OpenIDConnect-DotNet) | |
| Node.js |Az Azure AD Passport |[npm](https://www.npmjs.com/package/passport-azure-ad) |[GitHubon](https://github.com/AzureAD/passport-azure-ad) | [Webes API](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-webapi-nodejs)| |
| .NET |A WS-Federation OWIN |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) |[Codeplex webhelyen](http://katanaproject.codeplex.com) |[MVC webalkalmazás](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet) | |
| .NET |A .NET 4.5 identitás protokoll bővítményei |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Protocol.Extensions) |[GitHubon](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| .NET |A .NET 4.5 JWT-kezelő |[NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt) |[GitHubon](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |

### <a name="v20-client-libraries-msal"></a>v2.0 Klienskódtárak (MSAL)

A [az Azure AD v2.0-végponttól](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-compare) kombinálja az Azure AD és a Microsoft Accounts mögött egy végpontot. Hozzáférni ehhez a végponthoz, a fejlesztők használhatják a [éles által támogatott előzetes MSAL szalagtárak](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries) helyett az adal-t.

| Platform | Részletes ismertetés | Letöltés | Forráskód | Sample | Leírások
| --- | --- | --- | --- | --- | --- |
| .NET ügyfél, a Windows Store UWP, Xamarin iOS és Android rendszerhez |A .NET-hez (előzetes verzió) MSAL |[NuGet](https://www.nuget.org/packages/Microsoft.Identity.Client/1.1.0-preview) |[GitHubon](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | [Egy asztali alkalmazás](~/articles/active-directory/develop/guidedsetups/active-directory-windesktop.md) |[Referencia](https://docs.microsoft.com/dotnet/api/?view=identityclient-1.1.0-preview) |
| JavaScript |MSAL a JavaScript (előzetes verzió) |[GitHubon](https://github.com/AzureAD/microsoft-authentication-library-for-js) |[GitHubon](https://github.com/AzureAD/microsoft-authentication-library-for-js) | [Egyetlen alkalmazás](~/articles/active-directory/develop/GuidedSetups/active-directory-javascriptspa.md) | [Referencia](https://htmlpreview.github.io/?https://raw.githubusercontent.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/docs/classes/_useragentapplication_.msal.useragentapplication.html) |
| iOS |Az iOS számára (előzetes verzió) MSAL |[GitHubon](https://github.com/AzureAD/microsoft-authentication-library-for-objc) |[GitHubon](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [iOS-alkalmazás](~/articles/active-directory/develop/GuidedSetups/active-directory-ios.md) | [Referencia](https://azuread.github.io/docs/objc/) |
| Android |MSAL Android (előzetes verzió) |[GitHubon](https://github.com/AzureAD/microsoft-authentication-library-for-android) |[GitHubon](https://github.com/AzureAD/microsoft-authentication-library-for-android) | [Android-alkalmazás](~/articles/active-directory/develop/GuidedSetups/active-directory-android.md) | [Referencia](http://javadoc.io/doc/com.microsoft.identity.client/msal/0.1.1) |

## <a name="scenarios"></a>Forgatókönyvek

Az alábbiakban a három gyakori forgatókönyvek, amelyben az adal-t távoli erőforráshoz hozzáférő ügyfél hitelesítésére használható:  

### <a name="authenticating-users-of-a-native-client-application-running-on-a-device"></a>Az eszközön futó natív ügyfélalkalmazás a felhasználók hitelesítéséhez

Ebben a forgatókönyvben egy fejlesztő rendelkezik WPF ügyfélalkalmazás, amelyet a távoli Azure Active Directory, például a webes API-k által védett erőforrások eléréséhez. Ezután a Azure-előfizetéssel rendelkezik, tudja, hogyan lehet meghívni az alsóbb rétegbeli webes API-t, és ismeri az Azure AD-bérlő a webes API-t használó. Ennek eredményeképpen használhat adal-t az Azure ad-vel, teljes delegálás, a felhasználói hitelesítés az adal-ra vagy explicit módon a felhasználói hitelesítő adatok kezelése hitelesítésének elősegítésére. ADAL teszi könnyen hitelesíteni a felhasználót, egy hozzáférési jogkivonatot, és a frissítési jogkivonat beszerzése az Azure AD és a hozzáférési jogkivonat segítségével ellenőrizze mindent lekér a webes API-hoz.

Azt mutatja be ezt a forgatókönyvet az Azure AD hitelesítési kódminta, lásd: [natív ügyfél WPF alkalmazás webes API](https://github.com/azureadsamples/nativeclient-dotnet).

### <a name="authenticating-a-confidential-client-application-running-on-a-web-server"></a>A webkiszolgálón futó bizalmas ügyfélalkalmazás hitelesítése

Ebben a forgatókönyvben egy fejlesztő rendelkezik olyan kiszolgálóra, amely az Azure AD, például egy webes API által biztosított távoli erőforráshoz hozzáférésre van szüksége a futó alkalmazást. Ezután a Azure-előfizetéssel rendelkezik, tudja, hogyan lehet meghívni az alárendelt szolgáltatás, és ismeri az Azure AD-bérlő a webes API-t használ. Ennek eredményeképpen ADAL használhat explicit módon kezeli az alkalmazás hitelesítő adatait az Azure AD hitelesítési elősegítése érdekében. ADAL teszi könnyen jogkivonatot beolvasni az Azure AD az alkalmazás ügyfél-hitelesítő adatok használatával, és, hogy a jogkivonat segítségével ellenőrizze mindent lekér a webes API-hoz. Adal-t is kezeli, gyorsítótárazás, és szükség szerint megújítása azt a hozzáférési jogkivonat élettartamát kezelése. Azt mutatja be, ebben a forgatókönyvben kódminta, lásd: [démon console Application webes API](https://github.com/AzureADSamples/Daemon-DotNet).

### <a name="authenticating-a-confidential-client-application-running-on-a-server-on-behalf-of-a-user"></a>Egy felhasználó nevében a kiszolgálón futó bizalmas ügyfélalkalmazás hitelesítése

Ebben a forgatókönyvben egy fejlesztő rendelkezik olyan kiszolgálóra, amely az Azure AD, például egy webes API által biztosított távoli erőforráshoz hozzáférésre van szüksége a futó alkalmazást. A kérelem is kell adni egy Azure AD-felhasználó nevében. Ezután a Azure-előfizetéssel rendelkezik, tudja, hogyan lehet meghívni az alsóbb rétegbeli webes API-t, és ismeri az Azure AD bérlői a szolgáltatás által használt. A webalkalmazásnak a felhasználó hitelesítését követően az alkalmazás az Azure AD kérheti le az engedélyezési kód a felhasználó számára. A webes alkalmazás segítségével ADAL szerezze be a hozzáférési tokent, és a frissítési token engedélyezési kódot és az ügyfél hitelesítő adatok használatával az Azure ad-alkalmazáshoz kapcsolódó egy felhasználó nevében. Ha a webes alkalmazás a hozzáférési jogkivonat rendelkezik, akkor meghívhatja a webes API-t, mindaddig, amíg a jogkivonat lejár. A jogkivonat lejár, a webes alkalmazás adal-t a frissítés használatával új tokenre jogkivonatának beszerzéséhez korábban fogadott segítségével. Azt mutatja be, ebben a forgatókönyvben kódminta, lásd: [Web API webes API-t natív ügyfelet](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof).

## <a name="see-also"></a>Lásd még:

- [Az Azure Active Directory fejlesztői útmutatója](active-directory-developers-guide.md)
- [Az Azure Active directory hitelesítési forgatókönyvei](active-directory-authentication-scenarios.md)
- [Az Azure Active Directory-Kódminták](active-directory-code-samples.md)
