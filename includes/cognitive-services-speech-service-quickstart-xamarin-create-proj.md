---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 09/13/2019
ms.author: erhopf
ms.openlocfilehash: 0e4e67710c98b80dce2b0d55a86869625f3942d2
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2019
ms.locfileid: "71837475"
---
Ha Visual Studio-projektet szeretne létrehozni a platformfüggetlen Mobile App .NET-fejlesztéshez a Xamarin-mel, be kell állítania a Visual Studio fejlesztői beállításait, létre kell hoznia a projektet, ki kell választania a cél architektúrát, és telepítenie kell a Speech SDK-t.

### <a name="set-up-visual-studio-development-options"></a>A Visual Studio fejlesztési lehetőségeinek beállítása

A kezdéshez győződjön meg arról, hogy helyesen van beállítva a Visual Studióban a többplatformos mobil fejlesztéshez a .NET használatával:

1. Nyissa meg a Visual Studio 2019 alkalmazást.

1. A Visual Studio menüsávjában válassza az **eszközök** > **eszközök és szolgáltatások beolvasása** lehetőséget a Visual Studio telepítőjének megnyitásához, és tekintse meg a **módosítás** párbeszédpanelt.

   ![Munkaterhelések lap, módosítás párbeszédpanel, Visual Studio-telepítő](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-workload.png)

1. A **munkaterhelések** lapon, a **Windows**alatt keresse meg a **Mobile Development és a .net** számítási feladatok elemet. Ha a számítási feladat melletti jelölőnégyzet már be van jelölve, akkor a **módosítás** párbeszédpanelt, és lépjen az 5. lépésre.

1. Jelölje be a **Mobile Development a .net** -tel jelölőnégyzetet, válassza a **módosítás**lehetőséget, majd az **első lépések előtt** kattintson a **tovább** gombra a Mobile Development .net számítási feladattal való telepítéséhez. Az új szolgáltatás telepítése hosszabb időt is igénybe vehet.

1. Zárjuk be a Visual Studio telepítőjét.

### <a name="create-the-project"></a>A projekt létrehozása

1. A Visual Studio menüsávban válassza a **fájl** > **új** > **projekt** lehetőséget az **új projekt létrehozása** ablak megjelenítéséhez.

   ![Új projekt létrehozása – Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-create-new-project.png)

1. Keresse meg és válassza ki a **Mobile App (Xamarin Forms)** elemet.

1. Kattintson a **tovább** gombra az **új projekt konfigurálása** képernyő megjelenítéséhez. 

   ![Az új projekt konfigurálása – Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-configure-your-new-project.png)

1. A **Project Name (projekt neve**) mezőben adja meg a `helloworld` értéket.

1. A **hely**területen navigáljon, és válassza ki vagy hozza létre azt a mappát, amelybe menteni szeretné a projektet.

1. Válassza a **Létrehozás** lehetőséget az **új Mobile apps Xamarin űrlapok projekt** ablak megnyitásához.

   ![Új Univerzális Windows-platform projekt párbeszédpanel – Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-xamarin-new-xamarin-project.png)

1. **Üres** sablon kiválasztása

1. A **platform**területen keresse meg az **Android**, **iOS** és **Windows rendszerhez készült jelölőnégyzeteket (UWP)** .

1. Kattintson az **OK** gombra. A rendszer visszaadja a Visual Studio IDE-nek, és a **megoldáskezelő** ablaktáblán létrehozott és látható új projekttel rendelkezik.

   ![HelloWorld projekt – Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-helloworld.png)

Most válassza ki a cél platform architektúráját és az indítási projektet. A Visual Studio eszköztárán keresse meg a **megoldás platformok** legördülő listát. (Ha nem látja, válassza a **megtekintés** > **eszköztárak**@no__t – 3**standard** lehetőséget a **megoldási platformokat**tartalmazó eszköztár megjelenítéséhez.) Ha 64 bites Windows rendszert futtat, válassza a legördülő listából az **x64** lehetőséget. a 64 bites Windows rendszer 32 bites alkalmazásokat is futtathat, így ha szeretné, válassza az **x86** lehetőséget. Az **indítási projektek** legördülő lista HelloWorld beállítása. UWP (univerzális Windows).

### <a name="install-the-speech-sdk"></a>A Speech SDK telepítése

Telepítse a [SPEECH SDK NuGet-csomagot](https://aka.ms/csspeech/nuget), és hivatkozzon a projektben található Speech SDK-ra:

1. **Megoldáskezelőban**kattintson a jobb gombbal a megoldásra, és válassza a megoldás **NuGet-csomagok kezelése** lehetőséget, hogy megnyissa a **NuGet-megoldás** ablakát.

1. Válassza a **Tallózás** lehetőséget.

   ![A megoldás csomagjainak kezelésére szolgáló párbeszédpanel képernyőképe](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-nuget-solution-browse.png)

1. A **csomag forrása**területen válassza a **nuget.org**lehetőséget.

1. A **keresőmezőbe** írja be a `Microsoft.CognitiveServices.Speech` értéket, majd válassza ki a csomagot, miután az megjelenik a keresési eredmények között.

   ![A megoldás csomagjainak kezelésére szolgáló párbeszédpanel képernyőképe](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-xamarin-nuget-install.png)
   > Megjegyzés: a Microsoft. CognitiveServices. Speech nuget lévő iOS-függvénytárat nem engedélyezte a bitcode. Ha bitcode-kompatibilis függvénytárra van szüksége az alkalmazáshoz, kifejezetten a Microsoft. CognitiveServices. Speech. Xamarin. iOS nuget használja az iOS-projekthez.

1. A keresési eredmények melletti csomag állapota ablaktáblán válassza a minden projekt lehetőséget. **HelloWorld**, **HelloWorld. Android**, **HelloWorld. iOS** és **HelloWorld. UWP**.

1. Válassza az **Install** (Telepítés) lehetőséget.

1. A **módosítások előnézete** párbeszédpanelen kattintson **az OK gombra**.

1. A **licenc elfogadása** párbeszédpanelen tekintse meg a licencet, majd válassza az **Elfogadom** lehetőséget, és telepítse a Speech SDK-csomag hivatkozását az összes projektre. A telepítés sikeres befejezése után a következő figyelmeztetés jelenhet meg a HelloWorld. iOS esetében. Ez ismert probléma, és nem befolyásolja az alkalmazás funkcióit.

> Nem oldható fel a következő hivatkozás: "C:\Users\Default @ no__t-0nuget\packages\microsoft.cognitiveservices.speech\1.7.0\build\Xamarin.iOS\libMicrosoft.CognitiveServices.Speech.core.a". Ha ezt a hivatkozást a kód megköveteli, fordítási hibákat kaphat.
