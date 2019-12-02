---
title: Ismerkedés a Xamarin. Android-alkalmazásokkal
description: Ez az oktatóanyag az Azure-Mobile Apps Xamarin Android-fejlesztéshez való használatának megkezdéséhez nyújt útmutatást.
ms.assetid: 81649dd3-544f-40ff-b9b7-60c66d683e60
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/25/2019
ms.openlocfilehash: 1bac9ac03971765f1afc4f15ff3de6cc4b7d3883
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2019
ms.locfileid: "74668674"
---
# <a name="create-a-xamarinandroid-app"></a>Xamarin.Android-alkalmazás létrehozása
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

> [!NOTE]
> A Visual Studio App Center támogatja a végpontok közötti, valamint az integrált szolgáltatásközpont és a mobilalkalmazás közötti fejlesztést. A fejlesztők **buildelési**, **tesztelési** és **elosztási** szolgáltatásokkal állíthatják be a folyamatos integrációval és szolgáltatásnyújtással kapcsolatos folyamatot. Az alkalmazás üzembe helyezése után a fejlesztők **elemzési** és **diagnosztikai** szolgáltatásokkal monitorozhatják az alkalmazás állapotát és használatát, illetve **leküldéses** szolgáltatással kommunikálhatnak a felhasználókkal. Emellett a fejlesztők a **Hitelesítés** szolgáltatással hitelesíthetik felhasználóikat, az **Adatok** szolgáltatással pedig megőrizhetik és szinkronizálhatják az alkalmazásadatokat a felhőben.
>
> Ha szeretné a felhőszolgáltatásokat a mobilalkalmazásba integrálni, regisztráljon az [App Centerbe](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) még ma.

## <a name="overview"></a>Áttekintés
Ez az oktatóanyag azt ismerteti, hogyan adhat felhőalapú háttérszolgáltatásokat Xamarin.Android-alkalmazásokhoz. További információ: [Mi a Mobile Apps szolgáltatás?](app-service-mobile-value-prop.md).

Alább az elkészült alkalmazás képernyőképe látható:

![][0]

Az oktatóanyag végrehajtása feltétele a Mobile Apps Xamarin.Android-alkalmazásokra vonatkozó összes többi oktatóanyag elérésének.

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag teljesítéséhez a következő előfeltételekre lesz szüksége:

* Aktív Azure-fiók. Ha nem rendelkezik fiókkal, regisztráljon az Azure próba-előfizetésére, amellyel akár 10 ingyenes Mobile Apps-alkalmazáshoz is hozzájuthat. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* Visual Studio és Xamarin. Az útmutatót lásd: [Setup and install for Visual Studio and Xamarin](/visualstudio/cross-platform/setup-and-install) (A Visual Studio és a Xamarin beállítása és telepítése).

## <a name="create-an-azure-mobile-app-backend"></a>Azure Mobile Apps-háttéralkalmazás létrehozása
Mobile Apps-háttéralkalmazás létrehozásához tegye a következőket.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

Már kiépített egy Azure Mobile Apps-háttérszolgáltatást, amelyet mobil ügyfélalkalmazásai használni tudnak. A következő lépésben le kell töltenie egy kiszolgálóprojektet egy egyszerű „Teendőlista” háttéralkalmazáshoz, és közzé kell tennie az Azure-ban.

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Adatbázis-kapcsolatok létrehozása és az ügyfél és a kiszolgáló projekt konfigurálása
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-xamarinandroid-app"></a>A Xamarin. Android-alkalmazás futtatása
1. Nyissa meg a Xamarin. Android projektet.

2. Lépjen a [Azure Portalra](https://portal.azure.com/) , és navigáljon a létrehozott mobil alkalmazáshoz. A `Overview` panelen keresse meg azt az URL-címet, amely a Mobile App nyilvános végpontja. Példa – az sitename "test123" neve https://test123.azurewebsites.net lesz.

3. Nyissa meg a fájlt `ToDoActivity.cs` ebben a mappában – xamarin. Android/ZUMOAPPNAME/ToDoActivity. cs. Az alkalmazás neve `ZUMOAPPNAME`.

4. `ToDoActivity` osztályban cserélje le az `ZUMOAPPURL` változót a fenti nyilvános végpontra.

    `const string applicationURL = @"ZUMOAPPURL";`

    válik
    
    `const string applicationURL = @"https://test123.azurewebsites.net";`
    
5. Az alkalmazás üzembe helyezéséhez és futtatásához nyomja le az F5 billentyűt.

6. Az alkalmazásban írjon be egy értelmes szöveget, például *Az oktatóanyag befejezése*, majd kattintson a **Hozzáadás** gombra.

    ![][10]

    A kérelem adatai beillesztésre kerülnek a TodoItem táblába. A táblázatban tárolt elemeket a mobil-háttéralkalmazás visszaküldi, és az adatok megjelennek a listában.

   > [!NOTE]
   > A mobil-háttéralkalmazás számára az adatok lekérdezéséhez és beszúrásához hozzáférést biztosító kódot a ToDoActivity.cs C# fájlban tekintheti meg.
   
## <a name="troubleshooting"></a>Hibakeresés
Ha a megoldás elkészítése során problémákat tapasztal, futtassa a NuGet csomagkezelőt, és frissítse a `Xamarin.Android` támogatási csomagokat. Előfordulhat, hogy a gyorsindítási projektek nem tartalmazzák a legújabb verziót.

Vegye figyelembe, hogy a projektjében hivatkozott összes támogatási csomagnak azonos verziójúnak kell lennie. Az [Azure Mobile Apps NuGet csomag](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) Android platform esetén `Xamarin.Android.Support.CustomTabs`-függőséggel rendelkezik, így ha a projektjében újabb támogatási csomagokat használ, akkor az ütközések elkerülése érdekében közvetlenül kell telepítenie ennek a csomagnak a szükséges verzióját.

<!-- Images. -->
[0]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-completed-android.png
[10]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-startup-android.png
<!-- URLs. -->
[Visual Studio]: https://go.microsoft.com/fwLink/p/?LinkID=534203
