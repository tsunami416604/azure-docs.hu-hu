<properties
    pageTitle="Ismerkedés a Mobile Services Xamarin-alapú iOS-alkalmazásokkal való használatával | Microsoft Azure"
    description="Ez az oktatóanyag a Xamarinhoz készült Azure Mobile Services iOS-fejlesztések első lépéseihez nyújt segítséget."
    services="mobile-services"
    documentationCenter="xamarin"
    authors="conceptdev"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="03/16/2016"
    ms.author="craig.dunn@xamarin.com"/>

# <a name="getting-started"> </a>A Mobile Services használatának első lépései
[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]
&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> A témakör megfelelő Mobile Apps-verziójáért lásd: [Create a Xamarin.iOS App](../app-service-mobile/app-service-mobile-xamarin-ios-get-started.md) (Xamarin.iOS-alkalmazás létrehozása).

Az oktatóanyag bemutatja, hogyan adhat felhőalapú háttérszolgáltatást Xamarin.iOS-alkalmazásokhoz az Azure Mobile Services használatával. Az oktatóprogram során létrehoz egy új mobilszolgáltatást, valamint egy egyszerű *Teendőlista* alkalmazást, amely az alkalmazásadatokat az új mobilszolgáltatásban tárolja.

Ha inkább a videót tekintené meg, az alábbi klip ugyanazokat a lépéseket tartalmazza, mint az oktatóanyag.

Videó: „Getting Started with Xamarin and Azure Mobile Services” (A Xamarin és az Azure Mobile Services használatának első lépései) Craig Dunn-nal, a Xamarin fejlesztői szakértőjével (hossz: 10:05 perc)

> [AZURE.VIDEO getting-started-with-xamarin-and-mobile-services]

A kész alkalmazásról az alábbiakban látható egy képernyőkép:

![][0]

Az oktatóprogram elvégzéséhez Xcode és OS X-hez készült Xamarin Studio vagy Windowson futó Visual Studio szükséges egy hálózathoz csatlakozó Mac géppel. A teljes telepítési útmutatás itt található: [Setup and Install for Visual Studio and Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) (A Visual Studio és a Xamarin beállítása és telepítése). 

> [AZURE.IMPORTANT] Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. Ha nincs fiókja, regisztráljon az Azure-próbaverzióra, és a hozzá kapott akár 10 ingyenes mobilszolgáltatást a próbaidőszak után is használhatja. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-new-service"> </a>Új mobilszolgáltatás létrehozása

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## Új Xamarin.iOS-alkalmazás létrehozása

Miután létrehozta a mobilszolgáltatást, a klasszikus Azure portálon található egyszerű gyors üzembe helyezés szolgáltatással létrehozhat egy új alkalmazást, vagy módosíthat egy meglévő alkalmazást a mobilszolgáltatáshoz való csatlakozáshoz.

Ebben a szakaszban egy, a mobilszolgáltatásához kapcsolódó új Xamarin.iOS-alkalmazást hoz létre.

1.  A [klasszikus Azure portál] kattintson a **Mobile Services** elemre, majd az imént létrehozott mobilszolgáltatásra.

2. A gyors üzembe helyezés lap **Choose platform** (Platform kiválasztása) részében kattintson a **Xamarin.iOS** elemre, és bontsa ki a **Create a new Xamarin.iOS app** (Új Xamarin.iOS-alkalmazás létrehozása) részt.

    ![][6]

    Ez megjeleníti a mobilszolgáltatáshoz kapcsolódó új Xamarin.iOS-alkalmazás létrehozásának három egyszerű lépését.

    ![][7]

3. Ha még nem tette, töltse le és telepítse az Xcode-ot (a legújabb, az Xcode 6.0-s vagy újabb verzió javasolt) és a [Xamarin Studio].

4. Kattintson a **Create TodoItems table** (TodoItem tábla létrehozása) elemre az alkalmazásadatok tárolására szolgáló tábla létrehozásához.

5. A **Download and run app** (Alkalmazás letöltése és futtatása) területen kattintson a **Download** (Letöltés) gombra.

    Ezzel letölti a projektet a mobilszolgáltatáshoz kapcsolódó _Teendőlista_ mintaalkalmazáshoz, és hivatkozik az Azure Mobile Services Xamarin.iOS-összetevőjére. Mentse el a tömörített projektfájlt a helyi számítógépen, és jegyezze fel a mentési helyét.

## Az új Xamarin.iOS-alkalmazás futtatása

Az oktatóanyag utolsó szakasza az új alkalmazás felépítéséből és futtatásából áll.

1. Keresse meg a helyet, ahová a tömörített projektfájlokat mentette, tömörítse ki a fájlokat a számítógépre, és nyissa meg a **XamarinTodoQuickStart.iOS.sln** megoldásfájlt a Xamarin Studióban vagy a Visual Studióban.

    ![][8]

    ![][9]

2. Nyomja le a **Run** (Futtatás) gombot a projekt felépítéséhez és az alkalmazás elindításához az iPhone-emulátorban, amely ebben a projektben az alapértelmezés.

3. Az alkalmazásban írjon be egy jelentéssel bíró szöveget, például _Az oktatóanyag befejezése_, majd kattintson a plusz (**+**) ikonra.

    ![][10]

    Ez egy POST kérést küld az Azure-ban futtatott új mobilszolgáltatásnak. A kérelem adatai beillesztésre kerülnek a TodoItem táblába. A mobilszolgáltatás visszaadja a táblában tárolt elemeket, amelyek egy listában jelennek meg.

    > [AZURE.NOTE] A mobilszolgáltatáshoz az adatok lekérdezése és beszúrása céljából hozzáférő kódot át is tekintheti a TodoService.cs C# fájlban.

4. A [klasszikus Azure portál] visszatérve kattintson a **Data** (Adatok) fülre, majd a **TodoItems** táblára.

    ![][11]

    Így tallózással kiválaszthatja az alkalmazás által a táblába beszúrt adatokat.

    ![][12]


## Következő lépések
Most, hogy végzett a gyors üzembe helyezéssel, megtudhatja, hogyan hajthat végre további fontos feladatokat a Mobile Servicesben:

* [Bevezetés az offline adatszinkronizálás használatába] Megismerheti, hogy a gyors üzembe helyezés hogyan teszi gyorsabban kezelhetővé és robusztusabbá alkalmazását az offline adatszinkronizálás segítségével.

* [Bevezetés a hitelesítés használatába] Ismerje meg, hogyan hitelesítheti az alkalmazás felhasználóit egy identitásszolgáltatóval.

* [Bevezetés a leküldéses értesítések használatába] Ismerje meg, hogyan küldhet nagyon egyszerű leküldéses értesítéseket az alkalmazásnak.




[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Anchors. -->
[A Mobile Services használatának első lépései]:#getting-started
[Új mobilszolgáltatás létrehozása]:#create-new-service
[A mobilszolgáltatás-példány meghatározása]:#define-mobile-service-instance
[Következő lépések]:#next-steps

<!-- Images. -->
[0]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-completed-ios.png
[6]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-portal-quickstart-xamarin-ios.png
[7]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-steps-xamarin-ios.png
[8]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-xamarin-project-ios-xs.png
[9]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-xamarin-project-ios-vs.png
[10]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-data-tab.png
[12]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-data-browse.png


<!-- URLs. -->
[Bevezetés az offline adatszinkronizálás használatába]: mobile-services-xamarin-ios-get-started-offline-data.md
[Bevezetés a hitelesítés használatába]: partner-xamarin-mobile-services-ios-get-started-users.md
[Bevezetés a leküldéses értesítések használatába]: partner-xamarin-mobile-services-ios-get-started-push.md

[Xamarin Studio]: http://xamarin.com/download
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533

[klasszikus Azure portál]: https://manage.windowsazure.com/



<!--HONumber=Jun16_HO2-->


