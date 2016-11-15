---
title: "A Mobile Apps használatának első lépései a Xamarin.Forms eszközkészlettel"
description: "Ezt az oktatóanyagot követve megismerkedhet azokkal a kezdeti lépésekkel, amelyekkel Xamarin.Forms-alapú fejlesztésre használhatja a Mobile Apps szolgáltatást."
services: app-service\mobile
documentationcenter: xamarin
author: adrianhall
manager: erikre
editor: 
ms.assetid: 5e692220-cc89-4548-96c8-35259722acf5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 10/01/2016
ms.author: adrianha
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 6a48181c6c22e6042dd8ef39c12415979e384f8b


---
# <a name="create-a-xamarinforms-app"></a>Xamarin.Forms-alkalmazás létrehozása
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Áttekintés
Ez az oktatóanyag azt ismerteti, hogyan adhat felhőalapú háttérszolgáltatásokat a Xamarin.Forms-mobilalkalmazásokhoz egy Azure-alapú mobil-háttéralkalmazás segítségével. Létre fog hozni egy új mobil-háttéralkalmazást, illetve egy olyan egyszerű *Teendőlista* Xamarin.Forms-alkalmazást, amely az alkalmazásadatait az Azure-ban tárolja.

Az oktatóanyag végrehajtása feltétele a Mobile Apps Xamarin.Forms-alkalmazásokra vonatkozó összes többi oktatóanyagának elérésének.

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Aktív Azure-fiók. Ha nincs fiókja, regisztráljon az Azure próba-előfizetésére, és akár 10 ingyenes mobilalkalmazáshoz is hozzájuthat, amelyeket a próba-előfizetés lejárta után is tovább használhat. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* Visual Studio és Xamarin. Az útmutatót lásd: [Setup and install for Visual Studio and Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) (A Visual Studio és a Xamarin beállítása és telepítése). 
* Mac számítógép 7.0 vagy-s újabb verziójú Xcode-dal és Xamarin Studio Communityvel. Lásd: [Setup and install for Visual Studio and Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) (A Visual Studio és a Xamarin beállítása és telepítése) és [Setup, install, and verifications for Mac users](https://msdn.microsoft.com/library/mt488770.aspx) (Beállítás, telepítés és ellenőrzés Macintosh-felhasználók számára) (MSDN).

> [!NOTE]
> Ha az Azure App Service-t az Azure-fiók regisztrálása előtt szeretné kipróbálni, ugorjon [Az Azure App Service kipróbálása](https://tryappservice.azure.com/?appServiceName=mobile) oldalra. Itt azonnal létrehozhat egy ideiglenes, kezdő szintű mobilalkalmazást az App Service szolgáltatásban. Ehhez nincs szükség bankkártyára, és nem jár kötelezettségekkel.
> 
> 

## <a name="create-a-new-azure-mobile-app-backend"></a>Új Azure Mobile Apps-háttéralkalmazás létrehozása
Új Mobile Apps-háttéralkalmazás létrehozásához tegye a következőket.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

Már kiépített egy Azure Mobile Apps-háttérszolgáltatást, amelyet mobil ügyfélalkalmazásai használni tudnak. A következő lépésben le kell töltenie egy kiszolgálóprojektet egy egyszerű „Teendőlista” háttéralkalmazáshoz, és közzé kell tennie az Azure-ban.

## <a name="configure-the-server-project"></a>Kiszolgálóprojekt konfigurálása
Konfigurálja a kiszolgálóprojektet a Node.js vagy a .NET-háttéralkalmazás használatára az alábbi lépésekkel.

[!INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-xamarinforms-solution"></a>A Xamarin.Forms-megoldás letöltése és futtatása
Itt több lehetősége is van. Letöltheti a megoldást egy Mac-számítógépre, és megnyithatja a Xamarin Studióban, vagy letöltheti a megoldást egy Windows-számítógépre, és megnyithatja a Visual Studióban egy hálózati Mac-számítógép segítségével az iOS-alkalmazás buildjének elkészítéséhez. A Xamarin beállítási lehetőségeivel kapcsolatos bővebb információkat lásd a [Setup and install for Visual Studio and Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) (A Visual Studio és a Xamarin beállítása és telepítése) című cikkben.

Lássunk hozzá:

1. Macintosh- vagy Windows-számítógépén nyissa meg egy böngészőablakban az [Azure Portal].
2. A mobilalkalmazás beállítási paneljén kattintson az **Első lépések** (a Mobil alatt) > **Xamarin.Forms** elemre. A 3. lépésben kattintson az **Új alkalmazás létrehozása** lehetőségre, ha még nincs kiválasztva.  Ezután kattintson a **Letöltés** gombra.
   
   Ez letölti a projektet, amely tartalmazza a mobilalkalmazáshoz kapcsolódó ügyfélalkalmazást. Mentse el a tömörített projektfájlt a helyi számítógépen, és jegyezze fel a mentési helyét.
3. Bontsa ki a letöltött projektet, és nyissa meg a Xamarin Studio (vagy a Visual Studio) alkalmazást.
   
   ![][9]
   
   ![][8]

## <a name="optional-run-the-ios-project"></a>Az iOS-projekt futtatása (nem kötelező)
Ez a szakasz az iOS-eszközökhöz készült Xamarin iOS-projektek futtatásával foglalkozik. Kihagyhatja ezt a részt, ha nem dolgozik iOS-eszközökkel.

#### <a name="in-xamarin-studio"></a>Xamarin Studióban
1. Kattintson a jobb gombbal az iOS-projektre, és kattintson a **Set As Startup Project** (Beállítás kezdőprojektként) lehetőségre.
2. A **Run** (Futtatás) menüben kattintson a **Start Debugging** (Hibakeresés indítása) lehetőségre a projekt buildjének elkészítéséhez, és indítsa el az alkalmazást az iPhone-emulátoron.

#### <a name="in-visual-studio"></a>Visual Studióban
1. Kattintson a jobb gombbal az iOS-projektre, és kattintson a **Set as StartUp Project** (Beállítás kezdőprojektként) lehetőségre.
2. A **Build** menüben kattintson a **Configuration Manager** lehetőségre.
3. A **Configuration Manager** párbeszédpanelen jelölje be az iOS-projekthez tartozó **Build** és **Deploy** (Telepítés) jelölőnégyzetet.
4. Indítsa el a projekt buildjének elkészítését az **F5** billentyűt lenyomásával, és indítsa el az alkalmazást az iPhone-emulátoron.
   
   > [!NOTE]
   > Ha a build elkészítése során problémákat tapasztal, futtassa a NuGet csomagkezelőt, és frissítsen a Xamarin támogatási csomagjainak legújabb verziójára. Bizonyos esetekben előfordulhat, hogy a gyorssablonra épülő projektek nem a legfrissebb verziót használják.    
   > 
   > 

Az alkalmazásban írjon be egy értelmes szöveget, például *Xamarin-tanulás*, majd kattintson a **+** gombra.

![][10]

Ez egy POST kérést küld az Azure szolgáltatásban üzemeltetett új mobil-háttéralkalmazás számára. A kérelem adatai beillesztésre kerülnek a TodoItem táblába. A táblában tárolt elemeket a mobil-háttéralkalmazás visszaküldi, és az adatok megjelennek a listában.

> [!NOTE]
> A mobil-háttéralkalmazáshoz hozzáférést biztosító kódot a megoldás hordozhatóosztálytár-projektjének TodoItemManager.cs C# fájljában találja.
> 
> 

## <a name="optional-run-the-android-project"></a>Az Android-projekt futtatása (nem kötelező)
Ez a szakasz az Android rendszerre készült Xamarin Droid-projektek futtatásával foglalkozik. Kihagyhatja ezt a részt, ha nem dolgozik Android-eszközökkel.

#### <a name="in-xamarin-studio"></a>Xamarin Studióban
1. Kattintson a jobb gombbal az Android-projektre, és kattintson a **Set As Startup Project** (Beállítás kezdőprojektként) lehetőségre.
2. A **Run** (Futtatás) menüben kattintson a **Start Debugging** (Hibakeresés indítása) lehetőségre a projekt buildjének elkészítéséhez, és indítsa el az alkalmazást egy Android-emulátoron.

#### <a name="in-visual-studio"></a>Visual Studióban
1. Kattintson a jobb gombbal az Android- (Droid-) projektre, és kattintson a **Set as StartUp Project** (Beállítás kezdőprojektként) lehetőségre.
2. A **Build** menüben kattintson a **Configuration Manager** lehetőségre.
3. A **Configuration Manager** párbeszédpanelen jelölje be az Android-projekthez tartozó **Build** és **Telepítés** (Deploy) jelölőnégyzetet.
4. Indítsa el a projekt buildjének elkészítését az **F5** billentyű lenyomásával, és indítsa el az alkalmazást egy Android-emulátoron.
   
   > [!NOTE]
   > Ha a build elkészítése során problémákat tapasztal, futtassa a NuGet csomagkezelőt, és frissítsen a Xamarin támogatási csomagjainak legújabb verziójára. Bizonyos esetekben előfordulhat, hogy a gyorssablonra épülő projektek nem a legfrissebb verziót használják.    
   > 
   > 

Az alkalmazásban írjon be egy értelmes szöveget, például *Xamarin-tanulás*, majd kattintson a **+** gombra.

![][11]

Ez egy POST kérést küld az Azure szolgáltatásban üzemeltetett új mobil-háttéralkalmazás számára. A kérelem adatai beillesztésre kerülnek a TodoItem táblába. A táblában tárolt elemeket a mobil-háttéralkalmazás visszaküldi, és az adatok megjelennek a listában.

> [!NOTE]
> A mobil-háttéralkalmazáshoz hozzáférést biztosító kódot a megoldás hordozhatóosztálytár-projektjének TodoItemManager.cs C# fájljában találja.
> 
> 

## <a name="optional-run-the-windows-project"></a>A Windows-projekt futtatása (nem kötelező)
Ez a szakasz a Windows-eszközökhöz készült Xamarin WinApp-projektek futtatásával foglalkozik. Kihagyhatja ezt a részt, ha nem dolgozik Windows-eszközökkel.

#### <a name="in-visual-studio"></a>Visual Studióban
1. Kattintson a jobb gombbal a Windows-projektek egyikére, és kattintson a **Set as StartUp Project** (Beállítás kezdőprojektként) lehetőségre.
2. A **Build** menüben kattintson a **Configuration Manager** lehetőségre.
3. A **Configuration Manager** párbeszédpanelen jelölje be a választott Windows-projekthez tartozó **Build** és **Deploy** (Telepítés) jelölőnégyzetet.
4. Indítsa el a projekt buildjének elkészítését az **F5** billentyű lenyomásával, és indítsa el az alkalmazást egy Windows-emulátoron.
   
   > [!NOTE]
   > Ha a build elkészítése során problémákat tapasztal, futtassa a NuGet csomagkezelőt, és frissítsen a Xamarin támogatási csomagjainak legújabb verziójára. Bizonyos esetekben előfordulhat, hogy a gyorssablonra épülő projektek nem a legfrissebb verziót használják.    
   > 
   > 

Az alkalmazásban írjon be egy értelmes szöveget, például *Xamarin-tanulás*, majd kattintson a **+** gombra.

Ez egy POST kérést küld az Azure szolgáltatásban üzemeltetett új mobil-háttéralkalmazás számára. A kérelem adatai beillesztésre kerülnek a TodoItem táblába. A táblában tárolt elemeket a mobil-háttéralkalmazás visszaküldi, és az adatok megjelennek a listában.

![][12]

> [!NOTE]
> A mobil-háttéralkalmazáshoz hozzáférést biztosító kódot a megoldás hordozhatóosztálytár-projektjének TodoItemManager.cs C# fájljában találja.
> 
> 

## <a name="next-steps"></a>Következő lépések
* [Hitelesítés hozzáadása az alkalmazáshoz](app-service-mobile-xamarin-forms-get-started-users.md)  
  Ismerje meg, hogyan hitelesítheti az alkalmazás felhasználóit egy identitásszolgáltatóval.
* [Leküldéses értesítések hozzáadása az alkalmazáshoz](app-service-mobile-xamarin-forms-get-started-push.md)  
  Ismerje meg, hogyan adhat leküldéses értesítéseket az alkalmazásához, illetve hogyan konfigurálhatja Mobile Apps-háttéralkalmazását az Azure Notification Hubs használatára a leküldéses értesítések küldéséhez.
* [Az offline szinkronizálás engedélyezése az alkalmazás számára](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  Ismerje meg, hogyan adhat offline támogatást alkalmazásához egy Mobile Apps-háttéralkalmazás segítségével. A kapcsolat nélküli szinkronizálás a mobilalkalmazást használó végfelhasználók számára lehetővé teszi &mdash;az adatok megtekintését, hozzáadását és módosítását&mdash; akkor is, ha nincs hálózati kapcsolat.
* [A felügyelt ügyfelek használata az Azure Mobile Apps-alkalmazásokhoz](app-service-mobile-dotnet-how-to-use-client-library.md)  
  Ismerje meg, hogyan működnek a felügyelt ügyfél-SDK-k a Xamarin-alkalmazásokban. 

<!-- Anchors. -->
[Bevezetés a mobil-háttéralkalmazások használatába]:#getting-started
[Új mobil-háttéralkalmazás létrehozása]:#create-new-service
[Következő lépések]:#next-steps


<!-- Images. -->
[6]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart.png
[8]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart-vs.png
[9]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart-xs.png
[10]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-android.png
[12]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-windows.png


<!-- URLs. -->
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Mobilalkalmazás-SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Azure Portal]: https://portal.azure.com/




<!--HONumber=Nov16_HO2-->


