---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/27/2018
ms.author: glenga
ms.openlocfilehash: f9b853f0e86fc298ab35421928492ba813d85827
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444631"
---
## <a name="create-an-azure-functions-project"></a>A függvény a Functions-projekt létrehozása 

A Visual Studio Code Azure Functions projektsablonja egy olyan projektet hoz létre, amely közzétehető egy Azure-függvényalkalmazásban. A függvényalkalmazás lehetővé teszi, hogy logikai egységbe csoportosítsa a függvényeket az erőforrások felügyelete, üzembe helyezése és megosztása érdekében.

1. A Visual Studio Code-nyomja le az F1 billentyűt a parancskatalógus megnyitásához. A parancskatalógus keresése és kiválasztása `Azure Functions: Create new project...`.

1. Adja meg a projekt munkaterület könyvtár helyét, és válassza a **kiválasztása**.

    > [!NOTE]
    > Ezeket a lépéseket tervezték, hogy a munkaterület kívül befejezni. Ebben az esetben ne válasszon olyan projektmappát, amely valamely munkaterület része.

1. Kövesse az utasításokat a következő információkat biztosítja:

    | Kérdés | Érték | Leírás |
    | ------ | ----- | ----------- |
    | Válassza ki a függvényalkalmazás projektjét nyelvét | C# vagy JavaScript | Ez a cikk támogatja C# és a JavaScript. Python, lásd: [Python cikkben](https://code.visualstudio.com/docs/python/tutorial-azure-functions), és a PowerShell-lel, lásd: [PowerShell cikkben](../articles/azure-functions/functions-create-first-function-powershell.md).  |
    | A projekt első függvényét sablonjának kiválasztása | HTTP eseményindító | Egy HTTP által aktivált függvény létrehozása az új függvényalkalmazásban. |
    | Adja meg a függvény neve | HttpTrigger | Nyomja le az Enter billentyűt az alapértelmezett név használatát. |
    | Adja meg a névtér | My.Functions | (C# csak) C# osztálykódtárakat rendelkeznie kell egy névteret.  |
    | Authorization level (Engedélyszint) | Függvény | Szükséges egy [függvénykulcs](../articles/azure-functions/functions-bindings-http-webhook.md#authorization-keys) a függvény HTTP-végpont meghívására. |
    | Válassza ki, hogyan szeretné a projekt megnyitása | munkaterület hozzáadása | Az aktuális munkaterületen található a függvényalkalmazást hoz létre. |

A Visual Studio Code létrehozza a függvényalkalmazást egy új munkaterületen. Ez a projekt a [host.json](../articles/azure-functions/functions-host-json.md) és a [local.settings.json](../articles/azure-functions/functions-run-local.md#local-settings-file) konfigurációs fájlokat tartalmazza, valamint az esetleges nyelvspecifikus projektfájlokat is. 

Egy HTTP által aktivált új függvény is létrejön a függvényalkalmazás projektjét HttpTrigger mappájában.