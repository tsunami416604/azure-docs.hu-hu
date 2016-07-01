
<properties
    pageTitle="Ismerkedés az Azure Mobile Services for Android alkalmazásokkal"
    description="Ez az oktatóanyag segítséget nyújt az Azure Mobile Services for Android-fejlesztések első lépéseiben."
    services="mobile-services"
    documentationCenter="android"
    authors="RickSaling"
    manager="erikre"
    editor=""/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="get-started-article"
    ms.date="03/05/2016"
    ms.author="ricksal"/>


# <a name="getting-started"> </a>A Mobile Services használatának első lépései

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]

&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> A témakör megfelelő Mobile Apps-verzióját itt találja: [Android-alkalmazás létrehozása az Azure Mobile Apps szolgáltatásban](../app-service-mobile/app-service-mobile-android-get-started.md).

Az oktatóanyag bemutatja, hogyan adhat felhőalapú háttérszolgáltatást Android-alkalmazásokhoz az Azure Mobile Services használatával. Az oktatóprogram során létrehoz egy új mobilszolgáltatást, valamint egy egyszerű _Teendőlista_ alkalmazást, amely az alkalmazásadatokat az új mobilszolgáltatásban tárolja. Az így létrehozott mobilszolgáltatás a támogatott .NET-nyelveket használó Visual Studio használatával kezeli a kiszolgálóoldali üzleti logikát és a mobilszolgáltatást. A témakör alábbi szakasza bemutatja egy olyan mobilszolgáltatások létrehozását, amely lehetővé teszi a kiszolgálóoldali üzleti logika írását a JavaScript használatával: [JavaScript háttérrendszer verzió](mobile-services-android-get-started.md).

A kész alkalmazásról az alábbiakban látható egy képernyőkép:

![](./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-completed-android.png)

Az oktatóanyag elvégzéséhez szükség lesz az [Android Developer Tools][Android Studio] eszközökre, amelyek az Android Studio integrált fejlesztőkörnyezetet és a legújabb Android platformot foglalják magukban. Android 4.2 vagy újabb verzió szükséges.

A letöltött gyorsútmutató-projekt tartalmazza a Mobile Services SDK for Android csomagot.

> [AZURE.IMPORTANT] Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. Ha nincs fiókja, regisztráljon az Azure-próbaverzióra, és a hozzá kapott akár 10 ingyenes mobilszolgáltatást a próbaidőszak után is használhatja. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28).


## <a name="create-new-service"> </a>Új mobilszolgáltatás létrehozása

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../../includes/mobile-services-dotnet-backend-create-new-service.md)]

## Töltse le a mobilszolgáltatást a helyi számítógépre.

Miután létrehozta a mobilszolgáltatást, töltse le a személyre szabott mobilszolgáltatás-projektjét, amelyet a helyi számítógépén vagy virtuális gépén futtathat.

1. Kattintson az imént létrehozott mobilszolgáltatásra, majd a gyorsindítási lap **Platform kiválasztása** részében kattintson az **Android** lehetőségre, és bontsa ki az **Új Android-alkalmazás létrehozása** elemet.

    ![][1]

2. Ha még nem tette, töltse le és telepítse a [Visual Studio 2013 Professional](https://go.microsoft.com/fwLink/p/?LinkID=391934) eszközt, vagy ennek egy frissebb verzióját.

3. A második lépésben kattintson az **Alkalmazás letöltése és futtatása** menüben a **Letöltés** gombra.

    Ez letölti a mobilszolgáltatását megvalósító Visual Studio-projektet. Mentse el a tömörített projektfájlt a helyi számítógépen, és jegyezze fel a mentési helyét.

## Mobilszolgáltatás tesztelése

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service](../../includes/mobile-services-dotnet-backend-test-local-service.md)]

## Mobilszolgáltatás közzététele

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../../includes/mobile-services-dotnet-backend-publish-service.md)]

## Új Android-alkalmazás létrehozása

Ebben a szakaszban egy, a mobilszolgáltatásához csatlakozó új Android-alkalmazást hoz létre.

1. A [Klasszikus Azure portál] kattintson a **Mobile Services** elemre, majd az imént létrehozott mobilszolgáltatásra.

2. A gyorsindítási lap **Platform kiválasztása** szakaszában kattintson az **Android** lehetőségre, és bontsa ki az **Új Android-alkalmazás létrehozása** elemet.

    ![][2]

3. Ha még nem tette, töltse le és telepítse az [Android Developer Tools][Android SDK] eszközöket a helyi számítógépén vagy virtuális gépén.

4. A **Download and run your app** (Alkalmazás letöltése és futtatása) területen kattintson a **Download** (Letöltés) gombra.

    Ezzel letölti a projektet a mobilszolgáltatáshoz kapcsolódó _Teendőlista_ mintaalkalmazáshoz. Mentse el a tömörített projektfájlt a helyi számítógépen, és jegyezze fel a mentési helyét.

## Az Android-alkalmazás futtatása

[AZURE.INCLUDE [mobile-services-run-your-app](../../includes/mobile-services-android-get-started.md)]

## <a name="next-steps"> </a>Következő lépések
Most, hogy végzett a gyors üzembe helyezéssel, megtudhatja, hogyan hajthat végre további fontos feladatokat a Mobile Servicesben:

* [Leküldéses értesítések hozzáadása az alkalmazáshoz]
  <br/>Ismerje meg, hogyan küldhet nagyon egyszerű leküldéses értesítéseket az alkalmazásnak.

* [Hitelesítés hozzáadása az alkalmazáshoz]
  <br/>Ismerje meg, hogyan korlátozhatja a háttérrendszeri adatok elérhetőségét az alkalmazás egyes regisztrált felhasználóira.

* [Mobile Services .NET-háttérrendszer hibaelhárítása]
  <br/> Ismerje meg, hogyan diagnosztizálhatja és javíthatja ki a Mobile Services .NET-háttérrendszerekkel kapcsolatban felmerülő hibákat.

<!-- Anchors. -->
[A Mobile Services használatának első lépései]:#getting-started
[Új mobilszolgáltatás létrehozása]:#create-new-service
[A mobilszolgáltatás-példány meghatározása]:#define-mobile-service-instance
[Következő lépések]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-completed-android.png
[1]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-steps-vs-AS.png
[2]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-steps-android-AS.png


[6]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-portal-quickstart-android.png
[7]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-steps-android.png
[8]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-eclipse-quickstart.png

[10]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-startup-android.png
[11]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-data-browse.png

[14]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-services-import-android-workspace.png
[15]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-services-import-android-project.png

<!-- URLs. -->
[Első lépések (Eclipse)]: mobile-services-dotnet-backend-android-get-started-ec.md
[Leküldéses értesítések hozzáadása az alkalmazáshoz]: mobile-services-dotnet-backend-android-get-started-push.md
[Hitelesítés hozzáadása az alkalmazáshoz]: mobile-services-dotnet-backend-android-get-started-auth.md
[Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125
[Android Studio]: https://developer.android.com/sdk/index.html
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Mobile Services .NET-háttérrendszer hibaelhárítása]: mobile-services-dotnet-backend-how-to-troubleshoot.md

[Klasszikus Azure portál]: https://manage.windowsazure.com/



<!--HONumber=Jun16_HO2-->


