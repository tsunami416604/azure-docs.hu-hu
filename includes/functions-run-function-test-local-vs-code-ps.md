---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 02/19/2020
ms.author: glenga
ms.openlocfilehash: ca8da227c97a44abe14354a5c530d508ce357884
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2020
ms.locfileid: "88703906"
---
## <a name="run-the-function-locally"></a>A függvény helyi futtatása

A Azure Functions Core Tools a Visual Studio Code-ban integrálva lehetővé teszi egy Azure Functions-projekt helyi futtatását és hibakeresését. A Visual Studio Code-ban való hibakereséssel kapcsolatos részletekért lásd: a [PowerShell Azure functions helyi hibakeresése](../articles/azure-functions/functions-debug-powershell-local.md). 

1. Nyomja le az F5 billentyűt a Function app projekt elindításához. A Core Tools kimenete a **Terminal** (Terminál) panelen jelenik meg.

1. A **Terminal** (Terminál) panelen másolja a vágólapra a HTTP által indított függvény URL-végpontját.

    ![Az Azure helyi kimenete](./media/functions-run-function-test-local-vs-code-ps/functions-vscode-f5.png)

1. Fűzze hozzá a lekérdezési karakterláncot `?name=<yourname>` Ehhez az URL-címhez, majd a `Invoke-RestMethod` kérelem végrehajtásához egy második PowerShell-parancssorban használja a következőt:

    ```powershell
    PS > Invoke-RestMethod -Method Get -Uri http://localhost:7071/api/HttpTrigger?name=PowerShell
    Hello PowerShell
    ```

    A GET kérést egy böngészőből is végrehajthatja a következő URL-címről:

    `http://localhost:7071/api/HttpExample?name=PowerShell`

    Ha a HttpTrigger végpontot úgy hívja meg, hogy egy `name` paramétert sem lekérdezési, sem a törzsben kellene átadnia, a függvény hibát ad vissza `BadRequest` . Amikor áttekinti a kódot a run.ps1ban, láthatja, hogy ez a hiba a tervezés szerint történik.

1. A kérésre vonatkozó információk a **terminál** panelen jelennek meg.

    ![Függvény végrehajtása a terminál panelen](./media/functions-run-function-test-local-vs-code-ps/function-execution-terminal.png)

1. Ha elkészült, nyomja le a **CTRL + C** billentyűkombinációt az alapvető eszközök leállításához.

Miután ellenőrizte, hogy a függvény megfelelően fut a helyi számítógépen, tegye közzé a projektet az Azure-ban.