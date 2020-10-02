---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 09/13/2019
ms.author: erhopf
ms.openlocfilehash: 35133b64e43ca3814410d7bbf2ac1be9f760b369
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2020
ms.locfileid: "91654403"
---
Ha Visual Studio-projektet szeretne létrehozni a platformfüggetlen Mobile App .NET-fejlesztéshez a Xamarin-mel, a következőket kell tennie:
- A Visual Studio fejlesztési lehetőségeinek beállítása.
- Hozza létre a projektet, és válassza ki a cél architektúrát. 
- Telepítse a Speech SDK-t.

### <a name="set-up-visual-studio-development-options"></a>A Visual Studio fejlesztési lehetőségeinek beállítása

A kezdéshez győződjön meg arról, hogy helyesen van beállítva a Visual Studióban a többplatformos mobil fejlesztéshez a .NET használatával:

1. Nyissa meg a Visual Studio 2019 alkalmazást.

1. A Visual Studio menüsávjában válassza az **eszközök**eszközök  >  **és szolgáltatások beolvasása** lehetőséget a Visual Studio telepítőjének megnyitásához, és tekintse meg a **módosítás** párbeszédpanelt.

   ![A munkaterhelések lapot, a módosítás párbeszédpanelt és a Visual Studio telepítőjét bemutató képernyőkép](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-workload.png)

1. A **munkaterhelések** lapon, a **Windows**alatt keresse meg a **Mobile Development** szolgáltatást a .net munkaterhelés használatával. Ha a számítási feladat melletti jelölőnégyzet már be van jelölve, akkor a **módosítás** párbeszédpanelt, és lépjen az 5. lépésre.

1. Jelölje be a **Mobile Development a .net** -tel jelölőnégyzetet, majd kattintson a **módosítás**gombra. Az **első lépések** párbeszédpanelen válassza a **tovább** lehetőséget a Mobile Development .net-munkaterheléssel való telepítéséhez. Az új szolgáltatás telepítése hosszabb időt is igénybe vehet.

1. Zárjuk be a Visual Studio telepítőjét.

### <a name="create-the-project"></a>A projekt létrehozása

1. A Visual Studio menüsávján válassza a **fájl**  >  **új**  >  **projekt** elemet az **új projekt létrehozása** ablak megjelenítéséhez.

   ![Képernyőkép, amely bemutatja, hogyan hozhat létre új projektet a Visual Studióban.](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-create-new-project.png)

1. Keresse meg és válassza ki a **Mobile App (Xamarin. Forms)** elemet.

1. Kattintson a **tovább** gombra az **új projekt konfigurálása** képernyő megjelenítéséhez.

   ![Képernyőkép, amely bemutatja, hogyan konfigurálhatja az új projektet a Visual Studióban.](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-configure-your-new-project.png)

1. A **Project Name (projekt neve**) mezőben adja meg a *HelloWorld*.

1. A **hely**mezőben válassza ki a mappát, és válassza ki vagy hozza létre azt a mappát, amelybe menteni szeretné a projektet.

1. Válassza a **Létrehozás** lehetőséget az **új Mobile apps Xamarin űrlapok projekt** ablak megnyitásához.

   ![A Visual Studióban látható új Univerzális Windows-platform projekt párbeszédpanelt megjelenítő képernyőkép.](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-xamarin-new-xamarin-project.png)

1. Válassza ki az **üres** sablont.

1. A **platform**területen válassza az **Android**, **iOS**és **Windows rendszerhez készült (UWP)** jelölőnégyzetet.

1. Válassza az **OK** lehetőséget. A rendszer visszaadja a Visual Studio IDE-nek, és a **megoldáskezelő** ablaktáblán létrehozott és látható új projekttel rendelkezik.

   ![A HelloWorld projekt – Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-helloworld.png)

Most válassza ki a cél platform architektúráját és indítási projektjét. A Visual Studio eszköztárán keresse meg a **megoldás platformok** legördülő listát. (Ha nem látja, válassza a **nézet**  >  lehetőséget. **Eszköztárak**  >  **Standard** a **megoldási platformokat**tartalmazó eszköztár megjelenítéséhez.) Ha 64 bites Windows rendszert futtat, válassza a legördülő listából az **x64** elemet. Ha azt szeretné, hogy a 64-bites Windows is képes legyen a 32 bites alkalmazások futtatására, válassza az **x86** lehetőséget. Az **indítási projektek** legördülő listában állítsa be a **HelloWorld. UWP (univerzális Windows)**.

### <a name="install-the-speech-sdk"></a>A Speech SDK telepítése

Telepítse a [SPEECH SDK NuGet-csomagot](https://aka.ms/csspeech/nuget), és hivatkozzon a projektben található Speech SDK-ra.

1. A **megoldáskezelő**kattintson a jobb gombbal a megoldásra. Válassza a **megoldás NuGet-csomagok kezelése** lehetőséget a **NuGet-megoldás** ablak megnyitásához.

1. Válassza a **Tallózás** lehetőséget.

   ![Képernyőkép a megoldási csomagok kezelése párbeszédpanelről a Speech SDK telepítésekor.](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-nuget-solution-browse.png)

1. A **csomag forrása**területen válassza a nuget.org lehetőséget.

1. A **keresőmezőbe** írja be a *Microsoft. CognitiveServices. Speech*kifejezést. Ezután válassza ki a csomagot, miután megjelenik a keresési eredmények között.

   ![Képernyőkép a Microsoft. CognitiveServices. Speech csomag kiemeléséről.](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-xamarin-nuget-install.png)

   > [!NOTE] 
   > A NuGet-en belüli iOS-könyvtárnak nincs `Microsoft.CognitiveServices.Speech` engedélyezve a bitcode. Ha az bitcode-függvénytárat engedélyezve kell lennie az alkalmazásához, használja `Microsoft.CognitiveServices.Speech.Xamarin.iOS` a NuGet-t az iOS-projekthez.

1. A keresési eredmények melletti csomag állapota ablaktáblán válassza a minden projekt: **HelloWorld**, **HelloWorld lehetőséget. Android**, **HelloWorld. iOS**és **HelloWorld. UWP**.

1. Válassza a **Telepítés** lehetőséget.

1. A **módosítások előnézete** párbeszédpanelen kattintson **az OK gombra**.

1. A **licenc elfogadása** párbeszédpanelen tekintse meg a licencet, majd válassza az **Elfogadom**lehetőséget. Telepítse a Speech SDK-csomag hivatkozását az összes projektre. A telepítés sikeres befejezése után a következő figyelmeztetés jelenhet meg a HelloWorld. iOS esetében. Ez egy ismert probléma, és nem érintheti az alkalmazás funkcióit.

   > Nem oldható fel a következő hivatkozás: "C:\Users\Default \. nuget\packages\microsoft.cognitiveservices.speech\1.7.0\build\Xamarin.iOS\libMicrosoft.CognitiveServices.Speech.Core.a". Ha ezt a hivatkozást a kód megköveteli, fordítási hibákat kaphat.
