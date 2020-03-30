---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 02/19/2020
ms.author: glenga
ms.openlocfilehash: edf5fc33ec14d41630462cca1a4ace0663473196
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78191035"
---
## <a name="run-the-function-locally"></a>Függvény helyi futtatása

Az Azure Functions Core Tools integrálható a Visual Studio-kóddal, így helyileg futtathatja és debugolhatja az Azure Functions-projekteket. A Visual Studio-kód hibakereséséről a [Debug PowerShell Azure Functions helyi ben](../articles/azure-functions/functions-debug-powershell-local.md)található. 

1. Nyomja le az F5 billentyűt a függvényalkalmazás-projekt elindításához. A Core Tools kimenete a **Terminal** (Terminál) panelen jelenik meg.

1. A **Terminal** (Terminál) panelen másolja a vágólapra a HTTP által indított függvény URL-végpontját.

    ![Az Azure helyi kimenete](./media/functions-run-function-test-local-vs-code-ps/functions-vscode-f5.png)

1. Fűzze hozzá `?name=<yourname>` a lekérdezési karakterláncot `Invoke-RestMethod` ehhez az URL-címhez, majd használja egy második PowerShell parancssorban a kérelem végrehajtásához, az alábbiak szerint:

    ```powershell
    PS > Invoke-RestMethod -Method Get -Uri http://localhost:7071/api/HttpTrigger?name=PowerShell
    Hello PowerShell
    ```

    A GET kérést a következő URL-címről is végrehajthatja egy böngészőből:

    <http://localhost:7071/api/HttpExample?name=PowerShell>

    Ha a HttpTrigger-végpontot lekérdezési paraméterként vagy a törzsben történő `name` `BadRequest` átadás nélkül hívja meg, a függvény hibát ad vissza. Amikor áttekinti a kódot a run.ps1 alkalmazásban, láthatja, hogy ez a hiba szándékosan történik.

1. A kérelemmel kapcsolatos információk a **Terminál** panelen jelennek meg.

    ![Funkció végrehajtása a Terminál panelen](./media/functions-run-function-test-local-vs-code-ps/function-execution-terminal.png)

1. Ha elkészült, nyomja le a **Ctrl + C** billentyűkombinációt az Alapvető eszközök leállításához.

Miután ellenőrizte, hogy a függvény megfelelően fut a helyi számítógépen, tegye közzé a projektet az Azure-ban.