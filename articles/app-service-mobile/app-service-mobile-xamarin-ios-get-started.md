---
title: "Bevezetés az Azure App Service Mobile Apps szolgáltatásnak a Xamarin.iOS-alkalmazásokkal való használatába| Microsoft Docs"
description: "Ezt az oktatóanyagot követve megismerkedhet azokkal a kezdeti lépésekkel, amelyekkel Xamarin.iOS-alapú fejlesztésre használhatja a Mobile Apps szolgáltatást."
services: app-service\mobile
documentationcenter: xamarin
author: dhei
manager: adrianha
editor: 
ms.assetid: 14428794-52ad-4b51-956c-deb296cafa34
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 10/01/2016
ms.author: adrianha
ms.translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: ed289d0755bbad08de01b0f311d14f5514ce0631
ms.contentlocale: hu-hu
ms.lasthandoff: 02/16/2017

---
# <a name="create-a-xamarinios-app"></a>Xamarin.iOS-alkalmazás létrehozása
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Áttekintés
Ez az cikk azt ismerteti, hogyan adhat felhőalapú háttérszolgáltatást a Xamarin.iOS-mobilalkalmazásokhoz egy Azure-alapú mobil-háttéralkalmazás segítségével.  Létre fog hozni egy új mobil-háttéralkalmazást, illetve egy olyan egyszerű *Teendőlista* Xamarin.iOS-alkalmazást, amely az alkalmazásadatokat az Azure-ban tárolja.

Az oktatóanyag végrehajtása feltétele az Azure App Service Mobile Apps szolgáltatásának használatát ismertető többi Xamarin.iOS-oktatóanyag elérésének.

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag teljesítéséhez a következő előfeltételekre lesz szüksége:

* Aktív Azure-fiók. Ha nincs fiókja, regisztráljon az Azure próba-előfizetésére, és akár 10 ingyenes mobilalkalmazáshoz is hozzájuthat, amelyeket a próba-előfizetés lejárta után is tovább használhat. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* Visual Studio és Xamarin. Az útmutatót lásd: [Setup and install for Visual Studio and Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) (A Visual Studio és a Xamarin beállítása és telepítése).
* Mac számítógép&7;.0 vagy-s újabb verziójú Xcode-dal és Xamarin Studio Communityvel. Lásd: [Setup and install for Visual Studio and Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) (A Visual Studio és a Xamarin beállítása és telepítése) és [Setup, install, and verifications for Mac users](https://msdn.microsoft.com/library/mt488770.aspx) (Beállítás, telepítés és ellenőrzés Macintosh-felhasználók számára) (MSDN).

> [!NOTE]
> Ha nem szeretne regisztrálni Azure-fiókot az Azure App Service megismerése előtt, menjen [Az App Service kipróbálása](https://azure.microsoft.com/try/app-service/mobile/) oldalra. Itt azonnal létrehozhat egy ideiglenes, induló mobilalkalmazást az App Service szolgáltatásban – kötelezettségek és a bankkártyaadatok megadása nélkül.
>
>

## <a name="create-an-azure-mobile-app-backend"></a>Azure Mobile Apps-háttéralkalmazás létrehozása
Mobile Apps-háttéralkalmazás létrehozásához tegye a következőket.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="configure-the-server-project"></a>Kiszolgálóprojekt konfigurálása
Már kiépített egy Azure Mobile Apps-háttérszolgáltatást, amelyet mobil ügyfélalkalmazásai használni tudnak. A következő lépésben le kell töltenie egy kiszolgálóprojektet egy egyszerű „Teendőlista” háttéralkalmazáshoz, és közzé kell tennie az Azure-ban.

Konfigurálja a kiszolgálóprojektet a Node.js vagy a .NET-háttéralkalmazás használatára az alábbi lépésekkel.

[!INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-xamarinios-app"></a>A Xamarin.iOS-alkalmazás letöltése és futtatása
1. Nyissa meg az [Azure Portal] egy böngészőablakban.
2. A Mobile Apps beállítási paneljén kattintson az **Első lépések** > **Xamarin.iOS** elemre. A 3. lépésben kattintson az **Új alkalmazás létrehozása** lehetőségre, ha még nincs kiválasztva.  Ezután kattintson a **Letöltés** gombra.

      A program ekkor letölt egy, a mobilháttérmodulhoz csatlakozó ügyfélalkalmazást. Mentse el a tömörített projektfájlt a helyi számítógépen, és jegyezze fel a mentési helyét.
3. Bontsa ki a letöltött projektet, és nyissa meg a Xamarin Studio (vagy a Visual Studio) alkalmazást.

    ![][9]

    ![][8]
4. Készítse el a projekt buildjét az F5 billentyűt lenyomásával, és indítsa el az alkalmazást az iPhone-emulátoron.
5. Az alkalmazásban írjon be egy értelmes szöveget, például *Xamarin-tanulás*, majd kattintson a **+** gombra.

    ![][10]

    A kérelem adatai beillesztésre kerülnek a TodoItem táblába. A táblában tárolt elemeket a mobil-háttéralkalmazás visszaküldi, és az adatok megjelennek a listában.

> [!NOTE]
> A mobil-háttéralkalmazás számára az adatok lekérdezéséhez és beszúrásához hozzáférést biztosító kódot a QSTodoService.cs C# fájlban tekintheti meg.
>
>

## <a name="next-steps"></a>Következő lépések
* [Offline szinkronizálás hozzáadása az alkalmazáshoz](app-service-mobile-xamarin-ios-get-started-offline-data.md)
* [Hitelesítés hozzáadása az alkalmazáshoz ](app-service-mobile-xamarin-ios-get-started-users.md)
* [Leküldéses értesítések hozzáadása Xamarin.Android-alkalmazáshoz](app-service-mobile-xamarin-ios-get-started-push.md)
* [A felügyelt ügyfelek használata az Azure Mobile Apps-alkalmazásokhoz](app-service-mobile-dotnet-how-to-use-client-library.md)

<!-- Anchors. -->
[Getting started with mobile app backends]:#getting-started
[Create a new mobile app backend]:#create-new-service
[Next Steps]:#next-steps

<!-- Images. -->
[6]: ./media/app-service-mobile-xamarin-ios-get-started/xamarin-ios-quickstart.png
[8]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-xamarin-project-ios-vs.png
[9]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-xamarin-project-ios-xs.png
[10]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-quickstart-startup-ios.png

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com/

