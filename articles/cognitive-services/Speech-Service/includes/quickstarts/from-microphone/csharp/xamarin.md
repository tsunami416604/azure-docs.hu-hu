---
title: 'Rövid útmutató: Beszédfelismerés mikrofonból, C# (Xamarin) – Beszédszolgáltatás'
titleSuffix: Azure Cognitive Services
description: Ebben a cikkben hozzon létre egy platformfüggetlen C# Xamarin alkalmazást az univerzális Windows-platformhoz (UWP), Androidhoz és iOS-hez a Cognitive Services Speech SDK használatával. A beszéd eket valós időben írja át az eszköz vagy a szimulátor mikrofonjából. Az alkalmazás a Speech SDK NuGet csomaggal és a Microsoft Visual Studio 2019-tel készült.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/02/2020
ms.author: erhopf
ms.openlocfilehash: 699bbea9d50c24558f6c9d4671cb6035e4718a43
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2020
ms.locfileid: "80671553"
---
## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdene járni:

> [!div class="checklist"]
> * [Azure-beszédfelismerési erőforrás létrehozása](../../../../get-started.md)
> * [A fejlesztői környezet beállítása és üres projekt létrehozása](../../../../quickstarts/setup-platform.md?tabs=xamarin&pivots=programming-language-csharp)
> * Győződjön meg arról, hogy rendelkezik a mikrofonhoz a hangrögzítéshez

Ha ezt már megtetted, remek. Menjünk tovább.

## <a name="add-sample-code-for-the-common-helloworld-project"></a>Mintakód hozzáadása a közös helloworld projekthez

A közös helloworld projekt platformfüggetlen implementációkat tartalmaz a platformfüggetlen alkalmazáshoz. Most adja hozzá az XAML-kódot, amely meghatározza az alkalmazás felhasználói felületét, és adja hozzá a C# kódot a megvalósítás mögött.

1. A **Solution Explorer**programban a közös `MainPage.xaml`helloworld projekt alatt nyissa meg a .

1. A tervező XAML nézetében szúrja be a következő XAML-kódrészletet a **Rács** címkébe a következők között: `<StackLayout>` `</StackLayout>`

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld/MainPage.xaml)]

1. A **Megoldáskezelőben**nyissa meg a `MainPage.xaml.cs`mögötti kód forrásfájlt. A csoport ban `MainPage.xaml`van csoportosítva.

1. Cserélje le az összes benne lévő kódot a következő kódrészletre:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld/MainPage.xaml.cs)]

1. A forrásfájl kezelőjében `OnRecognitionButtonClicked` keresse `YourSubscriptionKey`meg a karakterláncot , és cserélje le az előfizetési kulcsra.


1. A `OnRecognitionButtonClicked` kezelőben keresse `YourServiceregion`meg a karakterláncot , és cserélje le az előfizetéshez társított [régió](https://aka.ms/speech/sdkregion) **régiórégiórégiórégióból** származó régióra. (Például használja `westus` az ingyenes próba-előfizetéshez.)

1. Ezután létre kell [hoznia egy Xamarin-szolgáltatást,](https://docs.microsoft.com/xamarin/android/app-fundamentals/services/creating-a-service/)amely különböző platformprojektek, például UWP, Android és iOS mikrofonengedélyeinek lekérdezésére szolgál. Ehhez adjon hozzá egy új mappát *Szolgáltatások* a helloworld projekt alatt, és hozzon létre egy új C# forrásfájlt alatta. Kattintson a jobb gombbal a *Szolgáltatások* mappára, és válassza **az** > **Új cikkkódfájl** > hozzáadása**parancsot**. Nevezze át `IMicrophoneService.cs`a fájlt , és helyezze az összes kódot a következő kódrészletből a fájlba:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld/Services/IMicrophoneService.cs)]

#### <a name="android"></a>[Android](#tab/x-android)
## <a name="add-sample-code-for-the-helloworldandroid-project"></a>Mintakód hozzáadása `helloworld.Android` a projekthez

Most adja hozzá a C# kódot, amely meghatározza az android-specifikus része az alkalmazás.

1. A **Solution Explorer**, a helloworld alatt. Android projekt, `MainActivity.cs`nyitott .

1. Cserélje le az összes benne lévő kódot a következő kódrészletre:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.Android/MainActivity.cs)]

1. Ezután add android-specifikus `MicrophoneService` megvalósítás létrehozásával az új mappa *Szolgáltatások* a helloworld. Android projekt. Ezután hozzon létre egy új C# forrásfájlt alatta. Nevezze át `MicrophoneService.cs`a fájlt . Másolja a fájlt a következő kódrészletbe, és illessze be a fájlt:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.Android/Services/MicrophoneService.cs)]

1. Ezután nyissa `AndroidManifest.xml` meg a *Tulajdonságok* mappát. Adja hozzá a mikrofon következő felhasználás-engedély beállítását a következők közé: `<manifest>` `</manifest>`

   ```xml
   <uses-permission android:name="android.permission.RECORD_AUDIO" />
   ```
   
#### <a name="ios"></a>[iOS](#tab/ios)
## <a name="add-sample-code-for-the-helloworldios-project"></a>Mintakód hozzáadása `helloworld.iOS` a projekthez

Most adja hozzá a C# kódot, amely meghatározza az alkalmazás iOS-specifikus részét. Hozzon létre Apple-eszközspecifikus konfigurációkat is a helloworld.iOS projekthez.

1. A **Solution Explorer**programban a helloworld.iOS projekt alatt nyissa meg a megnyitását. `AppDelegate.cs`

1. Cserélje le az összes benne lévő kódot a következő kódrészletre:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.iOS/AppDelegate.cs)]

1. Ezután adja hozzá az `MicrophoneService` iOS-specifikus implementációt az új mappa *szolgáltatások* helloworld.iO projekt alatt történő létrehozásával. Ezután hozzon létre egy új C# forrásfájlt alatta. Nevezze át `MicrophoneService.cs`a fájlt . Másolja a fájlt a következő kódrészletbe, és illessze be a fájlt:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.iOS/Services/MicrophoneService.cs)]

1. Nyissa `Info.plist` meg a helloworld.iOS projekt alatt a szövegszerkesztőben. Adja hozzá a következő kulcsértékpárt a diktálási szakaszhoz:

   <key>NSMicrophoneUsageDescription</key>
   <string>Ez a mintaalkalmazás mikrofonhozzáférést igényel</string>

   > [!NOTE]
   > Ha iPhone-eszközhöz épít, győződjön `Bundle Identifier` meg arról, hogy az megfelel az eszköz kiépítési profilalkalmazás-azonosítójának. Ellenkező esetben a build sikertelen lesz. Az iPhoneSimulator, akkor hagyja, ahogy van.

1. Ha Windows RENDSZERű számítógépre épít, hozzon létre kapcsolatot a Mac-eszközzel az**IOS-pár** >  **mac-es eszközökkel** > történő**kiépítéséhez.** A Mac-eszközzel való csatlakozás engedélyezéséhez kövesse a Visual Studio által biztosított utasításvarázslót.

#### <a name="uwp"></a>[UWP](#tab/helloworlduwp)
## <a name="add-sample-code-for-the-helloworlduwp-project"></a>Mintakód hozzáadása `helloworld.UWP` a projekthez

## <a name="add-sample-code-for-the-helloworlduwp-project"></a>Adja hozzá a helloworld mintakódját. UWP-projekt

Most adja hozzá a C# kódot, amely meghatározza az alkalmazás UWP-specifikus részét.

1. A **Solution Explorer**, a helloworld alatt. UWP projekt, `MainPage.xaml.cs`megnyitás.

1. Cserélje le az összes benne lévő kódot a következő kódrészletre:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.UWP/MainPage.xaml.cs)]

1. Ezután adjon hozzá egy UWP-specifikus implementációt `MicrophoneService` az új szolgáltatás mappa létrehozásával a helloworld alatt. *Services* UWP projekt. Ezután hozzon létre egy új C# forrásfájlt alatta. Nevezze át `MicrophoneService.cs`a fájlt . Másolja a fájlt a következő kódrészletbe, és illessze be a fájlt:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.UWP/Services/MicrophoneService.cs)]

1. Ezután kattintson `Package.appxmanifest` duplán a helloworld alatti fájlra. UWP-projekt a Visual Studio-ban. A **Képességek csoportban**győződjön meg arról, hogy a **Mikrofon** jelölőnégyzet be van jelölve, és mentse a fájlt.

1. Következő dupla `Package.appxmanifest` kattintás `helloworld.UWP` a fájl alatt a projekt belül Visual Studio és a **Képességek** > **mikrofon** ellenőrzi, és mentse a fájlt.
   > Megjegyzés: Abban az esetben, ha megjelenik figyelmeztetés : Tanúsítvány fájl nem létezik: helloworld. UWP_TemporaryKey.pfx, kérjük, ellenőrizze [beszéd szöveges](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp) minta további információkért.

1. A módosítások mentéséhez válassza a **menüsorban** > az**Összes mentése** lehetőséget.

## <a name="build-and-run-the-uwp-application"></a>Az UWP-alkalmazás létrehozása és futtatása

1. Állítsa be a helloworld-öt. UWP indítási projektként. Kattintson a jobb gombbal a helloworldre. UWP-projekt, és válassza **a Build az** alkalmazás létrehozásához.

1. Az alkalmazás elindításához válassza a **Hibakeresési** > **indítás i munkógombot** (vagy az **F5)** lehetőséget. Megjelenik **a helloworld** ablak.

   ![Minta UWP beszédfelismerő alkalmazás C# nyelven – rövid útmutató](../../../../media/sdk/qs-csharp-xamarin-helloworld-uwp-window.png)

1. Válassza **a Mikrofon engedélyezése**lehetőséget. Amikor megjelenik a hozzáférési engedélykérelem, válassza az **Igen**lehetőséget.

   ![Mikrofonhozzáférési engedély kérése](../../../../media/sdk/qs-csharp-xamarin-uwp-access-prompt.png)

1. Válassza **a Beszédfelismerés indítása**lehetőséget, és beszéljen egy angol kifejezést vagy mondatot az eszköz mikrofonjába. A rendszer továbbítja a beszédet a Speech Service-be, majd szöveggé alakítja át, amely ugyanabban az ablakban meg is jelenik.

   ![Beszédfelismerés felhasználói felülete](../../../../media/sdk/qs-csharp-xamarin-uwp-ui-result.png)
* * *

## <a name="build-and-run-the-android-and-ios-applications"></a>Android- és iOS-alkalmazások készítése és futtatása

Az Android és iOS alkalmazások létrehozása és futtatása a készülékben vagy szimulátorban hasonló módon történik, mint az UWP. Győződjön meg arról, hogy az összes SDK megfelelően van telepítve a cikk "Előfeltételek" című részében előírt módon.

## <a name="next-steps"></a>További lépések

[!INCLUDE [footer](../footer.md)]
