---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 09/13/2018
ms.author: wolfma
ms.openlocfilehash: 7a028f65467c517be59be6f0c6d2c45ef9a7685e
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2018
ms.locfileid: "51285761"
---
1. Indítsa el a Visual Studio 2017-et.

1. Válassza ki a Visual Studio menüsorára, **eszközök > Get Tools** , és ellenőrizze, hogy a **.Net asztali fejlesztés** számítási feladat érhető el. Ha a munkaterhelés nem lett telepítve, jelölje be a jelölőnégyzetet, majd kattintson a **módosítás** a telepítés elindításához. Töltse le és telepítse néhány percig is eltarthat.

   Ha a jelölőnégyzet be **.NET asztali fejlesztés** van kijelölve, a párbeszédpanel most már bezárhatja.

   ![A .NET asztali fejlesztés engedélyezése](~/articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

1. Következő lépésként hozzunk létre egy projektet. A menüsávon válassza **File > New > Project**. A párbeszédpanel akkor jelenik meg, ha a bal oldali panelen bontsa ki a ezekben a szakaszokban **telepített > Visual C# > Windows asztali** válassza **Console App (.NET Framework)**. Adja a projektnek *helloworld*.

    ![Create Visual C# Console App (.NET Framework) (Visual C#-konzolalkalmazás létrehozása (.NET keretrendszer))](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-01-new-console-app.png "Create Visual C# Console App (.NET Framework) (Visual C#-konzolalkalmazás létrehozása (.NET keretrendszer))")

1. Most, hogy a projekt beállítását, telepíteni kell a [beszéd SDK NuGet-csomagot](https://aka.ms/csspeech/nuget) , és hivatkozzon arra a kódban. Keresse meg a Megoldáskezelőben, és kattintson a jobb gombbal a helloworld. Válassza a menü **NuGet-csomagok kezelése...** .

   ![Kattintson a jobb gombbal a Manage NuGet Packages for Solution (NuGet-csomagok kezelése a megoldáshoz) elemre](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-02-manage-nuget-packages.png "Manage NuGet Packages for Solution (NuGet-csomagok kezelése a megoldáshoz)")

1. A jobb felső sarokban a NuGet-Csomagkezelőt, keresse meg a **csomag forrása** legördülő listából, és ellenőrizze, hogy **nuget.org** van kiválasztva. Ezután válassza ki **Tallózás** , és keresse meg a `Microsoft.CognitiveServices.Speech` csomagot, és telepítse a legújabb stabil verzióra.

   ![A Microsoft.CognitiveServices.Speech NuGet-csomag telepítése](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png "NuGet-csomag telepítése")

1. Fogadja el az összes szerződések és licencek a telepítés elindításához.

   ![A licenc elfogadása](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-04-nuget-license.png "A licenc elfogadása")

    A csomag telepítése után a Package Manager konzolban egy megerősítési üzenet jelenik meg.

1. A következő lépés, hogy a használatával hozhat létre és futtassa a konzolalkalmazást számítógép architektúrájának megfelelő platform konfiguráció létrehozásához. A menüsávban válassza **összeállítása** > **Configuration Manager...** .

    ![A Configuration Manager indítása](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-05-cfg-manager-click.png "A Configuration Manager indítása")

1. Az a **Configuration Manager** párbeszédpanelen keresse meg a **aktív megoldás platformját** legördülő listára, és válassza **új**.

    ![Add a new platform under the configuration manager window (Új platform hozzáadása egy Configuration Manager-ablakban)](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-06-cfg-manager-new.png "Add a new platform under the configuration manager window (Új platform hozzáadása egy Configuration Manager-ablakban)")

1. Ha futtatja a Windows 64 bites, amikor a rendszer kéri a **írja be vagy válassza az új platformra**, `x64`. Ha 32 bites Windows futtatja, jelölje be `x86`. Ha elkészült, kattintson a **OK**.

    ![64 bites Windows használata esetén adjon hozzá egy „x64” nevű új platformot](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-07-cfg-manager-add-x64.png "x64 nevű platform hozzáadása")
