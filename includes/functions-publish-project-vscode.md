---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/12/2020
ms.author: glenga
ms.openlocfilehash: 256510f855256e648ae9203f46eb9f66c9ffaed6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77029202"
---
## <a name="publish-the-project-to-azure"></a>A projekt közzététele az Azure-ban

Ebben a szakaszban hozzon létre egy függvényalkalmazást és a kapcsolódó erőforrásokat az Azure-előfizetésében, majd telepítse a kódot. 

1. Válassza az Azure ikont a tevékenységsávon, majd az **Azure: Functions** területen válassza a Telepítés a **függvényalkalmazáshoz...** gombot.

    ![A projekt közzététele az Azure-ban](media/functions-publish-project-vscode/function-app-publish-project.png)

1. Adja meg a következő információkat a következő utasításokat:

    + **Válassza ki az előfizetést:** Válassza ki a használni kívánt előfizetést. Ez nem jelenik meg, ha csak egy előfizetéssel rendelkezik.

    + **Válassza ki a Függvényalkalmazást az Azure-ban:** Válassza (nem `+ Create new Function App` `Advanced`). Ez a cikk nem támogatja a [speciális közzétételi folyamatot.](../articles/azure-functions/functions-develop-vs-code.md#enable-publishing-with-advanced-create-options) 
    
    >[!IMPORTANT]
    > Meglévő függvényalkalmazásba való közzététel felülírja az adott alkalmazás tartalmát az Azure-ban. 
    
    + **Adjon meg egy globálisan egyedi nevet a függvényalkalmazásnak**: Írjon be egy URL-elérési úton érvényes nevet. A beírt név érvényesítve van, hogy megbizonyosodjon arról, hogy az Azure Functions egyedi. 
    
    ::: zone pivot="programming-language-python"
    + **Válasszon futásidejű:** Válassza ki a Python helyileg futtatott verzióját. A paranccsal ellenőrizheti a `python --version` saját verzióját.
    ::: zone-end

    ::: zone pivot="programming-language-javascript,programming-language-typescript"
    + **Válasszon futásidejű:** Válassza ki a Node.js verzióját, amelyen helyileg futott. A paranccsal ellenőrizheti a `node --version` saját verzióját.
    ::: zone-end

    + **Válasszon helyet az új erőforrásokhoz:** A jobb teljesítmény érdekében válasszon egy közeli [régiót.](https://azure.microsoft.com/regions/) 
    
1.  Ha elkészült, a következő Azure-erőforrások jönnek létre az előfizetésben:

    + **[Erőforráscsoport:](../articles/azure-resource-manager/management/overview.md)** Tartalmazza az összes létrehozott Azure-erőforrások. A név a függvényalkalmazás nevén alapul.
    + **[Tárfiók:](../articles//storage/common/storage-introduction.md#types-of-storage-accounts)** A standard tárfiók jön létre egy egyedi nevet, amely a függvény alkalmazás neve alapján jön létre.
    + **[Üzemeltetési terv:](../articles/azure-functions/functions-scale.md)** Az USA nyugati régiójában egy felhasználási terv jön létre a kiszolgáló nélküli függvényalkalmazás üzemeltetéséhez.
    + **Függvényalkalmazás:** A projekt telepítve van, és ebben az új függvényalkalmazásban fut.
    + **Application Insights:** Egy példány, amely csatlakozik a függvényalkalmazáshoz, a függvény neve alapján jön létre.

    A függvényalkalmazás létrehozása és a telepítőcsomag alkalmazása után megjelenik egy értesítés. 
    
1. Válassza a **Kimenet megtekintése** ebben az értesítésben a létrehozási és üzembe helyezési eredmények megtekintéséhez, beleértve a létrehozott Azure-erőforrásokat. Ha elmulasztja az értesítést, a jobb alsó sarokban lévő csengő ikonra választva újra láthassa.

    ![Teljes értesítés létrehozása](media/functions-publish-project-vscode/function-create-notifications.png)
