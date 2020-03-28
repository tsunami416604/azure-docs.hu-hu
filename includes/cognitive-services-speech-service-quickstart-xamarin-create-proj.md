---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 09/13/2019
ms.author: erhopf
ms.openlocfilehash: 0bcdd315fe11b7472166a5a9ad4f7395e22d2126
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72675626"
---
Ha visual Studio-projektet szeretne létrehozni a Xamarin alkalmazásokkal való platformfüggetlen mobilalkalmazás-fejlesztéshez, a következőket kell a következőkre szánnia:
- A Visual Studio fejlesztési beállításainak megadása.
- Hozza létre a projektet, és válassza ki a célarchitektúrát. 
- Telepítse a beszédfelismerési SDK-t.

### <a name="set-up-visual-studio-development-options"></a>A Visual Studio fejlesztési beállításainak megadása

A kezdéshez győződjön meg arról, hogy helyesen van beállítva a Visual Studióban a platformfüggetlen mobilfejlesztéshez a .NET segítségével:

1. Nyissa meg a Visual Studio 2019-et.

1. A Visual Studio menüsorából válassza az **Eszközök** > **betöltőeszközök és szolgáltatások** lehetőséget a Visual Studio Installer megnyitásához és a **Módosítás** párbeszédpanel megtekintéséhez.

   ![Számítási feladatok lap, Módosítás párbeszédpanel, Visual Studio telepítő](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-workload.png)

1. A **Munkaterhelések** lap **Windows**csoportjában keresse meg a **Mobile-fejlesztést .NET munkaterheléssel.** Ha a munkaterhelés melletti jelölőnégyzet már be van jelölve, zárja be a **Módosítás párbeszédpanelt,** és folytassa az 5.

1. Jelölje be a **Mobilfejlesztés a .NET-tel** jelölőnégyzetet, majd a **Módosítás lehetőséget.** A **Mielőtt elkezdenénk** a párbeszédpanelt, válassza a **Folytatás** lehetőséget a mobilfejlesztés .NET munkaterheléssel való telepítéséhez. Az új szolgáltatás telepítése eltarthat egy ideig.

1. Zárja be a Visual Studio telepítőjét.

### <a name="create-the-project"></a>A projekt létrehozása

1. A Visual Studio menüsorában válassza az**Új** > **projekt** **fájlja** > lehetőséget az **Új projekt létrehozása** ablak megjelenítéséhez.

   ![Új projekt létrehozása - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-create-new-project.png)

1. Keresse meg és válassza **a Mobilalkalmazás (Xamarin.Forms) lehetőséget.**

1. Válassza a **Tovább** gombot az **Új projekt konfigurálása** képernyő megjelenítéséhez.

   ![Az új projekt konfigurálása - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-configure-your-new-project.png)

1. A **Project neve mezőbe**írja be a *helloworld*értéket.

1. A **Hely területen**nyissa meg a projekt mentéséhez vagy létrehozásához a mappát, és jelölje ki vagy hozza létre azt a mappát.

1. Válassza a **Létrehozás** gombot az **Új Xamarin űrlapprojekt** ablak megnyitásakor.

   ![Új univerzális Windows Platform Projekt párbeszédpanel – Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-xamarin-new-xamarin-project.png)

1. Válassza az **Üres** sablont.

1. A **Platform**alkalmazásban jelölje ki az **Android,** **az iOS**és a **Windows (UWP)** mezőket.

1. Válassza **az OK gombot.** A rendszer visszatér a Visual Studio IDE-be, és az új projekt et létrekell hoznia, és láthatóvá kell tennia a **Megoldáskezelő** ablaktáblában.

   ![A helloworld projekt - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-helloworld.png)

Most válassza ki a célplatform architektúráját és indítási projektjét. A Visual Studio eszköztárán keresse meg a **Megoldásplatformok** legördülő lista. (Ha nem látja, válassza az**Eszköztárak** > **szokásos** **megtekintése** > lehetőséget a **Megoldásplatformokat**tartalmazó eszköztár megjelenítéséhez.) Ha 64 bites Windows rendszert futtat, válassza az **x64** lehetőséget a legördülő menüben. Az **x86-ot** kiválaszthatja, mert a 64 bites Windows 32 bites alkalmazásokat is képes futtatni. Az **Indítási projektek** legördülő lista helloworld beállítását. ** UWP (Univerzális Windows)**.

### <a name="install-the-speech-sdk"></a>A beszédfelismerési SDK telepítése

Telepítse a [Speech SDK NuGet csomagot,](https://aka.ms/csspeech/nuget)és hivatkozzon a beszédfelismerési SDK-ra a projektben.

1. A **Megoldáskezelőben**kattintson a jobb gombbal a megoldásra. Válassza **a NuGet csomagok kezelése a megoldáshoz** lehetőséget a **NuGet - Megoldás** ablakban.

1. Válassza a **Tallózás** lehetőséget.

   ![A megoldás csomagjainak kezelésére szolgáló párbeszédpanel képernyőképe](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-nuget-solution-browse.png)

1. A **Csomagforrás csoportban**válassza a nuget.org lehetőséget.

1. A **Keresés mezőbe** írja be a *Microsoft.CognitiveServices.Speech*. Ezután válassza ki a csomagot, miután megjelenik a keresési eredmények között.

   ![A megoldás csomagjainak kezelésére szolgáló párbeszédpanel képernyőképe](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-xamarin-nuget-install.png)

   > [!NOTE] 
   > A NuGet-ben lévő `Microsoft.CognitiveServices.Speech` iOS-könyvtárban nincs engedélyezve a bitkód. Ha engedélyezni szeretné a bitkódkönyvtárat `Microsoft.CognitiveServices.Speech.Xamarin.iOS` az alkalmazásához, használja a NuGet-et kifejezetten az iOS-projekthez.

1. A keresési eredmények melletti csomagállapot-panelen válassza ki az összes projektet: **helloworld**, **helloworld. Android**, **helloworld.iOS**, és **helloworld. UWP**.

1. Válassza az **Install** (Telepítés) lehetőséget.

1. A **Változások megtekintése** párbeszédpanelen válassza az **OK gombot.**

1. A **Licencelfogadás** párbeszédpanelen tekintse meg a licencet, majd válassza az **Elfogadom**lehetőséget. Telepítse a Speech SDK csomag hivatkozását az összes projektre. A telepítés sikeres befejezése után a következő figyelmeztetés jelenhet meg a helloworld.iOS fájlhoz. Ez egy ismert probléma, és nem befolyásolja az alkalmazás működését.

   > Nem oldható fel a következő\.hivatkozás: "C:\Users\Default nuget\packages\microsoft.cognitiveservices.speech\1.7.0\build\Xamarin.iOS\libMicrosoft.CognitiveServices.Speech.core.a". Ha ezt a hivatkozást a kód megköveteli, fordítási hibákat kaphat.
