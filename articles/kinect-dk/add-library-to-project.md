---
title: Azure Kinect Library hozzáadása a Visual Studio-projekthez
description: Ismerje meg, hogyan adhatja hozzá az Azure Kinect NuGet-csomagot a Visual Studio-projekthez.
author: wes-b
ms.author: wesbarc
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, Azure, Sensor, SDK, Visual Studio 2017, Visual Studio 2019, nuget
ms.openlocfilehash: b0395118481cbaecd5ad0b6a3a6b3e89cc29dfaf
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85277308"
---
# <a name="add-azure-kinect-library-to-your-visual-studio-project"></a>Azure Kinect Library hozzáadása a Visual Studio-projekthez

Ez a cikk végigvezeti az Azure Kinect NuGet-csomag Visual Studio-projekthez való hozzáadásának folyamatán.

## <a name="install-azure-kinect-nuget-package"></a>Az Azure Kinect NuGet csomag telepítése

Az Azure Kinect NuGet-csomag telepítése:

1. A NuGet-csomagok a Visual Studióban történő telepítésével kapcsolatos részletes utasításokat a következő rövid útmutatóban találja [: csomag telepítése és használata a Visual Studióban](https://docs.microsoft.com/nuget/quickstart/install-and-use-a-package-in-visual-studio).
2. A csomag hozzáadásához a csomagkezelő felhasználói felületén kattintson a jobb gombbal a hivatkozások elemre, és válassza a NuGet-csomagok kezelése Megoldáskezelő lehetőségre.
3. Válassza a [nuget.org](https://www.nuget.org) lehetőséget a csomag forrásaként, válassza a Tallózás fület, és keressen rá `Microsoft.Azure.Kinect.Sensor` .
4. Válassza ki a csomagot a listából, és telepítse.

## <a name="use-azure-kinect-nuget-package"></a>Azure Kinect NuGet-csomag használata

Miután hozzáadta a csomagot, adja hozzá a fejlécet a forráskódhoz, például:

- `#include <k4a/k4a.h>`
- `#include <k4arecord/record.h>`
- `#include <k4arecord/playback.h>`

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
>[Most már készen áll az első alkalmazás felépítésére](build-first-app.md)
