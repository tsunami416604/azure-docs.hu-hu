---
title: Bevezetés az Azure App Service Mobile Apps szolgáltatásnak a Xamarin.iOS-alkalmazásokkal való használatába| Microsoft Docs
description: Ezt az oktatóanyagot követve megismerkedhet azokkal a kezdeti lépésekkel, amelyekkel Xamarin.iOS-alapú fejlesztésre használhatja a Mobile Apps szolgáltatást.
services: app-service\mobile
documentationcenter: xamarin
author: elamalani
manager: crdun
editor: ''
ms.assetid: 14428794-52ad-4b51-956c-deb296cafa34
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 26095757f8079f43e620e5f067f461f67c7042f9
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72024851"
---
# <a name="create-a-xamarinios-app"></a>Xamarin.iOS-alkalmazás létrehozása
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

> [!NOTE]
> Visual Studio App Center támogatja a teljes körű és integrált szolgáltatások központi használatát a Mobile apps fejlesztéséhez. A fejlesztők a szolgáltatások **kiépítését**, **tesztelését** és **terjesztését** használhatják a folyamatos integráció és a kézbesítési folyamat beállításához. Az alkalmazás üzembe helyezését követően a fejlesztők az **elemzési** és **diagnosztikai** szolgáltatások segítségével ellenőrizhetik az alkalmazás állapotát és használatát, és a **leküldéses** szolgáltatást használó felhasználókkal is elvégezhetik a felhasználókat. A fejlesztők **a hitelesítést a** felhasználók **és az adatszolgáltatások** hitelesítésére is használhatják a Felhőbeli alkalmazásadatok megőrzése és szinkronizálása érdekében.
> Ha szeretné integrálni a Cloud Servicest a mobil alkalmazásban, regisztráljon App Center [app Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) még ma.

## <a name="overview"></a>Áttekintés
Ez az cikk azt ismerteti, hogyan adhat felhőalapú háttérszolgáltatást a Xamarin.iOS-mobilalkalmazásokhoz egy Azure-alapú mobil-háttéralkalmazás segítségével.  Létre fog hozni egy új mobil-háttéralkalmazást, illetve egy olyan egyszerű *Teendőlista* Xamarin.iOS-alkalmazást, amely az alkalmazásadatokat az Azure-ban tárolja.

Az oktatóanyag végrehajtása feltétele az Azure App Service Mobile Apps szolgáltatásának használatát ismertető többi Xamarin.iOS-oktatóanyag elérésének.

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag teljesítéséhez a következő előfeltételekre lesz szüksége:

* Aktív Azure-fiók. Ha nincs fiókja, regisztráljon az Azure próba-előfizetésére, és akár 10 ingyenes mobilalkalmazáshoz is hozzájuthat, amelyeket a próba-előfizetés lejárta után is tovább használhat. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* Visual Studio for Mac. Lásd: [telepítés és telepítés a Visual Studio for Machoz](https://docs.microsoft.com/visualstudio/mac/installation?view=vsmac-2019)
* Mac Xcode 9,0 vagy újabb verzióval.
  
## <a name="create-an-azure-mobile-app-backend"></a>Azure Mobile Apps-háttéralkalmazás létrehozása
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Adatbázis-kapcsolatok létrehozása és az ügyfél és a kiszolgáló projekt konfigurálása
[!INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-xamarinios-app"></a>A Xamarin. iOS-alkalmazás futtatása
1. Nyissa meg a Xamarin. iOS projektet.

2. Lépjen a [Azure Portalra](https://portal.azure.com/) , és navigáljon a létrehozott mobil alkalmazáshoz. A `Overview` panelen keresse meg azt az URL-címet, amely a Mobile App nyilvános végpontja. Példa – az sitename "test123" neve https://test123.azurewebsites.net lesz.

3. Nyissa meg a `QSTodoService.cs` fájlt ebben a mappában – xamarin. iOS/ZUMOAPPNAME. Az alkalmazás neve `ZUMOAPPNAME`.

4. @No__t – 0 osztályban cserélje le a fenti nyilvános végponttal rendelkező `ZUMOAPPURL` változót.

    `const string applicationURL = @"ZUMOAPPURL";`

    válik
    
    `const string applicationURL = @"https://test123.azurewebsites.net";`
    
5. Nyomja le az F5 billentyűt az alkalmazás üzembe helyezéséhez és futtatásához egy iPhone-emulátorban.

6. Az alkalmazásban írjon be egy értelmes szöveget, például *fejezze be az oktatóanyagot* , majd kattintson a + gombra.

    ![][10]

    A kérelem adatai beillesztésre kerülnek a TodoItem táblába. A táblázatban tárolt elemeket a mobil-háttéralkalmazás visszaküldi, és az adatok megjelennek a listában.

   > [!NOTE]
   > A mobil-háttéralkalmazás számára az adatok lekérdezéséhez és beszúrásához hozzáférést biztosító kódot a ToDoActivity.cs C# fájlban tekintheti meg.
   
<!-- Images. -->
[10]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-quickstart-startup-ios.png