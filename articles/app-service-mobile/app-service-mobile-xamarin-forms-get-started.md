---
title: Első lépések a Xamarin.Forms-szal
description: Ezt az oktatóanyagot követve elkezdheti a Mobile Apps szolgáltatást Xamarin.Forms-alapú fejlesztésre használni.
ms.assetid: 5e692220-cc89-4548-96c8-35259722acf5
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/25/2019
ms.openlocfilehash: 9ae97ccbcc358a150e914a6e950579a972fef0bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461334"
---
# <a name="create-a-xamarinforms-app-with-azure"></a>Xamarin.Forms-alkalmazás létrehozása az Azure-ral

[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Áttekintés
Ez az oktatóanyag azt ismerteti, hogyan adhat felhőalapú háttérszolgáltatásokat a Xamarin.Forms-mobilalkalmazásokhoz az Azure App Service Mobile Apps szolgáltatását használva háttérrendszerként. Létrehoz egy új Mobile Apps-háttéralkalmazást, illetve egy olyan egyszerű Xamarin.Forms-alapú teendőlista alkalmazást, amely az alkalmazásadatait az Azure-ban tárolja.

Az oktatóanyag végrehajtása feltétele a Mobile Apps Xamarin.Forms-alkalmazásokra vonatkozó összes többi oktatóanyagának elérésének.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Aktív Azure-fiók. Ha nincs fiókja, regisztráljon az Azure próba-előfizetésére, és akár 10 ingyenes mobilalkalmazáshoz is hozzájuthat, amelyeket a próba-előfizetés lejárta után is tovább használhat. További információk: [Ingyenes Azure-próbaverzió](https://azure.microsoft.com/pricing/free-trial/).

* Visual Studio Tools for Xamarin, Visual Studio 2017-es vagy újabb verziós vagy Visual Studio for Mac. Útmutatást a [Xamarin telepítési oldalán][Install Xamarin] talál.

* (választható) iOS-alkalmazás készítéséhez Xcode 9.0 vagy újabb verzióval rendelkező Mac szükséges. A Visual Studio for Mac használható iOS-alkalmazások fejlesztésére, a Visual Studio 2017-es vagy újabb verziói is használhatók (feltéve, hogy a Mac elérhető a hálózaton).

## <a name="create-a-new-mobile-apps-back-end"></a>Új Mobile Apps-háttéralkalmazás létrehozása
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Adatbázis-kapcsolat létrehozása és az ügyfél- és kiszolgálóprojekt konfigurálása
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-xamarinforms-solution"></a>A Xamarin.Forms megoldás futtatása

A megoldás megnyitásához a Xamarinhoz készült Visual Studio Tools szükséges. Lásd a [Xamarin telepítési útmutatót][Install Xamarin]. Ha az eszközök már telepítve vannak, akkor a megoldást a következő lépesekben töltheti le és nyithatja meg:

### <a name="visual-studio-windows-and-mac"></a>Visual Studio (Windows és Mac)

1. Nyissa meg az [Azure Portalon,](https://portal.azure.com/) és keresse meg a létrehozott mobilalkalmazást. A `Overview` panelen keresse meg a mobilalkalmazás nyilvános végpontját képező URL-címet. Példa - a sitename az én app neve https://test123.azurewebsites.net"test123" lesz .

2. Nyissa meg `Constants.cs` a fájlt ebben a mappában - xamarin.forms/ZUMOAPPNAME. Az alkalmazás `ZUMOAPPNAME`neve .

3. Az `Constants.cs` osztályban `ZUMOAPPURL` cserélje le a változót a fenti nyilvános végpontra.

    `public static string ApplicationURL = @"ZUMOAPPURL";`

    Lesz

    `public static string ApplicationURL = @"https://test123.azurewebsites.net";`
    
4. Az alábbi utasításokat végrehajtva futtathatja az Androidos vagy Windowsos projekteket, és ha rendelkezik a hálózaton elérhető Mac-számítógéppel, az iOS-projektet is.

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

Ez a művelet egy közzétételi kérést küld az Azure szolgáltatásban üzemeltetett új Mobile Apps-háttéralkalmazás számára. A rendszer beilleszti kérés adatait a TodoItem táblába. A Mobile Apps-háttéralkalmazás visszaadja a táblában tárolt elemeket, amelyek egy listában jelennek meg.

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

1. Kattintson a jobb gombbal az iOS-projektre, és válassza **a Beállítás indítási projektként parancsot.**

2. A **Run** (Futtatás) menüben válassza a **Start Debugging** (Hibakeresés indítása) lehetőséget a projekt buildjének elkészítéséhez, és indítsa el az alkalmazást az iPhone-emulátoron.

Az alkalmazásban írjon be egy értelmes szöveget, például *Xamarin-tanulás*, majd válassza a pluszjelet (**+**).

![iOS-es Teendők alkalmazás][10]

Ez a művelet egy közzétételi kérést küld az Azure szolgáltatásban üzemeltetett új Mobile Apps-háttéralkalmazás számára. A rendszer beilleszti kérés adatait a TodoItem táblába. A Mobile Apps-háttéralkalmazás visszaadja a táblában tárolt elemeket, amelyek egy listában jelennek meg.

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

Ez a művelet egy közzétételi kérést küld az Azure szolgáltatásban üzemeltetett új Mobile Apps-háttéralkalmazás számára. A rendszer beilleszti kérés adatait a TodoItem táblába. A Mobile Apps-háttéralkalmazás visszaadja a táblában tárolt elemeket, amelyek egy listában jelennek meg.

![UWP Teendők alkalmazás][12]

> [!NOTE]
> A Mobile Apps-háttéralkalmazáshoz hozzáférést biztosító kódot a megoldás hordozhatóosztálytár-projektjének **TodoItemManager.cs** C#-fájljában találja.
>

## <a name="troubleshooting"></a>Hibaelhárítás

Ha a megoldás fordítása során problémákat tapasztal, futtassa a NuGet csomagkezelőt, és frissítsen a `Xamarin.Forms` legújabb verziójára, az Android-projektben pedig frissítse a `Xamarin.Android` támogatási csomagjait. Előfordulhat, hogy a gyorsindítási projektek nem tartalmazzák a legújabb verziót.

Vegye figyelembe, hogy az Android-projektjében hivatkozott összes támogatási csomagnak azonos verziójúnak kell lennie. Az [Azure Mobile Apps NuGet csomag](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) Android platform esetén `Xamarin.Android.Support.CustomTabs`-függőséggel rendelkezik, így ha a projektjében újabb támogatási csomagokat használ, akkor az ütközések elkerülése érdekében közvetlenül kell telepítenie ennek a csomagnak a szükséges verzióját.

<!-- Images. -->
[10]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-android.png
[12]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-windows.png

<!-- URLs. -->
[Install Xamarin]: https://docs.microsoft.com/xamarin/cross-platform/get-started/installation/
