---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 12/12/2018
ms.author: erhopf
ms.openlocfilehash: 777b2608cf5f326556dfaea307f4f3e9346213f8
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "66482326"
---
1. Indítsa el a Visual Studio 2019-et.

1. Győződjön róla meg, hogy elérhető a **Platformfüggetlen .NET-fejlesztés** számítási feladat.  > A **Tools**Visual Studio telepítőjének megnyitásához válassza a Visual Studio menüsoreszközök**betöltőeszközei és szolgáltatásai** parancsát. Ha ez a számítási feladat már engedélyezve van, zárja be a párbeszédpanelt.

   ![A Visual Studio telepítőjének képernyőképe, amelyen ki van emelve a Számítási feladatok lap](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-net-core-workload.png)

   Ha nincs engedélyezve, jelölje ki a **Platformfüggetlen .NET Core-fejlesztés** lehetőség jelölőnégyzetét, majd válassza a **Módosítás** elemet a párbeszédpanel jobb alsó sarkában. Az új funkció telepítése hosszabb ideig tart.

1. Hozzon létre egy új Visual C# .NET Core-konzolalkalmazást. Az **Új projekt** párbeszédpanel bal oldali ablaktáblájában bontsa ki a **Telepítve** > **Visual C#** > **.Net Core** elemet. Válassza a **Console App (.NET Core)** (Konzolalkalmazás (.NET Core)) lehetőséget. A projekt neve legyen *helloworld*.

   ![Képernyőkép az Új projekt párbeszédpanelről](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-01-new-console-app.png "Visual C# konzolalkalmazás létrehozása (.NET Core)")

1. Telepítse a [Speech SDK NuGet-csomagot](https://aka.ms/csspeech/nuget), és hivatkozzon rá. A Megoldáskezelőben kattintson a jobb gombbal a megoldásra, majd válassza a **Manage NuGet Packages for Solution** (NuGet-csomagok kezelése a megoldáshoz) lehetőséget.

   ![Képernyőkép a Megoldáskezelőről, kiemelve a NuGet csomagok kezelése a megoldáshoz](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-02-manage-nuget-packages.png "NuGet csomagok kezelése megoldáshoz")

1. A jobb felső sarokban, a **Csomagforrása** mezőben válassza **a nuget.org**lehetőséget. Keresse meg `Microsoft.CognitiveServices.Speech` a csomagot, és telepítse a **helloworld** projektbe.

   ![A megoldás csomagjainak kezelésére szolgáló párbeszédpanel képernyőképe](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-03-nuget-install-1.0.0.png "NuGet csomag telepítése")

1. A NuGet-csomag telepítésének indításához fogadja el a képernyőn megjelenő licencet.

   ![A licencelfogadási párbeszédpanel képernyőképe](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-04-nuget-license.png "A licenc elfogadása")

A csomag telepítése után a Package Manager konzolban egy megerősítési üzenet jelenik meg.
