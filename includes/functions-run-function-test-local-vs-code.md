---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/28/2020
ms.author: glenga
ms.openlocfilehash: a3cb0986637d5ce238930fb87aef71fed684097a
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/07/2020
ms.locfileid: "96842362"
---
## <a name="run-the-function-locally"></a>A függvény helyi futtatása

A Visual Studio Code integrálható [Azure functions alapeszközökkel](../articles/azure-functions/functions-run-local.md) , hogy a projektet a helyi fejlesztési számítógépen futtassa, mielőtt közzéteszi az Azure-ban.

1. A függvény meghívásához nyomja le az <kbd>F5</kbd> billentyűt a Function app projekt elindításához. A Core Tools kimenete a **Terminal** (Terminál) panelen jelenik meg. Ha a Windowsban nem fut a probléma, győződjön meg arról, hogy a Visual Studio Code alapértelmezett terminálja nem **WSL bash**.

1. Ha még nem telepítette Azure Functions Core Tools, válassza a **telepítés** lehetőséget a parancssorban. A központi eszközök telepítésekor az alkalmazás elindul a **terminál** panelen. Megtekintheti a helyileg futtatott HTTP-triggerű függvény URL-végpontját.

    ![Helyi függvény VS Code kimenet](./media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

1. A-t futtató alapvető eszközökkel navigáljon a következő URL-címre egy GET kérelem végrehajtásához, amely tartalmazza a `?name=Functions` lekérdezési karakterláncot.

    `http://localhost:7071/api/HttpExample?name=Functions`

1. A rendszer egy választ ad vissza, amely a következőhöz hasonlóan néz ki egy böngészőben:

    ![Böngésző – a localhost példa kimenete](./media/functions-run-function-test-local-vs-code/functions-test-local-browser.png)

1. A kérésre vonatkozó információk a **terminál** panelen jelennek meg.

    ![A feladat gazdagépének indítása – VS Code-terminál kimenete](./media/functions-run-function-test-local-vs-code/function-execution-terminal.png)

1. Nyomja le a <kbd>CTRL + C</kbd> billentyűkombinációt az alapvető eszközök leállításához és a hibakereső leválasztásához.
