---
title: A Mobile Apps használatának első lépései a Xamarin.Forms eszközkészlettel
description: Ezt az oktatóanyagot követve elkezdheti a Mobile Apps szolgáltatást Xamarin.Forms-alapú fejlesztésre használni.
services: app-service\mobile
documentationcenter: xamarin
author: elamalani
manager: crdun
ms.assetid: 5e692220-cc89-4548-96c8-35259722acf5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: bca0f0de7de321060635459c4435525f650c7467
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446326"
---
# <a name="create-a-xamarinforms-app-with-azure"></a>Xamarin.Forms-alkalmazás létrehozása az Azure-ral

[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

> [!NOTE]
> A Visual Studio App Center fektet a mobilalkalmazás-fejlesztés központi új, integrált szolgáltatások. A fejlesztők a **hozhat létre**, **teszt** és **terjesztése** állíthat be folyamatos integrációt és teljesítést folyamat szolgáltatások. Az alkalmazás telepítve van, a fejlesztők monitorozható az állapot és az alkalmazás használatával használatát a **Analytics** és **diagnosztikai** -szolgáltatásokat, és kapcsolatba léphet a felhasználókat a **leküldéses** a szolgáltatás. A fejlesztők is kihasználhatják a **Auth** azok a felhasználók hitelesítéséhez és **adatok** szolgáltatás és a felhőbeli alkalmazások adatainak szinkronizálása. Tekintse meg [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-xamarin-forms-get-started) még ma.
>

## <a name="overview"></a>Áttekintés
Ez az oktatóanyag azt ismerteti, hogyan adhat felhőalapú háttérszolgáltatásokat a Xamarin.Forms-mobilalkalmazásokhoz az Azure App Service Mobile Apps szolgáltatását használva háttérrendszerként. Létrehoz egy új Mobile Apps-háttéralkalmazást, illetve egy olyan egyszerű Xamarin.Forms-alapú teendőlista alkalmazást, amely az alkalmazásadatait az Azure-ban tárolja.

Az oktatóanyag végrehajtása feltétele a Mobile Apps Xamarin.Forms-alkalmazásokra vonatkozó összes többi oktatóanyagának elérésének.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Aktív Azure-fiók. Ha nincs fiókja, regisztráljon az Azure próba-előfizetésére, és akár 10 ingyenes mobilalkalmazáshoz is hozzájuthat, amelyeket a próba-előfizetés lejárta után is tovább használhat. További információk: [Ingyenes Azure-próbaverzió](https://azure.microsoft.com/pricing/free-trial/).

* A Xamarin, a Visual Studio 2017 vagy újabb, illetve a Visual Studio-eszközök vagy a Visual Studio for Mac. Tekintse meg a [Xamarin telepítési lapja][Install Xamarin] útmutatást.

* (választható) iOS-alkalmazás készítéséhez Xcode 9.0 vagy újabb verzióval rendelkező Mac szükséges. A Visual Studio for Mac, vagy a Visual Studio 2017 iOS-alkalmazások fejlesztéséhez használható, vagy később is használható (mindaddig, amíg a Mac az a hálózaton elérhető).

## <a name="create-a-new-mobile-apps-back-end"></a>Új Mobile Apps-háttéralkalmazás létrehozása
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Az ügyfél és kiszolgáló projekt egy adatbázis-kapcsolat létrehozása és konfigurálása
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-xamarinforms-solution"></a>A Xamarin.Forms-megoldás futtatása

A Visual Studio Tools for Xamarin szükségesek, nyissa meg a megoldást, tekintse meg a [Xamarin telepítési utasításokat][Install Xamarin]. Ha az eszközök már telepítve vannak, akkor a megoldást a következő lépesekben töltheti le és nyithatja meg:

### <a name="visual-studio-windows-and-mac"></a>A Visual Studio (Windows és Mac)

1. Nyissa meg a [az Azure portal](https://portal.azure.com/) , és keresse meg a mobilalkalmazás, amelyet Ön hozott létre. Az a `Overview` panelen keresse meg az URL-címet, amely a nyilvános végpont számára. Például: a sitename for my app name "test123" lesz https://test123.azurewebsites.net.

2. Nyissa meg a fájlt `Constants.cs` ebben a mappában - xamarin.forms/ZUMOAPPNAME. Az alkalmazás neve `ZUMOAPPNAME`.

3. A `Constants.cs` osztály, cserélje le `ZUMOAPPURL` változó fenti nyilvános végponthoz.

    `public static string ApplicationURL = @"ZUMOAPPURL";`

    válik

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

Az alkalmazásban írjon be egy értelmes szöveget, például *Xamarin-tanulás*, majd válassza a pluszjelet ( **+** ).

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

Az alkalmazásban írjon be egy értelmes szöveget, például *Xamarin-tanulás*, majd válassza a pluszjelet ( **+** ).

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

Az alkalmazásban írjon be egy értelmes szöveget, például *Xamarin-tanulás*, majd válassza a pluszjelet ( **+** ).

Ez a művelet egy közzétételi kérést küld az Azure szolgáltatásban üzemeltetett új Mobile Apps-háttéralkalmazás számára. A kérelem adatai beillesztésre kerülnek a TodoItem táblába. A Mobile Apps-háttéralkalmazás visszaadja a táblában tárolt elemeket, amelyek egy listában jelennek meg.

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
