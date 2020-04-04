---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 02/10/2020
ms.author: erhopf
ms.openlocfilehash: 72e9334b4df58b1b90288cb7363a7d94bb531661
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80658390"
---
Ha C++ szintű fejlesztésre szeretne létrehozni egy Visual Studio-projektet, be kell állítania a Visual Studio fejlesztési beállításait, létre kell hoznia a projektet, ki kell választania a célarchitektúrát, és telepítenie kell a beszédfelismerési SDK-t.

### <a name="set-up-visual-studio-development-options"></a>A Visual Studio fejlesztési beállításainak megadása

A kezdéshez győződjön meg arról, hogy helyesen van beállítva a Visual Studio for C++ asztali fejlesztésben:

1. A Start **ablak** megjelenítéséhez nyissa meg a Visual Studio 2019-et.

   ![Kezdőablak – Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-start-window.png)

1. Válassza a **Folytatás kód nélkül** lehetőséget a Visual Studio IDE megugrásához.

1. A Visual Studio menüsorából válassza az **Eszközök** > **betöltőeszközök és szolgáltatások** lehetőséget a Visual Studio Installer megnyitásához és a **Módosítás** párbeszédpanel megtekintéséhez.

   ![Számítási feladatok lap, Módosítás párbeszédpanel, Visual Studio telepítő](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-cpp-workload.png)

1. A **Munkaterhelések** lap **Windows**csoportjában keresse meg az **Asztal fejlesztését C++ munkaterheléssel.** Ha a munkaterhelés melletti jelölőnégyzet nincs bejelölve, jelölje be.

1. Az **Egyes összetevők** lapon keresse meg a **Nuget csomagkezelő** jelölőnégyzetet. Ha a jelölőnégyzet még nincs bejelölve, jelölje be.

1. Jelölje ki a **Bezárás** vagy a **Módosítás**feliratú sarokban lévő gombot. (A gomb neve attól függően változik, hogy kiválasztott-e valamilyen szolgáltatást a telepítéshez.) Ha a Módosítás lehetőséget **választja,** megkezdődik a telepítés, ami eltarthat egy ideig.

1. Zárja be a Visual Studio telepítőjét.

### <a name="create-the-project-and-select-the-target-architecture"></a>A projekt létrehozása és a célarchitektúra kiválasztása

Ezután hozza létre a projektet:

1. A Visual Studio menüsorában válassza az**Új** > **projekt** **fájlja** > lehetőséget az **Új projekt létrehozása** ablak megjelenítéséhez.

   ![Új projekt létrehozása, C++ - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-01-new-console-app.png)

1. Keresse meg és válassza a **Konzolalkalmazást**. Győződjön meg arról, hogy a projekttípus C++ verzióját választja ki (szemben a C# vagy a Visual Basic alkalmazással).

1. Válassza a **Tovább** gombot az **Új projekt konfigurálása** képernyő megjelenítéséhez.

   ![Az új projekt konfigurálása, C++ - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-cpp-configure-your-new-project.png)

1. A **Projekt**neve `helloworld`mezőbe írja be a be írt értéket.

1. A **Hely területen**keresse meg a projektmentéshez használt mappát, jelölje ki vagy hozza létre azt a mappát, amelyben menteni szeretné a projektet.

Most válassza ki a célplatform architektúráját. A Visual Studio eszköztárán keresse meg a **Megoldásplatformok** legördülő lista. (Ha nem látja, válassza az**Eszköztárak** > **szokásos** **megtekintése** > lehetőséget a **Megoldásplatformokat**tartalmazó eszköztár megjelenítéséhez.) Ha 64 bites Windows rendszert futtat, válassza az **x64** lehetőséget a legördülő menüben. A 64 bites Windows 32 bites alkalmazásokat is képes futtatni, így az **x86-ot** is választhatja.

### <a name="install-the-speech-sdk"></a>A beszédfelismerési SDK telepítése

Végül telepítse a [Speech SDK NuGet csomagot](https://aka.ms/csspeech/nuget), és hivatkozzon a beszédfelismerési SDK-ra a projektben:

1. A **Megoldáskezelőben**kattintson a jobb gombbal a megoldásra, és válassza a **NuGet csomagok kezelése a megoldáshoz parancsot** a **Nuget - Megoldás** ablakba való ugráshoz.

1. Válassza a **Tallózás** lehetőséget.

   ![NuGet - Megoldás lap, Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-03-manage-nuget-packages.png)

1. A **Csomagforrás ban**válassza **a nuget.org**lehetőséget.

1. A **Keresés** mezőbe `Microsoft.CognitiveServices.Speech`írja be a t, majd válassza ki a csomagot, miután megjelenik a keresési eredmények között.

   ![Microsoft.CognitiveServices.Speech C++ csomag telepítése – Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-04-nuget-install-1.0.0.png)

1. A keresési eredmények melletti csomagállapot-panelen válassza ki a **helloworld** projektet.

1. Válassza az **Install** (Telepítés) lehetőséget.

1. A **Változások megtekintése** párbeszédpanelen válassza az **OK gombot.**

1. A **Licencelfogadás** párbeszédpanelen tekintse meg a licencet, majd válassza az **Elfogadom**lehetőséget. A csomag telepítése megkezdődik, és **Output** a telepítés befejeztével a Kimenet `Successfully installed 'Microsoft.CognitiveServices.Speech 1.11.0' to helloworld`ablaktábla a következő höz hasonló üzenetet jelenít meg: .
