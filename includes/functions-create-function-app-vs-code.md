---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/27/2018
ms.author: glenga
ms.openlocfilehash: 79dbee33928fbc7560d0ea27be3af25cc510e996
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2018
ms.locfileid: "52638660"
---
## <a name="create-an-azure-functions-project"></a>Azure Functions-projekt létrehozása

A Visual Studio Code Azure Functions projektsablonja egy olyan projektet hoz létre, amely közzétehető egy Azure-függvényalkalmazásban. A függvényalkalmazás lehetővé teszi, hogy logikai egységbe csoportosítsa a függvényeket az erőforrások felügyelete, üzembe helyezése és megosztása érdekében.

1. A Visual Studio Code-ban, válassza az Azure logót az **Azure: Functions** terület megjelenítéséhez, majd válassza a Create New Project (Új projekt létrehozása) ikont.

    ![Függvényalkalmazás-projekt létrehozása](./media/functions-create-function-app-vs-code/create-function-app-project.png)

1. Adjon meg egy helyet a projekt munkaterületének, és válassza a **Select** (Kiválasztás) lehetőséget.

    > [!NOTE]
    > Ez a cikk munkaterületen kívüli teljesítésre lett tervezve. Ebben az esetben ne válasszon olyan projektmappát, amely valamely munkaterület része.

1. Válassza ki a függvényalkalmazási projektjének nyelvét. Ez a cikk a JavaScriptet használja.
    ![Projekt nyelvének kiválasztása](./media/functions-create-function-app-vs-code/create-function-app-project-language.png)

1. Amikor a program kéri, válassza az **Add to workspace** (Hozzáadás munkaterülethez) lehetőséget.

A Visual Studio Code létrehozza a függvényalkalmazást egy új munkaterületen. Ez a projekt a [host.json](../articles/azure-functions/functions-host-json.md) és a [local.settings.json](../articles/azure-functions/functions-run-local.md#local-settings-file) konfigurációs fájlokat tartalmazza, valamint az esetleges nyelvspecifikus projektfájlokat is. Emellett a projekt mappájában rendelkezésére áll majd egy új Git-adattár is.