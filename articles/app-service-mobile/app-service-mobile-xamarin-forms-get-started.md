---
title: "A Mobile Apps használatának első lépései a Xamarin.Forms eszközkészlettel"
description: "Ezt az oktatóanyagot követve elkezdheti a Mobile Apps szolgáltatást Xamarin.Forms-alapú fejlesztésre használni."
services: app-service\mobile
documentationcenter: xamarin
author: ggailey777
manager: syntaxc4
editor: 
ms.assetid: 5e692220-cc89-4548-96c8-35259722acf5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 10/01/2016
ms.author: glenga
ms.translationtype: HT
ms.sourcegitcommit: 5b6c261c3439e33f4d16750e73618c72db4bcd7d
ms.openlocfilehash: ee12caaad4095cff6dae3282f747ae804f93db81
ms.contentlocale: hu-hu
ms.lasthandoff: 08/28/2017

---
# <a name="create-a-xamarinforms-app"></a>Xamarin.Forms-alkalmazás létrehozása
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Áttekintés
Ez az oktatóanyag azt ismerteti, hogyan adhat felhőalapú háttérszolgáltatásokat a Xamarin.Forms-mobilalkalmazásokhoz az Azure App Service Mobile Apps szolgáltatását használva háttérrendszerként. Létrehoz egy új Mobile Apps-háttéralkalmazást, illetve egy olyan egyszerű Xamarin.Forms-alapú teendőlista alkalmazást, amely az alkalmazásadatait az Azure-ban tárolja.

Az oktatóanyag végrehajtása feltétele a Mobile Apps Xamarin.Forms-alkalmazásokra vonatkozó összes többi oktatóanyagának elérésének.

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Aktív Azure-fiók. Ha nincs fiókja, regisztráljon az Azure próba-előfizetésére, és akár 10 ingyenes mobilalkalmazáshoz is hozzájuthat, amelyeket a próba-előfizetés lejárta után is tovább használhat. További információk: [Ingyenes Azure-próbaverzió](https://azure.microsoft.com/pricing/free-trial/).

* Visual Studio és Xamarin. További információk: [A Visual Studio és a Xamarin beállítása és telepítése](https://msdn.microsoft.com/library/mt613162.aspx) oldal.

* Mac számítógép 7.0 vagy-s újabb verziójú Xcode-dal és Xamarin Studio Communityvel. További információk: [A Visual Studio és a Xamarin beállítása és telepítése[ és ](https://msdn.microsoft.com/library/mt488770.aspx)Beállítás, telepítés és ellenőrzés Mac felhasználók részére](https://msdn.microsoft.com/library/mt613162.aspx) (MSDN).

## <a name="create-a-new-mobile-apps-back-end"></a>Új Mobile Apps-háttéralkalmazás létrehozása

Új Mobile Apps-háttéralkalmazást a következő módon hozhat létre:

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

Ezennel beállított egy Mobile Apps-háttéralkalmazást, amelyet a mobilos ügyfélalkalmazásai használhatnak. A következő lépésben le kell töltenie egy kiszolgálóprojektet egy egyszerű teendőlista-háttéralkalmazáshoz, és közzé kell tennie az Azure-ban.

## <a name="configure-the-server-project"></a>Kiszolgálóprojekt konfigurálása

Konfigurálja a kiszolgálóprojektet a Node.js vagy a .NET-háttéralkalmazás használatára az alábbi lépésekkel:

[!INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-xamarinforms-solution"></a>A Xamarin.Forms-megoldás letöltése és futtatása

A megoldás kétféle módon tölthető le. Letöltheti egy Mac-számítógépre, és megnyithatja a Xamarin Studióban, vagy letöltheti egy Windows-számítógépre, és megnyithatja a Visual Studióban egy hálózati Mac-számítógép segítségével az iOS-alkalmazás buildjének elkészítéséhez. További információk: [A Visual Studio és a Xamarin beállítása és telepítése](https://msdn.microsoft.com/library/mt613162.aspx).

Tegye a következőket egy Mac- vagy Windows-számítógépen:

1. Nyissa meg az [Azure Portal].

2. A mobilalkalmazás **Beállítások** paneljén a **Mobil** területen válassza az **Első lépések** > **Xamarin.iOS** elemet. A **3. lépésben** válassza az **Új alkalmazás létrehozása**, majd a **Letöltés** lehetőséget.

   Ez a művelet letölt egy projektet, amely tartalmazza a mobilalkalmazáshoz kapcsolódó ügyfélalkalmazást. Mentse el a tömörített projektfájlt a helyi számítógépen, és jegyezze fel a mentési helyét.

3. Bontsa ki a letöltött projektet, és nyissa meg a Xamarin Studio (Mac) vagy a Visual Studio (Windows) alkalmazásban.

   ![Kibontott projekt a Xamarin Studióban][9]

   ![Kibontott projekt a Visual Studióban][8]

## <a name="optional-run-the-ios-project"></a>Az iOS-projekt futtatása (nem kötelező)
Ebben a szakaszban az iOS-eszközökhöz készült Xamarin iOS-projektet fogja futtatni. Kihagyhatja ezt a részt, ha nem dolgozik iOS-eszközökkel.

#### <a name="in-xamarin-studio"></a>Xamarin Studióban
1. Kattintson a jobb gombbal az iOS-projektre, és válassza a **Set As Startup Project** (Beállítás kezdőprojektként) lehetőséget.

2. A **Run** (Futtatás) menüben válassza a **Start Debugging** (Hibakeresés indítása) lehetőséget a projekt buildjének elkészítéséhez, és indítsa el az alkalmazást az iPhone-emulátoron.

#### <a name="in-visual-studio"></a>Visual Studióban
1. Kattintson a jobb gombbal az iOS-projektre, és válassza a **Set As Startup Project** (Beállítás kezdőprojektként) lehetőséget.

2. A **Build** menüben válassza a **Configuration Manager** lehetőséget.

3. A **Configuration Manager** párbeszédpanelen jelölje be az iOS-projekt melletti **Build** és **Deploy** (Telepítés) jelölőnégyzetet.

4. Nyomja le az **F5** billentyűt a projekt buildjének elkészítéséhez és az alkalmazás elindításához az iPhone-emulátoron.

   > [!NOTE]
   > Ha a build elkészítése során problémákat tapasztal, futtassa a NuGet csomagkezelőt, és frissítsen a Xamarin támogatási csomagjainak legújabb verziójára. Előfordulhat, hogy a gyorsindítási projektek lassan frissülnek a legújabb verzióra.    
   >
   >

5. Az alkalmazásban írjon be egy értelmes szöveget, például *Xamarin-tanulás*, majd válassza a pluszjelet (**+**).

    ![][10]

    Ez a művelet egy közzétételi kérést küld az Azure szolgáltatásban üzemeltetett új Mobile Apps-háttéralkalmazás számára. A kérelem adatai beillesztésre kerülnek a TodoItem táblába. A Mobile Apps-háttéralkalmazás visszaadja a táblában tárolt elemeket, amelyek egy listában jelennek meg.

    > [!NOTE]
    > A Mobile Apps-háttéralkalmazáshoz hozzáférést biztosító kódot a megoldás hordozhatóosztálytár-projektjének TodoItemManager.cs C#-fájljában találja.
    >
    >

## <a name="optional-run-the-android-project"></a>Az Android-projekt futtatása (nem kötelező)
Ebben a szakaszban az Android-eszközökhöz készült Xamarin droid projektet fogja futtatni. Kihagyhatja ezt a részt, ha nem dolgozik Android-eszközökkel.

#### <a name="in-xamarin-studio"></a>Xamarin Studióban

1. Kattintson a jobb gombbal az Android-projektre, és válassza a **Set As Startup Project** (Beállítás kezdőprojektként) lehetőséget.

2. A **Run** (Futtatás) menüben kattintson a **Start Debugging** (Hibakeresés indítása) lehetőségre a projekt buildjének elkészítéséhez, és az alkalmazás elindításához egy Android-emulátoron.

#### <a name="in-visual-studio"></a>Visual Studióban

1. Kattintson a jobb gombbal az Android-projektre (Droid), és válassza a **Set As Startup Project** (Beállítás kezdőprojektként) lehetőséget.

2. A **Build** menüben válassza a **Configuration Manager** lehetőséget.

3. A **Configuration Manager** párbeszédpanelen jelölje be az Android-projekt melletti **Build** és **Deploy** (Telepítés) jelölőnégyzetet.

4. Nyomja le az **F5** billentyűt a projekt buildjének elkészítéséhez és az alkalmazás elindításához az Android-emulátoron.

   > [!NOTE]
   > Ha a build elkészítése során problémákat tapasztal, futtassa a NuGet csomagkezelőt, és frissítsen a Xamarin támogatási csomagjainak legújabb verziójára. Előfordulhat, hogy a gyorsindítási projektek lassan frissülnek a legújabb verzióra.    
   >
   >

5. Az alkalmazásban írjon be egy értelmes szöveget, például *Xamarin-tanulás*, majd válassza a pluszjelet (**+**).

    ![][11]
    
    Ez a művelet egy közzétételi kérést küld az Azure szolgáltatásban üzemeltetett új Mobile Apps-háttéralkalmazás számára. A kérelem adatai beillesztésre kerülnek a TodoItem táblába. A Mobile Apps-háttéralkalmazás visszaadja a táblában tárolt elemeket, amelyek egy listában jelennek meg.
    
    > [!NOTE]
    > A Mobile Apps-háttéralkalmazáshoz hozzáférést biztosító kódot a megoldás hordozhatóosztálytár-projektjének TodoItemManager.cs C#-fájljában találja.
    >
    >

## <a name="optional-run-the-windows-project"></a>A Windows-projekt futtatása (nem kötelező)

Ebben a szakaszban a Windows-eszközökhöz készült Xamarin WinApp projektet fogja futtatni. Kihagyhatja ezt a részt, ha nem dolgozik Windows-eszközökkel.

#### <a name="in-visual-studio"></a>Visual Studióban

1. Kattintson a jobb gombbal a Windows-projektek egyikére, és válassza a **Set as StartUp Project** (Beállítás kezdőprojektként) lehetőséget.

2. A **Build** menüben válassza a **Configuration Manager** lehetőséget.

3. A **Configuration Manager** párbeszédpanelen jelölje be a választott Windows-projekt melletti **Build** és **Deploy** (Telepítés) jelölőnégyzetet.

4. Nyomja le az **F5** billentyűt a projekt buildjének elkészítéséhez és az alkalmazás elindításához a Windows-emulátoron.

   > [!NOTE]
   > Ha a build elkészítése során problémákat tapasztal, futtassa a NuGet csomagkezelőt, és frissítsen a Xamarin támogatási csomagjainak legújabb verziójára. Előfordulhat, hogy a gyorsindítási projektek lassan frissülnek a legújabb verzióra.    
   >
   >

5. Az alkalmazásban írjon be egy értelmes szöveget, például *Xamarin-tanulás*, majd válassza a pluszjelet (**+**).

    Ez a művelet egy közzétételi kérést küld az Azure szolgáltatásban üzemeltetett új Mobile Apps-háttéralkalmazás számára. A kérelem adatai beillesztésre kerülnek a TodoItem táblába. A Mobile Apps-háttéralkalmazás visszaadja a táblában tárolt elemeket, amelyek egy listában jelennek meg.
    
    ![][12]
    
    > [!NOTE]
    > A Mobile Apps-háttéralkalmazáshoz hozzáférést biztosító kódot a megoldás hordozhatóosztálytár-projektjének TodoItemManager.cs C#-fájljában találja.
    >
    >

## <a name="next-steps"></a>Következő lépések

* [Hitelesítés hozzáadása az alkalmazáshoz](app-service-mobile-xamarin-forms-get-started-users.md)  
  Ismerje meg, hogyan hitelesítheti az alkalmazás felhasználóit egy identitásszolgáltatóval.

* [Leküldéses értesítések hozzáadása az alkalmazáshoz](app-service-mobile-xamarin-forms-get-started-push.md)  
  Ismerje meg, hogyan adhat leküldéses értesítéseket az alkalmazásához, illetve hogyan konfigurálhatja Mobile Apps-háttéralkalmazását az Azure Notification Hubs használatára a leküldéses értesítések küldéséhez.

* [Az offline szinkronizálás engedélyezése az alkalmazás számára](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  Ismerje meg, hogyan adhat offline támogatást alkalmazásához egy Mobile Apps-háttéralkalmazás segítségével. A kapcsolat nélküli szinkronizálás lehetővé teszi a mobilalkalmazás adatainak megtekintését, hozzáadását és módosítását akkor is, ha nincs hálózati kapcsolat.

* [A felügyelt ügyfél használata a Mobile Apps-alkalmazásokhoz](app-service-mobile-dotnet-how-to-use-client-library.md)  
  Ismerje meg, hogyan működnek a felügyelt ügyfél-SDK-k a Xamarin-alkalmazásokban.

<!-- Anchors. -->
[Get started with Mobile Apps back ends]:#getting-started
[Create a new Mobile Apps back end]:#create-new-service
[Next steps]:#next-steps


<!-- Images. -->
[6]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart.png
[8]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart-vs.png
[9]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart-xs.png
[10]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-android.png
[12]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-windows.png


<!-- URLs. -->
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Mobile app SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Azure Portal]: https://portal.azure.com/

