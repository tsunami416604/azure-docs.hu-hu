<properties
    pageTitle="Bevezetés az Azure App Service Mobile Apps szolgáltatásnak a Xamarin.iOS-alkalmazásokkal való használatába| Microsoft Azure"
    description="Ezt az oktatóanyagot követve megismerkedhet azokkal a kezdeti lépésekkel, amelyekkel Xamarin.iOS-alapú fejlesztésre használhatja a Mobile Apps szolgáltatást."
    services="app-service\mobile"
    documentationCenter="xamarin"
    authors="wesmc7777"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="na"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="02/13/2016"
    ms.author="normesta"/>


#Xamarin.iOS-alkalmazás létrehozása

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

##Áttekintés

Ez az cikk azt ismerteti, hogyan adhat felhőalapú háttérszolgáltatást a Xamarin.iOS-mobilalkalmazásokhoz egy Azure-alapú mobil-háttéralkalmazás segítségével.  Létre fog hozni egy új mobil-háttéralkalmazást, illetve egy olyan egyszerű _Teendőlista_ Xamarin.iOS-alkalmazást, amely az alkalmazásadatokat az Azure-ban tárolja.

Az oktatóanyag végrehajtása feltétele az Azure App Service Mobile Apps szolgáltatásának használatát ismertető többi Xamarin.iOS-oktatóanyag elérésének.

##Előfeltételek

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Aktív Azure-fiók. Ha nincs fiókja, regisztráljon az Azure próba-előfizetésére, és akár 10 ingyenes mobilalkalmazáshoz is hozzájuthat, amelyeket a próba-előfizetés lejárta után is tovább használhat. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

* Visual Studio és Xamarin. Az útmutatót lásd: [Setup and install for Visual Studio and Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) (A Visual Studio és a Xamarin beállítása és telepítése). 

* Mac számítógép 7.0 vagy-s újabb verziójú Xcode-dal és Xamarin Studio Communityvel. Lásd: [Setup and install for Visual Studio and Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) (A Visual Studio és a Xamarin beállítása és telepítése) és [Setup, install, and verifications for Mac users](https://msdn.microsoft.com/library/mt488770.aspx) (Beállítás, telepítés és ellenőrzés Macintosh-felhasználók számára) (MSDN).

>[AZURE.NOTE] Ha az Azure App Service-t az Azure-fiók regisztrálása előtt szeretné kipróbálni, ugorjon [Az Azure App Service kipróbálása](https://tryappservice.azure.com/?appServiceName=mobile) oldalra. Itt azonnal létrehozhat egy ideiglenes, induló mobilalkalmazást az App Service szolgáltatásban – kötelezettségek és a bankkártyaadatok megadása nélkül.

## Új Azure Mobile Apps-háttéralkalmazás létrehozása

Új Mobile Apps-háttéralkalmazás létrehozásához tegye a következőket.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## Kiszolgálóprojekt konfigurálása

Már kiépített egy Azure Mobile Apps-háttérszolgáltatást, amelyet mobil ügyfélalkalmazásai használni tudnak. A következő lépésben le kell töltenie egy kiszolgálóprojektet egy egyszerű „Teendőlista” háttéralkalmazáshoz, és közzé kell tennie az Azure-ban.

Konfigurálja a kiszolgálóprojektet a Node.js vagy a .NET-háttéralkalmazás használatára az alábbi lépésekkel.

[AZURE.INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]


## A háttérprojekt helyi tesztelése (nem kötelező)

Ha a fenti .NET-háttérkonfigurációs megoldást választja, a háttéralkalmazást helyileg is tesztelheti (nem kötelező).

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-test-local-service](../../includes/app-service-mobile-dotnet-backend-test-local-service.md)]



## A Xamarin.iOS-alkalmazás letöltése és futtatása

1. Nyissa meg az [Azure portál] egy böngészőablakban.

2. A Mobile Apps beállítási paneljén kattintson az **Első lépések** > **Xamarin.iOS** elemre. A 3. lépésben kattintson az **Új alkalmazás létrehozása** lehetőségre, ha még nincs kiválasztva.  Ezután kattintson a **Letöltés** gombra.

    Ez letölti a projektet, amely tartalmazza a mobilalkalmazáshoz kapcsolódó ügyfélalkalmazást. Mentse a tömörített projektfájlt a helyi számítógépre, és jegyezze fel a mentési helyét.

3. Bontsa ki a letöltött projektet, és nyissa meg a Xamarin Studio (vagy a Visual Studio) alkalmazást.

    ![][9]

    ![][8]

4. Készítse el a projekt buildjét az F5 billentyűt lenyomásával, és indítsa el az alkalmazást az iPhone-emulátoron.

5. Az alkalmazásban írjon be egy értelmes szöveget, például _Xamarin-tanulás_, majd kattintson a **+** gombra.

    ![][10]

    Ez egy POST kérést küld az Azure szolgáltatásban üzemeltetett új mobil-háttéralkalmazás számára. A rendszer beilleszti kérés adatait a TodoItem táblába. A táblában tárolt elemeket a mobil-háttéralkalmazás visszaküldi, és az adatok megjelennek a listában.

>[AZURE.NOTE]A mobil-háttéralkalmazás számára az adatok lekérdezéséhez és beszúrásához hozzáférést biztosító kódot a QSTodoService.cs C# fájlban tekintheti meg.

##Következő lépések

* [Hitelesítés hozzáadása az alkalmazáshoz ](app-service-mobile-xamarin-ios-get-started-users.md)
  <br/>Ismerje meg, hogyan hitelesítheti alkalmazása felhasználóit identitásszolgáltató használatával.

* [Leküldéses értesítések hozzáadása az alkalmazáshoz](app-service-mobile-xamarin-ios-get-started-push.md)
  <br/>Ismerje meg, hogyan küldhet nagyon egyszerű leküldéses értesítéseket az alkalmazásnak.

<!-- Anchors. -->
[Bevezetés a mobil-háttéralkalmazások használatába]:#getting-started
[Új mobil-háttéralkalmazás létrehozása]:#create-new-service
[Következő lépések]:#next-steps



<!-- Images. -->
[6]: ./media/app-service-mobile-xamarin-ios-get-started/xamarin-ios-quickstart.png
[8]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-xamarin-project-ios-vs.png
[9]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-xamarin-project-ios-xs.png
[10]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-quickstart-startup-ios.png

<!-- URLs. -->
[Azure portál]: https://portal.azure.com/



<!--HONumber=Jun16_HO2--->


