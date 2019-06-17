---
title: Bevezetés az Azure App Service Mobile Apps szolgáltatásnak a Xamarin.iOS-alkalmazásokkal való használatába| Microsoft Docs
description: Ezt az oktatóanyagot követve megismerkedhet azokkal a kezdeti lépésekkel, amelyekkel Xamarin.iOS-alapú fejlesztésre használhatja a Mobile Apps szolgáltatást.
services: app-service\mobile
documentationcenter: xamarin
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 14428794-52ad-4b51-956c-deb296cafa34
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: crdun
ms.openlocfilehash: 559050cbc575fce5bdb5b32ec266e1cc3d09b2d5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66242715"
---
# <a name="create-a-xamarinios-app"></a>Xamarin.iOS-alkalmazás létrehozása
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Áttekintés
Ez az cikk azt ismerteti, hogyan adhat felhőalapú háttérszolgáltatást a Xamarin.iOS-mobilalkalmazásokhoz egy Azure-alapú mobil-háttéralkalmazás segítségével.  Létre fog hozni egy új mobil-háttéralkalmazást, illetve egy olyan egyszerű *Teendőlista* Xamarin.iOS-alkalmazást, amely az alkalmazásadatokat az Azure-ban tárolja.

Az oktatóanyag végrehajtása feltétele az Azure App Service Mobile Apps szolgáltatásának használatát ismertető többi Xamarin.iOS-oktatóanyag elérésének.

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag teljesítéséhez a következő előfeltételekre lesz szüksége:

* Aktív Azure-fiók. Ha nincs fiókja, regisztráljon az Azure próba-előfizetésére, és akár 10 ingyenes mobilalkalmazáshoz is hozzájuthat, amelyeket a próba-előfizetés lejárta után is tovább használhat. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* Visual Studio for Mac. Lásd: [telepítő, és telepítse a Visual Studio for Mac](https://docs.microsoft.com/visualstudio/mac/installation?view=vsmac-2019)
* Egy Mac Xcode 9.0-s vagy újabb.
  
## <a name="create-an-azure-mobile-app-backend"></a>Azure Mobile Apps-háttéralkalmazás létrehozása
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Az ügyfél és kiszolgáló projekt egy adatbázis-kapcsolat létrehozása és konfigurálása
[!INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-xamarinios-app"></a>A Xamarin.iOS-alkalmazás futtatása
1. Nyissa meg a Xamarin.iOS-projekt.

2. Nyissa meg a [az Azure portal](https://portal.azure.com/) , és keresse meg a mobilalkalmazás, amelyet Ön hozott létre. Az a `Overview` panelen keresse meg az URL-címet, amely a nyilvános végpont számára. Például: a sitename for my app name "test123" lesz https://test123.azurewebsites.net.

3. Nyissa meg a fájlt `QSTodoService.cs` ebben a mappában - xamarin.iOS/ZUMOAPPNAME. Az alkalmazás neve `ZUMOAPPNAME`.

4. A `QSTodoService` osztály, cserélje le `ZUMOAPPURL` változó fenti nyilvános végponthoz.

    `const string applicationURL = @"ZUMOAPPURL";`

    válik
    
    `const string applicationURL = @"https://test123.azurewebsites.net";`
    
5. Nyomja le az F5 billentyűt, üzembe helyezését, és futtassa az alkalmazást az iPhone-emulátoron.

6. Az alkalmazásban írjon be egy értelmes szöveget, például *az oktatóanyag elvégzéséhez* és kattintson a + gombra.

    ![][10]

    A kérelem adatai beillesztésre kerülnek a TodoItem táblába. A táblázatban tárolt elemeket a mobil-háttéralkalmazás visszaküldi, és az adatok megjelennek a listában.

   > [!NOTE]
   > A mobil-háttéralkalmazás számára az adatok lekérdezéséhez és beszúrásához hozzáférést biztosító kódot a ToDoActivity.cs C# fájlban tekintheti meg.
   
<!-- Images. -->
[10]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-quickstart-startup-ios.png