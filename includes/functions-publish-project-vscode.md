---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/12/2020
ms.author: glenga
ms.openlocfilehash: d8665b4cec3357baee5d6c1b77b5719645575419
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/10/2020
ms.locfileid: "81112869"
---
## <a name="publish-the-project-to-azure"></a>A projekt közzététele az Azure-ban

Ebben a szakaszban hozzon létre egy függvényalkalmazást és a kapcsolódó erőforrásokat az Azure-előfizetésében, majd telepítse a kódot. 

> [!IMPORTANT]
> Meglévő függvényalkalmazásba való közzététel felülírja az adott alkalmazás tartalmát az Azure-ban. 


1. Válassza az Azure ikont a tevékenységsávon, majd az **Azure: Functions** területen válassza a Telepítés a **függvényalkalmazáshoz...** gombot.

    ![A projekt közzététele az Azure-ban](media/functions-publish-project-vscode/function-app-publish-project.png)

1. Adja meg a következő információkat a következő utasításokat:

    + **Válassza ki az előfizetést:** Válassza ki a használni kívánt előfizetést. Ez nem jelenik meg, ha csak egy előfizetéssel rendelkezik.

    + **Válassza ki a Függvényalkalmazást az Azure-ban:** Válassza a lehetőséget. `+ Create new Function App` (Ne válassza azt `Advanced` a lehetőséget, amely nem szerepel ebben a cikkben.)
      
    + **Adjon meg egy globálisan egyedi nevet a függvényalkalmazásnak**: Írjon be egy URL-elérési úton érvényes nevet. A beírt név érvényesítve van, hogy megbizonyosodjon arról, hogy az Azure Functions egyedi. 
    
    ::: zone pivot="programming-language-python"
    + **Válasszon futásidejű:** Válassza ki a Python helyileg futtatott verzióját. A paranccsal ellenőrizheti a `python --version` saját verzióját.
    ::: zone-end

    ::: zone pivot="programming-language-javascript,programming-language-typescript"
    + **Válasszon futásidejű:** Válassza ki a Node.js verzióját, amelyen helyileg futott. A paranccsal ellenőrizheti a `node --version` saját verzióját.
    ::: zone-end

    + **Válasszon helyet az új erőforrásokhoz:** A jobb teljesítmény érdekében válasszon egy közeli [régiót.](https://azure.microsoft.com/regions/) 
    
1.  Ha elkészült, a következő Azure-erőforrások jönnek létre az előfizetésben, a függvényalkalmazás neve alapján alapuló nevek használatával:
    
    + Erőforráscsoport, amely a kapcsolódó erőforrások logikai tárolója.
    + Egy szabványos Azure Storage-fiók, amely fenntartja a projektek állapot- és egyéb adatait.
    + Egy felhasználási terv, amely meghatározza a kiszolgáló nélküli függvényalkalmazás alapjául szolgáló állomást. 
    + Egy függvényalkalmazás, amely biztosítja a függvénykód végrehajtásának környezetét. A függvényalkalmazás lehetővé teszi a függvények logikai egységként történő csoportosítását az erőforrások egyszerűbb kezelése, üzembe helyezése és megosztása érdekében ugyanazon a tárhelycsomagon belül.
    + A függvényalkalmazáshoz csatlakoztatott Application Insights-példány, amely a kiszolgáló nélküli függvény használatát követi nyomon.

    A függvényalkalmazás létrehozása és a telepítőcsomag alkalmazása után megjelenik egy értesítés. 
    
1. Válassza a **Kimenet megtekintése** ebben az értesítésben a létrehozási és üzembe helyezési eredmények megtekintéséhez, beleértve a létrehozott Azure-erőforrásokat. Ha elmulasztja az értesítést, a jobb alsó sarokban lévő csengő ikonra választva újra láthassa.

    ![Teljes értesítés létrehozása](media/functions-publish-project-vscode/function-create-notifications.png)
