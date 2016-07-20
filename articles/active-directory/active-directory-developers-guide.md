<properties
   pageTitle="Az Azure Active Directory fejlesztői útmutatója | Microsoft Azure"
   description="Ez a cikk az Azure Active Directory fejlesztőknek készített erőforrásairól nyújt átfogó képet."
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="msmbaldwin"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="04/02/2016"
   ms.author="mbaldwin"/>


# Az Azure Active Directory fejlesztői útmutatója

## Áttekintés
Mivel az Azure Active Directory egy identitáskezelést szolgáltatásként (IDMaaS) nyújtó platform, hatékony módot biztosít a fejlesztők számára, hogy az alkalmazásokba integrálhassák az identitáskezelést. A következő cikkek az Azure Active Directory implementálásának és fő szolgáltatásainak áttekintését nyújtják. Ajánlott ezeket sorrendben elolvasni, vagy ha készen áll, folytassa az [Első lépések](#getting-started) című szakasszal.


1. [Az Azure Active Directory integrációjának előnyei:](active-directory-how-to-integrate.md) Megtudhatja, miért kínálja a legjobb megoldást az Azure Active Directory integrációja a biztonságos bejelentkezéshez és hitelesítéshez.

1. [Az Active Directory hitelesítési forgatókönyvei:](active-directory-authentication-scenarios.md) Kihasználhatja az Azure Active Directory egyszerűsített hitelesítésének előnyeit az alkalmazásba történő bejelentkezéshez.

1. [Alkalmazások integrálása az Azure Active Directoryval:](active-directory-integrating-applications.md) Megtudhatja, hogyan adhat hozzá, frissíthet és távolíthat el alkalmazásokat az Azure Active Directoryból, és megismerheti az integrált alkalmazások arculati útmutatóját.

1. [Azure Active Directory Graph API:](active-directory-graph-api.md) Az Azure Active Directory Graph API-val programozott módon érheti el az Azure Active Directoryt REST API-végpontokon keresztül. Vegye figyelembe, hogy az Azure AD Graph API a [Microsoft Graph](https://graph.microsoft.io/) egyesített API-n keresztül is elérhető, amely lehetővé teszi több Microsoft-felhőszolgáltatás API-elérését egyetlen REST API-végponton keresztül, és egyetlen hozzáférési tokennel.

1. [Az Azure Active Directory hitelesítési kódtárai:](active-directory-authentication-libraries.md) A .NET, JavaScript, Objective-C, Android és további Azure AD-hitelesítési kódtárakkal könnyedén hitelesítheti a felhasználókat a hozzáférési tokenek beszerzése érdekében.


## Első lépések

Ezek az oktatóanyagok több platformot is lefednek, és segíthetnek, hogy minél gyorsabban elkezdje az Azure Active Directoryval folytatott fejlesztést. Előfeltételként [be kell szereznie egy Azure Active Directory-bérlőt](active-directory-howto-tenant.md).

### Mobil- és számítógépes alkalmazások első lépéseinek útmutatói

|[![iOS](./media/active-directory-developers-guide/ios.png)](active-directory-devquickstarts-ios.md)|[![Android](./media/active-directory-developers-guide/android.png)](active-directory-devquickstarts-android.md)|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-dotnet.md)|[![Windows Universal](./media/active-directory-developers-guide/windows.png)](active-directory-devquickstarts-windowsstore.md)|[![Xamarin](./media/active-directory-developers-guide/xamarin.png)](active-directory-devquickstarts-xamarin.md)|[![Cordova](./media/active-directory-developers-guide/cordova.png)](active-directory-devquickstarts-cordova.md)
|:--:|:--:|:--:|:--:|:--:|:--:|:--:
|[iOS](active-directory-devquickstarts-ios.md)|[Android](active-directory-devquickstarts-android.md)|[.NET](active-directory-devquickstarts-dotnet.md)|[Windows Phone](active-directory-devquickstarts-windowsphone.md)|[Windows Áruház](active-directory-devquickstarts-windowsstore.md)|[Xamarin](active-directory-devquickstarts-xamarin.md)|[Cordova](active-directory-devquickstarts-cordova.md)

### Webalkalmazások első lépéseinek útmutatói

|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapp-dotnet.md)|[![Java](./media/active-directory-developers-guide/java.png)](active-directory-devquickstarts-webapp-java.md)|[![Javascript](./media/active-directory-developers-guide/javascript.png)](active-directory-devquickstarts-angular.md)|[![Node.js](./media/active-directory-developers-guide/nodejs.png)](active-directory-devquickstarts-openidconnect-nodejs.md)
|:--:|:--:|:--:|:--:|
|[.NET](active-directory-devquickstarts-webapp-dotnet.md)|[Java](active-directory-devquickstarts-webapp-java.md)|[Javascript](active-directory-devquickstarts-angular.md)|[Node.js](active-directory-devquickstarts-openidconnect-nodejs.md)

### Webes API-k első lépéseinek útmutatói

|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapi-dotnet.md)|[![Node.js](./media/active-directory-developers-guide/nodejs.png)](active-directory-devquickstarts-webapi-nodejs.md)
|:--:|:--:|
|[.NET](active-directory-devquickstarts-webapi-dotnet.md)|[Node.js](active-directory-devquickstarts-webapi-nodejs.md)

### A címtárlekérdezés első lépéseinek útmutatója

| [![.NET](./media/active-directory-developers-guide/graph.png)](active-directory-graph-api-quickstart.md)|
|:--:|
|[Graph API](active-directory-graph-api-quickstart.md)|

## Használati útmutatók

Ezek a cikkek ismertetik, hogyan végezhet el bizonyos feladatokat az Azure Active Directoryval:

- [Azure Active Directory-bérlő beszerzése](active-directory-howto-tenant.md)
- [Az alkalmazás szerepeltetése az Azure Active Directory alkalmazáskatalógusában](active-directory-app-gallery-listing.md)
- [Az Azure Active Directory alkalmazásjegyzékének megismerése](active-directory-application-manifest.md)
- [Alkalmazás létrehozása Office 365 API-kkal](https://msdn.microsoft.com/office/office365/howto/getting-started-Office-365-APIs)
- [Webalkalmazások beküldése az Office 365 Értékesítői információközpontjába](https://msdn.microsoft.com/office/office365/howto/submit-web-apps-seller-dashboard)
- Megtudhatja, hogyan engedélyezheti az alkalmazások közötti SSO-t ADAL-kódtárral [Android](active-directory-sso-android.md) és [iOS](active-directory-sso-ios.md) rendszerű eszközökön
- [Előzetes verzió: Olyan alkalmazások fordítása, amelyekben a személyes és a munkahelyi vagy iskolai fiókokkal is bejelentkezhetnek a felhasználók](active-directory-appmodel-v2-overview.md)
- [Előzetes verzió: Olyan alkalmazások fordítása, amelyekben a végfelhasználók regisztrálhatnak és bejelentkezhetnek](../active-directory-b2c/active-directory-b2c-overview.md)


## Referencia

Ezek a cikkek a REST és hitelesítési kódtár API-k, protokollok, hibák, kódminták és végpontok alapvető referenciáit biztosítják  

###  Támogatás
- [Címkével ellátott kérdések:](http://stackoverflow.com/questions/tagged/azure-active-directory) Az [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) és [adal](http://stackoverflow.com/questions/tagged/adal) címkékkel ellátott Azure Active Directory-megoldások keresése a Stack Overflow oldalain.

### Kód

- [Az Azure Active Directory nyílt forráskódú kódtárai:](http://github.com/AzureAD) A kódtárforrások megkeresésének legegyszerűbb módja a [kódtárlistánk](active-directory-authentication-libraries.md) használata.

- [Azure Active Directory-példák:](https://github.com/azure-samples?query=active-directory) A példák listájában történő böngészés legegyszerűbb módja a [kódpéldák tárgymutatójának](active-directory-code-samples.md) használata.

- [ADAL for .NET:](https://msdn.microsoft.com/library/azure/mt417579.aspx) A .NET-hitelesítési kódtár dokumentációja.

### Graph API

- [Graph API-referencia:](https://msdn.microsoft.com/library/azure/hh974476.aspx) REST-referencia az Azure Active Directory Graph API-hoz. [A Graph API-referencia interaktív felhasználói élményének megismerése](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

- [Graph API-engedélyhatókörök:](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes) Olyan OAuth 2.0-engedélyhatókörök, amelyekkel az alkalmazások hozzáférése vezérelhető a bérlőkben lévő címtáradatokhoz.

### Hitelesítési protokollok

- [Az Azure Active Directory hitelesítési protokolljai:](active-directory-protocols.md) Megismerheti az Azure Active Directory által támogatott különböző hitelesítési és engedélyezési protokollokat.

- [A SAML 2.0 protokoll referenciája:](active-directory-saml-protocol-reference.md) A SAML 2.0 protokoll lehetővé teszi, hogy az alkalmazások egyszeri bejelentkezéses működést biztosítsanak a felhasználóiknak.

- [Az OAuth 2.0 protokoll referenciája:](active-directory-protocols-oauth-code.md) Az OAuth 2.0 protokollal a webalkalmazásokhoz és a webes API-khoz engedélyezheti a hozzáférést az Azure Active Directory-bérlőben.

- [Az OpenID Connect 1.0 protokoll referenciája:](active-directory-protocols-openid-connect-code.md) Az OpenID Connect 1.0 protokoll az OAuth 2.0 protokollt bővíti ki hitelesítési protokollként történő használat esetén.

- [WS-Federation 1.2 protokoll:](http://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html) Az Azure Active Directory támogatja a WS-Federation 1.2 protokoll a Web Services Federation Version 1.2 specifikációnak megfelelő használatát.

- [Támogatott token- és jogcímtípusok:](active-directory-token-and-claims.md) Ennek az útmutatónak a segítségével megismerheti és kiértékelheti a SAML 2.0 és a JSON Web Tokens (JWT) tokenek jogcímeit.

## Videók

### Felépítés

Ezek az áttekintő bemutatók az alkalmazásfejlesztést ismertetik Azure Active Directory kiemelt előadóinak közreműködésével, akik közvetlenül a mérnöki csapatban dolgoznak. A bemutatók alapvető témaköröket érintenek, beleértve az IDMaaS-t, a hitelesítést, az identitás-összevonást és az egyszeri bejelentkezést.

- [Microsoft Identity: Az unió állapota és a jövőbeli irány](https://azure.microsoft.com/documentation/videos/build-2016-microsoft-identity-state-of-the-union-and-future-direction/)
- [Azure Active Directory: Identitáskezelés a modern alkalmazások szolgáltatásaként](https://azure.microsoft.com/documentation/videos/build-2015-azure-active-directory-identity-management-as-a-service-for-modern-applications/)
- [Modern webalkalmazások fejlesztése az Azure Active Directoryval](https://azure.microsoft.com/documentation/videos/build-2015-develop-modern-web-applications-with-azure-active-directory/)
- [Modern natív alkalmazások fejlesztése az Azure Active Directoryval](https://azure.microsoft.com/documentation/videos/build-2015-develop-modern-native-applications-with-azure-active-directory/)

### Azure Friday
Az [Azure Friday](https://azure.microsoft.com/documentation/videos/azure-friday/) egy péntekenként ismétlődő videósorozat, amely rövid (10–15 perces), szakértőkkel készített interjúkat vonultat fel különböző Azure-témakörökkel kapcsolatban.  Az oldalon lévő Services (Szolgáltatások) szűrővel tekintheti meg az összes Azure Active Directory-videót.

- [Azure-identitás 101](https://azure.microsoft.com/documentation/videos/azure-identity-basics/)
- [Azure-identitás 102](https://azure.microsoft.com/documentation/videos/azure-identity-creating-active-directory/)
- [Azure-identitás 103](https://azure.microsoft.com/documentation/videos/azure-identity-application-to-authenticate/)

## Közösségi tartalom

- [Az Active Directory csapatának blogja:](http://blogs.technet.com/b/ad/) Ismerje meg az Azure Active Directory világának legújabb fejlesztéseit.

- [Az Azure Active Directory Graph csapatának blogja:](http://blogs.msdn.com/b/aadgraphteam) Ismerje meg a Graph API-val kapcsolatos Azure Active Directory-információkat.

- [Cloud Identity:](http://www.cloudidentity.net) Gondolatok az identitáskezelésről mint szolgáltatásról, az Azure Active Directory vezető projektmenedzserétől.  

- [Azure Active Directory a Twitteren:](https://twitter.com/azuread) Azure Active Directory-közlemények legfeljebb 140 karakterben.



<!--HONumber=Jun16_HO2-->


