---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 09/13/2018
ms.author: wolfma
ms.openlocfilehash: 8ff8e8341b6f39f66c2cc8014d41d3d3a2918d2b
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/28/2018
ms.locfileid: "47454358"
---
1. Indítsa el a Visual Studio 2017-et.
 
1. Bizonyosodjon meg róla, hogy a **.NET asztali környezet** tevékenységprofilja elérhető. A Visual Studio telepítőjének megnyitásához válassza a **Tools** (Eszközök) \> **Get Tools and Features** (Eszközök és funkciók beszerzése) elemet a Visual Studio menüsorából. Ha ez a tevékenységprofil már engedélyezve van, zárja be a párbeszédpanelt. 

    Ellenkező esetben jelölje be a **.NET desktop development** (.NET asztali fejlesztés) elem melletti jelölőnégyzetet, majd kattintson a párbeszédablak jobb alsó sarkában a **Modify** (Módosítás) gombra. Az új funkció telepítése hosszabb ideig tart.

    ![A .NET asztali fejlesztés engedélyezése](~/articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

1. Hozzon létre egy új Visual C#-konzolalkalmazást. A **New Project** (Új projekt) párbeszédpanel bal oldali ablaktáblájában bontsa ki az **Installed** (Telepített) \> **Visual C#** \> **Windows Desktop** csomópontot, majd válassza a **Console App (.NET Framework)** (Konzolalkalmazás (.NET keretrendszer)) elemet. A projekt neve legyen *helloworld*.

    ![Create Visual C# Console App (.NET Framework) (Visual C#-konzolalkalmazás létrehozása (.NET keretrendszer))](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-01-new-console-app.png "Create Visual C# Console App (.NET Framework) (Visual C#-konzolalkalmazás létrehozása (.NET keretrendszer))")

1. Telepítse a [Speech SDK NuGet-csomagot](https://aka.ms/csspeech/nuget), és hivatkozzon rá. A Solution Explorerben (Megoldáskezelőben) kattintson a jobb gombbal a megoldásra, majd válassza a **Manage NuGet Packages for Solution** (NuGet-csomagok kezelése a megoldáshoz) elemet.

    ![Kattintson a jobb gombbal a Manage NuGet Packages for Solution (NuGet-csomagok kezelése a megoldáshoz) elemre](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-02-manage-nuget-packages.png "Manage NuGet Packages for Solution (NuGet-csomagok kezelése a megoldáshoz)")

1. A jobb felső sarokban lévő **Package Source** (Csomag forrása) mezőben jelölje ki a **nuget.org** fájlt. Keressen rá a `Microsoft.CognitiveServices.Speech` csomagra, és telepítse azt a **helloworld** nevű projektbe.

    ![A Microsoft.CognitiveServices.Speech NuGet-csomag telepítése](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png "NuGet-csomag telepítése")

1. A NuGet-csomag telepítésének indításához fogadja el a képernyőn megjelenő licencet.

    ![A licenc elfogadása](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-04-nuget-license.png "A licenc elfogadása")

    A csomag telepítése után a Package Manager konzolban egy megerősítési üzenet jelenik meg.

1. A Configuration Manager használatával a számítógép architektúrájának megfelelő platformkonfigurációt adjon meg. Válassza a **Build** > **Configuration Manager** elemet.

    ![A Configuration Manager indítása](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-05-cfg-manager-click.png "A Configuration Manager indítása")

1. A **Configuration Manager** párbeszédpanelen adjon hozzá egy új platformot. Az **Active solution platform** (Aktív megoldás platformja) legördülő listában válassza a **New** (Új) elemet.

    ![Add a new platform under the configuration manager window (Új platform hozzáadása egy Configuration Manager-ablakban)](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-06-cfg-manager-new.png "Add a new platform under the configuration manager window (Új platform hozzáadása egy Configuration Manager-ablakban)")

1. Ha 64 bites Windowst használ, hozzon létre egy új platformkonfigurációt a következő néven: `x64`. Ha 32 bites Windowst használ, hozzon létre egy új platformkonfigurációt a következő néven: `x86`.

    ![64 bites Windows használata esetén adjon hozzá egy „x64” nevű új platformot](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-07-cfg-manager-add-x64.png "x64 nevű platform hozzáadása")


