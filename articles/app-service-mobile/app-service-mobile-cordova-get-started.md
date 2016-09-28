<properties
    pageTitle="Cordova-alkalmazás létrehozása az Azure App Service Mobile Apps szolgáltatásban| Microsoft Azure"
    description="Az útmutató bevezeti Önt az Azure-alapú mobil-háttéralkalmazások használatával megvalósítható, Apache Cordova keretrendszerben történő fejlesztésbe."
    services="app-service\mobile"
    documentationCenter="javascript"
    authors="adrianhall"
    manager="erikre"
    editor=""
    tags=""
    keywords="cordova,javascript,mobil,ügyfél" />

<tags
    ms.service="app-service-mobile"
    ms.workload="na"
    ms.tgt_pltfrm="mobile-html"
    ms.devlang="javascript"
    ms.topic="hero-article"
    ms.date="08/11/2016"
    ms.author="glenga"/>


#Apache Cordova-alkalmazás létrehozása

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## Áttekintés

Ez az oktatóanyag azt ismerteti, hogyan adhat felhőalapú háttérszolgáltatásokat az Apache Cordova-mobilalkalmazásokhoz egy Azure-alapú mobil-háttéralkalmazás segítségével.  Létre fog hozni egy új mobil-háttéralkalmazást, illetve egy egyszerű _Tennivalólista_ Apache Cordova-alkalmazást, amely alkalmazásadatokat tárol az Azure-ban.

Az oktatóanyag végrehajtása feltétele az Azure App Service Mobile Apps szolgáltatásának használatát ismertető többi Apache Cordova-oktatóanyag megértésének.

## Előfeltételek

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Számítógép, amelyen fut a [Visual Studio Community 2015] vagy újabb verzió.
* [Visual Studio Tools for Apache Cordova].
* [Aktív Azure-fiók](https://azure.microsoft.com/pricing/free-trial/).

Közvetlenül az Apache Cordova parancssorának használata esetén nincs szükség a Visual Studióra.  Ez akkor lehet hasznos, ha az oktatóanyagot Mac gépen szeretné elvégezni.  Az oktatóanyag nem tér ki arra, hogy miképpen lehet az Apache Cordova-ügyfélalkalmazásokat parancssor segítségével lefordítani.

## Új Azure Mobile Apps-háttéralkalmazás létrehozása

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

[Tekintse meg hasonló lépéseket ismertető videót](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-1-Create-an-Azure-Mobile-App)

## Kiszolgálóprojekt konfigurálása

[AZURE.INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## Az Apache Cordova-alkalmazás letöltése és futtatása

[AZURE.INCLUDE [app-service-mobile-cordova-run-app](../../includes/app-service-mobile-cordova-run-app.md)]

## Következő lépések

Ha elvégezte ezt a bevezető oktatóanyagot, lépjen tovább valamelyik további anyagra:

* [Hitelesítés hozzáadása] az Apache Cordova-alkalmazáshoz.
* [Leküldéses értesítések hozzáadása] az Apache Cordova-alkalmazáshoz.

További információk az Azure App Service alapjairól.

* [Authentication]
* [Leküldéses értesítések]

Útmutató az SDK-k használatához.

* [Apache Cordova SDK]
* [ASP.NET Server SDK]
* [Node.js Server SDK]

<!-- Images. -->

<!-- URLs -->
[Azure Portal]: https://portal.azure.com/
[Visual Studio Community 2015]: http://www.visualstudio.com/
[Visual Studio Tools for Apache Cordova]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[Hitelesítés hozzáadása]: app-service-mobile-cordova-get-started-users.md
[Leküldéses értesítések hozzáadása]: app-service-mobile-cordova-get-started-push.md
[Authentication]: app-service-mobile-auth.md
[Leküldéses értesítések]: ../notification-hubs/notification-hubs-push-notification-overview.md
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[ASP.NET Server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js Server SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md



<!--HONumber=Sep16_HO4-->


