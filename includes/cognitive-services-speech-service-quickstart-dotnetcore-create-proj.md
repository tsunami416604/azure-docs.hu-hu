---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: erhopf
ms.openlocfilehash: 3f1a24db459cabd65d9ce17b89105c3b9ab8abb7
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2020
ms.locfileid: "96188321"
---
1. Indítsa el a Visual Studio 2019-et.

1. Győződjön róla meg, hogy elérhető a **Platformfüggetlen .NET-fejlesztés** számítási feladat. Válassza **a** Visual  >  Studio menüsávjának eszközök **és szolgáltatások beolvasása** elemét, és nyissa meg a Visual Studio telepítőjét. Ha ez a számítási feladat már engedélyezve van, zárja be a párbeszédpanelt.

   ![A Visual Studio telepítőjének képernyőképe, amelyen ki van emelve a Számítási feladatok lap](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-net-core-workload.png)

   Ha nincs engedélyezve, jelölje ki a **Platformfüggetlen .NET Core-fejlesztés** lehetőség jelölőnégyzetét, majd válassza a **Módosítás** elemet a párbeszédpanel jobb alsó sarkában. Az új funkció telepítése hosszabb ideig tart.

1. Hozzon létre egy új Visual C# .NET Core-konzolalkalmazást. Az **Új projekt** párbeszédpanel bal oldali ablaktáblájában bontsa ki a **Telepítve** > **Visual C#** > **.Net Core** elemet. Válassza a **Console App (.NET Core)** (Konzolalkalmazás (.NET Core)) lehetőséget. A projekt neve legyen *helloworld*.

   ![Képernyőkép az Új projekt párbeszédpanelről](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-01-new-console-app.png "Visual C# Console-alkalmazás létrehozása (.NET Core)")

1. Telepítse a [Speech SDK NuGet-csomagot](https://aka.ms/csspeech/nuget), és hivatkozzon rá. A Megoldáskezelőben kattintson a jobb gombbal a megoldásra, majd válassza a **Manage NuGet Packages for Solution** (NuGet-csomagok kezelése a megoldáshoz) lehetőséget.

   ![Képernyőkép a Megoldáskezelőről, a NuGet-csomagok kezelése a Kiemelt megoldáshoz](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-02-manage-nuget-packages.png "Megoldás NuGet-csomagjainak kezelése")

1. A jobb felső sarokban, a **csomag forrása** mezőben válassza a **nuget.org** lehetőséget. Keresse meg a `Microsoft.CognitiveServices.Speech` csomagot, és telepítse a **HelloWorld** projektbe.

   ![A "csomagok kezelése a megoldáshoz" párbeszédpanel képernyőképe.](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-03-nuget-install-1.0.0.png "NuGet-csomag telepítése")

1. A NuGet-csomag telepítésének indításához fogadja el a képernyőn megjelenő licencet.

   ![A licencelfogadási párbeszédpanel képernyőképe](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-04-nuget-license.png "A licenc elfogadása")

A csomag telepítése után a Package Manager konzolban egy megerősítési üzenet jelenik meg.
