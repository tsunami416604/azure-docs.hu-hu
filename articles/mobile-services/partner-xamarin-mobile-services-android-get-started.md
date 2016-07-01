<properties
    pageTitle="A Xamarinhoz készült Mobile Services bemutatása | Microsoft Azure"
    writer="craigd"
    description="Ismerje meg, hogyan használható az Azure Mobile Services a Xamarin.Android-alkalmazásával."
    documentationCenter="xamarin"
    authors="lindydonna"
    manager="dwrede"
    editor=""
    services="mobile-services"/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-android"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="02/10/2016"
    ms.author="donnam"/>

# <a name="getting-started"></a>A Mobile Services használatának első lépései

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]
&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> A témakör megfelelő Mobile Apps-verziójáért lásd: [Create a Xamarin.Android App](../app-service-mobile/app-service-mobile-xamarin-android-get-started.md) (Xamarin.Android-alkalmazás létrehozása).

Az oktatóanyag bemutatja, hogyan adhat felhőalapú háttérszolgáltatást Xamarin.Android-alkalmazásokhoz az Azure Mobile Services használatával. Az oktatóprogram során létrehoz egy új mobilszolgáltatást, valamint egy egyszerű *Teendőlista* alkalmazást, amely az alkalmazásadatokat az új mobilszolgáltatásban tárolja.

Ha inkább a videót tekintené meg, az alábbi klip ugyanazokat a lépéseket tartalmazza, mint az oktatóanyag.

Videó: „Getting Started with Xamarin and Azure Mobile Services” (A Xamarin és az Azure Mobile Services használatának első lépései) Craig Dunn-nal, a Xamarin fejlesztői szakértőjével (hossz: 10:05 perc)

> [AZURE.VIDEO getting-started-with-xamarin-and-mobile-services]

A kész alkalmazásról az alábbiakban látható egy képernyőkép:

![][0]

Az oktatóprogram elvégzéséhez Xcode és OS X-hez készült Xamarin Studio vagy Windowson futó Visual Studio szükséges egy hálózathoz csatlakozó Mac géppel. A teljes telepítési útmutatás itt található: [Setup and Install for Visual Studio and Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) (A Visual Studio és a Xamarin beállítása és telepítése). 

A letöltött gyorsútmutató-projekt tartalmazza az Azure Mobile Services Xamarin.Androidhoz készült összetevőjét. Ez a projekt az Android 4.2-es és újabb verzióit célozza, de a Mobile Services SDK-hoz csak az Android 2.2-es és újabb verziói szükségesek.

> [AZURE.IMPORTANT] Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. Ha nincs fiókja, regisztráljon az Azure-próbaverzióra, és a hozzá kapott akár 10 ingyenes mobilszolgáltatást a próbaidőszak után is használhatja. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A9C9624B5).

## <a name="create-new-service"> </a>Új mobilszolgáltatás létrehozása

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## Új Xamarin.Android-alkalmazás létrehozása

Miután létrehozta a mobilszolgáltatást, a klasszikus Azure portálon található egyszerű gyors üzembe helyezés szolgáltatással létrehozhat egy új alkalmazást, vagy módosíthat egy meglévő alkalmazást a mobilszolgáltatáshoz való csatlakozáshoz.

Ebben a szakaszban egy, a mobilszolgáltatásához kapcsolódó új Xamarin.Android-alkalmazást hoz létre.

1.  A [klasszikus Azure portál] kattintson a **Mobile Services** elemre, majd az imént létrehozott mobilszolgáltatásra.

2. A gyors üzembe helyezés lap **Choose platform** (Platform kiválasztása) részében kattintson a **Xamarin.Android** elemre, és bontsa ki a **Create a new Xamarin.Android app** (Új Xamarin.Android-alkalmazás létrehozása) részt.

    ![][6]

    Ez megjeleníti a mobilszolgáltatáshoz kapcsolódó új Xamarin.Android-alkalmazás létrehozásának három egyszerű lépését.

    ![][7]

3. Kattintson a **Create TodoItem table** (TodoItem tábla létrehozása) elemre az alkalmazásadatok tárolására szolgáló tábla létrehozásához.

4. A **Download and run app** (Alkalmazás letöltése és futtatása) területen kattintson a **Download** (Letöltés) gombra.

    Ezzel letölti a projektet a mobilszolgáltatáshoz kapcsolódó _Teendőlista_ mintaalkalmazáshoz. Mentse el a tömörített projektfájlt a helyi számítógépen, és jegyezze fel a mentési helyét.

## Az Android-alkalmazás futtatása

Az oktatóanyag utolsó szakasza az új alkalmazás felépítéséből és futtatásából áll.

1. Keresse meg a helyet, ahová a tömörített projektfájlokat mentette, és tömörítse ki a fájlokat a számítógépre.

2. A Xamarin Studióban vagy a Visual Studióban kattintson a **File** (Fájl), majd az **Open** (Megnyitás) gombra, keresse meg a kitömörített mintafájlokat, majd válassza ki és nyissa meg a **XamarinTodoQuickStart.Android.sln** fájlt.

3. Nyomja le a **Run** (Futtatás) gombot a projekt felépítéséhez és az alkalmazás elindításához. A rendszer megkéri, hogy válasszon egy emulátort vagy egy csatlakoztatott USB-eszközt.

    > [AZURE.NOTE] A projekt az Android-emulátorban való futtatásához meg kell határoznia legalább egy Android virtuális eszközt (AVD). Ezeket az eszközöket az AVD Manager alkalmazással hozhatja létre és kezelheti.

4. Az alkalmazásban írjon be egy jelentéssel bíró szöveget, például _Az oktatóanyag befejezése_, majd kattintson az **Add** (Hozzáadás) gombra.

    ![][10]

    Ez egy POST kérést küld az Azure-ban futtatott új mobilszolgáltatásnak. A kérelem adatai beillesztésre kerülnek a TodoItem táblába. A mobilszolgáltatás visszaadja a táblában tárolt elemeket, amelyek egy listában jelennek meg.

    > [AZURE.NOTE] A mobilszolgáltatáshoz az adatok lekérdezése és beszúrása céljából hozzáférő kódot át is tekintheti a ToDoActivity.cs C# fájlban.

6. A [klasszikus Azure portál] visszatérve kattintson a **Data** (Adatok) fülre, majd a **TodoItems** táblára.

    ![][11]

    Így tallózással kiválaszthatja az alkalmazás által a táblába beszúrt adatokat.

    ![][12]

## <a name="next-steps"> </a>Következő lépések
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
[0]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-quickstart-completed-android.png
[2]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-create.png
[3]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-create-page1.png
[4]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-create-page2.png
[5]: ./media/partner-xamarin-mobile-services-android-get-started/obile-services-selection.png
[6]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-portal-quickstart-xamarin-android.png
[7]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-quickstart-steps-xamarin-android.png
[8]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-xamarin-project-android-xs.png
[9]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-xamarin-project-android-vs.png
[10]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-quickstart-startup-android.png
[11]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-data-tab.png
[12]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-data-browse.png
[13]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-services-diagram.png


<!-- URLs. -->
[Bevezetés az adatok használatába]: /develop/mobile/tutorials/get-started-with-data-xamarin-android
[Bevezetés az offline adatszinkronizálás használatába]: mobile-services-xamarin-android-get-started-offline-data.md
[Bevezetés a hitelesítés használatába]: /develop/mobile/tutorials/get-started-with-users-xamarin-android
[Bevezetés a leküldéses értesítések használatába]: /develop/mobile/tutorials/get-started-with-push-xamarin-android
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Azure]: http://azure.microsoft.com/
[klasszikus Azure portál]: https://manage.windowsazure.com/




<!--HONumber=Jun16_HO2--->


