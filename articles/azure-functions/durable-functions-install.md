---
title: "Telepítse a tartós funkciók bővítményt, és minták – Azure"
description: "A tartós funkciók bővítményének telepítése az Azure Functions portálon fejlesztési vagy a Visual Studio fejlesztői útmutató."
services: functions
author: cgillum
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: debadde78d937bcd4ec1df665aacfd1887fbcd02
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="install-the-durable-functions-extension-and-samples-azure-functions"></a>Telepítse a tartós funkciók bővítményt, és minták (az Azure Functions)

A [tartós funkciók](durable-functions-overview.md) Azure Functions kiterjesztése a NuGet-csomag megadott [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask). Ez a cikk bemutatja, hogyan telepíthetik a csomagot és minták a következő fejlesztési környezetben:

* A Visual Studio 2017 (ajánlott) 
* Azure Portal

## <a name="visual-studio-2017"></a>Visual Studio 2017

A Visual Studio jelenleg a legjobb élményt nyújt a tartós funkciók használó alkalmazások fejlesztésével.  A funkciók futtatható helyileg, és az Azure-ba is tehetők közzé. Egy üres projektet vagy minta funkciók is elindítható.

### <a name="prerequisites"></a>Előfeltételek

* Telepítse a [Visual Studio legújabb verziójának](https://www.visualstudio.com/downloads/) (15.3 vagy újabb verziója). Vegye fel az Azure-eszközök a beállítási lehetőség.

### <a name="start-with-sample-functions"></a>Indítsa el a minta-funkciók

1. Töltse le a [mintaalkalmazás .zip-fájlt a Visual Studio](https://azure.github.io/azure-functions-durable-extension/files/VSDFSampleApp.zip). Nem kell a NuGet hivatkozás hozzáadása, mert a minta-projekt már tartalmaz.
2. Telepítheti és futtathatja [Azure Storage Emulator](https://docs.microsoft.com/azure/storage/storage-use-emulator) 5.2-es vagy újabb verziója. Másik megoldásként frissítheti a *local.appsettings.json* valós Azure Storage kapcsolati karakterláncok fájlt.
3. Nyissa meg a projektet a Visual Studio 2017. 
4. A minta futtatásához útmutatást, kezdje [működéséhez láncolás – feladatütemezési példa Hello](durable-functions-sequence.md). A minta helyileg történő futtatása, vagy az Azure-bA közzé.

### <a name="start-with-an-empty-project"></a>Egy üres projekt indítása
 
Mint a minta kezdve azonos útmutatásait, de a következő lépések letöltésük helyett a *.zip* fájlt:

1. Hozzon létre egy függvény App projektet.
2. Adjon hozzá a következő NuGet csomag hivatkozást, a *.csproj* fájlt:

   ```xml
   <PackageReference Include="Microsoft.Azure.WebJobs.Extensions.DurableTask" Version="1.0.0-beta" />
   ```

## <a name="azure-portal"></a>Azure Portal

Tetszés szerint használhatja az Azure-portálon a tartós funkciók fejlesztése.

### <a name="create-an-orchestrator-function"></a>Az orchestrator-függvény létrehozása

1. Hozzon létre egy új funkció alkalmazást [functions.azure.com](https://functions.azure.com/signin).
2. A függvény alkalmazás konfigurálása [futtatókörnyezet 2.0-s verzióját használja a](functions-versions.md).
3. Hozzon létre egy új függvényt, és válassza ki a **tartós funkciók Orchestrator - C#** sablont.
4. A **-bővítmény nem**, kattintson a **telepítése** NuGet.org töltheti le a bővítményt.

### <a name="copy-sample-code-to-the-function-app"></a>A függvény app mintakód másolása

1. Töltse le a [DFSampleApp.zip](https://github.com/Azure/azure-functions-durable-extension/raw/master/docfx/files/DFSampleApp.zip) fájlt.
2. Bontsa ki a mintafájlt a `D:\home\site\wwwroot` az alkalmazásban függvény Kudu vagy FTP segítségével.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [A minta-láncolás funkció futtatásához](durable-functions-sequence.md)
