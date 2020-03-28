---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 02/10/2020
ms.author: erhopf
ms.openlocfilehash: 6804834dab81b0774dd29b1ede836492d0dfea4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78383966"
---
Visual Studio-projekt univerzális Windows-platformra (UWP) szóló fejlesztéséhez be kell állítania a Visual Studio fejlesztési beállításait, létre kell hoznia a projektet, ki kell választania a célarchitektúrát, be kell állítania a hangrögzítést, és telepítenie kell a Beszéd sdk-t.

### <a name="set-up-visual-studio-development-options"></a>A Visual Studio fejlesztési beállításainak megadása

A kezdéshez győződjön meg arról, hogy helyesen van beállítva a Visual Studio az UWP fejlesztésében:

1. A Start **ablak** megjelenítéséhez nyissa meg a Visual Studio 2019-et.

   ![Kezdőablak – Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-start-window.png)

1. Válassza a **Folytatás kód nélkül** lehetőséget a Visual Studio IDE megugrásához.

1. A Visual Studio menüsorából válassza az **Eszközök** > **betöltőeszközök és szolgáltatások** lehetőséget a Visual Studio Installer megnyitásához és a **Módosítás** párbeszédpanel megtekintéséhez.

   ![Számítási feladatok lap, Módosítás párbeszédpanel, Visual Studio telepítő](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-workload.png)

1. A **Számítási feladatok** lap **Windows**csoportjában keresse meg az **univerzális Windows-platform fejlesztési** munkaterhelését. Ha a munkaterhelés melletti jelölőnégyzet már be van jelölve, zárja be a **Módosítás párbeszédpanelt,** és folytassa a 6.

1. Jelölje be az **Univerzális Windows-platform fejlesztésének** jelölőnégyzetet, válassza a **Módosítás**lehetőséget, majd a **Mielőtt elkezdenénk** a párbeszédpanelen, válassza az UWP fejlesztési munkaterhelés telepítésének **folytatása** lehetőséget. Az új funkció telepítése eltarthat egy ideig.

1. Zárja be a Visual Studio telepítőjét.

### <a name="create-the-project-and-select-the-target-architecture"></a>A projekt létrehozása és a célarchitektúra kiválasztása

Ezután hozza létre a projektet:

1. A Visual Studio menüsorában válassza az**Új** > **projekt** **fájlja** > lehetőséget az **Új projekt létrehozása** ablak megjelenítéséhez.

   ![Új projekt létrehozása - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-create-new-project.png)

1. Keresse meg és válassza **az Üres alkalmazás (Univerzális Windows) lehetőséget.** Győződjön meg arról, hogy a projekttípus C# verzióját választja ki (nem pedig a Visual Basic et).

1. Válassza a **Tovább** gombot az **Új projekt konfigurálása** képernyő megjelenítéséhez.

   ![Az új projekt konfigurálása - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-configure-your-new-project.png)

1. A **Projekt**neve `helloworld`mezőbe írja be a be írt értéket.

1. A **Hely területen**keresse meg a projektmentéshez használt mappát, jelölje ki vagy hozza létre azt a mappát, amelyben menteni szeretné a projektet.

1. Válassza a **Létrehozás** gombot az **Új univerzális Windows platformprojekt** ablak ba.

   ![Új univerzális Windows Platform Projekt párbeszédpanel – Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-02-new-uwp-project.png)

1. A **Minimális verzió** (a második legördülő lista) mezőben válassza a Windows **10 Fall Creators Update (10.0; Build 16299)**, amely a beszédsdk minimális követelménye.

1. A **Célverzió (az** első legördülő lista) mezőben válasszon egy olyan értéket, amely megegyezik a Minimális verzió értékkel vagy azzal **későbbiértékkel.**

1. Válassza **az OK gombot.** A rendszer visszatér a Visual Studio IDE-be, és az új projekt et létrekell hoznia, és láthatóvá kell tennia a **Megoldáskezelő** ablaktáblában.

   ![helloworld projekt - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-helloworld.png)

Most válassza ki a célplatform architektúráját. A Visual Studio eszköztárán keresse meg a **Megoldásplatformok** legördülő lista. (Ha nem látja, válassza az**Eszköztárak** > **szokásos** **megtekintése** > lehetőséget a **Megoldásplatformokat**tartalmazó eszköztár megjelenítéséhez.) Ha 64 bites Windows rendszert futtat, válassza az **x64** lehetőséget a legördülő menüben. A 64 bites Windows 32 bites alkalmazásokat is képes futtatni, így az **x86-ot** is választhatja.

> [!NOTE]
> A Speech SDK csak az Intel-kompatibilis processzorokat támogatja. Az ARM processzorok jelenleg nem támogatottak.

### <a name="set-up-audio-capture"></a>Hangrögzítés beállítása

Ezután hagyja, hogy a projekt rögzítse a hangbemenetet:

1. A **Solution Explorer**ben kattintson duplán a **Package.appxmanifest** fájlra a csomagalkalmazás-jegyzékfájl megnyitásához.

1. Válassza a **Képességek** lapot.

   ![Képességek lap, Csomagalkalmazás jegyzékfájlja – Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-07-capabilities.png)

1. Jelölje be a Mikrofon funkció **jelölőnégyzetét.**

1. A módosítások mentéséhez válassza a **Menüsor Fájlmentése** > **csomag.appxmanifest** elemét.

### <a name="install-the-speech-sdk"></a>A beszédfelismerési SDK telepítése

Végül telepítse a [Speech SDK NuGet csomagot](https://aka.ms/csspeech/nuget), és hivatkozzon a beszédfelismerési SDK-ra a projektben:

1. A **Megoldáskezelőben**kattintson a jobb gombbal a megoldásra, és válassza a **NuGet csomagok kezelése a megoldáshoz parancsot** a **NuGet - Megoldás** ablakba való ugráshoz.

1. Válassza a **Tallózás** lehetőséget.

   ![A megoldás csomagjainak kezelésére szolgáló párbeszédpanel képernyőképe](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-nuget-solution-browse.png)

1. A **Csomagforrás ban**válassza **a nuget.org**lehetőséget.

1. A **Keresés** mezőbe `Microsoft.CognitiveServices.Speech`írja be a t, majd válassza ki a csomagot, miután megjelenik a keresési eredmények között.

   ![A megoldás csomagjainak kezelésére szolgáló párbeszédpanel képernyőképe](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-05-nuget-install-1.0.0.png)

1. A keresési eredmények melletti csomagállapot-panelen válassza ki a **helloworld** projektet.

1. Válassza az **Install** (Telepítés) lehetőséget.

1. A **Változások megtekintése** párbeszédpanelen válassza az **OK gombot.**

1. A **Licencelfogadás** párbeszédpanelen tekintse meg a licencet, majd válassza az **Elfogadom**lehetőséget. A csomag telepítése megkezdődik, és **Output** a telepítés befejeztével a Kimenet `Successfully installed 'Microsoft.CognitiveServices.Speech 1.10.0' to helloworld`ablaktábla a következő höz hasonló üzenetet jelenít meg: .
