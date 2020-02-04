---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/16/2020
ms.author: glenga
ms.openlocfilehash: 916aa2552e5dd004ec767df98ce7c78f7320efd0
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964112"
---
## <a name="run-the-function-locally"></a>Függvény helyi futtatása

A Azure Functions Core Tools a Visual Studio Code-ban integrálva lehetővé teszi egy Azure Functions-projekt helyi futtatását és hibakeresését.  

1. A függvény hibakereséséhez helyezzen be egy hívást az [`Wait-Debugger`](/powershell/module/microsoft.powershell.utility/wait-debugger?view=powershell-6) parancsmaghoz a függvény kódjában, mielőtt csatolni szeretné a hibakeresőt, majd nyomja le az F5 billentyűt a Function app projekt elindításához, és csatolja a hibakeresőt. A Core Tools kimenete a **Terminal** (Terminál) panelen jelenik meg.

1. A **Terminal** (Terminál) panelen másolja a vágólapra a HTTP által indított függvény URL-végpontját.

    ![Az Azure helyi kimenete](./media/functions-run-function-test-local-vs-code-ps/functions-vscode-f5.png)

1. Fűzze hozzá a lekérdezési karakterláncot `?name=<yourname>` ehhez az URL-címhez, majd a kérelem végrehajtásához használja a `Invoke-RestMethod` egy második PowerShell-parancssorban a következő módon:

    ```powershell
    PS > Invoke-RestMethod -Method Get -Uri http://localhost:7071/api/HttpTrigger?name=PowerShell
    Hello PowerShell
    ```

    A GET kérést egy böngészőből is végrehajthatja a következő URL-címről:

    <http://localhost:7071/api/HttpExample?name=PowerShell>

    Ha a HttpTrigger végpontot úgy hívja meg, hogy a `name` paramétert nem a lekérdezési paraméterként, sem pedig a törzsben küldi el, a függvény `BadRequest` hibát ad vissza. Amikor a Run. ps1 kódban áttekinti a kódot, láthatja, hogy ez a hiba a tervezés szerint történik.

1. A kérésre vonatkozó információk a **terminál** panelen jelennek meg.

    ![Függvény végrehajtása a terminál panelen](./media/functions-run-function-test-local-vs-code-ps/function-execution-terminal.png)

1. A hibakeresés leállításához nyomja le a CTRL + C billentyűkombinációt az alapvető eszközök leállításához.

Miután ellenőrizte, hogy a függvény megfelelően fut a helyi számítógépen, tegye közzé a projektet az Azure-ban.

> [!NOTE]
> A függvények Azure-ba való közzététele előtt ne felejtse el eltávolítani a `Wait-Debugger` hívásokat. 