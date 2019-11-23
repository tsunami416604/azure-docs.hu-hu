---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/19/2019
ms.author: erhopf
ms.openlocfilehash: 64c93e5c8e139dbdcfd3faad3a9ac4328fb01972
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72391658"
---
Ha Univerzális Windows-platform (UWP) fejlesztéshez szeretne létrehozni Visual Studio-projektet, be kell állítania a Visual Studio fejlesztői beállításait, létre kell hoznia a projektet, ki kell választania a cél architektúrát, be kell állítania a hangrögzítést, és telepítenie kell a Speech SDK-t.

### <a name="set-up-visual-studio-development-options"></a>A Visual Studio fejlesztési lehetőségeinek beállítása

A kezdéshez győződjön meg arról, hogy helyesen van beállítva a Visual Studióban a UWP-fejlesztéshez:

1. Nyissa meg a Visual Studio 2019 alkalmazást a **Start** ablak megjelenítéséhez.

   ![Start ablak – Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-start-window.png)

1. Válassza a **Folytatás kód nélkül** lehetőséget a Visual Studio ide elemre.

1. A Visual Studio menüsávjában válassza az **eszközök** > **eszközök és szolgáltatások beolvasása** lehetőséget a Visual Studio telepítőjének megnyitásához, és tekintse meg a **módosítás** párbeszédpanelt.

   ![Munkaterhelések lap, módosítás párbeszédpanel, Visual Studio-telepítő](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-workload.png)

1. A **munkaterhelések** lapon a **Windows**területen keresse meg a **univerzális Windows-platform-fejlesztési** számítási feladatot. Ha a számítási feladat melletti jelölőnégyzet már be van jelölve, akkor a **módosítás** párbeszédpanelt, és folytassa a 6. lépéssel.

1. Jelölje be a **univerzális Windows-platform fejlesztés** jelölőnégyzetet, válassza a **módosítás**lehetőséget, majd az **első lépések** PÁRBESZÉDPANELEN válassza a **tovább** lehetőséget a UWP-fejlesztési számítási feladat telepítéséhez. Az új szolgáltatás telepítése hosszabb időt is igénybe vehet.

1. Zárjuk be a Visual Studio telepítőjét.

### <a name="create-the-project-and-select-the-target-architecture"></a>Hozza létre a projektet, és válassza ki a cél architektúrát

Ezután hozza létre a projektet:

1. A Visual Studio menüsávjában válassza a **fájl** > **új** > **projekt** lehetőséget az **új projekt létrehozása** ablak megjelenítéséhez.

   ![Új projekt létrehozása – Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-create-new-project.png)

1. Keresse meg és válassza ki az **üres alkalmazást (univerzális Windows)** . Győződjön meg arról, hogy kijelöli a C# projekt típusának verzióját (a Visual Basic helyett).

1. Kattintson a **tovább** gombra az **új projekt konfigurálása** képernyő megjelenítéséhez. 

   ![Az új projekt konfigurálása – Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-configure-your-new-project.png)

1. A **Project Name (projekt neve**) mezőben adja meg a `helloworld`.

1. A **hely**területen navigáljon, és válassza ki vagy hozza létre azt a mappát, amelybe menteni szeretné a projektet.

1. Válassza a **Létrehozás** lehetőséget az **új univerzális Windows-platform projekt** ablak megnyitásához.

   ![Új Univerzális Windows-platform projekt párbeszédpanel – Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-02-new-uwp-project.png)

1. A **minimális verzióban** (a második legördülő listában) válassza a **Windows 10 Fall Creators Update (10,0; Build 16299)** , amely a SPEECH SDK minimális követelménye.

1. A **cél verziója** (az első legördülő lista) mezőben válasszon egy azonos vagy annál újabb értéket a **minimális verziónál**.

1. Kattintson az **OK** gombra. A rendszer visszaadja a Visual Studio IDE-nek, és a **megoldáskezelő** ablaktáblán létrehozott és látható új projekttel rendelkezik.

   ![HelloWorld projekt – Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-helloworld.png)

Most válassza ki a cél platform architektúráját. A Visual Studio eszköztárán keresse meg a **megoldás platformok** legördülő listát. (Ha nem látja, válassza a **megtekintés** > **eszköztárak** > **standard** lehetőséget a **megoldási platformokat**tartalmazó eszköztár megjelenítéséhez.) Ha 64 bites Windows rendszert futtat, válassza a legördülő listából az **x64** lehetőséget. a 64 bites Windows rendszer 32 bites alkalmazásokat is futtathat, így ha szeretné, válassza az **x86** lehetőséget.

> [!NOTE]
> A Speech SDK csak az Intel-kompatibilis processzorokat támogatja. Az ARM-processzorok jelenleg nem támogatottak.

### <a name="set-up-audio-capture"></a>Hangrögzítés beállítása

Ezután engedélyezze a projektnek a hangbemenet rögzítését:

1. A **megoldáskezelő**kattintson duplán a **Package. appxmanifest** elemre a Package Application manifest megnyitásához.

1. Válassza a **képességek** fület.

   ![Képességek lap Package Application manifest – Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-07-capabilities.png)

1. Jelölje be a **mikrofon** képességhez tartozó jelölőnégyzetet.

1. A menüsávban válassza a **fájl** > **Mentés csomag. appxmanifest** lehetőséget a módosítások mentéséhez.

### <a name="install-the-speech-sdk"></a>A Speech SDK telepítése

Végül telepítse a [SPEECH SDK NuGet-csomagot](https://aka.ms/csspeech/nuget), és hivatkozzon a projektben található Speech SDK-ra:

1. **Megoldáskezelőban**kattintson a jobb gombbal a megoldásra, és válassza a megoldás **NuGet-csomagok kezelése** lehetőséget, hogy megnyissa a **NuGet-megoldás** ablakát.

1. Válassza a **Tallózás** lehetőséget.

   ![A megoldás csomagjainak kezelésére szolgáló párbeszédpanel képernyőképe](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-nuget-solution-browse.png)

1. A **csomag forrása**területen válassza a **nuget.org**lehetőséget.

1. A **keresőmezőbe** írja be a `Microsoft.CognitiveServices.Speech`kifejezést, majd válassza ki a csomagot, miután az megjelenik a keresési eredmények között.

   ![A megoldás csomagjainak kezelésére szolgáló párbeszédpanel képernyőképe](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-05-nuget-install-1.0.0.png)

1. A keresési eredmények melletti csomag állapota ablaktáblán válassza ki a **HelloWorld** -projektet.

1. Válassza az **Install** (Telepítés) lehetőséget.

1. A **módosítások előnézete** párbeszédpanelen kattintson **az OK gombra**.

1. A **licenc elfogadása** párbeszédpanelen tekintse meg a licencet, majd válassza az **Elfogadom**lehetőséget. A csomag telepítése megkezdődik, és amikor a telepítés befejeződött, a **kimenet** ablaktáblán a következő szöveghez hasonló üzenet jelenik meg: `Successfully installed 'Microsoft.CognitiveServices.Speech 1.7.0' to helloworld`.
