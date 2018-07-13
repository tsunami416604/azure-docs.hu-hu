---
title: A Mobile Apps használatának első lépései a Xamarin.Forms eszközkészlettel
description: Ezt az oktatóanyagot követve elkezdheti a Mobile Apps szolgáltatást Xamarin.Forms-alapú fejlesztésre használni.
services: app-service\mobile
documentationcenter: xamarin
author: conceptdev
manager: crdun
ms.assetid: 5e692220-cc89-4548-96c8-35259722acf5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 05/25/2018
ms.author: crdun
ms.openlocfilehash: b2dafbcf0e41e7387157590e145f74430686321c
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38307119"
---
# <a name="create-a-xamarinforms-app-with-azure"></a>Xamarin.Forms-alkalmazás létrehozása az Azure-ral

[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

Ez az oktatóanyag azt ismerteti, hogyan adhat felhőalapú háttérszolgáltatásokat a Xamarin.Forms-mobilalkalmazásokhoz az Azure App Service Mobile Apps szolgáltatását használva háttérrendszerként. Létrehoz egy új Mobile Apps-háttéralkalmazást, illetve egy olyan egyszerű Xamarin.Forms-alapú teendőlista alkalmazást, amely az alkalmazásadatait az Azure-ban tárolja.

Az oktatóanyag végrehajtása feltétele a Mobile Apps Xamarin.Forms-alkalmazásokra vonatkozó összes többi oktatóanyagának elérésének.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Aktív Azure-fiók. Ha nincs fiókja, regisztráljon az Azure próba-előfizetésére, és akár 10 ingyenes mobilalkalmazáshoz is hozzájuthat, amelyeket a próba-előfizetés lejárta után is tovább használhat. További információk: [Ingyenes Azure-próbaverzió](https://azure.microsoft.com/pricing/free-trial/).

* A Xamarinhoz készült Visual Studio Tools a Visual Studio 2017-ben vagy a Visual Studio for Mac-ben. Útmutatást a [Xamarin telepítési oldalán][Install Xamarin] talál.

* (választható) iOS-alkalmazás készítéséhez Xcode 9.0 vagy újabb verzióval rendelkező Mac szükséges. iOS-alkalmazások fejlesztéséhez a Visual Studio for Mac használható, vagy a Visual Studio 2017 (ha a Mac elérhető a hálózaton).

## <a name="create-a-new-mobile-apps-back-end"></a>Új Mobile Apps-háttéralkalmazás létrehozása

Új Mobile Apps-háttéralkalmazást a következő módon hozhat létre:

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

Ezzel beállított egy Mobile App-háttéralkalmazást, amelyet a mobilos alkalmazásai használhatnak. A következő lépésben le kell töltenie egy kiszolgálóprojektet egy egyszerű teendőlista-háttéralkalmazáshoz, és közzé kell tennie az Azure-ban.

## <a name="configure-the-server-project"></a>Kiszolgálóprojekt konfigurálása

Konfigurálja a kiszolgálóprojektet a Node.js vagy a .NET-háttéralkalmazás használatára az alábbi lépésekkel:

[!INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-xamarinforms-solution"></a>A Xamarin.Forms-megoldás letöltése és futtatása

A megoldás megnyitásához a Xamarinhoz készült Visual Studio Tools szükséges. Lásd a [Xamarin telepítési útmutatót][Install Xamarin]. Ha az eszközök már telepítve vannak, akkor a megoldást a következő lépesekben töltheti le és nyithatja meg:

### <a name="visual-studio"></a>Visual Studio

1. Nyissa meg az [Azure Portal].

2. A mobilalkalmazás beállítási paneljén kattintson az (Üzemelő példány alatt található) **Első lépések** > **Xamarin.Forms** elemre. A 3. lépésben kattintson az **Új alkalmazás létrehozása** lehetőségre, ha még nincs kiválasztva.  Ezután kattintson a **Letöltés** gombra.

   Ez a művelet letölt egy projektet, amely tartalmazza a mobilalkalmazáshoz kapcsolódó ügyfélalkalmazást. Mentse el a tömörített projektfájlt a helyi számítógépen, és jegyezze fel a mentési helyét.

3. Bontsa ki a letöltött projektet, és nyissa meg a Visual Studio 2017-ben.

   ![Kibontott projekt a Visual Studióban][8]

4. Az alábbi utasításokat végrehajtva futtathatja az Androidos vagy Windowsos projekteket, és ha rendelkezik a hálózaton elérhető Mac-számítógéppel, az iOS-projektet is.

### <a name="visual-studio-for-mac"></a>Visual Studio for Mac

1. Nyissa meg az [Azure Portal].

2. A mobilalkalmazás beállítási paneljén kattintson az (Üzemelő példány alatt található) **Első lépések** > **Xamarin.Forms** elemre. A 3. lépésben kattintson az **Új alkalmazás létrehozása** lehetőségre, ha még nincs kiválasztva.  Ezután kattintson a **Letöltés** gombra.

   Ez a művelet letölt egy projektet, amely tartalmazza a mobilalkalmazáshoz kapcsolódó ügyfélalkalmazást. Mentse el a tömörített projektfájlt a helyi számítógépen, és jegyezze fel a mentési helyét.

3. Bontsa ki a letöltött projektet, és nyissa meg a Visual Studio for Mac-ben.

   ![Kibontott projekt a Visual Studio for Mac-ben][9]

4. Az Androidos vagy iOS-es projektek futtatásához kövesse az alábbi utasításokat.



## <a name="optional-run-the-android-project"></a>Az Android-projekt futtatása (nem kötelező)

Ebben a szakaszban a Xamarin.Android-projektet fogja futtatni. Kihagyhatja ezt a részt, ha nem dolgozik Android-eszközökkel.

### <a name="visual-studio"></a>Visual Studio

1. Kattintson a jobb gombbal az Android-projektre (Droid), és válassza a **Set As Startup Project** (Beállítás kezdőprojektként) lehetőséget.

2. A **Build** menüben válassza a **Configuration Manager** lehetőséget.

3. A **Konfigurációkezelő** párbeszédpanelen jelölje be az Android-projekt melletti **Fordítás** és **Telepítés** jelölőnégyzeteket, és ellenőrizze, hogy a közös kódú projekt **Fordítás** jelölőnégyzete is be van jelölve.

4. A projekt fordításához és az alkalmazás Android-emulátoron való futtatásához nyomja le az **F5** billentyűt, vagy kattintson az **Indítás** gombra.

### <a name="visual-studio-for-mac"></a>Visual Studio for Mac

1. Kattintson a jobb gombbal az Android-projektre, és válassza a **Set As Startup Project** (Beállítás kezdőprojektként) lehetőséget.

2. A projekt fordításához és az alkalmazás Android-emulátoron való futtatásához válassza a **Run** (Futtatás) menüt, majd a **Start Debugging** (Hibakeresés indítása) lehetőséget.



Az alkalmazásban írjon be egy értelmes szöveget, például *Xamarin-tanulás*, majd válassza a pluszjelet (**+**).

![Androidos Teendők alkalmazás][11]

Ez a művelet egy közzétételi kérést küld az Azure szolgáltatásban üzemeltetett új Mobile Apps-háttéralkalmazás számára. A kérelem adatai beillesztésre kerülnek a TodoItem táblába. A Mobile Apps-háttéralkalmazás visszaadja a táblában tárolt elemeket, amelyek egy listában jelennek meg.

> [!NOTE]
> A Mobile Apps-háttéralkalmazáshoz hozzáférést biztosító kódot a megoldás közös kódú projektjének **TodoItemManager.cs** C#-fájljában találja.
>

## <a name="optional-run-the-ios-project"></a>Az iOS-projekt futtatása (nem kötelező)

Ebben a szakaszban az iOS-eszközökhöz készült Xamarin.iOS-projektet fogja futtatni. Kihagyhatja ezt a részt, ha nem dolgozik iOS-eszközökkel.

### <a name="visual-studio"></a>Visual Studio

1. Kattintson a jobb gombbal az iOS-projektre, és válassza a **Set As Startup Project** (Beállítás kezdőprojektként) lehetőséget.

2. A **Build** menüben válassza a **Configuration Manager** lehetőséget.

3. A **Konfigurációkezelő** párbeszédpanelen jelölje be az iOS-projekt melletti **Fordítás** és **Telepítés** jelölőnégyzeteket, és ellenőrizze, hogy a közös kódú projekt **Fordítás** jelölőnégyzete is be van jelölve.

4. Nyomja le az **F5** billentyűt a projekt buildjének elkészítéséhez és az alkalmazás elindításához az iPhone-emulátoron.

### <a name="visual-studio-for-mac"></a>Visual Studio for Mac

1. Kattintson a jobb gombbal az iOS-projektre, és válassza a **Set As Startup Project** (Beállítás kezdőprojektként) lehetőséget.

2. A **Run** (Futtatás) menüben válassza a **Start Debugging** (Hibakeresés indítása) lehetőséget a projekt buildjének elkészítéséhez, és indítsa el az alkalmazást az iPhone-emulátoron.



Az alkalmazásban írjon be egy értelmes szöveget, például *Xamarin-tanulás*, majd válassza a pluszjelet (**+**).

![iOS-es Teendők alkalmazás][10]

Ez a művelet egy közzétételi kérést küld az Azure szolgáltatásban üzemeltetett új Mobile Apps-háttéralkalmazás számára. A kérelem adatai beillesztésre kerülnek a TodoItem táblába. A Mobile Apps-háttéralkalmazás visszaadja a táblában tárolt elemeket, amelyek egy listában jelennek meg.

> [!NOTE]
> A Mobile Apps-háttéralkalmazáshoz hozzáférést biztosító kódot a megoldás közös kódú projektjének **TodoItemManager.cs** C#-fájljában találja.
>

## <a name="optional-run-the-windows-project"></a>A Windows-projekt futtatása (nem kötelező)

Ebben a szakaszban a Xamarin.Forms Universal Windows Platform (UWP) projektet fogja futtatni Windows-eszközön. Kihagyhatja ezt a részt, ha nem dolgozik Windows-eszközökkel.

### <a name="visual-studio"></a>Visual Studio

1. Kattintson a jobb gombbal az UWP-projektre, és válassza a **Beállítás kezdőprojektként** lehetőséget.

2. A **Build** menüben válassza a **Configuration Manager** lehetőséget.

3. A **Konfigurációkezelő** párbeszédpanelen jelölje be a választott Windows-projekt melletti **Fordítás** és **Telepítés** jelölőnégyzeteket, és ellenőrizze, hogy a közös kódú projekt **Fordítás** jelölőnégyzete is be van jelölve.

4. A projekt fordításához és az alkalmazás Windows-emulátoron való futtatásához nyomja le az **F5** billentyűt, vagy kattintson az **Indítás** gombra (amelyen a **Helyi számítógép** szövegnek kell megjelennie).

> [!NOTE]
> A Windows-projekt macOS-en nem futtatható.



Az alkalmazásban írjon be egy értelmes szöveget, például *Xamarin-tanulás*, majd válassza a pluszjelet (**+**).

Ez a művelet egy közzétételi kérést küld az Azure szolgáltatásban üzemeltetett új Mobile Apps-háttéralkalmazás számára. A kérelem adatai beillesztésre kerülnek a TodoItem táblába. A Mobile Apps-háttéralkalmazás visszaadja a táblában tárolt elemeket, amelyek egy listában jelennek meg.

![UWP Teendők alkalmazás][12]

> [!NOTE]
> A Mobile Apps-háttéralkalmazáshoz hozzáférést biztosító kódot a megoldás hordozhatóosztálytár-projektjének **TodoItemManager.cs** C#-fájljában találja.
>

## <a name="troubleshooting"></a>Hibaelhárítás

Ha a megoldás fordítása során problémákat tapasztal, futtassa a NuGet csomagkezelőt, és frissítsen a Xamarin.Forms legújabb verziójára, az Android-projektben pedig frissítse a Xamarin.Android támogatási csomagjait. Előfordulhat, hogy a gyorsindítási projektek nem tartalmazzák a legújabb verziót.

## <a name="next-steps"></a>További lépések

* [Hitelesítés hozzáadása alkalmazáshoz](app-service-mobile-xamarin-forms-get-started-users.md) Ismerje meg, hogyan hitelesítheti az alkalmazás felhasználóit egy identitásszolgáltatóval.

* [Leküldéses értesítések hozzáadása alkalmazáshoz](app-service-mobile-xamarin-forms-get-started-push.md) Ismerje meg, hogyan adhat leküldéses értesítéseket az alkalmazásához, illetve hogyan konfigurálhatja Mobile Apps-háttéralkalmazását az Azure Notification Hubs használatára a leküldéses értesítések küldéséhez.

* [Alkalmazás offline szinkronizálásának engedélyezése](app-service-mobile-xamarin-forms-get-started-offline-data.md) Ismerje meg, hogyan adhat offline támogatást alkalmazásához egy Mobile Apps-háttéralkalmazás segítségével. A kapcsolat nélküli szinkronizálás lehetővé teszi a mobilalkalmazás adatainak megtekintését, hozzáadását és módosítását akkor is, ha nincs hálózati kapcsolat.

* [A felügyelt ügyfél használata a Mobile Apps-alkalmazásokhoz](app-service-mobile-dotnet-how-to-use-client-library.md) Ismerje meg, hogyan működnek a felügyelt ügyfél-SDK-k a Xamarin-alkalmazásokban.

* [Más Azure-szolgáltatások használata a Xamarin.Forms-zal](https://docs.microsoft.com/xamarin/xamarin-forms/data-cloud/) Olyan további Azure-funkciók hozzáadása a Xamarin.Forms-alkalmazásokhoz, mint a keresés, tárolás és a Cognitive Services.

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
[Install Xamarin]: https://docs.microsoft.com/xamarin/cross-platform/get-started/installation/
[Mobile app SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Azure Portal]: https://portal.azure.com/
