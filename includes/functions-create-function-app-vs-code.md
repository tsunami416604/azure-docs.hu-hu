---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/27/2018
ms.author: glenga
ms.openlocfilehash: 894ca0e78dfb75dffc124d3d25aa7a8e72adf627
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67065548"
---
## <a name="create-an-azure-functions-project"></a>Azure Functions-projekt létrehozása

A Visual Studio Code Azure Functions projektsablonja egy olyan projektet hoz létre, amely közzétehető egy Azure-függvényalkalmazásban. A függvényalkalmazás lehetővé teszi, hogy logikai egységbe csoportosítsa a függvényeket az erőforrások felügyelete, üzembe helyezése és megosztása érdekében.

1. A Visual Studio Code, válassza ki az Azure embléma megjelenítése a **Azure: Függvények** területre, és válassza az új projekt létrehozása ikonra.

    ![Függvényalkalmazás-projekt létrehozása](./media/functions-create-function-app-vs-code/create-function-app-project.png)

1. Adjon meg egy helyet a projekt munkaterületének, és válassza a **Select** (Kiválasztás) lehetőséget.

    > [!NOTE]
    > Ez a cikk munkaterületen kívüli teljesítésre lett tervezve. Ebben az esetben ne válasszon olyan projektmappát, amely valamely munkaterület része.

1. Válassza ki a függvényalkalmazási projektjének nyelvét. Ez a cikk a JavaScriptet használja.
    ![Projekt nyelvének kiválasztása](./media/functions-create-function-app-vs-code/create-function-app-project-language.png)

1. Válasszon egy sablont az első függvény a projekthez. Adja meg a függvény nevét.
    ![Válassza ki az első függvény](./media/functions-create-function-app-vs-code/create-function-app-project-first-function.png)

1. Amikor a program kéri, válassza az **Add to workspace** (Hozzáadás munkaterülethez) lehetőséget.

A Visual Studio Code létrehozza a függvényalkalmazást egy új munkaterületen. Ez a projekt a [host.json](../articles/azure-functions/functions-host-json.md) és a [local.settings.json](../articles/azure-functions/functions-run-local.md#local-settings-file) konfigurációs fájlokat tartalmazza, valamint az esetleges nyelvspecifikus projektfájlokat is. Emellett a projekt mappájában rendelkezésére áll majd egy új Git-adattár is.