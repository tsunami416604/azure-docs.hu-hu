<properties
    pageTitle="Ismerkedés a Mobile Services Xamarin-alapú iOS-alkalmazásokkal való használatával | Microsoft Azure"
    description="Ez az oktatóanyag segítséget nyújt az Azure Mobile Services for Xamarin iOS-fejlesztések első lépéseiben."
    services="mobile-services"
    documentationCenter="xamarin"
    authors="lindydonna"
    manager="dwrede"
    editor="mollybos"/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="get-started-article"
    ms.date="03/16/2016"
    ms.author="donnam"/>

# <a name="getting-started"> </a>A Mobile Services használatának első lépései

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]
&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> A témakör megfelelő Mobile Apps-verziójáért lásd: [Create a Xamarin.iOS App](../app-service-mobile/app-service-mobile-xamarin-ios-get-started.md) (Xamarin.iOS-alkalmazás létrehozása).

Az oktatóanyag bemutatja, hogyan adhat felhőalapú háttérszolgáltatást Xamarin iOS-alkalmazásokhoz az Azure Mobile Services használatával. Az oktatóprogram során létrehoz egy új mobilszolgáltatást, valamint egy egyszerű _Teendőlista_ alkalmazást, amely az alkalmazásadatokat az új mobilszolgáltatásban tárolja. Az így létrehozott mobilszolgáltatás a támogatott .NET-nyelveket használó Visual Studio használatával kezeli a kiszolgálóoldali üzleti logikát és a mobilszolgáltatást. A témakör alábbi szakasza bemutatja egy olyan mobilszolgáltatások létrehozását, amely lehetővé teszi a kiszolgálóoldali üzleti logika írását a JavaScript használatával: [JavaScript háttérrendszer verzió].

>[AZURE.NOTE]Ez a témakör bemutatja, hogyan hozhat létre egy új mobilszolgáltatás-projektet a klasszikus Azure portál használatával. A Visual Studio 2013 Update 2 használatával azt is megteheti, hogy új mobilszolgáltatás-projektet ad egy meglévő Visual Studio-megoldáshoz. További információ: [Gyorsindítás: Mobilszolgáltatás hozzáadása (.NET-háttérrendszer)](http://msdn.microsoft.com/library/windows/apps/dn629482.aspx)

A kész alkalmazásról az alábbiakban látható egy képernyőkép:

![][0]


Ennek az oktatóanyagnak az elvégzése előfeltétel minden további, Xamarin iOS-alkalmazásokkal kapcsolatos Mobile Services-oktatóanyag elvégzéséhez.

>[AZURE.NOTE]Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. Ha nincs fiókja, regisztráljon az Azure-próbaverzióra, és a hozzá kapott akár 10 ingyenes mobilszolgáltatást a próbaidőszak után is használhatja. További információkért lásd: <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-xamarin-ios-get-started" target="_blank">Ingyenes Azure-fiók létrehozása</a>.<br />Az oktatóanyag elvégzéséhez <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a> eszközre van szükség. Ennek létezik egy ingyenesen elérhető próbaverziója.

## Új mobilszolgáltatás létrehozása

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../../includes/mobile-services-dotnet-backend-create-new-service.md)]

## Új Xamarin iOS-alkalmazás létrehozása

Miután létrehozta a mobilszolgáltatást, a klasszikus Azure portálon található egyszerű gyors üzembe helyezés szolgáltatással létrehozhat egy új alkalmazást, vagy módosíthat egy meglévő alkalmazást a mobilszolgáltatáshoz való csatlakozáshoz.

Ebben a szakaszban egy új Xamarin iOS-alkalmazást és egy, a mobilszolgáltatásához kapcsolódó szolgáltatásprojektet fog letölteni.

1. Ha még nem tette volna meg, telepítse a Visual Studio with Xamarin eszközt. Útmutatást itt talál: [Visual Studio és Xamarin beállítása és telepítése](https://msdn.microsoft.com/library/mt613162.aspx) A Xamarin Studio akár Mac OS X gépeken is használható, ezt az alábbi témakör ismerteti: [Beállítás, telepítés és ellenőrzés Mac gépeken](https://msdn.microsoft.com/library/mt488770.aspx)

2. A [Klasszikus Azure portál] kattintson a **Mobile Services** elemre, majd az imént létrehozott mobilszolgáltatásra.

3. A gyorsindítási lap **Platform kiválasztása** részében kattintson a **Xamarin** lehetőségre, és bontsa ki az **Új Xamarin-alkalmazás létrehozása** elemet.

    ![][6]

    Ez megjeleníti a mobilszolgáltatáshoz csatlakozó új Xamarin iOS-alkalmazás létrehozásának három egyszerű lépését.

    ![][7]

4. A **Szolgáltatás letöltése és közzététele a felhőben** menüben válassza az **iOS** elemet, majd kattintson a **Letöltés** gombra.

    Ezzel letölt egy megoldást, amely a mobilszolgáltatás és a mobilszolgáltatáshoz kapcsolt _Tennivalók listája_ mintaalkalmazás projektjeit is tartalmazza. Mentse el a tömörített projektfájlt a helyi számítógépen, és jegyezze fel a mentési helyét.

5. Töltse le a közzétett profilját, mentse el a letöltött fájlt a helyi számítógépen, és jegyezze fel a mentési helyét.

## Mobilszolgáltatás tesztelése

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service](../../includes/mobile-services-dotnet-backend-test-local-service.md)]

## Mobilszolgáltatás közzététele

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../../includes/mobile-services-dotnet-backend-publish-service.md)]

## Xamarin iOS-alkalmazás futtatása

Az oktatóanyag utolsó szakasza az új alkalmazás felépítéséből és futtatásából áll.

1. Lépjen a mobilszolgáltatási megoldásban (akár a Visual Studióban, akár a Xamarin Studióban) az ügyfélprojekthez.

    ![][8]

    ![][9]

2. Nyomja meg a **Futtatás** gombot az ügyfélprojekt felépítéséhez és az alkalmazás elindításához az iPhone-emulátorban.

3. Az alkalmazásban írjon be egy jelentéssel bíró szöveget, például _Az oktatóanyag befejezése_, majd kattintson a plusz (**+**) ikonra.

    ![][10]

    Ez egy POST kérést küld az Azure-ban futtatott új mobilszolgáltatásnak. A kérelem adatai beillesztésre kerülnek a TodoItem táblába. A mobilszolgáltatás visszaadja a táblában tárolt elemeket, amelyek egy listában jelennek meg.

>[AZURE.NOTE]A mobilszolgáltatást adatok lekérésére és beszúrására meghívó kódot meg is tekintheti a QSTodoService.cs C#-fájlban.


## Következő lépések
Most, hogy végzett a gyors üzembe helyezéssel, megtudhatja, hogyan hajthat végre további fontos feladatokat a Mobile Servicesben:

* [Bevezetés az offline adatszinkronizálás használatába]
  <br/>Megismerheti, hogyan teszi a gyorsindítás az offline adatszinkronizálás segítségével gyorsabban kezelhetővé és robusztusabbá alkalmazását.

* [Bevezetés a hitelesítés használatába]
  <br/>Ismerje meg, hogyan hitelesítheti az alkalmazás felhasználóit egy identitásszolgáltatóval.

* [Bevezetés a leküldéses értesítések használatába]
  <br/>Ismerje meg, hogyan küldhet nagyon egyszerű leküldéses értesítéseket az alkalmazásnak.

* [Mobile Services .NET-háttérrendszer hibaelhárítása]
  <br/> Ismerje meg, hogyan diagnosztizálhatja és javíthatja ki a Mobile Services .NET-háttérrendszerekkel kapcsolatban felmerülő hibákat.

[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Anchors. -->
[A Mobile Services használatának első lépései]:#getting-started
[Új mobilszolgáltatás létrehozása]:#create-new-service
[Következő lépések]:#next-steps



<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-quickstart-completed-ios.png
[6]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-portal-quickstart-xamarin-ios.png
[7]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-quickstart-steps-xamarin-ios.png
[8]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-xamarin-project-ios-vs.png
[9]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-xamarin-project-ios-xs.png
[10]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-quickstart-startup-ios.png

<!-- URLs. -->
[Bevezetés az offline adatszinkronizálás használatába]: mobile-services-xamarin-ios-get-started-offline-data.md
[Bevezetés a hitelesítés használatába]: mobile-services-dotnet-backend-xamarin-ios-get-started-users.md
[Bevezetés a leküldéses értesítések használatába]: mobile-services-dotnet-backend-xamarin-ios-get-started-push.md
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[JavaScript és HTML]: mobile-services-win8-javascript/
[Klasszikus Azure portál]: https://manage.windowsazure.com/
[JavaScript háttérrendszer verzió]: mobile-services-ios-get-started.md
[Mobile Services .NET-háttérrendszer hibaelhárítása]: mobile-services-dotnet-backend-how-to-troubleshoot.md


<!--HONumber=Jun16_HO2-->


