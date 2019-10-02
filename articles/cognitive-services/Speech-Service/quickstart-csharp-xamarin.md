---
title: 'Gyors útmutató: Beszédfelismerés felismerése C# , (Xamarin) – beszédfelismerési szolgáltatás'
titleSuffix: Azure Cognitive Services
description: Ebben a cikkben a Cognitive Services Speech SDK használatával hozhat létre C# platformfüggetlen Xamarin-alkalmazást Windows UWP, Android és iOS rendszerhez. A beszédet valós időben, az eszköz vagy a szimulátor mikrofonja alapján is átmásolhatja. Az alkalmazás a Speech SDK NuGet csomagjával és a Microsoft Visual Studio 2019-mel készült.
services: cognitive-services
author: jhakulin
manager: robch
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 09/16/2019
ms.author: jhakulin
ms.custom: ''
ms.openlocfilehash: 02031e8e2800329134eaf34f5f99c93a20b99aac
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71804054"
---
# <a name="quickstart-recognize-speech-using-cross-platform-xamarin-app-by-using-the-speech-sdk"></a>Gyors útmutató: Beszéd felismerése platformfüggetlen Xamarin alkalmazással a Speech SDK használatával

A gyors útmutatók [szöveg](quickstart-csharp-uwp.md), [szöveg](quickstart-text-to-speech-csharp-uwp.md) és beszéd [fordítás](quickstart-translate-speech-uwp.md)céljából is elérhetők.

Ebben a cikkben egy platformfüggetlen C# alkalmazást fejleszt ki a Xamarin for univerzális Windows-platform (UWP), az Android és az iOS használatával a [Speech SDK](speech-sdk.md)használatával. A program valós időben átmásolja a beszédet az eszköz mikrofonjában lévő szövegbe. Az alkalmazás a [SPEECH SDK NuGet csomagjával](https://aka.ms/csspeech/nuget) és a Microsoft Visual Studio 2019-es verziójával (bármely kiadással) készült.

## <a name="prerequisites"></a>Előfeltételek

Ehhez a rövid útmutatóhoz a következőkre van szükség:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* Egy Azure-előfizetési kulcs a beszédfelismerési szolgáltatáshoz. [Szerezze be az egyiket ingyenesen](get-started.md).
* Windows 10 Fall Creators frissítéssel rendelkező Windows rendszerű számítógép (10,0; 16299) vagy újabb, valamint egy működő mikrofonnal.
* [Xamarin-telepítés a Visual studióba](https://docs.microsoft.com/xamarin/get-started/installation/?pivots=windows).
* [Xamarin Android-telepítés Windows rendszeren](https://docs.microsoft.com/xamarin/android/get-started/installation/windows).
* [Xamarin iOS-telepítés Windows rendszeren](https://docs.microsoft.com/xamarin/ios/get-started/installation/windows/?pivots=windows).
* Az Android megcélzása: 
   * Android-eszköz (ARM32/64, x86; 23. API: Android 6,0 Marshmallow vagy újabb) [engedélyezve van a fejlesztéshez](https://developer.android.com/studio/debug/dev-options) egy működő mikrofonnal.
* A cél iOS: iOS-eszköz (ARM64) vagy iOS-szimulátor (x64), amely egy működő mikrofonnal való [fejlesztéshez van engedélyezve](https://docs.microsoft.com/xamarin/ios/get-started/installation/device-provisioning/) .
* A Windows ARM64 Build támogatásához telepítse a [választható Build-eszközöket és a Windows 10 SDK-t az ARM/ARM64-hez](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/).

## <a name="create-a-visual-studio-project"></a>Visual Studio-projekt létrehozása

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-xamarin-create-proj.md)]

## <a name="add-sample-code-for-the-common-helloworld-project"></a>Mintakód hozzáadása a Common `helloworld` projekthez

A Common `helloworld` projekt platformtól független implementációkat tartalmaz a többplatformos alkalmazásokhoz.
Most adja hozzá az alkalmazás felhasználói felületét meghatározó XAML-kódot, és adja hozzá a C# megvalósítás mögötti kódot.

1. **Megoldáskezelő**a közös `helloworld` projekt alatt nyissa meg a `MainPage.xaml` csomópontot.

1. A tervező XAML nézetében szúrja be a következő XAML-kódrészletet a **Grid** címkébe (a `<StackLayout>` és a `</StackLayout>` között):

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld/MainPage.xaml)]

1. A **Megoldáskezelőban**nyissa meg a kód mögötti forrásfájlt `MainPage.xaml.cs`. (Ez a következő alá `MainPage.xaml`van csoportosítva:.)

1. Cserélje le az összes kódot a következő kódrészletre:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld/MainPage.xaml.cs)]

1. A forrásfájl `OnRecognitionButtonClicked` kezelőjében keresse meg a karakterláncot `YourSubscriptionKey`, és cserélje le az előfizetési kulcsra.

1. A `OnRecognitionButtonClicked` kezelőben keresse meg a `YourServiceRegion` karakterláncot, és cserélje le az előfizetéséhez tartozó [régióra](regions.md) . (Például használja `westus` az ingyenes próbaverziós előfizetést.)

1. Ezután létre kell hoznia egy [Xamarin-szolgáltatást](https://docs.microsoft.com/xamarin/android/app-fundamentals/services/creating-a-service/), amely a különböző platform-projektekben lévő mikrofon-engedélyek lekérdezésére szolgál (UWP, Android és iOS). Ehhez vegyen fel egy új mappát `Services` a `helloworld` projekt alatt, és C# hozzon létre egy új forrásfájlt az alatta (kattintson a jobb gombbal a `Services` mappára, és **vegye fel** > **új elemet** > **Code fájlt**), és nevezze át `IMicrophoneService.cs`-re, és helyezze át az összes kódot a következőre: a következő kódrészlet a fájlban: 0 @ no__t-11

## <a name="add-sample-code-for-the-helloworldandroid-project"></a>Mintakód hozzáadása a `helloworld.Android` projekthez

Most adja hozzá C# a kódot, amely meghatározza az alkalmazás Androidra vonatkozó részét.

1. **Megoldáskezelő**a `helloworld.Android` projekt alatt nyissa meg a `MainActivity.cs` csomópontot.

1. Cserélje le az összes kódot a következő kódrészletre:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.Android/MainActivity.cs)]

1. Ezután adja hozzá a `MicrophoneService` Android-specifikus implementációját új mappa létrehozásához `Services` a `helloworld.Android` projekt alatt. Ezután hozzon létre C# egy új forrásfájlt, és nevezze át `MicrophoneService.cs` értékre, és másolja a vágólapra a következő kódrészletet a fájlba.

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.Android/Services/MicrophoneService.cs)]

1. Ezután nyissa meg a `AndroidManifest.xml` mappát a `Properties` mappában, és adja hozzá a következő használati engedélyek beállítást a mikrofonhoz a `<manifest>` és a `</manifest>` között.
```xml
   <uses-permission android:name="android.permission.RECORD_AUDIO" />
```

## <a name="add-sample-code-for-the-helloworldios-project"></a>Mintakód hozzáadása a `helloworld.iOS` projekthez

Most adja hozzá C# a kódot, amely meghatározza az alkalmazás iOS-es részét, és az HelloWorld. iOS projekthez az Apple-eszközökre vonatkozó konfigurációkat is létrehoz.

1. **Megoldáskezelő**a `helloworld.iOS` projekt alatt nyissa meg a `AppDelegate.cs` csomópontot.

1. Cserélje le az összes kódot a következő kódrészletre:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.iOS/AppDelegate.cs)]

1. Ezután adja hozzá a `MicrophoneService` iOS-specifikus implementációját új mappa létrehozásához `Services` a `helloworld.iOS` projekt alatt. Ezután hozzon létre C# egy új forrásfájlt, és nevezze át `MicrophoneService.cs` értékre, és másolja a vágólapra a következő kódrészletet a fájlba.

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.iOS/Services/MicrophoneService.cs)]

1. Nyissa meg az info. plist fájlt a `helloworld.iOS` projektből a szövegszerkesztőbe, és adja hozzá a következő kulcs-érték párokat a dict szakaszban: <key>NSMicrophoneUsageDescription</key>
   <string>Ez a minta alkalmazás mikrofonos hozzáférést igényel</string>
   > Megjegyzés: Ha iPhone-eszköz kiépítését célozza, győződjön meg arról, hogy a `Bundle Identifier` megfelel az eszköz kiépítési profiljának alkalmazás-AZONOSÍTÓjának, máskülönben a Build művelet sikertelen lesz. A iPhoneSimulator segítségével a következőképpen hagyhat.

1. Ha a Windows rendszerű SZÁMÍTÓGÉPekre épít, létre kell hoznia egy kapcsolatot a Mac **-eszközzel az @no__t-** 1**iOS** > **pár Mac**-re való létrehozásához. A Mac-eszközhöz való kapcsolódás engedélyezéséhez kövesse a Visual Studio által biztosított utasítások varázslót.

## <a name="add-sample-code-for-the-helloworlduwp-project"></a>Mintakód hozzáadása a `helloworld.UWP` projekthez

Most adja hozzá C# a kódot, amely meghatározza az alkalmazás UWP megadott részét.

1. **Megoldáskezelő**a `helloworld.UWP` projekt alatt nyissa meg a `MainPage.xaml.cs` csomópontot.

1. Cserélje le az összes kódot a következő kódrészletre:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.UWP/MainPage.xaml.cs)]

1. Ezután adja hozzá a UWP-specifikus implementációt a `MicrophoneService` nevű új mappa létrehozásához `Services` a `helloworld.UWP` projekt alatt. Ezután hozzon létre C# egy új forrásfájlt, és nevezze át `MicrophoneService.cs` értékre, és másolja a vágólapra a következő kódrészletet a fájlba.

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.UWP/Services/MicrophoneService.cs)]

1. Ezután kattintson duplán `Package.appxmanifest` fájlra a Visual Studióban található `helloworld.UWP` projekt alatt, és a **képességek**területen jelölje be a következőt:  > **mikrofon** jelölőnégyzetet, és mentse a fájlt.
   > Megjegyzés: Ha a figyelmeztetést látja: A tanúsítványfájl nem létezik: HelloWorld. UWP_TemporaryKey. pfx, további információért tekintse [meg a beszédfelismerés szövegbe](quickstart-csharp-uwp.md) mintát.

1. A menüsávban válassza a **fájl** > **Mentés** lehetőséget a módosítások mentéséhez.

## <a name="build-and-run-the-uwp-application"></a>A UWP-alkalmazás létrehozása és futtatása

1. Állítsa be `helloworld.UWP` értéket indítási projektként, majd kattintson a jobb gombbal az egér használata `helloworld.UWP` projektre, és válassza a **Build** (létrehozás) lehetőséget az alkalmazás létrehozásához. 

1. Az alkalmazás indításához válassza a **hibakeresés**@no__t – 1**Indítás hibakeresése** (vagy az **F5**billentyű lenyomása) lehetőséget. Megjelenik a **HelloWorld** ablak.

   ![Példa UWP Speech Recognition C# -alkalmazásra – gyors üzembe helyezés](media/sdk/qs-csharp-xamarin-helloworld-uwp-window.png)

1. Válassza a **mikrofon engedélyezése**lehetőséget, és ha a hozzáférési engedély kérése megjelenik, válassza az **Igen**lehetőséget.

   ![Mikrofon-hozzáférési engedély kérése](media/sdk/qs-csharp-xamarin-uwp-access-prompt.png)

1. Válassza a **beszédfelismerés elindítása**lehetőséget, és beszéljen egy angol kifejezéssel vagy mondattal az eszköz mikrofonjában. A beszéd a beszédfelismerési szolgáltatásokhoz lett továbbítva, és szövegbe kerül, amely megjelenik az ablakban.

   ![Beszédfelismerés felhasználói felülete](media/sdk/qs-csharp-xamarin-uwp-ui-result.png)

## <a name="build-and-run-the-android-and-ios-applications"></a>Android-és iOS-alkalmazások létrehozása és futtatása

Az Android-és iOS-alkalmazások kiépítése és futtatása az eszközön vagy a szimulátoron hasonló módon történik, mint a UWP. Fontos, hogy ellenőrizze, hogy az összes SDK megfelelően van-e telepítve a dokumentum `Prerequisites` szakaszában.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Minták C# feltárása a githubon](https://aka.ms/csspeech/samples)
