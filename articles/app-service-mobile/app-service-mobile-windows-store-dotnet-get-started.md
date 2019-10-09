---
title: Azure Mobile Appst használó Univerzális Windows-platform (UWP) létrehozása | Microsoft Docs
description: Az útmutató bevezeti Önt az Azure-alapú, az Univerzális Windows-platformra (UWP) C#, Visual Basic vagy JavaScript nyelven történő alkalmazásfejlesztésre szolgáló mobil-háttéralkalmazások használatába.
services: app-service\mobile
documentationcenter: windows
author: elamalani
manager: crdun
editor: ''
ms.assetid: 47124296-2908-4d92-85e0-05c4aa6db916
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 6bb94828344b6f6e846b573a94e3049d03effe89
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72025187"
---
# <a name="create-a-windows-app-with-an-azure-backend"></a>Windows-alkalmazás létrehozása Azure-háttérrel

[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

> [!NOTE]
> Visual Studio App Center támogatja a teljes körű és integrált szolgáltatások központi használatát a Mobile apps fejlesztéséhez. A fejlesztők a szolgáltatások **kiépítését**, **tesztelését** és **terjesztését** használhatják a folyamatos integráció és a kézbesítési folyamat beállításához. Az alkalmazás üzembe helyezését követően a fejlesztők az **elemzési** és **diagnosztikai** szolgáltatások segítségével ellenőrizhetik az alkalmazás állapotát és használatát, és a **leküldéses** szolgáltatást használó felhasználókkal is elvégezhetik a felhasználókat. A fejlesztők **a hitelesítést a** felhasználók **és az adatszolgáltatások** hitelesítésére is használhatják a Felhőbeli alkalmazásadatok megőrzése és szinkronizálása érdekében.
> Ha szeretné integrálni a Cloud Servicest a mobil alkalmazásban, regisztráljon App Center [app Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) még ma.

## <a name="overview"></a>Áttekintés

Ez az oktatóanyag azt ismerteti, hogyan adhat felhőalapú háttérszolgáltatásokat Univerzális Windows Platform- (UWP-) alkalmazásokhoz. További információ: [Mi a Mobile Apps szolgáltatás?](app-service-mobile-value-prop.md). Az alábbiakban az elkészült alkalmazás képernyőképei láthatók:

![Az elkészült asztali alkalmazás](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-completed-desktop.png)

Az oktatóanyag végrehajtása feltétele a Mobile Apps UWP-alkalmazásokra vonatkozó összes többi oktatóanyag elérésének.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Aktív Azure-fiók. Ha nincs fiókja, regisztráljon az Azure próba-előfizetésére, és akár 10 ingyenes mobilalkalmazáshoz is hozzájuthat, amelyeket a próba-előfizetés lejárta után is tovább használhat. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* Windows 10.
* Visual Studio Community 2017.
* A UWP-alkalmazások fejlesztésének ismerete. A [UWP dokumentációjában](https://docs.microsoft.com/windows/uwp/) megtudhatja, hogyan [hozhat létre UWP-alkalmazásokat](https://docs.microsoft.com/windows/uwp/get-started/get-set-up) .

## <a name="create-a-new-azure-mobile-app-backend"></a>Új Azure Mobile Apps-háttéralkalmazás létrehozása

Új Mobile Apps-háttéralkalmazás létrehozásához tegye a következőket.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Adatbázis-kapcsolatok létrehozása és az ügyfél és a kiszolgáló projekt konfigurálása
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-client-project"></a>Az ügyfél projekt futtatása

1. Nyissa meg a UWP projektet.

2. Lépjen a [Azure Portalra](https://portal.azure.com/) , és navigáljon a létrehozott mobil alkalmazáshoz. A `Overview` panelen keresse meg azt az URL-címet, amely a Mobile App nyilvános végpontja. Példa – az sitename "test123" neve https://test123.azurewebsites.net lesz.

3. Nyissa meg `App.xaml.cs` fájlt ebben a mappában – Windows-uwp-cs/ZUMOAPPNAME/. Az alkalmazás neve `ZUMOAPPNAME`.

4. @No__t – 0 osztályban cserélje le az `ZUMOAPPURL` paramétert a fenti nyilvános végpontra.

    `public static MobileServiceClient MobileService = new MobileServiceClient("ZUMOAPPURL");`

    válik
    
    `public static MobileServiceClient MobileService = new MobileServiceClient("https://test123.azurewebsites.net");`

5. Az alkalmazás üzembe helyezéséhez és futtatásához nyomja le az F5 billentyűt.

6. Az alkalmazásban írjon be egy értelmes, például *Az oktatóanyag befejezése* szöveget az **Insert a TodoItem** (Teendő hozzáadása) szövegmezőbe, majd kattintson a **Save** (Mentés) gombra.

    ![Az elkészült, gyorssablonra épülő Windows-projekt asztali számítógépen](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-startup.png)

    Ez egy POST kérést küld az Azure szolgáltatásban üzemeltetett új mobil-háttéralkalmazás számára.
