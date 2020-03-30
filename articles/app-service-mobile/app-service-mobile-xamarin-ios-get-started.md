---
title: Első lépések a Xamarin.iOS alkalmazásokkal
description: Ezt az oktatóanyagot követve megismerkedhet azokkal a kezdeti lépésekkel, amelyekkel Xamarin.iOS-alapú fejlesztésre használhatja a Mobile Apps szolgáltatást.
ms.assetid: 14428794-52ad-4b51-956c-deb296cafa34
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/25/2019
ms.openlocfilehash: 6c35189e7c841fa2724f1cfe84afc689d5510676
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77459004"
---
# <a name="create-a-xamarinios-app"></a>Xamarin.iOS-alkalmazás létrehozása
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Áttekintés
Ez az cikk azt ismerteti, hogyan adhat felhőalapú háttérszolgáltatást a Xamarin.iOS-mobilalkalmazásokhoz egy Azure-alapú mobil-háttéralkalmazás segítségével.  Létre fog hozni egy új mobil-háttéralkalmazást, illetve egy olyan egyszerű *Teendőlista* Xamarin.iOS-alkalmazást, amely az alkalmazásadatokat az Azure-ban tárolja.

Az oktatóanyag végrehajtása feltétele az Azure App Service Mobile Apps szolgáltatásának használatát ismertető többi Xamarin.iOS-oktatóanyag elérésének.

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag teljesítéséhez a következő előfeltételekre lesz szüksége:

* Aktív Azure-fiók. Ha nincs fiókja, regisztráljon az Azure próba-előfizetésére, és akár 10 ingyenes mobilalkalmazáshoz is hozzájuthat, amelyeket a próba-előfizetés lejárta után is tovább használhat. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* Visual Studio for Mac. Lásd: [Telepítés és telepítés a Mac Visual Studio-hoz](https://docs.microsoft.com/visualstudio/mac/installation?view=vsmac-2019)
* Xcode 9.0-s vagy újabb Mac.
  
## <a name="create-an-azure-mobile-app-backend"></a>Azure Mobile Apps-háttéralkalmazás létrehozása
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Adatbázis-kapcsolat létrehozása és az ügyfél- és kiszolgálóprojekt konfigurálása
[!INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-xamarinios-app"></a>A Xamarin.iOS alkalmazás futtatása
1. Nyissa meg a Xamarin.iOS projektet.

2. Nyissa meg az [Azure Portalon,](https://portal.azure.com/) és keresse meg a létrehozott mobilalkalmazást. A `Overview` panelen keresse meg a mobilalkalmazás nyilvános végpontját képező URL-címet. Példa - a sitename az én app neve https://test123.azurewebsites.net"test123" lesz .

3. Nyissa meg `QSTodoService.cs` a fájlt ebben a mappában - xamarin.iOS/ZUMOAPPNAME. Az alkalmazás `ZUMOAPPNAME`neve .

4. Az `QSTodoService` osztályban `ZUMOAPPURL` cserélje le a változót a fenti nyilvános végpontra.

    `const string applicationURL = @"ZUMOAPPURL";`

    Lesz
    
    `const string applicationURL = @"https://test123.azurewebsites.net";`
    
5. Nyomja le az F5 billentyűt az alkalmazás iPhone-emulátorban történő telepítéséhez és futtatásához.

6. Az alkalmazásban írjon be értelmes szöveget, például *fejezze be az oktatóanyagot,* majd kattintson a + gombra.

    ![][10]

    A rendszer beilleszti kérés adatait a TodoItem táblába. A táblázatban tárolt elemeket a mobil-háttéralkalmazás visszaküldi, és az adatok megjelennek a listában.

   > [!NOTE]
   > A mobil-háttéralkalmazás számára az adatok lekérdezéséhez és beszúrásához hozzáférést biztosító kódot a ToDoActivity.cs C# fájlban tekintheti meg.
   
<!-- Images. -->
[10]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-quickstart-startup-ios.png
