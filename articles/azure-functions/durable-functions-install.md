---
title: Telepítse a Durable Functions bővítmény és -minták – Azure
description: Ismerje meg, hogyan telepítheti a Durable Functions bővítmény az Azure Functions szolgáltatáshoz, a fejlesztői portál vagy a Visual Studio fejlesztői.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 08/31/2018
ms.author: azfuncdf
ms.openlocfilehash: 2eb838bcb9d3f64d0bbf4657c516adb50d103223
ms.sourcegitcommit: 5843352f71f756458ba84c31f4b66b6a082e53df
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2018
ms.locfileid: "47585305"
---
# <a name="install-the-durable-functions-extension-and-samples-azure-functions"></a>Telepítse a Durable Functions bővítmény és a minták (az Azure Functions)

A [Durable Functions](durable-functions-overview.md) kiterjesztése az Azure Functions szolgáltatáshoz van megadva a NuGet-csomagot a [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask). Ez a cikk bemutatja, hogyan telepítheti a csomagot és több mintát a következő fejlesztési környezetek:

* A Visual Studio 2017-et (C# esetén ajánlott) 
* A Visual Studio Code (JavaScript esetén ajánlott)
* Azure Portal

## <a name="visual-studio-2017"></a>Visual Studio 2017

A Visual Studio jelenleg biztosítja a legoptimálisabb Durable Functions használó alkalmazások fejlesztéséhez.  A függvények helyi futtatása, és közzé lehet tenni az Azure-bA. Elkezdheti egy üres projekt vagy minta funkciók vannak beállítva.

### <a name="prerequisites"></a>Előfeltételek

* Telepítse a [Visual Studio legújabb verziójának](https://www.visualstudio.com/downloads/) (15.6-s vagy újabb verzió). Tartalmazza a **Azure-fejlesztési** a telepítési lehetőségek a számítási feladatok.

### <a name="start-with-sample-functions"></a>Indítsa el a minta-függvényekkel 

1. Töltse le a [mintaalkalmazás .zip-fájlt a Visual Studio](https://azure.github.io/azure-functions-durable-extension/files/VSDFSampleApp.zip). Ne adja hozzá a NuGet-hivatkozás, mert a projektben már van szüksége.
2. Telepítheti és futtathatja [Azure Storage Emulator](https://docs.microsoft.com/azure/storage/storage-use-emulator) 5.6-os vagy újabb verziója. Másik megoldásként frissítheti a *local.settings.json* valós Azure Storage kapcsolati karakterláncok fájlt.
3. Nyissa meg a projektet a Visual Studio 2017-ben. 
4. A minta futtatásához útmutatást, kezdje [függvény láncolás – feladatütemezési példa Hello](durable-functions-sequence.md). A minta helyi futtatásához vagy az Azure-ban közzétett.

### <a name="start-with-an-empty-project"></a>Kezdje egy üres projekt
 
Kezdve a minta meghajtóbetűjeleket azonos utasításait, de tegye a következőket, hanem a *.zip* fájlt:

1. Függvényalkalmazás-projekt létrehozása.
2. Keresse meg a következő NuGet csomag hivatkozás használatával *NuGet-csomagok kezelése* , és adja hozzá a projekthez: Microsoft.Azure.WebJobs.Extensions.DurableTask v1.6.0
   
## <a name="visual-studio-code"></a>Visual Studio Code

A Visual Studio Code kiterjedő minden főbb platformhoz – a Windows, macOS és Linux helyi fejlesztői élményt nyújt.  A függvények helyileg futtathatja, és az Azure-ba is tehetők közzé. Elkezdheti egy üres projekt vagy minta funkciók vannak beállítva.

### <a name="prerequisites"></a>Előfeltételek

* Telepítse a [legújabb verziója a Visual Studio Code](https://code.visualstudio.com/Download) 

* Kövesse "Telepítse az Azure Functions Core Tools" alatt található [kódolás és tesztelés az Azure Functions helyi](https://docs.microsoft.com/azure/azure-functions/functions-run-local)

    >[!IMPORTANT]
    > Ha már rendelkezik az Azure Functions Cross Platform Tools, frissíteni őket az elérhető legújabb verzióra.

    >[!IMPORTANT]
    >Tartós függvények használata Javascriptben verziója szükséges 2.x verzióját az Azure Functions Core Tools.

*  Ha egy Windows-gépen, telepítése és futtatása [Azure Storage Emulator](https://docs.microsoft.com/azure/storage/storage-use-emulator) 5.6-os vagy újabb verziója. Másik megoldásként frissítheti a *local.settings.json* fájl valós Azure Storage-kapcsolattal. 


### <a name="start-with-sample-functions"></a>Indítsa el a minta-függvényekkel

#### <a name="c"></a>C#

1. Klónozás a [Durable Functions-tárház](https://github.com/Azure/azure-functions-durable-extension.git).
2. A gépén, nyissa meg a [C# szkriptet mintákat tartalmazó mappára](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/csx). 
3. Azure Functions Durable-Extension telepítéséhez futtassa a következő parancsot parancssorba / terminál ablakában:

    ```bash
    func extensions install -p Microsoft.Azure.WebJobs.Extensions.DurableTask -v 1.6.0
    ```
4. Azure Functions Twilio-bővítmény telepítéséhez a következő parancs futtatásával kérdés / terminál ablakában:

    ```bash
    func extensions install -p Microsoft.Azure.WebJobs.Extensions.Twilio -v 3.0.0-beta8
    ```
5. Azure Storage Emulator vagy a frissítés futtatása a *local.settings.json* fájl valós Azure Storage kapcsolati karakterlánccal.
6. Nyissa meg a projektet a Visual Studio Code-ban. 
7. A minta futtatásához útmutatást, kezdje [függvény láncolás – feladatütemezési példa Hello](durable-functions-sequence.md). A minta helyi futtatásához vagy az Azure-ban közzétett.
8. A projekt elindításához a következő parancsot a kérdés / terminál a következő parancs futtatásával:
    ```bash
    func host start
    ```

#### <a name="javascript-functions-v2-only"></a>JavaScript (csak függvények v2)

1. Klónozás a [Durable Functions-tárház](https://github.com/Azure/azure-functions-durable-extension.git).
2. A gépén, nyissa meg a [JavaScript mintákat tartalmazó mappára](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/javascript). 
3. Azure Functions Durable-Extension telepítéséhez futtassa a következő parancsot parancssorba / terminál ablakában

    ```
    func extensions install
    ```
    > [!NOTE] 
    > Ehhez a [.NET Core SDK](https://www.microsoft.com/net/download) a gépre kell telepíteni
4. A következő parancs futtatásával az npm-csomagok visszaállítására kérdés / terminál ablakában:
    
    ```
    npm install
    ``` 
5. Frissítés a *local.settings.json* fájlt egy olyan kapcsolati karakterlánccal, egy Azure storage-fiókját a `AzureWebJobsStorage`.  Ez a tárfiók a tartós függvény állapota lesz használható.
6. Nyissa meg a projekt egy szövegszerkesztőben, például a Visual Studio Code-ot. 
7. A minta futtatásához útmutatást, kezdje [függvény láncolás – feladatütemezési példa Hello](durable-functions-sequence.md). A minta helyi futtatásához vagy az Azure-ban közzétett.
8. A projekt elindításához a következő parancsot a kérdés / terminál a következő parancs futtatásával:
    ```
    func start
    ```

### <a name="start-with-an-empty-project"></a>Kezdje egy üres projekt
 
1. A parancs parancssorba / terminál keresse meg a mappát, amely a függvényalkalmazás fogja futtatni.
3. Hozzon létre egy Függvényalkalmazást projektet a következő parancs futtatásával:

    ```
    func init
    ``` 
4. Futtassa az Azure Storage Emulator (csak Windows), vagy frissítse a *local.settings.json* valós Azure Storage kapcsolati karakterláncát tartalmazó fájl `AzureWebJobsStorage`.
5. Ezután hozzon létre egy új függvényt a következő parancs futtatásával, és kövesse a varázsló lépéseit:

    ```
    func new
    ```
    >[!IMPORTANT]
    > Jelenleg nem érhető el a tartós függvénysablon, de a támogatott beállítások egyikével, és ezután a kód módosítása. A minták referenciaként használható [Vezénylési ügyfél](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/csx/HttpStart), [Vezénylési eseményindító](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/csx/E1_HelloSequence), és [tevékenység eseményindító](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/csx/E1_HelloSequence).
2. Az Azure Functions tartós bővítmény telepítéséhez a következő parancs futtatásával az függvény alkalmazáskönyvtárban kérdés / terminál ablak:

    ```
    func extensions install
    ```

6. A projektmappa fájllistájának megnyitásához a Visual Studio Code-ban, és továbbra is a sablonban lévő kód módosításával. 
7. A projekt elindításához a következő parancsot a kérdés / terminál a következő parancs futtatásával:
    ```
    func start
    ```

## <a name="azure-portal"></a>Azure Portal

Igény szerint használhatja a [az Azure portal](https://portal.azure.com) Durable Functions-fejlesztéshez.

   > [!NOTE]
   > Tartós függvények használata Javascriptben még nem állnak rendelkezésre a portálon.

### <a name="create-an-orchestrator-function"></a>Az orchestrator-függvény létrehozása

1. Új függvényalkalmazás létrehozása a portálon, ahogyan az a [funkciók rövid cikk](functions-create-first-azure-function.md#create-a-function-app).

2. Konfigurálja a függvényalkalmazást, hogy [2.0 futtatókörnyezet verziójának használatához](set-runtime-version.md).

   A Durable Functions bővítmény 1.X futásidejű és a 2.0-s futásidejű is együttműködik, de az Azure-portál sablonok csak érhetők el, amikor a 2.0-s futásidejű célzó.

3. Hozzon létre egy új függvényt kiválasztásával **"hozza létre saját egyéni függvényét."** .

4. Módosítsa a **nyelvi** a **C#**, **forgatókönyv** , **Durable Functions** , és válassza ki a **Durable Functions – Http-alapkészlet – C#** sablont.

5. A **nincsenek telepítve a bővítmények**, kattintson a **telepítése** NuGet.org töltheti le a bővítményt. 

6. A telepítés befejeződése után folytassa egy vezénylési ügyfél függvényt – létrehozásának **"HttpStart"** kiválasztásával létrehozott **Durable Functions – Http-alapkészlet – C#** sablont.

7. Most hozzon létre egy vezénylési függvény **"HelloSequence"** a **Durable Functions-vezénylő – C#** sablont.

8. És az utolsó függvény hívása **"Hello"** a **Durable Functions-tevékenység – C#** sablont.

9. Lépjen a **"HttpStart"** funkciót, és másolja az URL-CÍMÉT.

10. Postman vagy a cURL használatával meghívja a tartós függvényt. -Beli tesztelése előtt cserélje le az URL-címben **{{functionName}** orchestrator függvény nevű - **HelloSequence**.  Adatok nem kötelező, csak a POST műveletet használja. 

    ```bash
    curl -X POST https://{your function app name}.azurewebsites.net/api/orchestrators/HelloSequence
    ```

11. Ezután a **"statusQueryGetUri"** végpont, és a tartós függvény aktuális állapotának megtekintéséhez.

    ```json
        {
            "runtimeStatus": "Running",
            "input": null,
            "output": null,
            "createdTime": "2017-12-01T05:37:33Z",
            "lastUpdatedTime": "2017-12-01T05:37:36Z"
        }
    ```

12. Továbbra is hívása a **"statusQueryGetUri"** végpont addig, amíg a állapota **"Kész"** 

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

Gratulálunk! Az első tartós függvény működik és elérhető az Azure Portal!

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A minta-láncolás függvény futtatása](durable-functions-sequence.md)
