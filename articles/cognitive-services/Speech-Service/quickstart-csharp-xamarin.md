---
title: 'Gyors útmutató: beszédfelismerés felismerése C# (Xamarin) – beszédfelismerési szolgáltatás'
titleSuffix: Azure Cognitive Services
description: Ebben a cikkben egy platformfüggetlen C# Xamarin-alkalmazást hoz létre a univerzális Windows-platform (UWP), az Android és az iOS rendszerhez a Cognitive Services Speech SDK használatával. A beszédet valós időben, az eszköz vagy a szimulátor mikrofonja alapján is átmásolhatja. Az alkalmazás a Speech SDK NuGet csomagjával és a Microsoft Visual Studio 2019-mel készült.
services: cognitive-services
author: jhakulin
manager: robch
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 09/16/2019
ms.author: jhakulin
ms.custom: ''
ms.openlocfilehash: ad1c6f7d17d1e04fcfa5b187a29dadd2fa61edd8
ms.sourcegitcommit: 9a4296c56beca63430fcc8f92e453b2ab068cc62
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/20/2019
ms.locfileid: "72675602"
---
# <a name="quickstart-recognize-speech-by-using-a-cross-platform-xamarin-app-that-uses-the-speech-sdk"></a>Gyors útmutató: beszédfelismerés felismerése egy platformfüggetlen Xamarin-alkalmazás használatával, amely a Speech SDK-t használja

A gyors útmutató [szöveg](quickstart-csharp-uwp.md), [szöveg és beszéd](quickstart-text-to-speech-csharp-uwp.md) [fordítás](quickstart-translate-speech-uwp.md)céljából is elérhető.

Ebben a cikkben egy platformfüggetlen C# alkalmazást fejleszt ki az Azure Cognitive Services [Speech SDK](speech-sdk.md)-t használó univerzális Windows-platform (UWP), Android és iOS rendszerhez készült Xamarin használatával. A program valós időben átmásolja a beszédet az eszköz mikrofonjában lévő szövegbe. Az alkalmazás a [SPEECH SDK NuGet csomagjával](https://aka.ms/csspeech/nuget) és a Microsoft Visual Studio 2019 bármely kiadásával készült.

## <a name="prerequisites"></a>Előfeltételek

Ehhez a rövid útmutatóhoz a következőkre van szükség:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* Egy Azure-előfizetési kulcs a beszédfelismerési szolgáltatáshoz. [Szerezze be az egyiket ingyenesen](get-started.md).
* Windows 10 Fall Creators frissítéssel rendelkező Windows rendszerű számítógép (10,0; 16299) vagy újabb, valamint egy működő mikrofonnal.
* [Xamarin-telepítés a Visual studióba](https://docs.microsoft.com/xamarin/get-started/installation/?pivots=windows).
* [Xamarin Android-telepítés Windows rendszeren](https://docs.microsoft.com/xamarin/android/get-started/installation/windows).
* [Xamarin iOS-telepítés Windows](https://docs.microsoft.com/xamarin/ios/get-started/installation/windows/?pivots=windows)rendszeren.
* Az Android megcélzása: 
   * Android-eszköz (ARM32/64, x86; 23. API: Android 6,0 Marshmallow vagy újabb) [engedélyezve van a fejlesztéshez](https://developer.android.com/studio/debug/dev-options) egy működő mikrofonnal.
* Az iOS megcélzása: 
    * Egy iOS-eszköz (ARM64) vagy egy iOS-szimulátor (x64), amely a [fejlesztéshez engedélyezve van](https://docs.microsoft.com/xamarin/ios/get-started/installation/device-provisioning/) egy működő mikrofonnal.
* A Windows ARM64 Build támogatásához telepítse a [választható Build-eszközöket és a ARM/ARM64 Windows 10 SDK](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/)-t.

## <a name="create-a-visual-studio-project"></a>Visual Studio-projekt létrehozása

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-xamarin-create-proj.md)]

## <a name="add-sample-code-for-the-common-helloworld-project"></a>Mintakód hozzáadása a Common HelloWorld projekthez

Az általános HelloWorld projekt platform-független implementációkat tartalmaz a platformfüggetlen alkalmazásokhoz. Most adja hozzá az alkalmazás felhasználói felületét meghatározó XAML-kódot, és adja hozzá a C# megvalósítás mögötti kódot.

1. **Megoldáskezelő**a Common HelloWorld projekt alatt nyissa meg `MainPage.xaml`.

1. A tervező XAML nézetében szúrja be a következő XAML-kódrészletet a **Grid** címkébe `<StackLayout>` és `</StackLayout>` között:

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld/MainPage.xaml)]

1. A **Megoldáskezelőban**nyissa meg a kód mögötti forrásfájlt `MainPage.xaml.cs`. A csoport a `MainPage.xaml` alatt van csoportosítva.

1. Cserélje le az összes kódot a következő kódrészletre:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld/MainPage.xaml.cs)]

1. A forrásfájl `OnRecognitionButtonClicked` kezelőjében keresse meg a `YourSubscriptionKey` karakterláncot, és cserélje le az előfizetési kulcsra.

1. A `OnRecognitionButtonClicked` kezelőben keresse meg a `YourServiceRegion` karakterláncot, és cserélje le az előfizetéséhez tartozó [régióra](regions.md) . Használja például a `westus` értéket az ingyenes próbaverziós előfizetéshez.

1. Ezután létre kell hoznia egy [Xamarin szolgáltatást](https://docs.microsoft.com/xamarin/android/app-fundamentals/services/creating-a-service/), amely a különböző platform-projektekben, például a UWP, az Androidban és az iOS-ben lévő mikrofon-engedélyek lekérdezésére szolgál. Ehhez vegyen fel egy új, *Services* nevű mappát a HelloWorld projekt alatt, és hozzon létre C# egy új forrásfájlt. Kattintson a jobb gombbal a *szolgáltatások* mappára, és válassza az**új elem** **hozzáadása**  >  a**fájl** >  elemet. Nevezze át a fájlt `IMicrophoneService.cs`, és az alábbi kódrészletből helyezze el az összes kódot:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld/Services/IMicrophoneService.cs)]

## <a name="add-sample-code-for-the-helloworldandroid-project"></a>Mintakód hozzáadása a HelloWorld. Android-projekt

Most adja hozzá C# a kódot, amely meghatározza az alkalmazás Android-specifikus részét.

1. **Megoldáskezelő**a HelloWorld alatt. Android-projekt, `MainActivity.cs` megnyitása.

1. Cserélje le az összes kódot a következő kódrészletre:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.Android/MainActivity.cs)]

1. Következő lépésként adja hozzá az Android-specifikus implementációt `MicrophoneService`hoz az új HelloWorld létrehozásához. Android-projekt. Ezután hozzon létre egy új C# forrásfájlt. Nevezze át a fájlt `MicrophoneService.cs`. Másolja és illessze be a következő kódrészletet a fájlba:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.Android/Services/MicrophoneService.cs)]

1. Ezután nyissa meg `AndroidManifest.xml` a *Properties (Tulajdonságok* ) mappában. Adja hozzá a következő használati engedélyek beállítást a mikrofonhoz `<manifest>` és `</manifest>` között:

   ```xml
   <uses-permission android:name="android.permission.RECORD_AUDIO" />
   ```

## <a name="add-sample-code-for-the-helloworldios-project"></a>Mintakód hozzáadása a HelloWorld. iOS projekthez

Most adja hozzá C# a kódot, amely meghatározza az alkalmazás iOS-specifikus részét. Hozzon létre az Apple Device-specifikus konfigurációkat is az HelloWorld. iOS projekthez.

1. **Megoldáskezelő**a HelloWorld. iOS projekt alatt nyissa meg `AppDelegate.cs`.

1. Cserélje le az összes kódot a következő kódrészletre:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.iOS/AppDelegate.cs)]

1. Ezután adja hozzá a `MicrophoneService` iOS-specifikus implementációját az helloworld.iO projektben *található új Mappabeállítások* létrehozásával. Ezután hozzon létre egy új C# forrásfájlt. Nevezze át a fájlt `MicrophoneService.cs`. Másolja és illessze be a következő kódrészletet a fájlba:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.iOS/Services/MicrophoneService.cs)]

1. Nyissa meg `Info.plist` a HelloWorld. iOS projekt alatt a szövegszerkesztőben. Adja hozzá a következő kulcs-érték párokat a dict szakaszban:

   <key>NSMicrophoneUsageDescription</key> 
   <string>a minta alkalmazáshoz mikrofon-hozzáférés szükséges</string>

   > [!NOTE] 
   > Ha iPhone-eszközt épít, győződjön meg arról, hogy `Bundle Identifier` megfelel az eszköz kiépítési profiljának alkalmazás-AZONOSÍTÓjának. Ellenkező esetben a Build sikertelen lesz. A iPhoneSimulator-mel a következőképpen hagyhatja.

1. Ha Windows rendszerű számítógépet használ, hozzon létre egy kapcsolatot a Mac-eszközzel **a  > ** **iOS** - > **pár Mac-re való**létrehozásához. A Mac-eszközhöz való kapcsolódás engedélyezéséhez kövesse a Visual Studio által biztosított utasítások varázslót.

## <a name="add-sample-code-for-the-helloworlduwp-project"></a>Mintakód hozzáadása a HelloWorld. UWP projekt

Most adja hozzá C# a kódot, amely meghatározza az alkalmazás UWP-specifikus részét.

1. **Megoldáskezelő**a HelloWorld alatt. UWP projekt, `MainPage.xaml.cs` megnyitása.

1. Cserélje le az összes kódot a következő kódrészletre:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.UWP/MainPage.xaml.cs)]

1. Ezután adjon hozzá egy UWP-specifikus implementációt a `MicrophoneService`hoz, ehhez hozza létre az új HelloWorld *a mappában.* UWP projekt. Ezután hozzon létre egy új C# forrásfájlt. Nevezze át a fájlt `MicrophoneService.cs`. Másolja és illessze be a következő kódrészletet a fájlba:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.UWP/Services/MicrophoneService.cs)]

1. Ezután kattintson duplán a HelloWorld alatt található `Package.appxmanifest` fájlra. UWP-projekt a Visual studión belül. A **képességek**területen győződjön meg arról, hogy a **mikrofon** be van jelölve, és mentse a fájlt.

   > [!NOTE] 
   > Ha a "tanúsítványfájl nem létezik: HelloWorld" hibaüzenet jelenik meg. UWP_TemporaryKey. pfx: "További információért olvassa [el a beszéd – szöveg](quickstart-csharp-uwp.md) mintát.

1. A menüsávban válassza a **fájl**  >  az**összes mentése** lehetőséget a módosítások mentéséhez.

## <a name="build-and-run-the-uwp-application"></a>A UWP-alkalmazás létrehozása és futtatása

1. HelloWorld beállítása. A UWP indítási projektként szolgál. Kattintson a jobb gombbal a HelloWorld. UWP projekt, majd válassza a **Build** elemet az alkalmazás létrehozásához. 

1. Az alkalmazás indításához válassza a **hibakeresés**  >  a**hibakeresés megkezdése** (vagy az **F5 billentyű**kiválasztása) lehetőséget. Megjelenik a **HelloWorld** ablak.

   ![Példa UWP Speech Recognition C# -alkalmazásra – gyors üzembe helyezés](media/sdk/qs-csharp-xamarin-helloworld-uwp-window.png)

1. Válassza a **mikrofon engedélyezése**lehetőséget. Ha megjelenik a hozzáférési engedélyre vonatkozó kérelem, válassza az **Igen**lehetőséget.

   ![Mikrofon-hozzáférési engedély kérése](media/sdk/qs-csharp-xamarin-uwp-access-prompt.png)

1. Válassza a **beszédfelismerés elindítása**lehetőséget, és beszéljen egy angol kifejezéssel vagy mondattal az eszköz mikrofonjában. A beszédfelismerés a Speech Services szolgáltatásba kerül, és szövegbe kerül, amely megjelenik az ablakban.

   ![Beszédfelismerés felhasználói felülete](media/sdk/qs-csharp-xamarin-uwp-ui-result.png)

## <a name="build-and-run-the-android-and-ios-applications"></a>Android-és iOS-alkalmazások létrehozása és futtatása

Az Android-és iOS-alkalmazások kiépítése és futtatása az eszközön vagy a szimulátoron hasonló módon történik a UWP. Győződjön meg arról, hogy az összes SDK megfelelően van telepítve a jelen cikk "Előfeltételek" szakaszában.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Minták C# feltárása a githubon](https://aka.ms/csspeech/samples)
