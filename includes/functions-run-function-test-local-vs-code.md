---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/12/2020
ms.author: glenga
ms.openlocfilehash: f4075b8d05c179e8115ff46c9f82751817372491
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76842216"
---
## <a name="run-the-function-locally"></a>Függvény helyi futtatása

A Visual Studio Code integrálva van [Azure functions Core Tools](../articles/azure-functions/functions-run-local.md) , hogy lehetővé tegye a projekt futtatását a helyi fejlesztési számítógépen, mielőtt közzéteszi az Azure-ban.

1. A függvény meghívásához nyomja le az F5 billentyűt a Function app projekt elindításához. A Core Tools kimenete a **Terminal** (Terminál) panelen jelenik meg.

1. Ha még nem telepítette Azure Functions Core Tools, válassza a **telepítés** lehetőséget a parancssorban. A központi eszközök telepítésekor az alkalmazás elindul a **terminál** panelen.

1. A **Terminal** (Terminál) panelen másolja a vágólapra a HTTP által indított függvény URL-végpontját. 

    ![Az Azure helyi kimenete](./media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

1. Illessze be a HTTP-kérelem URL-címét a böngésző címsorába. Fűzze hozzá a lekérdezési karakterláncot `?name=<yourname>` ehhez az URL-címhez, és hajtsa végre a GET kérelmet. 

1. A rendszer egy választ ad vissza, amely a következőhöz hasonlóan néz ki egy böngészőben:

    ![A függvény által visszaadott localhost válasz a böngészőben](./media/functions-run-function-test-local-vs-code/functions-test-local-browser.png)

1. Nyomja le a SHIFT + F5 billentyűkombinációt az alapvető eszközök leállításához és a hibakereső leválasztásához.
