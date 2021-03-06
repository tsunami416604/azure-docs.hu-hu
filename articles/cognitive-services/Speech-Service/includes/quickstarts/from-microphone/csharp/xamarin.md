---
title: 'Gyors útmutató: beszéd felismerése mikrofonból, C# (Xamarin) – beszédfelismerési szolgáltatás'
titleSuffix: Azure Cognitive Services
description: Ebben a cikkben egy platformfüggetlen C# Xamarin-alkalmazást hoz létre a Univerzális Windows-platform (UWP), az Android és az iOS rendszerhez a Cognitive Services Speech SDK használatával. A beszédet valós időben, az eszköz vagy a szimulátor mikrofonja alapján is átmásolhatja. Az alkalmazás a Speech SDK NuGet csomagjával és a Microsoft Visual Studio 2019-mel készült.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/02/2020
ms.author: erhopf
ms.custom: devx-track-csharp
ms.openlocfilehash: f89bdbfd144fb327c1daae020a1e371c00045859
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91377610"
---
## <a name="prerequisites"></a>Előfeltételek

Az első lépések előtt:

> [!div class="checklist"]
> * [Azure Speech-erőforrás létrehozása](../../../../overview.md#try-the-speech-service-for-free)
> * [Állítsa be a fejlesztési környezetet, és hozzon létre egy üres projektet](../../../../quickstarts/setup-platform.md?tabs=xamarin&pivots=programming-language-csharp)
> * Győződjön meg arról, hogy van hozzáférése egy mikrofonhoz a hangrögzítéshez

Ha már megtette ezt, remek. Folytasd a munkát.

## <a name="add-sample-code-for-the-common-helloworld-project"></a>Mintakód hozzáadása a Common HelloWorld projekthez

Az általános HelloWorld projekt platform-független implementációkat tartalmaz a platformfüggetlen alkalmazásokhoz. Most adja hozzá az alkalmazás felhasználói felületét meghatározó XAML-kódot, és adja hozzá a C#-kódot a megvalósítás mögött.

1. **Megoldáskezelő**a Common HelloWorld projekt alatt nyissa meg a t `MainPage.xaml` .

1. A tervező XAML nézetében szúrja be a következő XAML-kódrészletet a **Grid** címkébe a és a között `<StackLayout>` `</StackLayout>` :

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld/MainPage.xaml)]

1. A **megoldáskezelő**nyissa meg a kód mögötti forrásfájlt `MainPage.xaml.cs` . A csoport a alatt van csoportosítva `MainPage.xaml` .

1. Cserélje le az összes kódot a következő kódrészletre:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld/MainPage.xaml.cs)]

1. A forrásfájl `OnRecognitionButtonClicked` kezelőjében keresse meg a karakterláncot `YourSubscriptionKey` , és cserélje le az előfizetési kulcsra.


1. A `OnRecognitionButtonClicked` kezelőben keresse meg a karakterláncot `YourServiceregion` , és cserélje le az előfizetéséhez tartozó [régió-](https://aka.ms/speech/sdkregion) **azonosítóra** . 

1. Ezután létre kell hoznia egy [Xamarin szolgáltatást](https://docs.microsoft.com/xamarin/android/app-fundamentals/services/creating-a-service/), amely a különböző platform-projektekben, például a UWP, az Androidban és az iOS-ben lévő mikrofon-engedélyek lekérdezésére szolgál. Ehhez vegyen fel egy új, *Services* nevű mappát a HelloWorld projekt alatt, és hozzon létre egy új C#-forrásfájlt. Kattintson a jobb gombbal a *szolgáltatások* mappára, és válassza az **Add**  >  **új elem**  >  **kódjának**hozzáadása elemet. Nevezze át a fájlt `IMicrophoneService.cs` , és az alábbi kódrészletből helyezze el az összes kódot:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld/Services/IMicrophoneService.cs)]

#### <a name="android"></a>[Android](#tab/x-android)
## <a name="add-sample-code-for-the-helloworldandroid-project"></a>Mintakód hozzáadása a `helloworld.Android` projekthez

Most adja hozzá a C#-kódot, amely meghatározza az alkalmazás Android-specifikus részét.

1. **Megoldáskezelő**a HelloWorld alatt. Android-projekt, megnyitás `MainActivity.cs` .

1. Cserélje le az összes kódot a következő kódrészletre:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.Android/MainActivity.cs)]

1. Következő lépésként adja hozzá az Android-specifikus implementációt a `MicrophoneService` HelloWorld alatt található új mappa- *szolgáltatások* létrehozásához. Android-projekt. Ezután hozzon létre egy új C#-forrásfájlt. Nevezze át a fájlt `MicrophoneService.cs` . Másolja és illessze be a következő kódrészletet a fájlba:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.Android/Services/MicrophoneService.cs)]

1. Ezt követően nyissa meg `AndroidManifest.xml` a *Properties (Tulajdonságok* ) mappát. Adja hozzá a következő használati engedélyek beállítást a mikrofonhoz a `<manifest>` és a között `</manifest>` :

   ```xml
   <uses-permission android:name="android.permission.RECORD_AUDIO" />
   ```
   
#### <a name="ios"></a>[iOS](#tab/ios)
## <a name="add-sample-code-for-the-helloworldios-project"></a>Mintakód hozzáadása a `helloworld.iOS` projekthez

Most adja hozzá a C#-kódot, amely meghatározza az alkalmazás iOS-specifikus részét. Hozzon létre az Apple Device-specifikus konfigurációkat is az HelloWorld. iOS projekthez.

1. **Megoldáskezelő**a HelloWorld. iOS projekt alatt nyissa meg a t `AppDelegate.cs` .

1. Cserélje le az összes kódot a következő kódrészletre:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.iOS/AppDelegate.cs)]

1. Következő lépésként adja hozzá az iOS-specifikus implementációt a `MicrophoneService` HelloWorld.IO projektben található új mappa- *szolgáltatások* létrehozásával. Ezután hozzon létre egy új C#-forrásfájlt. Nevezze át a fájlt `MicrophoneService.cs` . Másolja és illessze be a következő kódrészletet a fájlba:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.iOS/Services/MicrophoneService.cs)]

1. Nyissa meg a `Info.plist` HelloWorld. iOS projektet a szövegszerkesztőben. Adja hozzá a következő kulcs-érték párokat a dict szakaszban:

   <key>NSMicrophoneUsageDescription</key> 
    <string>Ehhez a minta alkalmazáshoz mikrofon-hozzáférés szükséges</string>

   > [!NOTE]
   > Ha iPhone-eszközt fejleszt, győződjön meg arról, hogy az eszköz kiépítési `Bundle Identifier` profiljának alkalmazás-azonosítója megfelel. Ellenkező esetben a Build sikertelen lesz. A iPhoneSimulator-mel a következőképpen hagyhatja.

1. Ha Windows rendszerű számítógépet használ, hozzon létre egy kapcsolatot a Mac-eszközzel, amely a Mac- **eszközök**  >  **iOS**  >  **-pair használatával történő**létrehozásához szükséges. A Mac-eszközhöz való kapcsolódás engedélyezéséhez kövesse a Visual Studio által biztosított utasítások varázslót.

#### <a name="uwp"></a>[UWP](#tab/helloworlduwp)
## <a name="add-sample-code-for-the-helloworlduwp-project"></a>Mintakód hozzáadása a `helloworld.UWP` projekthez

## <a name="add-sample-code-for-the-helloworlduwp-project"></a>Mintakód hozzáadása a HelloWorld. UWP projekt

Most adja hozzá a C#-kódot, amely meghatározza az alkalmazás UWP-specifikus részét.

1. **Megoldáskezelő**a HelloWorld alatt. UWP projekt, megnyitás `MainPage.xaml.cs` .

1. Cserélje le az összes kódot a következő kódrészletre:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.UWP/MainPage.xaml.cs)]

1. Ezután adjon hozzá egy UWP-specifikus megvalósítást a létrehozásához a `MicrophoneService` HelloWorld alatt található új mappa- *szolgáltatások* létrehozásával. UWP projekt. Ezután hozzon létre egy új C#-forrásfájlt. Nevezze át a fájlt `MicrophoneService.cs` . Másolja és illessze be a következő kódrészletet a fájlba:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.UWP/Services/MicrophoneService.cs)]

1. Ezután kattintson duplán a `Package.appxmanifest` fájlra a HelloWorld alatt. UWP-projekt a Visual studión belül. A **képességek**területen győződjön meg arról, hogy a **mikrofon** be van jelölve, és mentse a fájlt.

1. A következő dupla kattintásos `Package.appxmanifest` fájl a `helloworld.UWP` Visual Studióban, a **képességek**  >  **mikrofon** alatt pedig be van jelölve, és mentse a fájlt.
   > Megjegyzés: Ha a figyelmeztetést látja, a tanúsítványfájl nem létezik: HelloWorld. UWP_TemporaryKey. pfx, további információért tekintse [meg a beszédfelismerés szövegbe](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp) mintát.

1. A menüsávban válassza a **fájl**  >  **mentése** lehetőséget a módosítások mentéséhez.

## <a name="build-and-run-the-uwp-application"></a>A UWP-alkalmazás létrehozása és futtatása

1. HelloWorld beállítása. A UWP indítási projektként szolgál. Kattintson a jobb gombbal a HelloWorld. UWP projekt, majd válassza a **Build** elemet az alkalmazás létrehozásához.

1. **Debug**  >  Az alkalmazás indításához válassza a hibakeresés**indítása** (vagy az **F5 billentyűt**) lehetőséget. Megjelenik a **HelloWorld** ablak.

   ![Minta UWP beszédfelismerési alkalmazás a C#-ben – gyors útmutató](../../../../media/sdk/qs-csharp-xamarin-helloworld-uwp-window.png)

1. Válassza a **mikrofon engedélyezése**lehetőséget. Ha megjelenik a hozzáférési engedélyre vonatkozó kérelem, válassza az **Igen**lehetőséget.

   ![Mikrofon-hozzáférési engedély kérése](../../../../media/sdk/qs-csharp-xamarin-uwp-access-prompt.png)

1. Válassza a **beszédfelismerés elindítása**lehetőséget, és beszéljen egy angol kifejezéssel vagy mondattal az eszköz mikrofonjában. A rendszer továbbítja a beszédet a Speech Service-be, majd szöveggé alakítja át, amely ugyanabban az ablakban meg is jelenik.

   ![Beszédfelismerés felhasználói felülete](../../../../media/sdk/qs-csharp-xamarin-uwp-ui-result.png)
* * *

## <a name="build-and-run-the-android-and-ios-applications"></a>Android-és iOS-alkalmazások létrehozása és futtatása

Az Android-és iOS-alkalmazások kiépítése és futtatása az eszközön vagy a szimulátoron hasonló módon történik a UWP. Győződjön meg arról, hogy az összes SDK megfelelően van telepítve a jelen cikk "Előfeltételek" szakaszában.

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]
