---
title: Bevezetés az Azure Mobile Apps szolgáltatásnak a Xamarin.Android-alkalmazásokkal való használatába
description: Ezt az oktatóanyagot követve megismerkedhet azokkal a kezdeti lépésekkel, amelyekkel Xamarin Android-alapú fejlesztésre használhatja a Mobile Apps szolgáltatást
services: app-service\mobile
documentationcenter: xamarin
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 81649dd3-544f-40ff-b9b7-60c66d683e60
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: crdun
ms.openlocfilehash: f3e8ca4f9736dffe4928fc8920b0890dff87367b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66236029"
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
* Visual Studio és Xamarin. Az útmutatót lásd: [Setup and install for Visual Studio and Xamarin](/visualstudio/cross-platform/setup-and-install) (A Visual Studio és a Xamarin beállítása és telepítése).

## <a name="create-an-azure-mobile-app-backend"></a>Azure Mobile Apps-háttéralkalmazás létrehozása
Mobile Apps-háttéralkalmazás létrehozásához tegye a következőket.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

Már kiépített egy Azure Mobile Apps-háttérszolgáltatást, amelyet mobil ügyfélalkalmazásai használni tudnak. A következő lépésben le kell töltenie egy kiszolgálóprojektet egy egyszerű „Teendőlista” háttéralkalmazáshoz, és közzé kell tennie az Azure-ban.

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Az ügyfél és kiszolgáló projekt egy adatbázis-kapcsolat létrehozása és konfigurálása
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-xamarinandroid-app"></a>A Xamarin.Android-alkalmazás futtatása
1. Nyissa meg a Xamarin.Android-projekt.

2. Nyissa meg a [az Azure portal](https://portal.azure.com/) , és keresse meg a mobilalkalmazás, amelyet Ön hozott létre. Az a `Overview` panelen keresse meg az URL-címet, amely a nyilvános végpont számára. Például: a sitename for my app name "test123" lesz https://test123.azurewebsites.net.

3. Nyissa meg a fájlt `ToDoActivity.cs` ebben a mappában - xamarin.android/ZUMOAPPNAME/ToDoActivity.cs. Az alkalmazás neve `ZUMOAPPNAME`.

4. A `ToDoActivity` osztály, cserélje le `ZUMOAPPURL` változó fenti nyilvános végponthoz.

    `const string applicationURL = @"ZUMOAPPURL";`

    válik
    
    `const string applicationURL = @"https://test123.azurewebsites.net";`
    
5. Nyomja le az F5 billentyűt, üzembe helyezését, és futtassa az alkalmazást.

6. Az alkalmazásban írjon be egy értelmes szöveget, például *Az oktatóanyag befejezése*, majd kattintson a **Hozzáadás** gombra.

    ![][10]

    A kérelem adatai beillesztésre kerülnek a TodoItem táblába. A táblázatban tárolt elemeket a mobil-háttéralkalmazás visszaküldi, és az adatok megjelennek a listában.

   > [!NOTE]
   > A mobil-háttéralkalmazás számára az adatok lekérdezéséhez és beszúrásához hozzáférést biztosító kódot a ToDoActivity.cs C# fájlban tekintheti meg.
   
## <a name="troubleshooting"></a>Hibaelhárítás
Ha a megoldás elkészítése során problémákat tapasztal, futtassa a NuGet csomagkezelőt, és frissítse a `Xamarin.Android` támogatási csomagokat. Előfordulhat, hogy a gyorsindítási projektek nem tartalmazzák a legújabb verziót.

Vegye figyelembe, hogy a projektjében hivatkozott összes támogatási csomagnak azonos verziójúnak kell lennie. Az [Azure Mobile Apps NuGet-csomag](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) Android platform esetén `Xamarin.Android.Support.CustomTabs`-függőséggel rendelkezik, így ha a projektjében újabb támogatási csomagokat használ, akkor az ütközések elkerülése érdekében közvetlenül kell telepítenie ennek a csomagnak a szükséges verzióját.

<!-- Images. -->
[0]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-completed-android.png
[10]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-startup-android.png
<!-- URLs. -->
[Visual Studio]: https://go.microsoft.com/fwLink/p/?LinkID=534203
