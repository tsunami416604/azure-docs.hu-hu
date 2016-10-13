<properties
    pageTitle="Android-alkalmazás létrehozása az Azure App Service Mobile Apps szolgáltatásban| Microsoft Azure"
    description="Az útmutató bevezeti Önt az Azure-alapú mobil-háttéralkalmazások használatával megvalósítható, Android rendszerben történő fejlesztésbe."
    services="app-service\mobile"
    documentationCenter="android"
    authors="yuaxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="na"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="hero-article"
    ms.date="10/01/2016"
    ms.author="yuaxu"/>


#Android-alkalmazás létrehozása

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## Áttekintés

Ez az oktatóanyag azt ismerteti, hogyan adhat felhőalapú háttérszolgáltatásokat az Android-mobilalkalmazásokhoz egy Azure-alapú mobil-háttéralkalmazás segítségével.  Létre fog hozni egy új mobil-háttéralkalmazást, illetve egy egyszerű _Tennivalólista_ Android-alkalmazást, amely alkalmazásadatokat tárol az Azure-ban.

Az oktatóanyag végrehajtása feltétele az Azure App Service Mobile Apps szolgáltatásának használatát ismertető többi Android-oktatóanyag megértésének.

## Előfeltételek

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* [Android Developer Tools](https://developer.android.com/sdk/index.html): ez a csomag tartalmazza az androidos integrált fejlesztőkörnyezetet, valamint a legújabb Android-platformot.
* Azure Mobile Android SDK, amelyre a rendszer automatikusan hivatkozik a letöltött bevezető gyorssablonos projekt letöltésekor.
* [Aktív Azure-fiók](https://azure.microsoft.com/pricing/free-trial/).

## Új Azure Mobile Apps-háttéralkalmazás létrehozása

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## Kiszolgálóprojekt konfigurálása

[AZURE.INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## Az Android-alkalmazás letöltése és futtatása

[AZURE.INCLUDE [app-service-mobile-android-run-app](../../includes/app-service-mobile-android-run-app.md)]


<!-- Images. -->

<!-- URLs -->
[Azure Portal]: https://portal.azure.com/
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203



<!--HONumber=Oct16_HO1-->


