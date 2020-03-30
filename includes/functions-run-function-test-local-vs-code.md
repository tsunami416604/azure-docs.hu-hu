---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/12/2020
ms.author: glenga
ms.openlocfilehash: f4af3c202d4f00c4ac3041921175c92226f0db7c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "76964113"
---
## <a name="run-the-function-locally"></a>Függvény helyi futtatása

A Visual Studio Code integrálható [az Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md) alkalmazással, így a projektet a helyi fejlesztői számítógépen futtathatja, mielőtt közzétetelne az Azure-ban.

1. A függvény hívásához nyomja le az F5 billentyűt a függvényalkalmazás-projekt elindításához. A Core Tools kimenete a **Terminal** (Terminál) panelen jelenik meg.

1. Ha még nem telepítette az Azure Functions Core Tools eszközt, válassza a **Telepítés** lehetőséget a parancssorba. A Core Tools telepítésekor az alkalmazás elindul a **Terminál** panelen. Láthatja a HTTP-aktivált függvény helyileg futó URL-végpontját. 

    ![Az Azure helyi kimenete](./media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

1. A Core Tools futásával keresse meg a következő URL-címet egy GET-kérelem végrehajtásához, amely tartalmazza a `?name=Functions` lekérdezési karakterláncot.

    <http://localhost:7071/api/HttpExample?name=Functions>

1. A válasz a következőhöz hasonlóan jelenik meg a böngészőben:

    ![A függvény által visszaadott localhost válasz a böngészőben](./media/functions-run-function-test-local-vs-code/functions-test-local-browser.png)

1. A kérelemmel kapcsolatos információk a **Terminál** panelen jelennek meg.

    ![Funkció végrehajtása a Terminál panelen](./media/functions-run-function-test-local-vs-code/function-execution-terminal.png)

1. Nyomja le a Ctrl + C billentyűkombinációt az alapvető eszközök leállításához és a hibakereső leválasztásához.
