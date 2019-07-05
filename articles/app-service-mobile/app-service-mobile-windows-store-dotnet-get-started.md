---
title: Hozzon létre egy univerzális Windows Platform (UWP), amely használja az Azure Mobile Apps |} A Microsoft Docs
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
ms.openlocfilehash: 2b718a000bb5805cc615a59aebb2d5dcca6906a4
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67440215"
---
# <a name="create-a-windows-app-with-an-azure-backend"></a>-Windows-alkalmazás létrehozása az Azure-környezetben

[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

> [!NOTE]
> A Visual Studio App Center fektet a mobilalkalmazás-fejlesztés központi új, integrált szolgáltatások. A fejlesztők a **hozhat létre**, **teszt** és **terjesztése** állíthat be folyamatos integrációt és teljesítést folyamat szolgáltatások. Az alkalmazás telepítve van, a fejlesztők monitorozható az állapot és az alkalmazás használatával használatát a **Analytics** és **diagnosztikai** -szolgáltatásokat, és kapcsolatba léphet a felhasználókat a **leküldéses** a szolgáltatás. A fejlesztők is kihasználhatják a **Auth** azok a felhasználók hitelesítéséhez és **adatok** szolgáltatás és a felhőbeli alkalmazások adatainak szinkronizálása. Tekintse meg [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-windows-store-dotnet-get-started) még ma.
>

## <a name="overview"></a>Áttekintés

Ez az oktatóanyag azt ismerteti, hogyan adhat felhőalapú háttérszolgáltatásokat Univerzális Windows Platform- (UWP-) alkalmazásokhoz. További információ: [Mi a Mobile Apps szolgáltatás?](app-service-mobile-value-prop.md). Az alábbiakban az elkészült alkalmazás képernyőképei láthatók:

![Az elkészült asztali alkalmazás](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-completed-desktop.png)

Az oktatóanyag végrehajtása feltétele a Mobile Apps UWP-alkalmazásokra vonatkozó összes többi oktatóanyag elérésének.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Aktív Azure-fiók. Ha nincs fiókja, regisztráljon az Azure próba-előfizetésére, és akár 10 ingyenes mobilalkalmazáshoz is hozzájuthat, amelyeket a próba-előfizetés lejárta után is tovább használhat. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* Windows 10.
* Visual Studio Community 2017.
* UWP-alkalmazások fejlesztéséhez ismeretét. Látogasson el a [UWP dokumentációja](https://docs.microsoft.com/windows/uwp/) megtudhatja, hogyan [beállításához](https://docs.microsoft.com/windows/uwp/get-started/get-set-up) UWP-alkalmazásokat hozhat létre.

## <a name="create-a-new-azure-mobile-app-backend"></a>Új Azure Mobile Apps-háttéralkalmazás létrehozása

Új Mobile Apps-háttéralkalmazás létrehozásához tegye a következőket.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Az ügyfél és kiszolgáló projekt egy adatbázis-kapcsolat létrehozása és konfigurálása
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-client-project"></a>Az ügyfél-projekt futtatása

1. Nyissa meg a projekt uwp se Nepovedlo.

2. Nyissa meg a [az Azure portal](https://portal.azure.com/) , és keresse meg a mobilalkalmazás, amelyet Ön hozott létre. Az a `Overview` panelen keresse meg az URL-címet, amely a nyilvános végpont számára. Például: a sitename for my app name "test123" lesz https://test123.azurewebsites.net.

3. Nyissa meg a fájlt `App.xaml.cs` ebben a mappában – windows-uwp-cs/ZUMOAPPNAME /. Az alkalmazás neve `ZUMOAPPNAME`.

4. A `App` osztály, cserélje le `ZUMOAPPURL` nyilvános végpontot a fenti paraméterrel.

    `public static MobileServiceClient MobileService = new MobileServiceClient("ZUMOAPPURL");`

    válik
    
    `public static MobileServiceClient MobileService = new MobileServiceClient("https://test123.azurewebsites.net");`

5. Nyomja le az F5 billentyűt, üzembe helyezését, és futtassa az alkalmazást.

6. Az alkalmazásban írjon be egy értelmes, például *Az oktatóanyag befejezése* szöveget az **Insert a TodoItem** (Teendő hozzáadása) szövegmezőbe, majd kattintson a **Save** (Mentés) gombra.

    ![Az elkészült, gyorssablonra épülő Windows-projekt asztali számítógépen](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-startup.png)

    Ez egy POST kérést küld az Azure szolgáltatásban üzemeltetett új mobil-háttéralkalmazás számára.
