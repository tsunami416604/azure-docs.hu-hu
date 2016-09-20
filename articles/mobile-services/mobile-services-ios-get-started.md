<properties
    pageTitle="Ismerkedés az Azure Mobile Services iOS-alkalmazásokkal való használatával | JavaScript-háttérrendszer"
    description="Ez az oktatóanyag segítséget nyújt az Azure Mobile Services iOS-fejlesztésre való használatának első lépéseiben."
    services="mobile-services"
    documentationCenter="ios"
    authors="krisragh"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="hero-article"
    ms.date="07/21/2016"
    ms.author="krisragh"/>

# <a name="getting-started"> </a>A Mobile Services használatának első lépései

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]

&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> A témakör megfelelő Mobile Apps-verziójáért lásd: [Create an iOS app in Azure Mobile Apps](../app-service-mobile/app-service-mobile-ios-get-started.md) (iOS-alkalmazás létrehozása az Azure Mobile Apps szolgáltatásban).

Az oktatóanyag bemutatja, hogyan adhat felhőalapú háttérszolgáltatást iOS-alkalmazásokhoz az Azure Mobile Services használatával.

Az oktatóprogram során létrehoz egy új mobilszolgáltatást, valamint egy egyszerű _Teendőlista_ alkalmazást, amely az alkalmazásadatokat az új mobilszolgáltatásban tárolja. A létrehozandó mobilszolgáltatás JavaScriptet használ a kiszolgálóoldali üzleti logikához. A .NET-es kiszolgálóoldali üzleti logikát használó mobilszolgáltatások létrehozásával kapcsolatban lásd a témakör [.NET háttérrendszer verziója] szakaszát.

> [AZURE.NOTE] Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. Ha nincs fiókja, regisztráljon az Azure-próbaverzióra, és a hozzá kapott [ingyenes mobilszolgáltatásokat a próbaidőszak után is használhatja](https://azure.microsoft.com/pricing/details/mobile-services/). További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-ios%2F%20).

## <a name="create-new-service"> </a>Új mobilszolgáltatás létrehozása

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## Új iOS-alkalmazás létrehozása

A klasszikus Azure portálon található gyors üzembe helyezési szolgáltatással létrehozhat egy, a mobilszolgáltatáshoz kapcsolódó új alkalmazást:

1. A [klasszikus Azure portál] kattintson a **Mobile Services** elemre, majd az imént létrehozott mobilszolgáltatásra.

2. A Quick Start (Gyors üzembe helyezés) lap **Choose a platform** (Platform kiválasztása) területén kattintson az **iOS** elemre, és bontsa ki a **Create a new iOS app** (Új iOS-alkalmazás létrehozása) elemet. Ez megjeleníti a mobilszolgáltatáshoz kapcsolódó új iOS-alkalmazás létrehozásának lépéseit.

3. Kattintson a **Create TodoItem table** (TodoItem tábla létrehozása) elemre az alkalmazásadatok tárolására szolgáló tábla létrehozásához.

4. A **Download and run your app** (Alkalmazás letöltése és futtatása) területen kattintson a **Download** (Letöltés) gombra. Ezzel letölti a projektet a mobilszolgáltatáshoz kapcsolt _Teendőlista_ mintaalkalmazáshoz, továbbá letölti a Mobile Services iOS SDK-t is. Mentse el a tömörített projektfájlt a helyi számítógépen, és jegyezze fel a mentési helyét.

## Az új iOS-alkalmazás futtatása

[AZURE.INCLUDE [mobile-services-ios-run-app](../../includes/mobile-services-ios-run-app.md)]

<ol start="4">
<li><p>A klasszikus Azure portál visszatérve kattintson a **DATA** (ADATOK) fülre, majd a **TodoItem** táblára. Így tallózással kiválaszthatja az alkalmazás által a táblába beszúrt adatokat.<p></li></ol></p>

## <a name="next-steps"> </a>Következő lépések
Megtudhatja, hogyan hajthat végre további fontos feladatokat a Mobile Servicesben:

* [Bevezetés az offline adatszinkronizálás használatába]
    <br/>Megismerheti, hogyan teheti gyorsabban kezelhetővé és robusztusabbá alkalmazását az offline adatszinkronizálás segítségével.

* [Hitelesítés hozzáadása meglévő alkalmazáshoz]
    <br/>Ismerje meg, hogyan hitelesítheti az alkalmazás felhasználóit egy identitásszolgáltatóval.

* [Leküldéses értesítések hozzáadása meglévő alkalmazáshoz]
    <br/>Ismerje meg, hogyan küldhet nagyon egyszerű leküldéses értesítéseket az alkalmazásnak.

[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]


<!-- Anchors. -->
[A Mobile Services használatának első lépései]:#getting-started
[Új mobilszolgáltatás létrehozása]:#create-new-service
[A mobilszolgáltatás-példány meghatározása]:#define-mobile-service-instance
[Következő lépések]:#next-steps

<!-- Images. -->
[6]: ./media/mobile-services-ios-get-started/mobile-portal-quickstart-ios.png
[7]: ./media/mobile-services-ios-get-started/mobile-quickstart-steps-ios.png
[8]: ./media/mobile-services-ios-get-started/mobile-xcode-project.png

[10]: ./media/mobile-services-ios-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/mobile-services-ios-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-ios-get-started/mobile-data-browse.png


<!-- URLs. -->
[Bevezetés az offline adatszinkronizálás használatába]: mobile-services-ios-get-started-offline-data.md
[Hitelesítés hozzáadása meglévő alkalmazáshoz]: mobile-services-dotnet-backend-ios-get-started-users.md
[Leküldéses értesítések hozzáadása meglévő alkalmazáshoz]: mobile-services-dotnet-backend-ios-get-started-push.md


[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[klasszikus Azure portál]: https://manage.windowsazure.com/
[XCode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[.NET háttérrendszer verziója]: mobile-services-dotnet-backend-ios-get-started.md



<!--HONumber=sep16_HO1-->


