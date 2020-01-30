---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/16/2020
ms.author: glenga
ms.openlocfilehash: c54145cf48912d3911a39e681d85cb6907be8e52
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76842316"
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

    A GET kérést egy böngészőből is végrehajthatja.

    Ha a HttpTrigger-végpontot úgy hívja meg, hogy `name` paramétert nem egy lekérdezési paraméterként vagy a törzsben adja át, a függvény egy [HttpStatusCode]:: BadRequest hibát ad vissza. Amikor a Run. ps1 kódban áttekinti a kódot, láthatja, hogy ez a hiba a tervezés szerint történik.

1. A hibakeresés leállításához nyomja le a Shift + F5 billentyűkombinációt.

Miután ellenőrizte, hogy a függvény megfelelően fut a helyi számítógépen, tegye közzé a projektet az Azure-ban.

> [!NOTE]
> A függvények Azure-ba való közzététele előtt ne felejtse el eltávolítani a `Wait-Debugger` hívásokat. 