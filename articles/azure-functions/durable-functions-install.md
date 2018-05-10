---
title: Telepítse a tartós funkciók bővítményt, és minták – Azure
description: A tartós funkciók bővítményének telepítése az Azure Functions portálon fejlesztési vagy a Visual Studio fejlesztői útmutató.
services: functions
author: cgillum
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/19/2018
ms.author: azfuncdf
ms.openlocfilehash: 4dd4bbb9c382b772f8f60b259844e7e471ec73e3
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="install-the-durable-functions-extension-and-samples-azure-functions"></a>Telepítse a tartós funkciók bővítményt, és minták (az Azure Functions)

A [tartós funkciók](durable-functions-overview.md) Azure Functions kiterjesztése a NuGet-csomag megadott [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask). Ez a cikk bemutatja, hogyan telepíthetik a csomagot és minták a következő fejlesztési környezetben:

* A Visual Studio 2017 (C# esetén ajánlott) 
* A Visual Studio Code (JavaScript ajánlott)
* Azure Portal

## <a name="visual-studio-2017"></a>Visual Studio 2017

A Visual Studio jelenleg a legjobb élményt nyújt a tartós funkciók használó alkalmazások fejlesztésével.  A funkciók futtatható helyileg, és az Azure-ba is tehetők közzé. Egy üres projektet vagy minta funkciók is elindítható.

### <a name="prerequisites"></a>Előfeltételek

* Telepítse a [Visual Studio legújabb verziójának](https://www.visualstudio.com/downloads/) (15.3 vagy újabb verziója). Tartalmazza a **Azure fejlesztési** munkaterhelés a telepítési beállítások.

### <a name="start-with-sample-functions"></a>Indítsa el a minta-funkciók 

1. Töltse le a [mintaalkalmazás .zip-fájlt a Visual Studio](https://azure.github.io/azure-functions-durable-extension/files/VSDFSampleApp.zip). Nem kell a NuGet hivatkozás hozzáadása, mert a minta-projekt már tartalmaz.
2. Telepítheti és futtathatja [Azure Storage Emulator](https://docs.microsoft.com/azure/storage/storage-use-emulator) 5.2-es vagy újabb verziója. Másik megoldásként frissítheti a *local.appsettings.json* valós Azure Storage kapcsolati karakterláncok fájlt.
3. Nyissa meg a projektet a Visual Studio 2017. 
4. A minta futtatásához útmutatást, kezdje [működéséhez láncolás – feladatütemezési példa Hello](durable-functions-sequence.md). A minta helyileg történő futtatása, vagy az Azure-bA közzé.

### <a name="start-with-an-empty-project"></a>Egy üres projekt indítása
 
Mint a minta kezdve azonos útmutatásait, de a következő lépések letöltésük helyett a *.zip* fájlt:

1. Hozzon létre egy függvény App projektet.
2. Keresse meg a következő NuGet csomag hivatkozás segítségével *NuGet-csomagok kezelése* és adja hozzá a projekthez: Microsoft.Azure.WebJobs.Extensions.DurableTask v1.4.0 (ellenőrizze *prerelease tartalmazza* számára Keresse meg a csomag)
   
## <a name="visual-studio-code"></a>Visual Studio Code

A Visual Studio Code vonatkozó összes fő platformok – a Windows, a macOS és a Linux helyi fejlesztési élményt nyújt.  A funkciók futtatható helyileg, és szintén közzé kell tenni, az Azure-bA. Egy üres projektet vagy minta funkciók is elindítható.

### <a name="prerequisites"></a>Előfeltételek

* Telepítse a [Visual Studio Code legújabb verziója](https://code.visualstudio.com/Download) 

* Kövesse a "Az Azure Functions Core eszközeinek telepítése" a [kódot és helyileg Azure Functions tesztelése](https://docs.microsoft.com/azure/azure-functions/functions-run-local)

    >[!IMPORTANT]
    > Ha már rendelkezik az Azure funkciók Cross Platform eszközök, frissítse azokat az elérhető legújabb verzióra.

    >[!IMPORTANT]
    >A JavaScript tartós funkciók verziójára van szükség az Azure Functions Core eszközök 2.x.

*  Ha Windows-gépen, telepíteni és futtatni [Azure Storage Emulator](https://docs.microsoft.com/azure/storage/storage-use-emulator) 5.2-es vagy újabb verziója. Másik megoldásként frissítheti a *local.appsettings.json* valós Azure Storage-kapcsolattal rendelkező fájl. 


### <a name="start-with-sample-functions"></a>Indítsa el a minta-funkciók

#### <a name="c"></a>C#

1. Klónozott a [tartós funkciók tárház](https://github.com/Azure/azure-functions-durable-extension.git).
2. A számítógépen keresse meg a [C# minták parancsfájlmappa](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/csx). 
3. Azure Functions tartós bővítményének telepítése a következő parancs futtatásával Rákérdezés / terminál ablakban:

    ```bash
    func extensions install -p Microsoft.Azure.WebJobs.Extensions.DurableTask -v 1.4.0
    ```
4. Azure Functions Twilio-kiterjesztés telepítése a következő parancs futtatásával Rákérdezés / terminál ablakban:

    ```bash
    func extensions install -p Microsoft.Azure.WebJobs.Extensions.Twilio -v 3.0.0-beta5
    ```
5. Futtassa az Azure Storage Emulator vagy a frissítés a *local.appsettings.json* fájl valós Azure Storage kapcsolati karakterlánccal.
6. Nyissa meg a projektet a Visual Studio Code. 
7. A minta futtatásához útmutatást, kezdje [működéséhez láncolás – feladatütemezési példa Hello](durable-functions-sequence.md). A minta helyileg történő futtatása, vagy az Azure-bA közzé.
8. Indítsa el a projekt parancsban Rákérdezés / terminál a következő parancs futtatásával:
    ```bash
    func host start
    ```

#### <a name="javascript-functions-v2-only"></a>JavaScript (csak funkciók v2)

1. Klónozott a [tartós funkciók tárház](https://github.com/Azure/azure-functions-durable-extension.git).
2. A számítógépen keresse meg a [JavaScript minták mappa](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/javascript). 
3. Azure Functions tartós bővítményének telepítése a következő parancs futtatásával Rákérdezés / terminál ablakban:

    ```bash
    func extensions install -p Microsoft.Azure.WebJobs.Extensions.DurableTask -v 1.4.0
    ```
4. Állítsa vissza az npm csomagok a következő parancs futtatásával Rákérdezés / terminál ablakban:
    
    ```bash
    npm install
    ``` 
5. Frissítés a *local.appsettings.json* valós Azure Storage kapcsolati karakterlánccal rendelkező fájl.
6. Nyissa meg a projektet a Visual Studio Code. 
7. A minta futtatásához útmutatást, kezdje [működéséhez láncolás – feladatütemezési példa Hello](durable-functions-sequence.md). A minta helyileg történő futtatása, vagy az Azure-bA közzé.
8. Indítsa el a projekt parancsban Rákérdezés / terminál a következő parancs futtatásával:
    ```bash
    func host start
    ```

### <a name="start-with-an-empty-project"></a>Egy üres projekt indítása
 
1. A parancs, parancssor / terminál keresse meg a mappát, a függvény alkalmazást üzemeltető.
2. Az Azure-funkciók tartós bővítményének telepítése a következő parancs futtatásával Rákérdezés / terminál ablakban:

    ```bash
    func extensions install -p Microsoft.Azure.WebJobs.Extensions.DurableTask -v 1.4.0
    ```
3. Függvény-alkalmazás projekt létrehozása a következő parancs futtatásával:

    ```bash
    func init
    ``` 
4. Futtassa az Azure Storage Emulator vagy a frissítés a *local.appsettings.json* fájl valós Azure Storage kapcsolati karakterlánccal.
5. Ezután hozzon létre egy új függvényt a következő parancs futtatásával, és kövesse a varázsló lépéseit:

    ```bash
    func new
    ```
    >[!IMPORTANT]
    > Jelenleg nem érhető el a tartós függvény sablon, de a támogatott beállítások egyikével, és módosítsa a kódot. A minták referenciaként használható [Vezénylési ügyfél](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/csx/HttpStart), [Vezénylési eseményindító](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/csx/E1_HelloSequence), és [tevékenység eseményindító](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/csx/E1_HelloSequence).

6. Nyissa meg a projektmappa Visual Studio Code, és folytassa a sablon kódjának a módosítása. 
7. Indítsa el a projekt parancsban Rákérdezés / terminál a következő parancs futtatásával:
    ```bash
    func host start
    ```

## <a name="azure-portal"></a>Azure Portal

Tetszés szerint használhatja az Azure-portálon a tartós funkciók fejlesztése.

   > [!NOTE]
   > A JavaScript tartós funkciók még nem állnak rendelkezésre a portálon.

### <a name="create-an-orchestrator-function"></a>Az orchestrator-függvény létrehozása

1. Hozzon létre egy új funkció alkalmazást [functions.azure.com](https://functions.azure.com/signin).

2. A függvény alkalmazás konfigurálása [futtatókörnyezet 2.0-s verzióját használja a](set-runtime-version.md).

   A tartós funkciók bővítmény a 1.X futásidejű és a 2.0-s futásidejű is működik, de az Azure-portál sablonok csak érhetők el, ha a 2.0-s futásidejű célzó.

3. Hozzon létre egy új függvényt kiválasztásával **"létrehozása a saját egyéni függvény."** .

4. Módosítsa a **nyelvi** való **C#**, **forgatókönyv** való **tartós funkciók** válassza ki a **tartós funkciók Http alapszintű -C#** sablont.

5. A **-bővítmény nem**, kattintson a **telepítése** NuGet.org töltheti le a bővítményt. 

6. A telepítés befejezése után folytatja a vezénylési ügyfél funkcióinak – létrehozása **"HttpStart"** kiválasztásával létrehozott **tartós funkciók Http Starter - C#** sablont.

7. Ezután hozzon létre egy vezénylési függvény **"HelloSequence"** a **tartós funkciók Orchestrator - C#** sablont.

8. És az utolsó függvényt hívja **"Hello"** a **tartós funkciók tevékenység - C#** sablont.

9. Ugrás a **"HttpStart"** funkciót, és másolja az URL-címet.

10. Postman vagy a cURL használatával hívja meg a tartós függvényt. Tesztelés előtt cserélje le az URL-címben **a(z) %{FunctionName/}** az orchestrator függvény nevével - **HelloSequence**.  Adatot nem kötelező, csak a POST művelet használata. 

    ```bash
    curl -X POST https://{your function app name}.azurewebsites.net/api/orchestrators/HelloSequence
    ```

11. Majd, meghívják a **"statusQueryGetUri"** végpontot, és a tartós függvény aktuális állapotának megtekintése

    ```json
        {
            "runtimeStatus": "Running",
            "input": null,
            "output": null,
            "createdTime": "2017-12-01T05:37:33Z",
            "lastUpdatedTime": "2017-12-01T05:37:36Z"
        }
    ```

12. Továbbra is hívása a **"statusQueryGetUri"** végpont, amíg az állapot **"Kész"** 

    ```json
    {
            "runtimeStatus": "Completed",
            "input": null,
            "output": [
                "Hello Tokyo!",
                "Hello Seattle!",
                "Hello London!"
            ],
            "createdTime": "2017-12-01T05:38:22Z",
            "lastUpdatedTime": "2017-12-01T05:38:28Z"
        }
    ```

Gratulálunk! Az első tartós függvény megfelelően működik, és az Azure portálon!

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A minta-láncolás funkció futtatásához](durable-functions-sequence.md)
