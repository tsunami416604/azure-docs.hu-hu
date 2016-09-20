<properties
    pageTitle="Bevezetés az Azure Mobile Apps szolgáltatásnak a Xamarin.Android-alkalmazásokkal való használatába"
    description="Ezt az oktatóanyagot követve megismerkedhet azokkal a kezdeti lépésekkel, amelyekkel Xamarin Android-alapú fejlesztésre használhatja a Mobile Apps szolgáltatást"
    services="app-service\mobile"
    documentationCenter="xamarin"
    authors="ggailey777"
    manager="erikre"
    editor="" />

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-android"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="08/11/2016"
    ms.author="glenga" />

#Xamarin.Android-alkalmazás létrehozása

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

##Áttekintés

Ez az oktatóanyag azt ismerteti, hogyan adhat felhőalapú háttérszolgáltatásokat Xamarin.Android-alkalmazásokhoz. További információ: [Mi a Mobile Apps szolgáltatás?](app-service-mobile-value-prop.md).

Alább az elkészült alkalmazás képernyőképe látható:

![][0]

Az oktatóanyag végrehajtása feltétele a Mobile Apps Xamarin.Android-alkalmazásokra vonatkozó összes többi oktatóanyag elérésének.

##Előfeltételek

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Aktív Azure-fiók. Ha nincs fiókja, regisztráljon az Azure próba-előfizetésére, és akár 10 ingyenes mobilalkalmazáshoz is hozzájuthat, amelyeket a próba-előfizetés lejárta után is tovább használhat. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

* Visual Studio és Xamarin. Az útmutatót lásd: [Setup and install for Visual Studio and Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) (A Visual Studio és a Xamarin beállítása és telepítése).  
 
>[AZURE.NOTE] Ha az Azure App Service-t az Azure-fiók regisztrálása előtt szeretné kipróbálni, ugorjon [Az Azure App Service kipróbálása](https://tryappservice.azure.com/?appServiceName=mobile) oldalra. Itt azonnal létrehozhat egy ideiglenes, kezdő szintű mobilalkalmazást az App Service szolgáltatásban. Ehhez nincs szükség bankkártyára, és nem jár kötelezettségekkel.

## Új Azure Mobile Apps-háttéralkalmazás létrehozása

Új Mobile Apps-háttéralkalmazás létrehozásához tegye a következőket.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

Már kiépített egy Azure Mobile Apps-háttérszolgáltatást, amelyet mobil ügyfélalkalmazásai használni tudnak. A következő lépésben le kell töltenie egy kiszolgálóprojektet egy egyszerű „Teendőlista” háttéralkalmazáshoz, és közzé kell tennie az Azure-ban.

## Kiszolgálóprojekt konfigurálása

[AZURE.INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## A Xamarin.Android-alkalmazás letöltése és futtatása

1. A **Download and run your Xamarin.Android project** (A Xamarin.Android-projekt letöltése és futtatása) területen kattintson a **Download** (Letöltés) gombra.

    Ez letölti a projektet, amely tartalmazza a mobilalkalmazáshoz kapcsolódó ügyfélalkalmazást. Mentse a tömörített projektfájlt a helyi számítógépre, és jegyezze fel a mentési helyét.

2. Indítsa el a projekt buildjének elkészítését az **F5** billentyű lenyomásával, és indítsa el az alkalmazást.

3. Az alkalmazásban írjon be egy értelmes szöveget, például _Az oktatóanyag befejezése_, majd kattintson a **Hozzáadás** gombra.

    ![][10]

    Ez egy POST kérést küld az Azure szolgáltatásban üzemeltetett új mobil-háttéralkalmazás számára. A rendszer beilleszti kérés adatait a TodoItem táblába. A táblázatban tárolt elemeket a mobil-háttéralkalmazás visszaküldi, és az adatok megjelennek a listában.

    > [AZURE.NOTE] A mobil-háttéralkalmazás számára az adatok lekérdezéséhez és beszúrásához hozzáférést biztosító kódot a ToDoActivity.cs C# fájlban tekintheti meg.

##Következő lépések

* [Hitelesítés hozzáadása az alkalmazáshoz ](app-service-mobile-xamarin-android-get-started-users.md)  
Ismerje meg, hogyan hitelesítheti alkalmazása felhasználóit identitásszolgáltató használatával.
* [Leküldéses értesítések hozzáadása Xamarin.Android-alkalmazáshoz](app-service-mobile-xamarin-android-get-started-push.md)  
Ismerje meg, hogyan adhat leküldéses értesítéseket az alkalmazáshoz.
* [A felügyelt ügyfelek használata az Azure Mobile Apps-alkalmazásokhoz](app-service-mobile-dotnet-how-to-use-client-library.md)  
Ismerje meg, hogyan működnek a felügyelt ügyfél-SDK-k a Xamarin-alkalmazásokban. 


<!-- Images. -->
[0]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-completed-android.png
[6]: ./media/app-service-mobile-xamarin-android-get-started/mobile-portal-quickstart-xamarin.png
[8]: ./media/app-service-mobile-xamarin-android-get-started/mobile-xamarin-project-android-vs.png
[9]: ./media/app-service-mobile-xamarin-android-get-started/mobile-xamarin-project-android-xs.png
[10]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-startup-android.png

<!-- URLs. -->
[Azure Portal]: https://azure.portal.com/
[Visual Studio]: https://go.microsoft.com/fwLink/p/?LinkID=534203



<!--HONumber=sep16_HO1-->


