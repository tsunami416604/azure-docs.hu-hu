---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/12/2020
ms.author: glenga
ms.openlocfilehash: 8c63d314c253152d2815a70831870fe331071c68
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964115"
---
## <a name="publish-the-project-to-azure"></a>A projekt közzététele az Azure-ban

Ebben a szakaszban egy Function-alkalmazást és egy kapcsolódó erőforrást hoz létre az Azure-előfizetésében, majd üzembe helyezi a kódot. 

1. Válassza ki az Azure ikont a tevékenység sávjában, majd az **Azure: functions** területen válassza az **üzembe helyezés az alkalmazásban** ... gombot.

    ![A projekt közzététele az Azure-ban](media/functions-publish-project-vscode/function-app-publish-project.png)

1. Adja meg a következő információkat a kérdésekben:

    + **Előfizetés kiválasztása**: válassza ki a használni kívánt előfizetést. Ez nem jelenik meg, ha csak egy előfizetéssel rendelkezik.

    + **Függvényalkalmazás kiválasztása az Azure-ban**: válassza a `+ Create new Function App` (nem `Advanced`) lehetőséget. Ez a cikk nem támogatja a [speciális közzétételi folyamatot](../articles/azure-functions/functions-develop-vs-code.md#enable-publishing-with-advanced-create-options). 
    
    >[!IMPORTANT]
    > Meglévő függvényalkalmazásba való közzététel felülírja az adott alkalmazás tartalmát az Azure-ban. 
    
    + **Adja meg a Function alkalmazás globálisan egyedi nevét**: írjon be egy URL-útvonalon érvényes nevet. A rendszer érvényesíti a beírt nevet, hogy a Azure Functions egyedi legyen. 
    
    ::: zone pivot="programming-language-python"
    + **Válasszon egy futtatókörnyezetet**: válassza ki, hogy a Python melyik verzióját helyileg futtatja. Az `python --version` parancs használatával megtekintheti a verziót.
    ::: zone-end

    ::: zone pivot="programming-language-javascript,programming-language-typescript"
    + **Válassza ki a kívánt futtatókörnyezetet**: válassza ki a helyileg futtatott Node. js-verziót. Az `node --version` parancs használatával megtekintheti a verziót.
    ::: zone-end

    + **Válasszon egy helyet az új erőforrásokhoz**: a jobb teljesítmény érdekében válasszon egy Önhöz közeli [régiót](https://azure.microsoft.com/regions/) . 
    
1.  Ha elkészült, a következő Azure-erőforrások jönnek létre az előfizetésben:

    + **[Erőforráscsoport](../articles/azure-resource-manager/management/overview.md)** : az összes létrehozott Azure-erőforrást tartalmazza. A név a Function alkalmazás nevén alapul.
    + **[Storage-fiók](../articles//storage/common/storage-introduction.md#types-of-storage-accounts)** : a standard Storage-fiók egy egyedi névvel jön létre, amely a függvény alkalmazásának nevén alapul.
    + **[Üzemeltetési terv](../articles/azure-functions/functions-scale.md)** : az USA nyugati régiójában jön létre egy használati terv, amely a kiszolgáló nélküli Function alkalmazást üzemelteti.
    + **Function alkalmazás**: a projekt üzembe helyezése és futtatása ebben az új Function alkalmazásban történik.
    + **[Application Insights]()** : a Function alkalmazáshoz csatlakoztatott példány a függvény neve alapján jön létre.

    A függvényalkalmazás létrehozása és a telepítőcsomag alkalmazása után megjelenik egy értesítés. 
    
1. Válassza ki az értesítés **kimenetének megtekintése** lehetőséget a létrehozási és a telepítési eredmények megtekintéséhez, beleértve a létrehozott Azure-erőforrásokat is. Ha kihagyja az értesítést, válassza a jobb alsó sarokban található harang ikont az újbóli megjelenítéshez.

    ![Teljes értesítés létrehozása](media/functions-publish-project-vscode/function-create-notifications.png)
