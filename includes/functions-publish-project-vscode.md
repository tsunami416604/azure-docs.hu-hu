---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/12/2020
ms.author: glenga
ms.openlocfilehash: f1553a5c9d55366b2764877b48d0606ff8e0b370
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76842195"
---
## <a name="publish-the-project-to-azure"></a>A projekt közzététele az Azure-ban

Ebben a szakaszban egy Function-alkalmazást és egy kapcsolódó erőforrást hoz létre az Azure-előfizetésében, majd üzembe helyezi a kódot. 

1. Válassza ki az Azure ikont a tevékenység sávjában, majd az **Azure: functions** területen válassza az **üzembe helyezés az alkalmazásban** ... gombot.

    ![A projekt közzététele az Azure-ban](media/functions-publish-project-vscode/function-app-publish-project.png)

1. Adja meg a következő információkat a kérdésekben:

    ::: zone pivot="programming-language-csharp,programming-language-powershell"

    | Kérdés | Value (Díj) | Leírás |
    | ------ | ----- | ----- |
    | Előfizetés kiválasztása | Az Ön előfizetése | Több előfizetés esetén jelenik meg. |
    | függvényalkalmazás kiválasztása az Azure-ban | + Új függvényalkalmazás létrehozása | Meglévő függvényalkalmazásba való közzététel felülírja az adott alkalmazás tartalmát az Azure-ban. |
    | Adja meg a Function alkalmazás globálisan egyedi nevét | Egyedi név | A függvényalkalmazás nevéhez használható érvényes karakterek a következők: `a-z`, `0-9` és `-`. |
    | Válasszon helyet az új erőforrásokhoz | Region (Régió) | Válasszon egy Önhöz közeli [régiót](https://azure.microsoft.com/regions/). | 

    ::: zone-end

    ::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python"

    | Kérdés | Value (Díj) | Leírás |
    | ------ | ----- | ----- |
    | Előfizetés kiválasztása | Az Ön előfizetése | Több előfizetés esetén jelenik meg. |
    | függvényalkalmazás kiválasztása az Azure-ban | + Új függvényalkalmazás létrehozása | Meglévő függvényalkalmazásba való közzététel felülírja az adott alkalmazás tartalmát az Azure-ban. |
    | Adja meg a Function alkalmazás globálisan egyedi nevét | Egyedi név | A függvényalkalmazás nevéhez használható érvényes karakterek a következők: `a-z`, `0-9` és `-`. |
    | Futtatókörnyezet kiválasztása | Az Ön verziója | Válassza ki a helyileg futtatott nyelvi verziót. |
    | Válasszon helyet az új erőforrásokhoz | Region (Régió) | Válasszon egy Önhöz közeli [régiót](https://azure.microsoft.com/regions/). | 

    ::: zone-end

    
1.  Ha elkészült, a következő Azure-erőforrások jönnek létre az előfizetésben:

    + **[Erőforráscsoport](../articles/azure-resource-manager/management/overview.md)** : az összes létrehozott Azure-erőforrást tartalmazza. A név a Function alkalmazás nevén alapul.
    + **[Storage-fiók](../articles//storage/common/storage-introduction.md#types-of-storage-accounts)** : a standard Storage-fiók egy egyedi névvel jön létre, amely a függvény alkalmazásának nevén alapul.
    + **[Üzemeltetési terv](../articles/azure-functions/functions-scale.md)** : az USA nyugati régiójában jön létre egy használati terv, amely a kiszolgáló nélküli Function alkalmazást üzemelteti.
    + **Function alkalmazás**: a projekt üzembe helyezése és futtatása ebben az új Function alkalmazásban történik.
    + **[Application Insights]()** : a Function alkalmazáshoz csatlakoztatott példány a függvény neve alapján jön létre.

    A függvényalkalmazás létrehozása és a telepítőcsomag alkalmazása után megjelenik egy értesítés. 
    
1. Válassza ki az értesítés **kimenetének megtekintése** lehetőséget a létrehozási és a telepítési eredmények megtekintéséhez, beleértve a létrehozott Azure-erőforrásokat is.

    ![Teljes értesítés létrehozása](media/functions-publish-project-vscode/function-create-notifications.png)

1. Vissza az **Azure: functions** területen az oldal sávján bontsa ki az új Function alkalmazást az előfizetés alatt. Bontsa ki a **függvények**elemet, kattintson a jobb gombbal (Windows) vagy a CTRL + kattintás (MacOS) elemre a **HttpExample**, majd válassza a **függvény URL-címének másolása**lehetőséget.

    ![A függvény URL-címének másolása az új HTTP-triggerhez](./media/functions-publish-project-vscode/function-copy-endpoint-url.png)
