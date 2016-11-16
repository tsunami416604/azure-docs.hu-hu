---
title: "Bevezetés az Azure Mobile Apps szolgáltatásnak a Xamarin.Android-alkalmazásokkal való használatába"
description: "Ezt az oktatóanyagot követve megismerkedhet azokkal a kezdeti lépésekkel, amelyekkel Xamarin Android-alapú fejlesztésre használhatja a Mobile Apps szolgáltatást"
services: app-service\mobile
documentationcenter: xamarin
author: adrianhall
manager: erikre
editor: 
ms.assetid: 81649dd3-544f-40ff-b9b7-60c66d683e60
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 10/01/2016
ms.author: adrianha
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: e59a87ec6017061078d5cb5df93207c7f93ec00b


---
# <a name="create-a-xamarinandroid-app"></a>Xamarin.Android-alkalmazás létrehozása
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Áttekintés
Ez az oktatóanyag azt ismerteti, hogyan adhat felhőalapú háttérszolgáltatásokat Xamarin.Android-alkalmazásokhoz. További információ: [Mi a Mobile Apps szolgáltatás?](app-service-mobile-value-prop.md).

Alább az elkészült alkalmazás képernyőképe látható:

![][0]

Az oktatóanyag végrehajtása feltétele a Mobile Apps Xamarin.Android-alkalmazásokra vonatkozó összes többi oktatóanyag elérésének.

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag teljesítéséhez a következő előfeltételekre lesz szüksége:

* Aktív Azure-fiók. Ha nem rendelkezik fiókkal, regisztráljon az Azure próba-előfizetésére, amellyel akár 10 ingyenes Mobile Apps-alkalmazáshoz is hozzájuthat. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* Visual Studio és Xamarin. Az útmutatót lásd: [Setup and install for Visual Studio and Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) (A Visual Studio és a Xamarin beállítása és telepítése).

> [!NOTE]
> Ha nem szeretne regisztrálni Azure-fiókot az Azure App Service megismerése előtt, lépjen [Az App Service kipróbálása](https://tryappservice.azure.com/?appServiceName=mobile) oldalra.  Itt azonnal létrehozhat egy ideiglenes, induló mobilalkalmazást az App Service szolgáltatásban. Ehhez nincs szükség bankkártyára, és nem jár kötelezettségekkel.
> 
> 

## <a name="create-an-azure-mobile-app-backend"></a>Azure Mobile Apps-háttéralkalmazás létrehozása
Mobile Apps-háttéralkalmazás létrehozásához tegye a következőket.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

Már kiépített egy Azure Mobile Apps-háttérszolgáltatást, amelyet mobil ügyfélalkalmazásai használni tudnak. A következő lépésben le kell töltenie egy kiszolgálóprojektet egy egyszerű „Teendőlista” háttéralkalmazáshoz, és közzé kell tennie az Azure-ban.

## <a name="configure-the-server-project"></a>Kiszolgálóprojekt konfigurálása
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-xamarinandroid-app"></a>A Xamarin.Android-alkalmazás letöltése és futtatása
1. A **Download and run your Xamarin.Android project** (A Xamarin.Android-projekt letöltése és futtatása) területen kattintson a **Download** (Letöltés) gombra.
   
      Mentse el a tömörített projektfájlt a helyi számítógépen, és jegyezze fel a mentési helyét.
2. Indítsa el a projekt buildjének elkészítését az **F5** billentyű lenyomásával, és indítsa el az alkalmazást.
3. Az alkalmazásban írjon be egy értelmes szöveget, például *Az oktatóanyag befejezése*, majd kattintson a **Hozzáadás** gombra.
   
    ![][10]
   
    A kérelem adatai beillesztésre kerülnek a TodoItem táblába. A táblázatban tárolt elemeket a mobil-háttéralkalmazás visszaküldi, és az adatok megjelennek a listában.
   
   > [!NOTE]
   > A mobil-háttéralkalmazás számára az adatok lekérdezéséhez és beszúrásához hozzáférést biztosító kódot a ToDoActivity.cs C# fájlban tekintheti meg.
   > 
   > 

## <a name="next-steps"></a>Következő lépések
* [Offline szinkronizálás hozzáadása az alkalmazáshoz](app-service-mobile-xamarin-android-get-started-offline-data.md)
* [Hitelesítés hozzáadása az alkalmazáshoz ](app-service-mobile-xamarin-android-get-started-users.md)
* [Leküldéses értesítések hozzáadása Xamarin.Android-alkalmazáshoz](app-service-mobile-xamarin-android-get-started-push.md)
* [A felügyelt ügyfelek használata az Azure Mobile Apps-alkalmazásokhoz](app-service-mobile-dotnet-how-to-use-client-library.md)

<!-- Images. -->
[0]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-completed-android.png
[6]: ./media/app-service-mobile-xamarin-android-get-started/mobile-portal-quickstart-xamarin.png
[8]: ./media/app-service-mobile-xamarin-android-get-started/mobile-xamarin-project-android-vs.png
[9]: ./media/app-service-mobile-xamarin-android-get-started/mobile-xamarin-project-android-xs.png
[10]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-startup-android.png

<!-- URLs. -->
[Azure Portal]: https://azure.portal.com/
[Visual Studio]: https://go.microsoft.com/fwLink/p/?LinkID=534203



<!--HONumber=Nov16_HO2-->


