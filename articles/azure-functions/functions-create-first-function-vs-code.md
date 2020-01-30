---
title: Az első függvény létrehozása az Azure-ban a Visual Studio Code használatával
description: Hozzon létre és tegyen közzé az Azure-on egy egyszerű, HTTP-eseményindítót használó függvényt az Azure Functions-bővítmény használatával a Visual Studio Code-ban.
ms.topic: quickstart
ms.date: 01/10/2020
ms.custom: mvc, devcenter
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 0540c7b01d693975f34515c7d13f0477ac74d4a1
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76842256"
---
# <a name="quickstart-create-an-azure-functions-project-using-visual-studio-code"></a>Gyors útmutató: Azure Functions projekt létrehozása a Visual Studio Code használatával

Ebben a cikkben a Visual Studio Code használatával hoz létre egy olyan függvényt, amely válaszol a HTTP-kérelmekre. A kód helyi tesztelését követően a Azure Functions kiszolgáló nélküli környezetében helyezheti üzembe. A rövid útmutató elvégzésével az Azure-fiókjában néhány USD értékű vagy annál kisebb költséggel jár. 

A cikk [CLI-alapú verziója](functions-create-first-azure-function-azure-cli.md) is létezik.

## <a name="prerequisites"></a>Előfeltételek

+ [Visual Studio Code](https://code.visualstudio.com/) az egyik [támogatott platformon](https://code.visualstudio.com/docs/supporting/requirements#_platforms). 
::: zone pivot="programming-language-csharp"
+ A Visual Studio Code [ C# bővítménye](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) .
::: zone-end
::: zone pivot="programming-language-javascript,programming-language-typescript"
+ [Node. js](https://nodejs.org/), aktív LTS és karbantartási LTS-verziók (8.11.1 és 10.14.1 ajánlott).
::: zone-end
::: zone pivot="programming-language-python"
+ [Python 3,7](https://www.python.org/downloads/release/python-375/) vagy [Python 3,6](https://www.python.org/downloads/release/python-368/), amelyet Azure functions támogat. A Python 3,8 még nem támogatott. 

+ A [Python-bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-python.python) a Visual Studio Code-hoz.
::: zone-end
::: zone pivot="programming-language-powershell"
+ [PowerShell-mag](/powershell/scripting/install/installing-powershell-core-on-windows)

+ A [.net Core SDK 2.2 +](https://www.microsoft.com/net/download)

+ A [Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell)-hoz készült PowerShell-bővítmény. 
::: zone-end

+ A Visual Studio Code [Azure functions bővítménye](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) . 

+ Aktív előfizetéssel rendelkező [Azure-fiók](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing) . Ha még nincs előfizetése, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), mielőtt hozzákezd.

## <a name="create-an-azure-functions-project"></a>A helyi projekt létrehozása 

Ebben a szakaszban a Visual Studio Code használatával hozzon létre egy helyi Azure Functions projektet a választott nyelven. A cikk későbbi részében a függvény kódját közzé fogja tenni az Azure-ban. 

1. Válassza ki az Azure ikont a tevékenység sávjában, majd az **Azure: functions** területen válassza az **új projekt létrehozása...** ikont.

    ![Válassza az új projekt létrehozása lehetőséget.](media/functions-create-first-function-vs-code/create-new-project.png)

1. Válasszon egy címtárbeli helyet a projekt munkaterülethez, és válassza a **kiválasztás**lehetőséget.

    > [!NOTE]
    > Ezeket a lépéseket úgy tervezték, hogy a munkaterületen kívül is el lehessen végezni. Ebben az esetben ne válasszon olyan projektmappát, amely valamely munkaterület része.

1. Adja meg a következő információkat a kérdésekben:

    ::: zone pivot="programming-language-csharp"

    | Kérdés | Value (Díj) | 
    | ------ | ----- | 
    | Válasszon nyelvet a függvény projekthez | C# |
    | Válasszon verziót | Azure Functions v2 | 
    | Válasszon sablont a projekt első függvényéhez | HTTP eseményindító | 
    | Adja meg a függvény nevét | HttpExample | 
    | Névtér megadása | Saját. functions | 
    | Authorization level (Engedélyszint) | Névtelen | 
    | Válassza ki, hogyan szeretné megnyitni a projektet? | Hozzáadás a munkaterülethez |

    ::: zone-end

    ::: zone pivot="programming-language-javascript"

    | Kérdés | Value (Díj) | 
    | ------ | ----- | 
    | Válasszon nyelvet a függvény projekthez | JavaScript | 
    | Válasszon verziót | Azure Functions v2 | 
    | Válasszon sablont a projekt első függvényéhez | HTTP eseményindító | 
    | Adja meg a függvény nevét | HttpExample | 
    | Authorization level (Engedélyszint) | Névtelen | 
    | Válassza ki, hogyan szeretné megnyitni a projektet? | Hozzáadás a munkaterülethez |

    ::: zone-end

    ::: zone pivot="programming-language-typescript"

    | Kérdés | Value (Díj) | 
    | ------ | ----- | 
    | Válasszon nyelvet a függvény projekthez | TypeScript | 
    | Válasszon verziót | Azure Functions v2 | 
    | Válasszon sablont a projekt első függvényéhez | HTTP eseményindító | 
    | Adja meg a függvény nevét | HttpExample | 
    | Authorization level (Engedélyszint) | Névtelen | 
    | Válassza ki, hogyan szeretné megnyitni a projektet? | Hozzáadás a munkaterülethez |

    ::: zone-end

    ::: zone pivot="programming-language-powershell"

    | Kérdés | Value (Díj) | 
    | ------ | ----- | 
    | Válasszon nyelvet a függvény projekthez | PowerShell | 
    | Válasszon verziót | Azure Functions v2 | 
    | Válasszon sablont a projekt első függvényéhez | HTTP eseményindító | 
    | Adja meg a függvény nevét | HttpExample | 
    | Authorization level (Engedélyszint) | Névtelen | 
    | Válassza ki, hogyan szeretné megnyitni a projektet? | Hozzáadás a munkaterülethez |

    ::: zone-end

    ::: zone pivot="programming-language-python"

    | Kérdés | Value (Díj) | 
    | ------ | ----- | 
    | Válasszon nyelvet a függvény projekthez | Python | 
    | Válasszon verziót | Azure Functions v2 | 
    | Válasszon egy Python-aliast a virtuális környezet létrehozásához | Python-alias | 
    | Válasszon sablont a projekt első függvényéhez | HTTP eseményindító | 
    | Adja meg a függvény nevét | HttpExample | 
    | Authorization level (Engedélyszint) | Névtelen | 
    | Válassza ki, hogyan szeretné megnyitni a projektet? | Hozzáadás a munkaterülethez | 

    ::: zone-end

1. A Visual Studio Code a következő műveleteket végzi el:

    + Létrehoz egy Azure Functions projektet egy új munkaterületen, amely tartalmazza a [Host. JSON](functions-host-json.md) és a [Local. Settings. JSON](functions-run-local.md#local-settings-file) konfigurációs fájlokat. 

    ::: zone pivot="programming-language-csharp"

    + Létrehoz egy [HttpExample.cs-függvénytár-fájlt](functions-dotnet-class-library.md#functions-class-library-project) , amely megvalósítja a függvényt.

    ::: zone-end
        
    ::: zone pivot="programming-language-javascript"
    
    + Létrehoz egy Package. JSON fájlt a gyökérkönyvtárban.

    + Létrehoz egy HttpExample mappát, amely tartalmazza a [function. JSON definíciós fájlt](functions-reference-node.md#folder-structure) és az [index. js fájlt](functions-reference-node.md#exporting-a-function), a függvény kódját tartalmazó Node. js fájlt.
    
    ::: zone-end
    
    ::: zone pivot="programming-language-typescript"
    
    + Létrehoz egy Package. JSON fájlt és egy tsconfig. JSON fájlt a gyökérkönyvtárban.

    + Létrehoz egy HttpExample mappát, amely tartalmazza a [function. JSON definíciós fájlt](functions-reference-node.md#folder-structure) és az [index. ts fájlt](functions-reference-node.md#typescript), a függvény kódját tartalmazó írógéppel.
    
    ::: zone-end
    
    ::: zone pivot="programming-language-powershell"
    
    + Létrehoz egy HttpExample mappát, amely tartalmazza a [function. JSON definíciós fájlt](functions-reference-python.md#programming-model) és a Run. ps1 fájlt, amely tartalmazza a függvény kódját.
    
    ::: zone-end
    
    ::: zone pivot="programming-language-python"
    
    + Létrehoz egy projekt szintű követelményeket tartalmazó. txt fájlt, amely felsorolja a függvények által igényelt csomagokat.
    
    + Létrehoz egy HttpExample mappát, amely tartalmazza a [function. JSON definíciós fájlt](functions-reference-python.md#programming-model) , valamint a \_\_init\_\_. file. a fájlt, amely tartalmazza a függvény kódját.
    
    ::: zone-end

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-python"

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

::: zone-end

::: zone pivot="programming-language-powershell"

[!INCLUDE [functions-run-function-test-local-vs-code-ps](../../includes/functions-run-function-test-local-vs-code-ps.md)]

::: zone-end

Miután meggyőződött róla, hogy a függvény megfelelően fut a helyi számítógépen, ideje, hogy a Visual Studio Code használatával tegye közzé a projektet közvetlenül az Azure-ban. 

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

## <a name="run-the-function-in-azure"></a>A függvény futtatása az Azure-ban

1. Másolja a vágólapra a HTTP-eseményindító URL-címét az **Output** (Kimenet) panelről. Ismét adja hozzá a `name` lekérdezési karakterláncot az URL-cím végéhez `?name=<yourname>` és hajtsa végre a kérelmet.

    A HTTP-eseményindítót használó függvényt meghívó URL-címnek az alábbi formátumban kell lennie:

        http://<functionappname>.azurewebsites.net/api/httpexample?name=<yourname> 

1. Illessze be a HTTP-kérelem új URL-címét a böngésző címsorába. Az alábbi példa a böngészőben a függvény által visszaadott távoli GET kérelemre adott választ mutatja be: 

    ![A függvény által visszaadott válasz a böngészőben](./media/functions-create-first-function-vs-code/functions-test-remote-browser.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha folytatja a következő lépéssel, [vegyen fel egy Azure Storage-várólista-kötést a függvényhez](functions-add-output-binding-storage-queue-vs-code.md), az összes erőforrást meg kell őriznie, hogy az Ön által már elvégzett feladatra épít.

Ellenkező esetben a következő lépésekkel törölheti a Function alkalmazást és annak kapcsolódó erőforrásait, hogy elkerülje a további költségek felmerülését.

[!INCLUDE [functions-cleanup-resources-vs-code.md](../../includes/functions-cleanup-resources-vs-code.md)]

A functions-költségekkel kapcsolatos további tudnivalókért lásd: a [felhasználási terv költségeinek becslése](functions-consumption-costs.md).

## <a name="next-steps"></a>Következő lépések

A Visual Studio Code segítéségével létrehozott egy egyszerű, HTTP-eseményindítóval aktivált függvényt tartalmazó függvényalkalmazást. A következő cikkben kibonthatja ezt a függvényt egy kimeneti kötés hozzáadásával. Ez a kötés a HTTP-kérelemből írja be a karakterláncot egy Azure Queue Storage-várólistában lévő üzenetbe. 

> [!div class="nextstepaction"]
> [Azure Storage-várólista kötésének hozzáadása a függvényhez](functions-add-output-binding-storage-queue-vs-code.md)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
