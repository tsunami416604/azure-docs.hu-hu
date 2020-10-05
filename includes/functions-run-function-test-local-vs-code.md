---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/28/2020
ms.author: glenga
ms.openlocfilehash: 43da0ea4ddfc5410425465d436522523739218fe
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91408554"
---
## <a name="run-the-function-locally"></a>A függvény helyi futtatása

A Visual Studio Code integrálható [Azure functions alapeszközökkel](../articles/azure-functions/functions-run-local.md) , hogy a projektet a helyi fejlesztési számítógépen futtassa, mielőtt közzéteszi az Azure-ban.

1. A függvény meghívásához nyomja le az <kbd>F5</kbd> billentyűt a Function app projekt elindításához. A Core Tools kimenete a **Terminal** (Terminál) panelen jelenik meg.

1. Ha még nem telepítette Azure Functions Core Tools, válassza a **telepítés** lehetőséget a parancssorban. A központi eszközök telepítésekor az alkalmazás elindul a **terminál** panelen. Megtekintheti a helyileg futtatott HTTP-triggerű függvény URL-végpontját.

    ![Helyi függvény VS Code kimenet](./media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

1. A-t futtató alapvető eszközökkel navigáljon a következő URL-címre egy GET kérelem végrehajtásához, amely tartalmazza a `?name=Functions` lekérdezési karakterláncot.

    `http://localhost:7071/api/HttpExample?name=Functions`

1. A rendszer egy választ ad vissza, amely a következőhöz hasonlóan néz ki egy böngészőben:

    ![Böngésző – a localhost példa kimenete](./media/functions-run-function-test-local-vs-code/functions-test-local-browser.png)

1. A kérésre vonatkozó információk a **terminál** panelen jelennek meg.

    ![A feladat gazdagépének indítása – VS Code-terminál kimenete](./media/functions-run-function-test-local-vs-code/function-execution-terminal.png)

1. Nyomja le a <kbd>CTRL + C</kbd> billentyűkombinációt az alapvető eszközök leállításához és a hibakereső leválasztásához.
